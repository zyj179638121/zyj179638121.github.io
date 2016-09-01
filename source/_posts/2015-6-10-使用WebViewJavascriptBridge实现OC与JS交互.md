---
layout: post
title: "使用WebViewJavascriptBridge实现OC与JS交互"
date: 2015-6-10 10:22:27 +0800
comments: true
categories: 
---

#### 前言
当下,很多APP里面都会有HTML5网页,我们除了简单的用WebView加载显示外，很多情况下，我们还需要和WebView进行交互,比如点击WebView上面的按钮调用OC端的函数实现具体操作。

<!--more-->

因此今天为大家介绍一个优秀的开源小项目,就是我们今天的主角--`WebViewJavascriptBridge`。它主要帮助我们优雅的实现OC与JS的交互,非常方便简洁。

当然,有很多优秀的开发者写过WebViewJavascriptBridge的使用包括实现原理,首先表示感激和敬意。但是作为一个初学者或者一个急于实现功能的人来说,大部分时候我们更希望一个直奔主题的文章。所以,本文的核心就是WebViewJavascriptBridge在实际开发中的使用。
使用步骤
去Github上下载`WebJavaScriptBridge`
解压缩包,将`WebViewJavascriptBridge`文件夹拖到你的工程中

 
在需要使用`WebViewJavascriptBridge`的类文件中引入头文件`WebViewJavascriptBridge.h`
加载UIWebView,遵守`<UIWebViewDelegate>`协议
具体使用
创建并初始化WebJavaScriptBridge实例


	   #import "ExampleUIWebViewController.h"
	   #import "WebViewJavascriptBridge.h"

	  @interface ExampleUIWebViewController ()
      //声明`WebViewJavascriptBridge`对象为属性
	   @property WebViewJavascriptBridge* bridge;

	  @end

      - (void)viewWillAppear:(BOOL)animated {

       //用UIWebView加载web
        UIWebView* webView = [[UIWebView alloc] initWithFrame:self.view.bounds];

     //设置能够进行桥接 
        [WebViewJavascriptBridge enableLogging];
     // 初始化*WebViewJavascriptBridge*实例,设置代理,进行桥接
       _bridge = [WebViewJavascriptBridge bridgeForWebView:webView webViewDelegate:self handler:^(id         data, WVJBResponseCallback responseCallback) {
 	
	NSLog(@"ObjC received message from JS: %@", data);
         responseCallback(@"Response for message from ObjC");
         }];

	}
#### JS调用OC

什么是JS调用OC?

举个例子来说就是在网页中有一个登录按钮,点击登录按钮后,具体的登录功能是由OC端实现的,即登录功能实现需要我们在工程里有一个类似loginMethod的函数去具体操作。
工作流程

站在实际开发的角度来解释,就是假如现在有一个网页,在网页中有个登录按钮需要通过JS调OC的方式实现。那么我们首先需要跟负责网页编码的人员(一般是后台)商定出一个方法名称,也就是给这个登录按钮点击事件取个名字,例如叫loginCallBack。然后我们需要在代码里注册这个事件并负责它的具体实现。当用户点击这个登录按钮的时候,后台就会通知给这个事件的注册者去执行,有点像block的执行顺序。
代码实现

假如我们现在商定了一个事件名称为loginFunc,我们来看一下代码实现。

      /***
      /@param registerHandler 要注册的事件名称(这里我们为loginFunc)
      /@param handel 回调block函数 当后台触发这个事件的时候会执行block里面的代码
     ***/
     [_bridge registerHandler:@"loginFunc" handler:^(id data, WVJBResponseCallback responseCallback) {
     // data 后台传过来的参数,例如用户名、密码等

	   NSLog(@"testObjcCallback called: %@", data);

     //具体的登录事件的实现,这里的login代表实现登录功能的一个OC函数。
                [self login];   

     // responseCallback 给后台的回复    

        responseCallback(@"Response from testObjcCallback");
      }];
#### OC调用JS

什么是OC调用JS

如果你理解了JS调用OC,那么OC调用JS就应该差不多懂了。同样举个例子,我们的原生APP上有个输入框,我们输入完成后,让它显示在网页上面的用户名处。这样,我们就是OC要实现的一个事件让网页去真正实现了,也就是OC调用JS。
OC调用JS

和JS调用OC的流程大致一样,还是需要和网页编写人员商定出一个事件名,然后在网页里面先把注册这样一个事件并把实现体写好,等到我们OC去触发这个事件(比如点击按钮)就会去网页里面找到这个事件的实现体并执行。
代码实现

假如我们现在商定了一个注册事件,事件名称为registerFunc,我
们来代码实现一下	

	   //不需要传参数,不需要后台返回执行结果
      [_bridge callHandler:@"registerFunc"];

    //需要传参数,不需要从后台返回执行结果
      /***
       @param callHandler 商定的事件名称,用来调用网页里面相应的事件实现
       @param data id类型,相当于我们函数中的参数,向网页传递函数执行需要的参数
     ***/

     [_bridge callHandler:@"registerFunc" data:@"name"];

    //需要传参数,需要从后台返回执行结果

     [_bridge callHandler:@"registerFunc" data:@"name" responseCallback:^(id responseData) {

        NSLog(@"后台执行完成后返回的数据");

      }];
发送消息

#### 概述

我们可以单纯地向JS发送数据,比如我们可以在网页加载完成后向网页发送一条加载完成的消息,或者传一个标题。
代码实现

	  //不需要后台返回执行结果或数据
      [_bridge send:@"红色"];

	  //需要后台返回执行结果或数据
        [_bridge send:@"红色" responseCallback:^(id responseData) {

        NSLog(@"后台执行完成后返回的数据 %@", responseData);

       }];
#### 注意点
一定要注意和网页端商定的方法名必须要保证OC代码 里和网页编码里统一。在调用之前,必须要先注册事件,写好实现体。
总结
本文只是简单地介绍了WebViewJavascriptBridge在实际开发中常用的几个方法,还有一些方法没有意义详讲,希望大家在掌握了这几个方法的使用后也去研究一下里面的其他方法。另外,本文重点是让ios工程师能快速使用,一些原理的东西还有JS方面的编码都省略了,可能文中有些地方理解不当或者不够深入,后期会再完善修改.最后,希望大家多提宝贵意见。

大家可去gitHub下载demo查看源码。