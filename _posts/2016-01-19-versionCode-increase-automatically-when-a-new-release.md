---
title: 当发布新版本时自动递增 versionCode
date: 2016-01-19 15:31:57 +0800
categories: [技术]
tags:
- android
---
## 需求
`versionCode`是`android`应用内部用来识别版本，判断新旧用的, 希望每次发布新版本时能够自动化的自增，避免人工修改可能带来的问题

## 更好的做法
```gradle
import java.util.regex.Pattern
task('increaseVersionCode') << {
    def buildFile = file("build.gradle")
    def pattern = Pattern.compile("versionCode\\s+(\\d+)")
    def manifestText = buildFile.getText()
    def matcher = pattern.matcher(manifestText)
    matcher.find()
    def versionCode = Integer.parseInt(matcher.group(1))
    def manifestContent = matcher.replaceAll("versionCode " + ++versionCode)
    buildFile.write(manifestContent)
}

tasks.whenTaskAdded { task ->
    if (task.name.matches('assemble.*?Release')) {
        task.dependsOn 'increaseVersionCode'
    }
}
```
这样就实现了每次发布新版本时，自动递增`versionCode`, 比普通做法的好处是，无需增加另外的文件, 更简便

## 普通做法
将`versionCode`放在一个文件里，每次发布新版本时，读取这个值，递增一次，最后再写回这个文件
具体代码如下
```gradle
def versionPropsFile = file('version.properties')

if (versionPropsFile.canRead()) {
    def Properties versionProps = new Properties()

    versionProps.load(new FileInputStream(versionPropsFile))

    def code = versionProps['VERSION_CODE'].toInteger() + 1

    versionProps['VERSION_CODE'] = code.toString()
    versionProps.store(versionPropsFile.newWriter(), null)

    defaultConfig {
        versionCode code
        versionName "1.1"
        minSdkVersion 14
        targetSdkVersion 18
    }
}
else {
    throw new GradleException("Could not read version.properties!")
}
```

## 参考链接
* [How to autoincrement versionCode in Android Gradle](http://stackoverflow.com/a/29256798/2227031)
* [Versioning Your Applications](http://developer.android.com/tools/publishing/versioning.html)
* [Autoincrement VersionCode with gradle extra properties](http://stackoverflow.com/a/21405744/2227031)
