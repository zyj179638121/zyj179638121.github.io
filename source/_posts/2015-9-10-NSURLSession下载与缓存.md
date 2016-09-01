---
layout: post
title: "NSURLSession下载与缓存"
date: 2015-9-10 10:00:25 +0800
comments: true
categories: 
---
 
NSURLSession下载与缓存
iOS9要求网络请求需要使用NSURLSession,那么本篇文章
就使用NSURLsession来实现视频的下载,图片的下载、取消下载、恢复下
载和缓存功能，期望与大家一起学习。

<!--more-->

NSURLSessionConfiguration(参数配置类)

NSURLSession状态同时对应着多个连接,不像之前使用共享的一个全局状态。会话是通过工厂方法（类方法)来创建对象
NSURLSessionConfiguration。

总共有三种会话：

- 1.defaultSessionConfiguration 默认的，进程内会话

- 2.ephemeralSessionConfiguration短暂的（内
存），进程内会话

- 3.backgroundSessionConfigurationWithIdentifier后台会话

相关属性

	//后台任务的标识符

	@property (nullable, readonly, copy) NSString *id entifier;

	//缓存的策略

	@property NSURLRequestCachePolicy requestCachePolicy;

	//请求超时时长

	@property NSTimeInterval timeoutIntervalForRequest;

	//网络服务类型

	@property NSURLRequestNetworkServiceType networkServiceType;

	//是否在非无线的情况下请求网络

	@property BOOL allowsCellularAccess;

#### 手动下载视频
#### 步骤：

在Info.plist中添加`NSAppTransportSecurity`类型Dictionary。
在`NSAppTransportSecurity`下添加`NSAllowsArbitraryLoads`类型Boolean,值设为YES;
Xcode7需要添加此条支持http

在工程内引入`AVFoundation`框架，并在相关类引入AVKit、AVFoundation头文件;

遵守`NSURLSessionDelegate`，`NSURLSessionDownloadDelegate`协议；
准备一个MP4格式的url，进行下载；
将下载完成的视频资源存入本地，并进行播放。
#### 声明相关属性

	 //下载任务  
	 @property (nonatomic, strong)NSURLSessionDownloadTask *downTask;  

	 //网络会话  
	 @property (nonatomic, strong)NSURLSession * downLoadSession;
#### 配置相关参数并下载

	    //参数设置类  简单的网络下载使用             
	defaultSessionConfiguration即可
    NSURLSessionConfiguration          *sessionConfig =        
    [NSURLSessionConfiguration
    defaultSessionConfiguration];

     //创建网络会话  
     self.downLoadSession = [NSURLSession sessionWithConfiguration:sessionConfig delegate:self delegateQueue:[NSOperationQueue new]];


     //数据请求  
     /*
      *@param URL 资源url  
      *@param timeoutInterval 超时时长
      */
     NSURLRequest *imgRequest = [NSURLRequest requestWithURL:[NSURL URLWithString:MP3URL] cachePolicy:5 timeoutInterval:60.f];

     //创建下载任务  
     self.downTask = [self.downLoadSession downloadTaskWithRequest:imgRequest];

     //启动下载任务  
     [self.downTask resume];
#### 实现代理方法

	  #pragma mark 下载过程

	  -(void)URLSession:(NSURLSession *)sessiondownloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
	{
	   //获取下载进度
      double currentProgress = totalBytesWritten / (double)totalBytesExpectedToWrite;

      dispatch_async(dispatch_get_main_queue(), ^{

	   //进行UI操作  设置进度条

        self.downLoadProgress.progress = currentProgress;

    });

	  #pragma mark 下载完成 无论成功失败

	 -(void)URLSession:(NSURLSession *)session task: (NSURLSessionTask *)task didCompleteWithError:(NSError *)error
	 {

        NSLog(@" function == %s, line == %d, error ==  %@",__FUNCTION__,__LINE__,error);

	 }
	   #pragma mark - 下载成功 获取下载内容
	 -(void)URLSession:(NSURLSession *)session   downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location
	 {
    //存储本地

    //1.获取Documents文件夹路径 （不要将视频、音频等较大资源存储在Caches路径下）
        *方法一
        NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask, YES) lastObject];

        *方法二
        NSFileManager *manager = [NSFileManager defaultManager];
        NSURL * documentsDirectory = [fileManager URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0];

	   //2.创建资源存储路径
       NSString *appendPath = [NSString stringWithFormat:@"/new.mp4"];
       NSString *file = [documentsPath stringByAppendingString:appendPath];

	   //3.将下载好的视频资源存储在路径下

      //删除之前相同路径的文件
        BOOL remove  = [manager removeItemAtPath:file error:nil];

      //将视频资源从原有路径移动到自己指定的路径
        BOOL success = [manager copyItemAtPath:location.path toPath:file error:nil];

        if (success) {

      //回到主线程进行本地视频播放
        dispatch_async(dispatch_get_main_queue(), ^{

        //创建视频播放的本地路径

        *** 请使用此方法创建本地路径
        NSURL *url = [[NSURL alloc]initFileURLWithPath:file];

        *** 此方法创建的路径无法播放 不是一个完整的路径
       //NSURL *url2 = [[NSURL alloc]initWithString:file];

        //系统的视频播放器
        AVPlayerViewController *controller = [[AVPlayerViewController alloc]init];
        //播放器的播放类
        AVPlayer * player = [[AVPlayer alloc]initWithURL:url];

        controller.player = player;
        //自动开始播放
        [controller.player play];
        //推出视屏播放器
        [self presentViewController:controller animated:YES completion:nil];


        });
    }

	 }
	NSURLSessionDownloadTask支持取消下载，可以在下载过程中随时取消继续下载，同时也可以实现恢复下载。

#### 取消下载 cancelByProducingResumeData

	//用当前NSURLSessionDownloadTask对象去调用取消下载

	 [self.downTask cancelByProducingResumeData:
    ^(NSData * _Nullable resumeData) {

     //全局变量 接收当前下载的资源
      self.data = resumeData;
    }
     //将当前下载任务置为空
      self.downTask = nil;
#### 恢复下载 downloadTaskWithResumeData

	//恢复下载 实际上是建立了一个新的下载任务 去继续之前的下载

	self.downTask = [self.downLoadSession downloadTaskWithResumeData:self.data];

	   //开启任务     
	  [self.downTask resume];

    }
#### 自动缓存
上面讲到的是自己去手动操控整个下载过程，那么，很多伙伴就会讲这样是不是太麻烦了。如果你了解了NSURLSession的缓存策略，那么，你就会发现，我们这么写确实是太麻烦了，那么下面我们就来学习一下NSURsession的自动缓存。

在开始之前，先介绍一下`NSURLRequestUseProtocolCachePolicy`的几种缓存策略：

- 1>NSURLRequestUseProtocolCachePolicy = 0, 默认的缓存策略， 如果缓存不存在，直接从服务端获取。如果缓存存在，
会根据response中的Cache-Control字段判断下一步操作，如: Cache-Control字段为must-revalidata, 则询问服务端该数据是否有更新，无更新的话直接返回给用户缓存数据，若已更新，则请求服务端.

- 2>NSURLRequestReloadIgnoringLocalCacheData = 1, 忽略本地缓存数据，直接请求服务端.

- 3>NSURLRequestIgnoringLocalAndRemoteCacheData = 4, 忽略本地缓存，代理服务器以及其他中介，直接请求源服务端.

- 4>NSURLRequestReloadIgnoringCacheData = NSURLRequestReloadIgnoringLocalCacheData

- 5>NSURLRequestReturnCacheDataElseLoad = 2, 有缓存就使用，不管其有效性(即忽略Cache-Control字段), 无则请求服务端.

- 6>NSURLRequestReturnCacheDataDontLoad = 3, 死活加载本地缓存. 没有就失败. (确定当前无网络时使用).

- 7>NSURLRequestReloadRevalidatingCacheData = 5, 缓存数据必须得得到服务端确认有效才使用(貌似是NSURLRequestUseProtocolCachePolicy中的一种情况)
因此，我们可以根据自己的需求去设置不同的缓存策略，而默认的就是如果有缓存就通过缓存获取数据，没有缓存就去请求网络数据。
这里，我们通过一个请求图片的例子，来窥探一下神奇的自动缓存。

代码：

	  	//创建一个UIImageView+MyImageView.h的类目，
	  在.h添加一个方法
	 - (void)loadIamgeWithURL:(NSString *)urlString
	  //在.m去实现此方法  
     - (void)loadIamgeWithURL:(NSString *)urlString
	{

    //创建下载图片的url
      NSURL *url = [NSURL URLWithString:urlString];

    //创建网络请求配置类
      NSURLSessionConfiguration * configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

    //创建网络会话
      NSURLSession *session = [NSURLSession sessionWithConfiguration:configuration delegate:nil delegateQueue:[NSOperationQueue new]];

    //创建请求并设置缓存策略以及超时时长
      NSURLRequest *imgRequest = [NSURLRequest requestWithURL:url cachePolicy:0 timeoutInterval:30.f];
	   //*也可通过configuration.requestCachePolicy 设置缓存策略

    //创建一个下载任务
      NSURLSessionDownloadTask *task = [session downloadTaskWithRequest:imgRequest completionHandler:^(NSURL * _Nullable location, NSURLResponse * _Nullable response, NSError * _Nullable error) {

      //下载完成后获取数据 此时已经自动缓存到本地，下次会直接从本地缓存获取，不再进行网络请求
      NSData * data = [NSData dataWithContentsOfURL:location];

      //回到主线程  
       dispatch_async(dispatch_get_main_queue(), ^{

      //设置图片      
       self.image = [UIImage imageWithData:data];
     });


    }];


    //启动下载任务
     [task resume];

 	 }
 	 
### swift下载任务
import UIKit

class ViewController: UIViewController,NSURLSessionDelegate,NSURLSessionDataDelegate {
    
    var session:NSURLSession!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        
        //let config = NSURLSessionConfiguration.backgroundSessionConfiguration("back")//不赞成使用
        //let config = NSURLSessionConfiguration.backgroundSessionConfigurationWithIdentifier("back")//委托中不能使用,使用系统处理下载,就算APP没有运行了,也可以实现
        //let config = NSURLSessionConfiguration.ephemeralSessionConfiguration();//这个是临时数据下载,适用于小数据下载
        let config = NSURLSessionConfiguration.defaultSessionConfiguration()//默认配置
        config.timeoutIntervalForRequest = 15 //连接超时时间
        
        session = NSURLSession(configuration: config, delegate: self, delegateQueue:nil)//队列中,如果想要程序在主线程中执行,可以使用NSOperationQueue.mainQueue()
        
        let url = NSURL(string: "http://www.wutongwei.com")
        
        
        let task = session.dataTaskWithURL(url!, completionHandler: { (
            data, response, error) -> Void in
            
            
            let str = NSString(data: data, encoding: NSUTF8StringEncoding)
            println("Done!")
            
            self.session.finishTasksAndInvalidate() //确保执行完成后,释放session
            
            
            if error == nil {
                
                let manager = NSFileManager()
                
                //
                var error:NSError?
                var destinationPath = manager.URLForDirectory(NSSearchPathDirectory.CachesDirectory, inDomain: NSSearchPathDomainMask.UserDomainMask, appropriateForURL: url, create: true, error: &error)!
                //
                let componenetsOfUrl = url?.absoluteString!.componentsSeparatedByString("/")
                let index = componenetsOfUrl!.count - 1
                let fileNameFromUrl = componenetsOfUrl![index]
                
                destinationPath = destinationPath.URLByAppendingPathComponent(fileNameFromUrl)
                
                
                //
                manager.moveItemAtURL(url!, toURL: destinationPath, error: nil)
                
                let message = "保存下载数据到 = \(destinationPath)"
                
                self.displayAlertWithTitle("Success", message: message)
                
                
            }else{
                self.displayAlertWithTitle("Error", message: "不能下载这数据,一个错误抛出")
            }
            
            
            
        })
        
        task.resume() //这个是启动任务的,不调用,则不会执行请求
                 
    }
    
    
    func displayAlertWithTitle(title:String,message:String){
        
        let controller = UIAlertController(title: title, message: message, preferredStyle: UIAlertControllerStyle.Alert)
        controller.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.Default, handler: nil))
        presentViewController(controller, animated: true, completion: nil)
    }
    
    
    ///
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    
}
  
通过这种缓存策略，我们就可以实现图片下载并自动缓存，当我们需要再次使用此资源的时候，它就会自动去本地缓存查找是否有已经下载好的图片资源，如果有就会直接去本地的，从而不需要再去进行网络请求。可以在下载完成后，将网络断开进行测试。
总结
本次主要是讲解了NSURLSession的下载，以及自动缓存策略。它的功能非常强大，还有很多没来得及细细研究，如果你也喜欢它😍，那就抓紧时间去研究吧~