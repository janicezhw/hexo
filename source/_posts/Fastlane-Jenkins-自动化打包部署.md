---
title: Fastlane + Jenkins 自动化打包部署
date: 2019-05-06 15:59:18
tags:
---

手动打包、手动上传是一件非常烦琐的事件，通常会浪费开发者大量的时间，这时候自动打包与分发就起到的重要的角色。目的主要要实现以下几个功能：

1. 从git拉取最新代码
2. 根据配置打包ipa
3. 自动分包到pgyer或fir
4. 邮件通知相关人员


本文将一步步实现上述功能：

<!-- more -->

## 安装Fastlane

需要Ruby2.0+ 环境，Mac系统细带Ruby环境，但是涉及很多权限问题，建议另外rvm来管理ruby

### 安装RVM & Ruby

Install RVM stable with ruby:

```
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```

等待一段时间后就可以成功安装好 RVM。

然后，重新打开 Termal ，会自动重新载入rvm环境，如果没有执行以下代码

```
source ~/.bashrc  
source ~/.bash_profile  
```

`rvm -v  ruby -v`  查看是否安装正确

`rvm list`  查看已经安装的ruby版本 

切换ruby

```
rvm use 2.3.1 
```

设置rvm默认版本

```
rvm --default 2.3.1
```

### 安装fastlane

```
gem install fastlane -NV
```

### 工程配置fastlane

bundler是gem包管理工具，建议通过gemfile管理gem包

#### 进入工程目录，创建Gemfile

```
source "https://gems.ruby-china.com/"
 
gem "fastlane"
gem "cocoapods"
```

```
bundle install --path vendor/bundler
```

`--path vendor/bundler` 表示我們希望把程式安裝在當前的目錄底下，而不是安裝到系統檔案夾如 /usr*local*bin 或 *usr*bin 裡面，這樣你的 project 就不會跟你的系統有任何掛勾了。

会在工程目录下生成vendor目录，管理gem包

*請記得將 vendor 這個資料夾加入 .gitignore 之中。*

#### 初始化一個 fastlane

```
bundler exec fastlane init
```

\1. 📸  Automate screenshots

\2. 👩‍✈️  Automate beta distribution to TestFlight

\3. 🚀  Automate App Store distribution

\4. 🛠  Manual setup - manually setup your project to automate your tasks

选择4 自定义



##### 通过fastlane打包并上传pgyer

```
fastlane add_plugin fir/pgyer
```

编辑fastlane文件

```
default_platform(:ios)
platform :ios do
  desc "Description of what the lane does"
  lane :keji do
    build_app(workspace:"MyApp.xcworkspace",
          scheme:"MyApp",
          export_method:"development",
          output_directory:"./fastlane/ipadFile",
          configuration:"Debug")
    pgyer(api_key: "7f15xxxxxxxxxxxxxxxxxx141", user_key: "4a5bcxxxxxxxxxxxxxxx3a9e", update_description: "update by fastlane")
  end
end
```

执行打包任务：

```
fastlane keji
```



##### 通过jenkins做上传pgyer的步骤：

fastlane不用安装pgyer插件

推荐通过gym 做fastlane打包（命令更灵活）相关的fastfile：

fastlane gym -h 查看支持的命令

```
default_platform(:ios)

platform :ios do
  desc "Description of what the lane does"
  lane :dev do
 	 # 打包，具体参数可fastlane gym -h查看
     gym(
     	# clean: true,
     	workspace: "OnstreetSaaS.xcworkspace",
     	# scheme: "OnstreetSaaS",
     	# configuration: "Debug",
     	# output_directory: "./fastlane/ipaFile",
     	output_name: "OnstreetSaaS",
     	# export_method:"development", #app-store、ad-hoc、development、enterprise
     	# include_bitcode: false
     	)
  end
end
```

fastlane gym 打包命令：

```
fastlane gym (--export_method development --scheme ${IPANAME} --output_directory ${OUTPUTDIC} --include_bitcode false --include_symbols false --clean true)
```

关于gym命令：fastlane gym 中可以—增加参数都可以生效，但是在fastfile中 设置参数有些无效，不知道是不是姿势错误…

关于Ad-hoc 需要configuration设置为release: --export_method ad-hoc --configuration Release  ,



## 安装Jenkins

### 安装jenkins
推荐通过homebrew安装jenkins （或者通过war包安装 注意需要jdk 8，[参考](https://www.pgyer.com/doc/view/jenkins_ios)）

```
brew install jenkins
```

### 启动jenkins

```
jenkins
```

### 访问jenkins

```
http://localhost:8080 // 打开jenkins可视化页面`
http://localhost:8080/exit //退出Jenkins`
http://localhost:8080/restart //重启`
http://localhost:8080/reload //重新加载

```

![img](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/1212454-682a0b9af82d7eee.png)

![img](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/1212454-61e6a345d4a73da4.png)

### 新建任务：

选择自由风格

![](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190124152717237.png)

如果构建代码是通过git获取，需要先设置git 密码或者SSH,

**Source code** > git

![](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190124153158039.png)

![](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190124153216719.png)

![image-20190313155007480](http://pr2qnzw6t.bkt.clouddn.com/image/markdown/image-20190124153306761.png)

**Build** > 执行shell

```
IPANAME="OnstreetSaaS"
OUTPUTDIC="./fastlane/ipaFile"
PGYUKEY="*****"
PGYAPPKEY="*****"
cd ./${IPANAME}
fastlane gym --export_method development --scheme ${IPANAME} --output_directory ${OUTPUTDIC} --include_bitcode false --include_symbols false --clean true
curl -F "file=@${OUTPUTDIC}/${IPANAME}.ipa" -F "uKey=${PGYUKEY}" -F "_api_key=${PGYAPPKEY}" https://qiniu-storage.pgyer.com/apiv1/app/upload
```

### 关于邮件通知：

可以自己在jenkins 系统设置 配置smtp邮件发送，在**Post-build Actions**> 增加邮件发送

但是由于我们用pgyer，直接用pgyer的邮件服务就可以。