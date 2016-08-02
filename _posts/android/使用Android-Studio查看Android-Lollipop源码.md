---
title: 使用Android Studio查看Android Lollipop源码
date: 2016-08-02 20:27:07
tags: Android
---
> 今天下载了一下android源码，并且编译了下，以前用sourcelight查看源码，但感觉不好用（可能是用的不习惯），尝试了下用Andorid Studio查看android源码。

## 准备工作
1. 下载android源码
2. 编译android源码

## 开始
1. 进入Android 源码根目录
2. 执行
```bash
$ mmm development/tools/idegen/
```
这行命令的意思是编译idegen这个项目,生成idegen.jar文件.生成成功后,会显示这个jar包的位置,并显示  
```bash
#### make completed successfully (43 seconds) ####
```
3. 执行
```bash
$ sh ./development/tools/idegen/idegen.sh
```
这行命令的意思是生成对应的文件:android.iws, android.ipr, android.iml .
4. 生成对应的文件后,打开Android Studio,选择打开一个现有的Android Studio项目,选择Android源码的根目录,导入即可(起作用的是android.irp文件).在配置sdk版本之后就可以查看Android 源码了.


参考文章
[1](http://www.jianshu.com/p/c85984cf99e2)
