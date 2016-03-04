>#[原文出自：标哥的技术博客](http://www.henishuo.com/strings-and-characters-of-swift/)

#前言

字符串是一系列的字符的集合。在Swift中，使用String类型表示字符串类型，而使用Character表示字符类型。

Swift的String类型与Foundation中的NSString类型可以无缝桥接，因此可以随时互相转换。在实际开发中，有的时候我们会选择转换成NSString类型以使用其中的API。

>Swift版本：2.1<br>
>Xcode: 7.2

#字符串字面量（String Literals）

在代码中包含一段预定义的字符串值作为字符串字面量。字符串字面量是由双引号 ("") 包裹着的具有固定顺序的字符集。字符串字面量可以用于为常量和变量提供初始值：

```
// 使用双引号
let websiteTitle = "标哥的技术博客"
var websiteUrl = "http://www.henishuo.com"
```

#初始化空字符串（Initializing An Empty String）

初始化空字符串有以下几种方式：

* 直接使用`""`
* 使用无参构造函数
* 使用有参构造函数，但是参数传`""`

```
var string = "" // 空字符串
string = String() // 空字符串
string = String("")// 空字符串
```

String类型提供了字符串是否为空的只读属性，其定义如下：

```
/// `true` iff `self` contains no characters.
public var isEmpty: Bool { get }
```

既然是只读属性，那么就不能修改。由于是属性而不是方法，因此在调用时不能使用圆括号：

```
if string.isEmpty {
  print("stirng is empty!")
}
```

#字符串可变性(String Mutability)

可以通过将一个特定字符串分配给一个变量来对其进行修改，或者分配给一个常量来保证其不会被修改。与Objective-C不同的是，Swift只提供了String类型表示字符串，要声明为可变字符串，只需要使用`var`关键字声明。若希望字符串不可改变，只需要使用`let`关键字声明即可。

```
// 使用双引号
let websiteTitle = "标哥的技术博客"
// Error，要报字符串类型为不可变类型，不可修改的错误
// websiteTitle = "标哥"

var websiteUrl = "http://www.henishuo.com"
// Ok，可变字符串是可以修改的
websiteUrl = "https://github.com/CoderJackyHuang"
```

#字符串是值类型（Strings Are Value Types）

Swift的String类型是结构体类型，因此它是值类型。 如果创建一个新的字符串，那么当其进行常量、变量赋值操作，或在函数/方法中传递时会进行值拷贝。任何情况下都会对已有字符串值创建新副本，并对该新副本进行传递或赋值操作。

Swift默认字符串拷贝的方式保证了在函数/方法中传递的是字符串的值。我们可以明确拥有这个值，而不用管它来自哪里。 可确信传递的字符串不会被修改，除非自己去修改它。

在某些场景下，Swift编译器会优化字符串的使用，使实际的复制只发生在绝对必要的情况下，这意味着您将字符串作为值类型的同时可以获得极高的性能。

由于是值拷贝，而不是引用，因此我们将参数string传到参数中，在函数内部修改参数值，并不会影响外部的参数string，因此只是值的拷贝：

```
// 字符串是结构体类型，而结构体是值类型，因此字符串也是值类型。在传值时，会进行值拷贝而不是引用。
string = "值拷贝"
func testStringCopy(var str: String) {
  str = "这里新值"
  print(str + "  " + string)
}

// 打印结果："这里新值  值拷贝\n"
testStringCopy(string)
```

#使用字符（Working With Characters）

我们可以通过`for-in`循环来遍历字符串中的characters属性来获取每一个字符的值。

```
let dogString = "Dog!🐶"
for character in dogString.characters {
    print(character)
}
// D
// o
// g
// !
// 🐶
```

我们还可以通过`SequenceType`协议中的`enumerate`方法来遍历：

```
for (index, c) in dogString.characters.enumerate() {
  print("c = \(c), index= \(index)")
}

// 打印结果为：
c = D, index= 0
c = o, index= 1
c = g, index= 2
c = !, index= 3
c = 🐶, index= 4
```

如果我们要声明字符数组，需要指定变量类型：

```
// 会被推断为[String]类型
let chars = ["A", "B", "C"]

// 这才是被声明为字符数组
let chars1: [Character] = ["A", "B", "C"]
```

#连接字符串和字符 (Concatenating Strings and Characters)

字符串连接，可以使用`+`、`+=`连接，当然还有其他函数可以实现。

##+连接字符串
使用`+`连接字符串，是因为系统为我们定义并实现了符号函数：

```
public func +(lhs: String, rhs: String) -> String
```

我们在使用的时候，编译会自动转换成符号函数调用，比如下面的`str1 = str1 + str2`会被转换成：`str1 = +(str1, str2)`调用。其实这么一看，像不像将中缀表达式转换成前缀表达式呢？（随口一说，忽略）:

```
var str1 = "My Website"
var str2 = " Is http://www.henishuo.com"

str1 = str1 + str2
print(str1)
// 打印结果为：My Website Is http://www.henishuo.com
```

##+=连接字符串

我们先看看字符串的`+=`符号函数声明：

```
public func +=(inout lhs: String, rhs: String)
```

我们看到第一个参数有`inout`关键字，因此传的是引用，所以第一个参数值会被修改。当编译器执行`str1 += str2`时，最终调用会是类似`+=(&str1, str2)`：

```
var str1 = "My Website"
var str2 = " Is http://www.henishuo.com"

str1 += str2
print(str1)
// 打印结果为：My Website Is http://www.henishuo.com
```

#字符串插值 (String Interpolation)

对于`\()`，使用也是很常见的，有的时候要格式化成某个字符串，而其中一部分值来源于某些变量，因此这时候使用`\()`就很好用了：

```
var str1 = "My Website"
var str2 = " Is http://www.henishuo.com"
str1 = "\(str1)\(str2)"
print(str1)
// 打印结果为：My Website Is http://www.henishuo.com
```

#Unicode

Unicode是国际标准文本编码和表示。它可用标准格式表示任意语言中几乎所有的字符，并能够对文本文件或网页这样的外部资源中的字符进行读写操作。Swift的String和Character类型是完全兼容Unicode标准的。

关于这一小节的内容，不详细说了。其实，我们只需要知道兼容Unicode就好了，正常开发中并不会记得这么多的Unicode字符，在需要的时候去查一查Unicode字符表就可以了。

当然我们需要知道一些特殊字符：

* 转义字符：\0(空字符)、\\(反斜线)、\t(水平制表符)、\n(换行符)、\r(回车符)、\"(双引号)、\'(单引号)。
* Unicode标量：写成\u{n}(u为小写)，其中n为任意一到八位十六进制数且可用的Unicode 位码。

#计算字符个数(Counting Characters)

```
let str = "计算characters\'s count"
let count = str.characters.count
// 20
```

我们需要记住的是，计算字符串的个数始终使用`count`属性来获取就可以了，因为它会根据字符编码计算出正确的个数。

由于每个字符占用的内存空间不一定相同，因此如果我们转换成`NSString`调用`length`属性获取长度，并不一定正确。因为`NSString`类型获取的`length`实际是`String`的`utf16Count`。

#访问和修改字符串 (Accessing and Modifying a String)

对于字符串的访问和修改，我们可以通字符串的属性和方法来访问和读取它，当然也可以用下标语法完成。

##字符串索引 (String Indices)

每一个String值都有一个关联的索引(index)类型，即String.Index，它对应着字符串中的每一个Character的位置。

不同的字符可能会占用不同数量的内存空间，所以要知道Character的确定位置，就必须从String开头遍历每一个Unicode标量直到结尾。因此，Swift的字符串不能用整数(integer)做索引。

使用startIndex属性可以获取一个String的第一个Character的索引。使用endIndex属性可以获取最后一个Character的后一个位置的索引。因此，endIndex属性不能作为一个字符串的有效下标。如果String是空串，startIndex和endIndex是相等的。

通过调用String.Index的predecessor()方法，可以立即得到前面一个索引，调用successor()方法可以立即得到后面一个索引。任何一个String的索引都可以通过锁链作用的这些方法来获取另一个索引，也可以调用advancedBy(_:)方法来获取。但如果尝试获取出界的字符串索引，就会抛出一个运行时错误。

```
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G

// endIndex代表字符串最后一个位置的下一个位置，
// 而predecessor()则是获取前一个位置，
// 因此就获取到了最后一个位置
greeting[greeting.endIndex.predecessor()]
// !

// startIndex是获取第一个位置，
// 而successor是获取下一个位置
greeting[greeting.startIndex.successor()]
// u

// 关于advancedBy的声明：
// public func advancedBy(n: Self.Distance) -> Self
// 可知这是个相对距离函数
// startIndex第一个位置，后移7个位置，对应于字符a所在的位置
let index = greeting.startIndex.advancedBy(7)
greeting[index]
// a
```

我们可以通过索引来遍历访问：

```
for index in greeting.characters.indices {
   print("\(greeting[index]) ", terminator: " ")
}
// 打印输出 "G u t e n   T a g !"
```

##插入和删除 (Inserting and Removing)

通过`insert(_:atIndex:)`插入单个字符,通过`insertContentsOf(_:at:)`插入字符集合或者字符串：

```
// 插入字符
var welcome = "Hello world"

// 插入单个字符
welcome.insert("!", atIndex: welcome.endIndex)

// 插入多个字符
welcome.insertContentsOf(["A", "B"], at: welcome.endIndex)

// 插入字符串
welcome.insertContentsOf("标哥的技术博客".characters, at: welcome.endIndex)

print(welcome)
```

通过`removeAtIndex(_:)`移除单个字符，通过`removeRange(_:)`移除某个范围的字符，通过`removeAll()`移除所有的字符，也就是清空：

```
// 移除最后一个字符
welcome.removeAtIndex(welcome.endIndex.predecessor())
print(welcome)

// 移除一个范围内的字符
welcome.removeRange(Range(start: welcome.startIndex.successor(),
  end: welcome.endIndex))
print(welcome)// H

// 移除最后6个字符
let range = welcome.endIndex.advancedBy(-6)..<welcome.endIndex
welcome.removeRange(range)

// 清空
welcome.removeAll()
print(welcome)
```

#比较字符串 (Comparing Strings)

Swift 提供了三种方式来比较文本值：字符串字符相等、前缀相等和后缀相等。

##字符串/字符相等 (String and Character Equality)

判断相等就比OC简单了，`==`就可以判断了。能这么用的原因是苹果为我们定义并实现了：

```
public func ==(lhs: String, rhs: String) -> Bool
```

所以我们就可以在使用的时候直接使用`==`：

```
// 判断字符串字符相等
let string1 = "This is string1"
let string2 = "This is string2"

if string1 == string2 {
  print("string1 == string2")
} else {
  print("string1 != string2")
}

// 判断前缀是否相等
if string1.hasPrefix("This") {
  print("string1 has prefix: This")
} else {
  print("string1 doesn\'t have prefix: This")
}

// 判断后缀是否相同
if string1.hasSuffix("1") {
  print("ok, has suffix 1")
}
```

#写在最后

本篇博文是笔者在学习Swift 2.1的过程中记录下来的，可能有些翻译不到位，还请指出。另外，所有例子都是笔者练习写的，若有不合理之处，还望指出。

学习一门语言最好的方法不是看万遍书，而是动手操作、动手练习。如果大家喜欢，可以关注哦，尽量2-3天整理一篇Swift 2.1的文章。这里所写的是基础知识，如果您已经是大神，还请绕路！

#关注我


如果在使用过程中遇到问题，或者想要与我交流，可加入有问必答**QQ群：[324400294]()**

关注微信公众号：[**iOSDevShares**]()

关注新浪微博账号：[标哥Jacky](http://weibo.com/u/5384637337)

#支持并捐助

如果您觉得文章对您很有帮助，希望得到您的支持。您的捐肋将会给予我最大的鼓励，感谢您的支持！

支付宝捐助      | 微信捐助
------------- | -------------
![image](http://www.henishuo.com/wp-content/uploads/2015/12/alipay-e1451124478416.jpg) | ![image](http://www.henishuo.com/wp-content/uploads/2015/12/weixin.jpg)
