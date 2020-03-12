---
title: Swift 介绍
date: 2020-03-11 15:16:48
tags:
---


# 什么是[Swift](https://developer.apple.com/swift/)
> Swift
The powerful programming language that is also easy to learn. 

<!-- more -->

## 历史
Objective-C，80年代初发明，依赖于 GCC 编译链，而开源的GCC，不会为了苹果去做优化，因此苹果把Chris Lattner（LLVM 框架,Clang 编译器的作者)招入麾下，把 GCC 的编译链先替换成 LLVM +GCC，又替换成 LLVM+Clang ，做语法简化、自动引用计数、增加 Blocks 和 GCD 多线程异步处理技术…… 终于，OC 在 30 年后重新焕发出勃勃生机，语言排名最高到第三。
但是，苹果却有点烦了，OC 改进了这么多年，怎么看都像是在修修补补，用 Blocks 去实现一个类似 Python 的 lambda 闭包功能，看起来总是那么别扭。好吧，既然已经全盘掌握了 LLVM 和 Clang，为什么我们不去基于现在的编译器设计一门全新的语言呢？一门属于苹果的语言！于是 Swift 诞生了……

Chris Lattner ，从2010年开始研发，WWDC 2014发布，用于 OS X 和 iOS 平台新的编程语言。
Swift 是一门博采众长的现代语言，在设计的过程中，克里斯参考了Objective-C，Rust，JS，Haskell，Ruby，Python，C# 等优秀语言的特点，最终形成了 Swift 的语法特性。


# 为什么现在要用 Swift

## 趋势
1. 首先 Objective-C 已经不更新了。
2. Swift 发布到 5.1，ABI 稳定。
3. 很多OC的第三方库，已经不更新，新的系统可能出现bug或者适配问题
4. AppStore 排名前 1000的App做分析，国内使用 Swift 的 APP 约占比 22%，美区使用 Swift 的 APP 约占比 78%，其中美区剩余没有使用 Swift的APP大部分来自中国地区本地化的产品，如抖音，快手等，可以得出一个结论，国内还是小众的 Swift，在国外已经是现状。
5. WWDC 19 年发布了 4 个 Pure Swift 框架，无法简单的被 Objective-C 混编。

## Swift 的优势

| 优势 | Swift |
| --- | --- |
| 新特性 | 元组，闭包 ，泛型，命名空间 |
| 先进性 | Struct, enum更强大灵活，控制流更先进（if let 、 guard 、区间操作符、switch case任意类型） |
| 安全性  | 类型安全，禁止隐式转换，optional保护 |
| 包大小 | 编译后减少10% |
| 编码效率 | 代码更简短 |
| 性能 | 没有动态派发，函数调用速度更快，编译速度是OC 1.3倍 <br>同时带来更高的安全性，函数不容易被动态修改 |
| 跨平台 | 支持 |
| 编程范式 | 面向协议编程，函数式编程，SwiftUI  |


### enum 类型灵活（switch case）

```
enum Barcode {
    // 条形码
    case upc(Int, Int, Int, Int) 
    // 二维码
    case qrCode(String)
}

var productBarcode = Barcode.upc(8, 85909, 51226, 3)

switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

### 区间运算符

```
for index in 0...5 {
    print(index)
}

(0...5).reversed() // 区间反向取值


//stride(from: 0, to: 10, by: 2)  从0到9（不包括10）, 每次跳过2个
for index in stride(from: 0, through: 10, by: 2) {
    print(index)
}

log: 
0
2
4
6
8
10
```

### 可选类型
Swift不仅对类型是否匹配有严格要求，还对一个类型是否有值格外严格。

```
var a: String?
var b: String = "b"
```

对于上面两个变量a和b，虽然他们都是String类型的，但实际上他们是不一样的，a属于可选String类型，b是String类型。
```
// 确定不为空值的时候强制解包
print(a!)

// 不确定是不是空值的时候，进行可选绑定
if let aTemp = a {
   print(a)
}

// 使用空合运算符保证在解包失败的时候有一个备选值
print(a ?? "0")
```

### 闭包
OC 定义闭包
```
@property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);

typedef returnType (^TypeName)(parameterTypes);
```
Swift 定义闭包
```
var c = { ()->() in }
typealias TypeName = ((parameterTypes) -> Void)
```


### Struct 与 Class 对比
|  | Struct | Class |
| --- | --- | --- |
| 优点 | 简单，快，可以深拷贝，真正的不变性，不会内存泄漏等 | 可继承，可以和OC语言一起用 | 
| 类型 | 值类型 - value type-直接存在栈（stack）中 | 引用类型 - referenc type-在栈（stack）中存储的是它的引用地址(真正存在堆中) |
| 初始化 | 会自动默认生成一个成员变量初始化方法 | 必须要有一个初始化方法 |
| 拷贝 | 深拷贝 - 值拷贝 | 浅拷贝 - 只拷贝了一份引用，指向同一个内存地址 | 
| 数据的不变性 | 数据的不变性--定义为let后，不可改变其中的属性 | 即使用let定义，仍旧可以随意改变其中的属性 |
 

### 面向协议开发POP
所谓面向协议编程，就是使用protocol声明方法，然后使用extension提供默认的实现，只要需要使用到该方法的类遵循该protocol，就可以直接使用该extension的实现。
OOP对比：类只能继承自其它一个类，但协议可以继承自多个协议。 并且面向对象方法，容易造成耦合。
```
protocol animal {
    var food: String {get}
    func eat()
}

extension animal {
    func eat() {
        print("food name is \(food)")
    }
}

struct Cat: animal {
    
    var food: String = "mouse"
}

struct Dog:animal {
    var food: String = "cat"
}

let cat = Cat()
let dog = Dog()
cat.eat()
dog.eat()

log:
food name is mouse
food name is cat
```


# 怎么做迁移
## [Swiftify](https://objectivec2swift.com/#/converter/)
有一个比较好的自动化工具Swiftify，可以将OC文件甚至OC工程整个转成Swift，只用了免费版，很多地方需要手动修改，并且由于之前OC没有按照swift的特性, 只是语法转化显得不够理想。

## 与 OC 混编
通过桥接文件，import 相关类库，就可以在代码中实现混编。

## [SwiftLint](https://github.com/realm/SwiftLint)
对于OC转Swift之后的语法变化还有很多细节值得注意，特别是对于初次使用Swift这门语言的同学，很容易遗漏或者待着OC的思想去写代码。这里推荐一个语法检查的框架SwiftLint，可以自动化的检查我们的代码是否符合Swift规范

# 其他扩展
## 开源
Swift 以开源形式在 Swift.org 上开发而成，其源代码、bug 跟踪器、论坛和常规开发版本将对所有人公开。在这个由众多开发者组成的广泛社区中，Apple 内部员工与成百上千位外部贡献者携手合作，让 Swift 愈加出色。该社区还会提供更多博客、播客、会议和聚会，让社区中的开发者分享有关如何发挥 Swift 巨大潜能的经验。
### 跨平台
Swift 已能够支持所有 Apple 平台和 Linux，社区成员正积极地努力将 Swift 移植到更多平台上。借助 SourceKit-LSP，社区成员还在努力将 Swift 支持集成到各种开发者工具中。我们非常期待看到 Swift 以更多元的方式构建更安全、更快速的软件，同时也让编程变得更有趣。

### Swift for Sever
Swift 不仅能够为 Apple 平台开发很多新 app，而且还适用于一系列新的现代化服务器 app。Swift 非常适合用于需要运行时安全、编译性能和较小内存占用空间的服务器 app。为了把握使用 Swift 开发和部署服务器 app 的方向，Swift 社区成立了 Swift Server 工作组。该工作组为此而推出的首个产品为 SwiftNIO，这是一款跨平台的异步事件驱动型网络应用程序框架，适用于高性能协议服务器和客户端。它是构建其他面向服务器的工具和技术的基础，包括正在积极开发的日志记录、指标和数据库驱动程序。

### [swift for Tensorflow](https://www.tensorflow.org/swift)


## [swiftUI](https://developer.apple.com/cn/xcode/swiftui/)
swiftUI 采用声明式语法，与React，Flutter相似。UI 数据绑定，数据更新UI进行更新（类似 setData）

在 Xcode 中不仅仅可以通过代码改变实时预览，还可以通过编辑预览生成代码
SwiftUI 可以在 Xcode 里面直接切换 LiveMode 可以不运行设备直接进入交互模式，再具有多个预览设备时可以很方便的动态调试 UI 布局。

## 架构设计
将在后续文章中分析