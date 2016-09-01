---
layout: post
title: "3DTouch、ShortcutItem、Peek And Pop"
date: 2015-11-10 11:01:28 +0800
comments: true
categories: 
---

### 3DTouch

<!--more-->

![](http://i12.tietuku.com/14bc7ec46d5364ab.jpg)
#### UITouch类里API的变化
##### iOS9中添加的属性
`altitudeAngle`

- 当笔平行于平面时,该值为0

- 当笔垂直于平面时,该值为Pi / 2

`estimatedProperties`

- 当前触摸对象估计的触摸特性

- 返回值是UITouchPropertyies

`updatedProperties`

- 当前触摸对象已经更新的触摸特性

- 返回值是UITouchPropertyies

`estimationUpdateIndex`

- 当每个触摸对象的触摸特性发生变化时，该值将会单独增加

- 返回值是NSNumber

##### iOS9中添加的方法
`- PreciseLocationInView:`

- 当前触摸对象的坐标
`- PrecisePreviousLocationInView:`

- 当前触摸对象的前置坐标
`- azimuthAngleInview:`

- 沿着x轴正向的方位角

- 当与x轴正向方向相同时,该值为0

- 当view参数为nil时，默认为keyWindow

`- azimuthUnitVectorInView:`

- 当前触摸对象的方向上的单位向量

- 当view参数为nil时，默认为keyWindow

#### UIForceTouchCapability
`UIForceTouchCapabilityUnknown`

- 不能确定是否支持压力感应
`UIForceTouchCapabilityUnavailable`
- 不能支持压力感应
`UIForceTouchCapabilityAvailable`
- 可以支持压力感应
#### UITouchType
`UITouchTypeDirect`

- 垂直的触摸类型
`UITouchTypeIndirect`
- 非初值的触摸类型
`UITouchTypeStylus`
- 水平的触摸类型
`UITouchProperties`
`UITouchPropertyForce`

---
### ShortcutItem

---
![](http://i12.tietuku.com/3aa54dab286d6e1c.jpg)

#### 静态方式
- 打开Info.plist文件
- 在对应UIApplicationShortcutItems关键字下添加item
#### 动态方式
##### 修改当前应用程序的某个shortcutItem
	
		  //获取第0个shortcutItem  
		  id oldItem = [existingShortcutItems objectAtIndex: 0];  
		  //将旧的shortcutItem改变为可修改类型shortcutItem  
		  id mutableItem = [oldItem mutableCopy];  
		  //修改shortcutItem的显示标题  
		  [mutableItem setLocalizedTitle: @“Click Lewis”];
##### 获取当前应用程序的shortcutItems
	
		  //获取当前应用程序对象  
		  UIApplication *app = [UIApplication sharedApplication];  
		  //获取一个应用程序对象的shortcutItem列表  
		  id existingShortcutItems = [app shortcutItems];
##### 重置当前应用程序的shortcutItems
	
		  //根据旧的shortcutItems生成可变shortcutItems数组  
		  id updatedShortcutItems = [existingShortcutItems mutableCopy];  
		  //修改可变shortcutItems数组中对应index下的元素为新的shortcutItem  
		  [updatedShortcutItems replaceObjectAtIndex: 0 withObject: mutableItem];  
		  //修改应用程序对象的shortcutItems为新的数组  
		  [app setShortcutItems: updatedShortcutItems];
##### 创建一个新的UIApplicationShortcutItem
- 初始化函数

  - initWithType:localizedTitle:localizedSubtitle:icon:userInfo:
 - initWithType:localizedTitle:
- 属性

  - localizedTitle:NSString

  - localizedSubtitle:NSString

  - type:NSString

  - icon:UIApplicationShortcutIcon

  - userInfo:NSDictionary

  - 只有只读特性，想要进行修改时，需要通过mutableCopy方法转变为
NSMutableApplicationShortcutItem
#### 创建一个新的Item图标
- 初始化函数

  + iconWithType:

  + iconWithTemplateImageName:

  + iconWithContact:

#### 当程序启动时
- 判断launchOptions字典内的UIApplicationLaunchOptionsShortcutItemKey是否为空
- 当不为空时,application:didFinishLaunchWithOptions方法返回false，否则返回true
- 在application:performActionForShortcutItem:completionHandler方法内处理点击事件

---
### Peek and Pop

---
![](http://i12.tietuku.com/544e882ddf85ae59.jpg)
注册预览功能的代理对象和源视图
代理对象需要接受UIViewControllerPreviewingDelegate协议
  @interface RootVC<UIViewControllerPreviewingDelegate>  
  {}  
  @end
代理对象实现协议内的Peek和Pop方法
	
	  @implementation RootVC  
	  - (UIViewController *)previewingContext:(id<UIViewControllerPreviewing>)context viewControllerForLocation:(CGPoint) point  
	  {  
      UIViewController *childVC = [[UIViewController alloc] init];  
      childVC.preferredContentSize = CGSizeMake(0.0f,300f);  

      CGRect rect = CGRectMake(10, point.y - 10, self.view.frame.size.width - 20,20);  
      context.sourceRect = rect;  
      return childVC;  
	  }  
	  - (void)previewContext:(id<UIViewControllerPreviewing>)context commitViewController:(UIViewController*)vc  
	  {  
      [self showViewController:vc sender:self];  
	  }  
	  @end
#### 注册方法声明在UIViewController类内
	[self registerForPreviewingWithDelegate:self sourceView:self.view];  	 