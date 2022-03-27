---
title: 实现 DownloadManager 下载完 apk 自动提示安装的功能
date: 2016-01-22 16:22:16 +0800
categories: [技术]
tags:
- android
---
## 运行环境
* Android 5.1.1, API 22

## 解决方案
1. 下载新版本的 `apk`
    ```java
    public void downloadNewVersion() {
        mDownloadManager = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
        // apkDownloadUrl 是 apk 的下载地址
        DownloadManager.Request request = new DownloadManager.Request(Uri.parse(apkDownloadUrl));
        // 获取下载队列 id
        enqueueId = mDownloadManager.enqueue(request);
    }
    ```

2. 注册接收下载完成的广播
    ```java
    BroadcastReceiver receiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            long downloadCompletedId = intent.getLongExtra(
                    DownloadManager.EXTRA_DOWNLOAD_ID, 0);
            // 检查是否是自己的下载队列 id, 有可能是其他应用的
            if (enqueueId != downloadCompletedId) {
                return;
            }
            DownloadManager.Query query = new DownloadManager.Query();
            query.setFilterById(enqueueId);
            Cursor c = mDownloadManager.query(query);
            if (c.moveToFirst()) {
                int columnIndex = c.getColumnIndex(DownloadManager.COLUMN_STATUS);
                // 下载失败也会返回这个广播，所以要判断下是否真的下载成功
                if (DownloadManager.STATUS_SUCCESSFUL == c.getInt(columnIndex)) {
                    // 获取下载好的 apk 路径
                    String uriString = c.getString(c.getColumnIndex(DownloadManager.COLUMN_LOCAL_FILENAME));
                    // 提示用户安装
                    promptInstall(Uri.parse("file://" + uriString));
                }
            }
        }
    };
    // 注册广播, 设置只接受下载完成的广播
    registerReceiver(receiver, new IntentFilter(
            DownloadManager.ACTION_DOWNLOAD_COMPLETE));
    ```

3. 取消注册广播, 不取消注册的话, 调用`recreate`时会报`Are you missing a call to unregisterReceiver()?`错误
    ```java
    @Override
    protected void onDestroy() {
    	super.onDestroy();
    
    	unregisterReceiver(mBroadcastReceiver);
    }
    ```


4. 提示用户安装
    ```java
    private void promptInstall(Uri data) {
        Intent promptInstall = new Intent(Intent.ACTION_VIEW)
                .setDataAndType(data, "application/vnd.android.package-archive");
        // FLAG_ACTIVITY_NEW_TASK 可以保证安装成功时可以正常打开 app
        promptInstall.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(promptInstall);
    }
    ```
    就是这样，运行 app 看下效果吧

## 总结
坑比较多，花了3天时间才完全实现这个效果
1个坑是必须使用`setDataAndType()`，而不能单独使用`setData()`和`setType()`
另外一个坑是`getUriForDownloadedFile`现在返回的是`content://`这种格式的链接了，无法用于启动`intent`
最后一个坑是`intent`必须设置`FLAG_ACTIVITY_NEW_TASK`, 否则安装完成后无法正常打开`app`

## 常见问题
### 提示`解析程序包时出现问题`
#### 答
`mDownloadManager.getUriForDownloadedFile(enqueueId)`返回的`Uri`是这种格式的`content://downloads/my_downloads/83`, 这种格式的`Uri`启动`intent`会报`解析程序包时出现问题`

### No Activity found to handle Intent
> No Activity found to handle Intent { act=android.intent.action.VIEW dat=/storage/sdcard/download/demo.apk typ=application/vnd.android.package-archive

#### 答 
可能原因1:
有可能是没有权限读取这个文件或者文件路径错误

可能原因2:
```java
// Intent promptInstall = new Intent(Intent.ACTION_VIEW)
//            .setData(data)
//            .setType("application/vnd.android.package-archive");
Intent promptInstall = new Intent(Intent.ACTION_VIEW)
                .setDataAndType(data, "application/vnd.android.package-archive");
```                
单独设置`data`和`type`也会造成这个错误，原因请看[http://developer.android.com/reference/android/content/Intent.html#setData(android.net.Uri)](http://developer.android.com/reference/android/content/Intent.html#setData(android.net.Uri))
你会发现无论何时你去设置`data`或者`type`, 另外一个都会自动的变为空, 例如: `setData()`会使得`type`参数值为空, 如果你想让两者都生效的话只能使用`setDataAndType()`

### 提示`应用程序未安装`
#### 答
有可能是因为`APK` 签名不一致, 比如之前是`debug`版(无签名), 现在你更新安装`release`版(有签名), 就会出现这个问题

## 参考链接
* [Android 安卓系统提示应用程序未安装的解决方法](http://blog.csdn.net/ljz2009y/article/details/7645734)
* [Android DownloadManager and Media Scanner](http://stackoverflow.com/questions/22736258/android-downloadmanager-and-media-scanner)
* [Android: install .apk programmatically](http://stackoverflow.com/a/4969421/2227031)
* [为何必须用setDataAndType, 而不能单独设置data和type](http://stackoverflow.com/a/5338066/2227031)
* [Android DownloadManager Example](http://blog.vogella.com/2011/06/14/android-downloadmanager-example/)
* [在应用中更新App版本](http://blog.csdn.net/caroline_wendy/article/details/50475854)
* [DownloadManager补漏](http://www.cnblogs.com/wlrhnh/p/4641105.html)
* [全局接收下载完成的广播](http://www.cnblogs.com/zhengxt/p/3657833.html)

## 更新纪录
* 2016年3月8日 添加取消注册广播的代码
* 2016年1月22日 发布
