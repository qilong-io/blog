---
title: Swift 学习笔记
date: 2019-09-10 14:46:21
tags: Swift
---

本文记录 学习 swift 学习过程中，遇到的一些问题，以及相关知识点，仅作为学习记录，文中引用相关资料均已注明参考链接，如有侵权，请联系我删除。

[toc]

<!--more-->
### 知识点

#### 对let 和 var 的理解
**_let 和 var 的区别就是在于对象内存地址可不可以改变_**
**let** 代表不可变对象，即对象首地址不能发生变更，也就是说内存地址不能改变，但也可以修饰可变对象。

```
let a: String = "sb"

a = "sbc"    // 会报错误 ，因为let修饰的变量不能改变指针指向

```

```
let array: NSMutableArray = NSMutableArray()

array.add("123")  //不会报错，因为数组a 的指针没有重新指向
```

**var** 代表可变对象，即内存地址可以发生改变，可以修饰指针重新指向的变量。


```
var b: String = "sb"

b = "sbc" // 不会报错，因为var修饰的变量指针可以重新指向
```

#### 对 ！和 ？ 的理解 [参考链接](https://blog.csdn.net/Lu_Ca/article/details/80248800)

？其实就是swift的一种新的类型，这个类型是一个可选类型（optional）。这个标志表示我定义一个变量为可选类型的。例如 

```
 let num:Int?
```
这个可选类型就是表示，我生明了一个类型如果有值就为Int类型的，如果没有值那么就是nil。所以说我并没有声明变量num为Int类型，而是可选类型的，可能是Int也可能是nil。

当我对num显式赋值时

```
let num:Int? = 3
```
那么num就是Int类型，当我没有对num显式赋值，那么num为nil类型

如果想要操作一个optional类型的变量，需要先进行解包，然后才能操作，这时就用到了！

！表示强解包，在使用的时候要注意，如果有值就会得到变量的值，如果为nil那么程序会crash

例如

```
// 定义一个string的可选类型str，显式赋值
var str: String? = "Hello"
if str{
    let message = "你好" + str！
    print(message)
}
```
因为已经给str赋值为hello，所以if判断条件成立，然后给name强解包，得到hello

同样

```
// 定义一个string的可选类型str，但是没有显式赋值
var str: String?
let message = "你好" + str！
print(message)
```
强解包程序会crash掉

所以在使用！强解包的时候一定要确定解包对象不为nil，否则会引起程序崩溃

#### 对 as？ 和 as！的理解 [参考链接](https://www.jianshu.com/p/e798cd9d3303)

1. as的使用场合
    从派生类转换为基类，向上转类型(upcasting)
    ```
        class Animal{}
        class Dog:Animal{}
        let  cat = Dog()
        let dog = cat as Animal
    ```
    消除二义性，数值类型转换
    ```
    let num1 = 4 as Int
    let num2 = 5.09 as CGFloat
    ```
    switch语句中进行模式匹配
    如果不知道一个对象是什么类型，可以通过switch语法检测他的类型，并且尝试在不同类型下使用对应的类型进行相应的处理。
    ```
    switch animal{
         case let dog as Dog:
               print("如果是Dog类型对象，则做相应处理")
         case let cat as Cat:
               print("如果是Cat类型对象，则做相应处理")
         default: break
    }
    ```
    
2. as？ 使用场合
    向下转型(Downcasting)时使用,由于是强制类型转换，如果转换失败会报runtime运行错误
    ```
    class Animal{}
    class Cat:Animal{}
    let animal:Animal = Cat()
    let cat = animal as! Cat
    ```

3. as！ 使用场合
    as?和as!操作符的转换规则是一样的，只是as?在转换失败之后会返回nil对象，转换成功之后返回一个可选类型(optional)，需要我们拆包使用。
由于as?转换失败也不会报错，所以对于能够100%确定使用as!能够转换成功的，使用as!,否则使用as?
    ```
    let animal:Animal = Cat()
    if let cat = animal as? Cat{
       print("这里有猫")
    }else{
      print("这里没有猫")
    }
    ```

### 语法记录

####  extension（Category）添加属性写法


```
extension UIViewController{
    
    
    var testString : String? {
        get {
            return (objc_getAssociatedObject(self, "testStringKey") as? String)
        }
        set {
            objc_setAssociatedObject(self, "testStringKey", newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
}
```

#### 懒加载写法

```
lazy var testView : UIView = {
        let view = UIView()
        view.backgroundColor = .red
        return view()
    }()
```

#### get set 方法

1. swift 中我们不能使用  _c 来表示 OC 中 @property 修饰的变量 c

    ```
    var x: CGFloat{
        set{
            x = newValue
        }
        get{
            return  23.1
        }
    }
    ```

2. swift 5.0 中使用了willset 和 didSet 这连个特性 来见识属性的除初始化之外的 属性值变化，和 OC 比较，我们可以在didSet 里面执行一些改变UI的操作

    ```
     var newAge:String?{
            willSet{
                //
                print("===========")
            }
            didSet{
                print("did set "+newAge!)
            }
        }
    ```

#### 自定义 DEBUG logs 输出

```
func QLog<T>(_ message : T, fileName : String = #file, lineNum : Int = #line) {
    #if DEBUG
    // 处理fileName
    let file = (fileName as NSString).lastPathComponent
    print("\(file):[\(lineNum)] \(message)")
    #endif
}
```