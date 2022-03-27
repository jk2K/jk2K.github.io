---
title: discuz 添加在发布文章时输入自定义关键字的功能
date: 2014-04-08 11:15:54 +0800
categories: [技术]
tags:
- discuz
---
## 环境
* discuz x3.1

## 操作步骤：
1. 修改`template/default/portal/portalcp_article.htm`，在大约206行，修改为如下代码
    ```php
    <dt>{lang article_author}</dt>
    <dd><input type="text" name="author" class="px p_fre" value="$article[author]" size="30" /></dd>
    //添加的代码开始
    <dt>keywords</dt>
    <dd><input type="text" name="keywords" class="px p_fre" value="$article[keywords]" size="30" /></dd>
    //添加的代码结束
    <!--{if $category[$catid][allowcomment]}-->
    ```

2. 修改`source/include/portalcp/portalcp_article.php`，在大约97行，修改为如下代码
    ```php
    'highlight' => $style,
    'showinnernav' => empty($_POST['showinnernav']) ? '0' : '1',
    'keywords' => dhtmlspecialchars($_GET['keywords'])//添加的代码
    );
    ```

3. 修改`source/module/portal/portal_view.php`，在大约278行，修改为如下代码
    ```php
    if(strpos($articleprimaltplname, ':') !== false) {
    list($tpldirectory, $articleprimaltplname) = explode(':', $articleprimaltplname);
    }
    $metakeywords = $article['keywords'];//添加的代码
    include_once template("diy:portal/view:{$catid}", NULL, $tpldirectory, NULL, $articleprimaltplname);
    ```

4. 修改数据库表`pre_portal_article_title`，新增字段keywords varchar 255
