---
layout: post
title: "CocoaPods使用"
date: 2015-1-20 19:32:44 +0800
comments: true
categories: 
---


cocopods的安装与使用


 <!--more-->
 
### 更新导入库用此条命令! 解决卡死问题
### pod install --no-repo-update


### CocoaPods是什么
当我们开发iOS项目时候，会经常使用到第三方类库，并且会使用很多。
大家的做法基本上都是到GitHub上下载一个一个的类库，然后导入到工程中，并且引入各种的类库，做各种的配置。有时候还要处理ARC与MRC的问题，很麻烦。
还有一种情况，就是当第三方类库出现升级的情况时候，我们还需要删掉旧的，下载新的，再重新导入。

CocoaPods就是类库管理工具，刚刚提到所有麻烦的操作，CocoaPods都能解决!

 <!--more-->
### 安装CocoaPods
安装CocoaPods需要本地Ruby环境，默认情况下，Mac自带Ruby环境，可以通过在终端输入命令ruby -v查看Ruby版本，接下来进行安装，使用如下命令：

	 $ sudo gem install CocoaPods
	 输入以上命令，可能会一直没有反应，或者出现ERROR。注意了，我们在天朝，是有一堵墙的，挡住了，所以你安装失败。不过没关系，我们可以使用另外一个地址来访问CocoaPods。

既然官网地址被墙了，我们就使用淘宝的，淘宝的Ruby镜像可以帮助我们访问CocoaPods，分别在终端输入如下命令，将Ruby镜像替换为淘宝的镜像地址:

	$ gem sources —remove https://rubygems.org/
	$ gem sources -a http://ruby.taobao.org/

完成之后，我们可以使用如下命令来查看Ruby镜像是否已经指向了淘宝：

$ gem sources -l
如果看到如下信息，表示已经成功！！！
![](http://i5.tietuku.com/935159867a2c3f82.jpg)

下面，重新执行安装命令，等待安装结束就可以了

	$ sudo gem install CocoaPods
等待执行完毕，看到如下提示，恭喜你，安装完成。
![](http://ww2.sinaimg.cn/large/74311666jw1eygxopggdtj20gw02mjrt.jpg)
 
如果遇到其它的安装问题，请自行Google解决吧。




### 使用CocoaPods
安装已经完成了，下面就来教大家如何使用。
做一个项目，项目中需要使用SDWebImage，看看如何使用CocoaPods进行安装
首先使用如下命令，查看CocoaPods是否支持SDWebImage类库

	$ pod search SDWebImage
可以看到下面这些搜索结果，表示SDWebImage是可以的，
 ![](http://i12.tietuku.com/5847b5e6afb84ba5.jpg)

注意圈住的那一行，一会需要使用。这行信息是告诉CocoaPods去下载哪一个第三方类库和它的哪一个版本，是一条配置信息。



#### 下面创建空的项目，然后使用CocoaPods往项目中导入SDWebImage类库：

1>. 创建项目 CocoaPods_Test，保存到桌面

2>. 在终端输入 cd空格，拖入项目文件夹，回车，进入项目所在的根目录

3>.通过命令行创建Podfile文件，该文件的作用是配置第三方类库的信息，就是告诉CocoaPods去管理哪些类库

	$ vim Podfile
vim打开文件后，按i进入编辑模式，输入如下文字：
  ![](http://i12.tietuku.com/5266e8d6fc217c17.jpg)
里面的`pod 'SDWebImage', '~> 3.7.1'`就是我们在上面圈住的那一行。
输入完成后按ESC退出编辑模式，接着输入:wq，并按回车键进行保存并退出。
上面的操作完成之后，我们可以在项目根目录发现一个没有后缀的Podfile文件，这就是刚刚创建的文件，里面保存了需要使用的第三方类库的信息。(注意：Podfile文件必须和.xcodeproj文件在同一目录下)

4>. 接下来，我们就可以使用命令来安装第三方类库了，(确保终端路径在项目根目录)运行如下命令：

	$ pod install
当我们看到这样的提示，就表示你操作成功：
![](http://i12.tietuku.com/5266e8d6fc217c17.jpg)
第三方类库导入成功
#### 注意最后一句话：”Please close any current Xcode sessions and use ‘CocoaPods_Test.xcworkspace’for this project from now on”。这就是告诉我们，从现在开始需要使用这个文件来打开项目，而不在是之前的.xcodeproj

5>. 双击项目中的CocoaPods_Test.xcworkspace文件来打开项目，你会看到如下目录结构：
 
 
 
 <a href="http://m.yea.im/4ev.html" title="87276160ad89fb15" ><img src="http://m1.yea.im/4ev_m.jpg" alt="87276160ad89fb15" /></a>
  
我们会发现多了一个Pods的依赖工程，打开Pods文件夹后，发现SDWebImage已经在这里面了。

6>. 现在，我们就可以在想要使用SDWebImage的文件中，引入需要的文件，然后就可以正常使用了。

7>. 到现在为止，你已经学会了如何使用CocoaPods来添加第三方类库。如果我们需要使用多个第三方类库，只需要修改Podfile文件就可以了，如下：
添加多个第三方类库
![](http://i12.tietuku.com/52b00c779be4764e.jpg)
NOTE:
怎么样，过程很简单吧。只需要: 1.查询 2.修改Podfile文件 3.安装 即可完成!

#### 其它
当我们想要重新安装CocoaPods的时候，需要卸载这个东西，很简单，只需要执行以下命令即可：

	$ sudo gem uninstall CocoaPods
如果安装了多个版本，选择All，即可全部卸载！

第三方版本号的各种写法与含义：

	pod 'AFNetworking'      //不显式指定依赖库版本，表示每次都获取最新版本
	pod 'AFNetworking',  '2.0'     //只使用2.0版本
	pod 'AFNetworking', '>2.0'     //使用高于2.0的版本
	pod 'AFNetworking', '>=2.0'     //使用大于或等于2.0的版本
	pod 'AFNetworking', '<2.0'     //使用小于2.0的版本
	pod 'AFNetworking', '<=2.0'     //使用小于或等于2.0的版本
	pod 'AFNetworking', '~>0.1.2'     //使用大于等于0.1.2但小于0.2的版本，相当于>=0.1.2并且<0.2.0
	pod 'AFNetworking', '~>0.1'     //使用大于等于0.1但小于1.0的版本
	pod 'AFNetworking', '~>0'     //高于0的版本，写这个限制和什么都不写是一个效果，都表示使用最新版本
