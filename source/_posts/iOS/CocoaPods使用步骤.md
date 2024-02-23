---
title: CocoaPods使用步骤
categories: iOS
tags:
  - CocoaPods
---
```
$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/
$ gem sources -l
```
<!--more-->
#### 1.打开终端,输入
`$ gem sources --remove https://rubygems.org/`

等有反应之后再敲入以下命令

`$ gem sources -a http://ruby.taobao.org/`

为了验证你的Ruby镜像是并且仅是taobao，可以用以下命令查看：

`$ gem sources -l`

只有在终端中出现下面文字才表明你上面的命令是成功的：

`*** CURRENT SOURCES ***`

`http://ruby.taobao.org/`

1.5升级gem命令：

`$ sudo gem update —system【sudo gem install -n /usr/local/bin cocoapods --pre】`

#### 2.安装CocoaPods,在终端输入
`$ sudo gem install cocoapods`

#### 3.如果cocoaPod镜像有问题
`$ pod repo remove master`

`$ pod repo add master https://gitcafe.com/akuandev/Specs.git`

`$ pod repo update`

#### 4.假设我们要在工程中导入AFNetworking类库.
为了确定AFNetworking是否支持CocoaPods,可以用CocoaPods的搜索功能验证一下。在终端中输入:

`$ pod search AFNetworking`

如果显示绿体字类库名,说明CocoaPods支持该类库

#### 5.现在建一个工程,叫CocoaPodsDemo

#### 6.CocoaPods的工作原理是利用Podfile文件来下载我们指定的类库,每个工程只需要一个Podfile文件,但Podfile文件需要我们利用终端自行创建

#### 7.在终端中使用cd命令进入工程所在目录,例如:
`$ cd /Users/lanouhn/Desktop/Shared/CocoaPods`

#### 8.当前目录下,利用vim创建Podfile,在终端中输入:
`$ vim Podfile`

#### 9.按【i】或【a】进入插入模式,输入以下内容:
`$ platform :ios, '7.0'`

`$ pod "AFNetworking", "~> 2.0"`

(这两行内容是在AFNetworking的github页面找到的。
这两句文字的意思是，当前AFNetworking支持的iOS最高版本是iOS 7.0, 要下载的AFNetworking版本是2.0)

输入完毕后,按【ESC】返回命令行模式
按【:】(冒号)进入末行模式,输入【wq】保存命令并退出vim

#### 10.这时你会发现工程目录中，出现一个名为Podfile的文件，而且文件内容就是刚刚输入的内容。
(注意,Podfile文件应该和工程文件.xcodeproj在同一个目录下)

此时就可以利用CocoPods下载AFNetworking类库了,还是在终端中的当前工程目录下，运行以下命令:

`$ pod install`

(个人猜测,执行该命令后,CocoPods自动会找到该目录下的Podfile文件,下载文件内容对应的第三方类库)

[ps.最近使用CocoaPods来添加第三方类库，无论是执行pod install还是pod update都卡在了Analyzing dependencies不动
原因在于当执行以上两个命令的时候会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。加参数的命令如下：

`pod install --verbose --no-repo-update`

`pod update --verbose --no-repo-update]`

#### 11.执行完毕后,终端会出现以下信息:
EricmatoMacBook-Pro:CocoaPodsDemo ericwang

$ pod install

Analyzing dependencies

Downloading dependencies

Installing AFNetworking (2.0.2)

Generating Pods project

Integrating client project

[!] From now on use `CocoaPodsDemo.xcworkspace`.

(注意最后一句话，意思是:以后打开工程就用 CocoaPodsDemo.xcworkspace 打开,而不是之前的.xcodeproj文件)

#### 12.此时工程目录会多一个.xcworkspace文件,一个“Podfile.lock”文件和一个文件夹“Pods”

#### 13.点击 .xcworkspace 打开工程,你会发现,AFNetwoking已经成功导入项目了

```
对于工程发生的变化，有几点需要说明：

第三方库会被编译成静态库供我们真正的工程使用

CocoaPods会将所有的第三方库以target的方式组成一个名为Pods的工程，该工程就放在刚才新生成的Pods目录下。整个第三方库工程会生成一个名称为libPods.a的静态库提供给我们自己的CocoaPodsTest工程使用。

我们的工程和第三方库所在的工程会由一个新生成的workspace管理

为了方便我们直观的管理工程和第三方库，CocoaPodsTest工程和Pods工程会被以workspace的形式组织和管理，也就是我们刚才看到的CocoaPodsTest.xcworkspace文件。

原来的工程设置已经被更改了，这时候我们直接打开原来的工程文件去编译就会报错，只能使用新生成的workspace来进行项目管理。
```