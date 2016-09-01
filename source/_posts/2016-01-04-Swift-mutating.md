---
layout: post
title: "Swift_mutating"
date: 2016-01-04 10:33:02 +0800
comments: true
categories: 
---

### 方法添加 mutating 关键字
<!--more-->
在swift中，enum和struct与C语言中的不同是：在Swift中我们可以给枚举和结构体添加方法，而C语言中不可以
在这些方法中，我们可以任意获取属性的值，但是默认情况下不允许修改属性的值。我们需要添加mutating关键字，这样就可以对属性进行修改



示例代码
在结构体中的方法中添加mutating关键字，实现在方法内部修改属性的值

	struct Point {

    var x = 0, y = 0

    // 不添加 mutating 关键字，不能在实例方法中修改属性值
    mutating func moveXBy(x: Int, yBy y: Int) {
        self.x += x
        self.y += y
    }
	}
在枚举中的方法中添加mutating关键字，实现在方法内部修改自身的值

	enum ChangeStateSwitch {

    case Off, Low, High

    // 不添加 mutating 关键字，不能在实例方法中修改属性值
    mutating func next() {
        switch self {
        case .Off:
            self = .Low
        case .Low:
            self = .High
        case .High:
            self = .Off
        }
    }
	}


将protocol的方法声明为mutating

在声明协议的时候，给协议方法添加mutating关键字，针对结构体和枚举来说，会有很大的帮助，这样，结构体和枚举在这个协议方法中就可以实现修改属性的值了

	protocol MyProtocol {
    mutating func change()
	}
当我们的结构体遵守了协议，实现协议方法的时候，才可以在协议方法中对属性进行修改，否则直接报错

	struct MyCar: MyProtocol {
    var price = 3459234.23

    mutating func change() {
        price = 56783.2
    }
	}
对于类来说，协议方法中添加了mutating是无意义的，因为类中的方法本身就可以修改变量

	class Car: MyProtocol {

    var price = 8923.2

    func change() {
        price = 3243.2
    }

	}