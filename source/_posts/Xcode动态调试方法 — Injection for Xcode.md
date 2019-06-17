---
title: Xcode动态调试方法 — Injection for Xcode
date: 2019-05-09 15:46:29
tags:

---

![](https://static001.geekbang.org/resource/image/a2/13/a239763b1a5c7226e5ee8d7481285a13.gif)

<!-- more -->

### 安装Injection
App Store下载安装Injection，安装完成后打开应用
![](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/E6979875-556C-4BBA-AE09-4C4A4ECCAD2B.png)

### Xcode工程配置动态调试
Xcode打开项目工程
在项目的Appdelegate 中添加连接代码：

`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 

```
#if DEBUG
//or oc
[[NSBundle bundleWithPath:@"/Applications/InjectionIII.app/Contents/Resources/iOSInjection.bundle"] load];
//or switf
//Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/iOSInjection.bundle")?.load()
//for tvOS:
//Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/tvOSInjection.bundle")?.load()
//Or for macOS:
//Bundle(path: "/Applications/InjectionIII.app/Contents/Resources/macOSInjection.bundle")?.load()
#endif
```
Xcode使用模拟器运行工程

### Injection -  Open Project 选择对应工程目录
在Injection菜单选择Open Project 选择对应工程目录

在Xcode视图将显示`💉 Injection connected, watching /Users/****/Desktop/InjectionIII/**` 表明连接成功

### 动态调试

在需要动态调试的ViewController中加入如下代码 ：

```
- (void)injected {
    //self.view.backgroundColor = [UIColor yellowColor];
    [self viewDidLoad];
}
```
打断点，cmd+s ，如果正常将进入断点，证明动态调试成功，模拟器将看到重新载入页面

