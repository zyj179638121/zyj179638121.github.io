---
layout: post
title: "@synthesize 与@dynamic 的不同"
date: 2015-2-10 12:03:10 +0800
comments: true
categories: 
---

一、SDK中描述是在声明property的时候，有2个选择 
1：通过@synthesize 指令告诉编译器在编译期间产生getter/setter方法。 
2：通过@dynamic指令，告诉编译器属性的setter与getter 方法由用户自己实现。
<!--more-->
	假如一个属性被声明为@dynamic str， 然后没有提供setter和getter方法，编译的时候没有问题，但是当程序运行到 instacnce.str = @”jasehruer”,由于缺setter方法导致程序崩溃，或者运行到 someValue = instance.var 时，由于缺少getter 方法同样导致程序崩溃。

	编译时没有问题，运行时才执行相应的方法,这就是所谓的动态绑定。@dynamic 可用于在分类中添加属性，（需要用objc_getAssociateObject 和objc_setAssiociatedObject 函数）。

	有些存取是在运行时动态创建的，如在CoreData的NSManagedObject类使用的某些。如果你想这些情况下，声明和使用属性，但要避免缺少方法在编译时的警告，你可以使用@dynamic动态指令，而不是@synthesize合成指令。例如
代码演示：

.h 文件

	#import <UIKit/UIKit.h>
	@interface ViewController : UIViewController
	@property (nonatomic,strong) NSString *string;
	@property (nonatomic,strong) NSString *str;
	@end
 
.m 文件

	#import "ViewController.h"
	@implementation ViewController
	@synthesize string = _string;
	@dynamic str;
	- (void)viewDidLoad {
    [super viewDidLoad];
    self.str = @"asdf";
	}