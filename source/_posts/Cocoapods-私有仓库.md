---
title: Cocoapods 私有仓库
date: 2019-05-06 16:59:38
tags:
---

Cocoapods仓库分为两类：公共仓库、私有仓库

公共仓库：项目中用pod管理的第三方库，我们也可以创建自己的pod仓库。

私有仓库：主要用于自己一些公共库封装管理；或者项目组件化后，各个组件独立项目，成为一个pod仓库

本文主要讨论Cocoapods私有仓库的创建：

<!-- more -->

### 1、创建 仓库specs项目

![image-20190311150441424](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/newspecsProject.png)

添加到repo

```
pod repo add zwkRepos git@git.dev.tencent.com:jindouyun_wk/ZWKSpecs.git
```

需要注意是否有git权限(项目公钥)
添加成功：

![image-20190311151024393](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190311151024393.png)

### 2、创建 仓库项目

![image-20190307180231720](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190311144640390.png)

```
git clone 项目
```

cd 进入项目，初始化创建podspec文件

```
pod spec create ZWKCategory
```

![image-20190311173138843](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190311173138843.png)

编辑内容， 主要用到的参数。

```
Pod::Spec.new do |s|
  s.name         = "ZWKCategory" # 项目名称
  s.version      = "0.0.1"        # 版本号 与 你仓库的 标签号 对应
  s.summary      = "分类总结 ZWKCategory." # 项目简介
  s.license      = "MIT"          # 开源证书

  s.homepage     = "https://dev.tencent.com/u/jindouyun_wk/p/ZWKCategory" # 仓库>的主页
  s.source       = { :git => "https://git.dev.tencent.com/jindouyun_wk/ZWKCategory.git", :tag => "#{s.version}" }#你的仓库地址，不能用SSH地址
  s.source_files = "ZWKCategory/*.{h,m}" # 你代码的位置， BYPhoneNumTF/*.{h,m} 表示 BYPhoneNumTF 文件夹下所有的.h和.m文件
  s.requires_arc = true # 是否启用ARC
  s.platform     = :ios, "7.0" #平台及支持的最低版本
  # s.frameworks   = "UIKit", "Foundation" #支持的框架
  # s.dependency   = "AFNetworking" # 依赖库

  # User
  s.author             = { "WK" => "janicezhw@gmail.com" } # 作者信息
  s.social_media_url   = "https://janicezhw.github.io/" # 个人主页

end
```

验证podspec 配置文件是否正确

```
pod lib lint ZWKCategory.podspec
```

验证成功后

git push 项目

git tag （0.0.1）并推送tag  （  s.version      = "0.0.1"  与这个值一致）

3、在项目中进行pod install
podFile 设置：

```
project 'TestPodsDemo.xcodeproj'
# source 'git@git.dev.tencent.com:jindouyun_wk/ZWKSpecs.git' # ssh 需要配置公钥，不是管理账号的设备不建议使用。
source 'https://git.dev.tencent.com/jindouyun_wk/ZWKSpecs.git' # HTTPS 第一次 pod install 需要输入用户名、密码。
source 'https://github.com/CocoaPods/Specs.git'

target 'TestPodsDemo' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  # use_frameworks!

  # Pods for TestPodsDemo
pod 'Masonry'
pod 'ZWKCategory'
...
```

> 参考：
>
> http://www.jianshu.com/p/0c640821b36f
>
> https://segmentfault.com/a/1190000007947371
>
> https://mp.weixin.qq.com/s/X1ITlu3bGvJNUV6jGGYabw

