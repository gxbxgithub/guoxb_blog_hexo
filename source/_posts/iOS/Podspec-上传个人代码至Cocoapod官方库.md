---
title: Podspec-上传个人代码至CocoaPods官方库
categories: iOS
tags:
- Podspec
- CocoaPods
---
Cocoapods作为iOS开发的包管理器，给我们的开发带来了极大的便利，而且越来越多的第三方类库支持Pod，可以通过Pod傻瓜式的集成到自己的工程中，那么问题来了，我自己也有一系列的小工具类，怎么让它也支持pod集成进而让其他朋友方便的使用呢?
<!--more-->

### 1.上传项目至github(在此不作赘述)

### 2.cd到该项目目录，创建podspec文件

$ pod spec create [项目名]

### 3.编辑Podspec文件

附上我的Podspec文件，供大家参考

```
Pod::Spec.new do |s|
    s.name         = 'AJDownload'
    s.version      = '1.0.0'
    s.summary      = 'Download manager based on AFNetworking 3.0'
    s.homepage     = 'https://github.com/gxbxgithub/AJDownload'
    s.license      = 'MIT'
    s.authors      = { 'guoxb' => 'gxbxemail@163.com' }
    s.platform     = :ios, '8.0'
    s.source       = { :git => 'https://github.com/gxbxgithub/AJDownload.git', :tag => s.version.to_s }
    s.source_files = 'AJDownload/**/*.{h,m}'
    s.framework    = 'UIKit'
    s.dependency 'AFNetworking', '~> 3.1.0'
    s.requires_arc = true
end
```
### 4.验证Podspec文件

$ pod lib lint

### 5.打tag上传Podspec

$ git tag -m "first commit" "0.0.1"

$ git push --tags

### 6.最后使用pod trunk 推送到CocoaPod官方库，只是此操作需要注册，注册指令：

$ pod trunk register gxbxemail@163.com

### 7.如果已经注册，请忽略第6步；推送到CocoaPod

$ pod trunk push AJDownload.podspec