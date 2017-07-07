---
title: Android压力测试工具Monkey
date: 2017-07-07 10:45:46
categories: monkey
---

## Monkey 简介
Monkey 是一个可以运行于模拟器或者真机的命令行工具，通过 Monkey 工具模拟用户触摸、手势和系统级别等伪随机操作，我们可以对 APP 进行压力测试。
<!--more-->
## Monkey options
Monkey 包含一系列 options，可以把它们归为以下四类：
1. 基本配置项，比如设置事件数
2. 操作约束，比如只对特定应用包执行测试
3. 事件类型和百分比，比如设置伪随机的种子值
4. 调试项，比如忽略 crash

## 如何使用 Monkey
### 基本语法格式如下
```bash
$ adb shell monkey [options] <event-count>
```
以 oschina 客户端为测试对象，发送100次事件，命令格式如下：
```bash
$ adb shell monkey -p net.oschina.app 100
```
若执行后，出现 injection failed，可以
```bash
$ adb root
$ adb shell monkey -p net.oschina.app 100
```

### 参数 -s <seed>
当做压测的时候，APP 出现异常，如何去复现问题呢？Monkey 发送的都是随机事件，如何保证每次执行都是相同的呢？指定相同的 seed 可以满足，伪随机事件的缘由也在此
```bash
$ adb shell monkey -p net.oschina.app -s 427 100
```

### 参数 --throttle <milliseconds>
不指定此参数，事件之间没有延迟，你会发现指令很快执行完毕。throttle 可以指定一个固定的时间间隔，单位是毫秒
```bash
$ adb shell monkey -p net.oschina.app -s 427 --throttle 500 100
```

### 参数 --pct-touch <percent>
设置 touch 事件在整个执行过程的比例，最大值为100，表示全部执行 touch 事件
```bash
$ adb shell monkey -p net.oschina.app -s 427 --throttle 500 --pct-touch 80 100
```
--pct-motion, --pct-trackball, --pct-nav, --pct-majornav, --pct-syskeys, --pct-appswitch 和 --pct-anyevent 的用法也一样，但是配置的时候要保证 percent 的和不能超过100

### 参数 --ignore-crashes
测试的过程中经常会遇到 APP crash 掉或者出现其它异常情况，这个时候想继续执行未完成的事件，就可以加上此参数
```bash
$ adb shell monkey -p net.oschina.app -s 427 --throttle 500 --pct-touch 80 --ignore-crashes 100 
```
--ignore-timeouts 是另外一个常用配置，比如出现 ANR 的时候想继续下去，就可以加上此参数。



