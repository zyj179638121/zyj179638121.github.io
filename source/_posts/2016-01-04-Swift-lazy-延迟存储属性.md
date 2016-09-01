---
layout: post
title: "Swift_lazy()延迟存储属性"
date: 2016-01-04 10:28:37 +0800
comments: true
categories: 
---


所谓的延迟存储属性，就是我们OC中所谓的懒加载对象，也就是把创建对象的步骤放在重写的getter方法中进行。这样，只有当我们使用这个属性的getter方法的时候，才会创建对象，可以为我们节省不必要得内存开销。
<!--more-->
在OC中，UIViewController的view属性是懒加载对象，还有，UITableViewCell的textLabel、detailTextLabel、imageView属性也都是懒加载对象，只有当我们使用的时候才会被创建。除了这些，我们自己也可以写很多。

那么，在Swift中，如何实现所谓的懒加载对象呢？

在Swift中，只需要一个lazy关键字即可实现，叫做延迟存储属性!



首先看看OC中如何实现懒加载对象的吧。。。

	@interface ViewController ()

	// 声明可变数组属性
	@property (nonatomic, strong) NSMutableArray 	*allDataArray;

	@end



	@implementation ViewController

	// 重写getter方法，成为懒加载对象
	- (NSMutableArray *)allDataArray
	{
    if (_allDataArray == nil) {
        self.allDataArray = [NSMutableArray array];
    }
    return _allDataArray;
	}

	- (void)viewDidLoad
	{
    [super viewDidLoad];
	    // 当我们访问allDataArray的getter方法的时候，数组才被创建
    	[self.allDataArray addObject:@"first object"];
	}

	@end


在Swift中，普通的存储属性这样声明，当我们创建当前类的对象时候，属性也被创建了

	class DataManager: NSObject {

    // 声明可变数组属性
    let allDataArray = NSMutableArray()

	}

	// 创建对象
	let manager = DataManager()
当manager对象被创建的时候，allDataArray数组就被创建，如果我们长时间不使用allDataArray数组，这块空间就被浪费了，可以改成如下写法：在属性声明前面添加lazy关键字

	class DataManager2: NSObject {

    // 声明可变数组属性
    lazy var allDataArray = NSMutableArray()

	}

	// 创建对象
	let manager2 = DataManager2()

	// 使用allDataArray属性
	manager2.allDataArray.addObject("first obj")
在属性前面添加一个lazy关键字，这个属性就成了延迟存储属性，或者叫做懒加载对象。意思就是：只有当我们使用这个属性的时候，这个对象才被创建。如果我们不使用这个对象，也不会造成我们内存的浪费了。



注意:延迟存储属性必须是变量(var),不可以是常量(let)

还可以有如下写法：

	class DataManager3: NSObject {

    // 声明延迟存储属性，当调用的时候才被创建
    lazy var allDataArray: NSMutableArray = {

            let array = NSMutableArray()
            array.addObject("first")
            array.addObject("second")
            return array

        }()
	}
   在项目中，某些地方使用延迟存储属性可以提高我们的代码质量，慢慢习惯使用吧

