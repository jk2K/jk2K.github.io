---
title: Gemini CLI 源码核心逻辑解析
date: 2025-07-14 16:00:00 +0800
categories: [技术]
mermaid: true
tags:
- ai
---

## Gemini CLI 介绍
Gemini CLI 是 Google 2025 年 6 月 25 号发布的开源 AI 编码 agent，以终端方式运行。他的特色如下
1. 遵循 Unix 哲学，可以像调用普通 Linux 命令那样使用
2. 内置 Google Search
3. 支持 MCP 协议，可调用外部工具

目前 Gemini CLI 免费版频率限制如下: 每分钟 60 次模型请求，一天最多 1000 次请求

介绍一个 Gemini CLI 的用例：生成一个 30 秒的短视频，讲述橘猫环游澳大利亚的冒险故事

{% include embed/video.html src='/assets/video/GenMedia-demo-keyword.mp4' %}

上述视频的提示词
> Make me a 30s video showing the story of a ginger cat's adventures around Australia

复刻上述视频需要安装 mcp 工具，用于图片和视频生成，感兴趣的可以参考[文档](https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia)安装

Gemini CLI 源码是 [Apache 2.0](https://github.com/google-gemini/gemini-cli/blob/main/LICENSE) 协议。
研究它的源码对设计 agent 类的应用会有很大帮助

## 目录结构
- `packages/cli`: ui 层，负责处理用户输入、展示 ai 模型的输出
- `packages/core`: 后端，对接 ui 层，负责跟 Gemini API 进行交互，管理工具的执行
- `packages/core/src/tools/`: 各种扩展工具，例如网络搜索、shell 脚本、文件搜索、编辑文件等

## 交互流程
Gemini CLI 典型流程如下

1. 用户输入: 用户在终端输入提示词或者命令，由 `packages/cli` 负责
2. 向 `core` 包发送请求: `packages/cli` 发送用户输入至 `packages/core`
3. 请求处理: `core` 包:
  - 构造发送给 Gemini API 的提示词，包含历史消息、可被调用的工具列表等信息
  - 发送提示词给 Gemini API
4. Gemini API 响应: Gemini API 处理提示词并返回响应，响应可能是明确的答复或者请求执行工具
5. 执行工具（如果需要的话）：
  - 当 Gemini API 请求执行一个工具，`core` 包负责执行这个工具
  - 如果这个工具能够修改文件系统或者执行 shell 命令，用户会收到这个工具的介绍和参数，用户同意后才会执行
  - 只读的操作，例如读取文件，不需要用户同意就可以执行
  - 用户一旦同意执行，或者这个工具是只读的，`core`包会执行这个工具，执行结果会发回给 Gemini API
  - Gemini API 处理这个工具的执行结果，生成最终的响应
6. 向 `cli` 包返回响应: `core` 包发送响应至 `cli` 包
7. 展示给用户: `cli` 包将响应格式化后展示在终端

## 消息处理流程
![message handle process](/assets/img/how-gemini-cli-works/gemini-cli-message-process-pipeline.png)

1. 用户输入消息后，`cli` 包里的 `submitQuery` 会发起请求
2. submitQuery 调用 `GeminiClient` 类的 `sendMessageStream` 方法，并处理该方法返回的事件列表
3. `GeminiClient` 类的 `sendMessageStream` 方法根据消息历史构造请求，调用 `Turn` 类的 `run` 方法
4. `Turn` 类的 `run` 方法调用 `GeminiChat` 类的 `sendMessageStream` 方法，`run` 方法对 Gemini 的响应进行处理，抛出事件
5. `GeminiChat` 类的 `sendMessageStream` 方法调用 `contentGenerator` 的 `generateContentStream` 方法对大模型发起请求
6. `contentGenerator` 是 `interface` 类型，当前的实现类是 `CodeAssistServer`

## 历史消息压缩
消息正常会全部发给大模型，填充到上下文里供大模型了解背景信息，以实现更准确的推理。大模型的上下文是有限的，上下文以 token 计数，`gemini-2.5-pro` 的输入 token 上限是 1048576（100 万）。DeepSeek-V3 的输入 token 上限是 128K（12 万）

当聊天消息太多的话，就需要对聊天消息进行压缩

Gemini CLI 源码里有一个阈值`COMPRESSION_PRESERVE_THRESHOLD`，当前是 `0.3`，也就是会保留消息历史中最新的`30%`的消息，更老的`70%`的消息会使用大模型进行压缩

这块使用大模型，借助提示词来压缩历史消息，是我之前没有想到的用法

```typescript
const historyToCompress = curatedHistory.slice(0, compressBeforeIndex);
const historyToKeep = curatedHistory.slice(compressBeforeIndex);

this.getChat().setHistory(historyToCompress);

const { text: summary } = await this.getChat().sendMessage(
  {
    message: {
      text: 'First, reason in your scratchpad. Then, generate the <state_snapshot>.',
    },
    config: {
      systemInstruction: { text: getCompressionPrompt() },
    },
  },
  prompt_id,
);
this.chat = await this.startChat([
  {
    role: 'user',
    parts: [{ text: summary }],
  },
  {
    role: 'model',
    parts: [{ text: 'Got it. Thanks for the additional context!' }],
  },
  ...historyToKeep,
]);
````

## 上下文
可以通过上下文来控制 Gemini CLI 的行为，让它记住咱们的喜好。

上下文其实由一堆提示词组成，Gemini 支持多级（全局、项目、当前工作目录）上下文

- `~/.gemini/GEMINI.md` 负责维护全局提示词，可跨项目使用
- 项目根目录下的 `GEMINI.md` 负责维护当前项目使用的提示词
- 当前工作目录下也可以维护 `GEMINI.md`，方便维护某个组件、模块特定的提示词

通过特定的命令 `/memory add` 可以让 Gemini 记住一些事实或者代码风格。这些信息会保存在 `~/.gemini/GEMINI.md` 里

## 快照
Gemini CLI 对文件进行修改前，会自动对项目生成一个快照，方便后续回滚，撤销改动

快照功能默认没有启用，可通过 `--checkpointing` 参数开启
```bash
gemini --checkpointing`
```

## 后记
这篇文章也是在 Gemini Cli 的帮助下完成的，他帮我分析找到代码的运行入口，核心逻辑

## 参考资料
- https://github.com/google-gemini/gemini-cli/blob/main/docs/architecture.md
