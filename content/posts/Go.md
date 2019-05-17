---
title: "Golang 信手拈来"
date: 2018-11-28T22:57:01+08:00
draft: false
---

# Golang基本特性

1. Golang是静态语言，需要先编译再执行，静态语言的好处是具有类型安全特性
2. 并发(goroutine)是go语言最重要的特性之一, goroutine类似于线程，但是比线程使用的内存更少
3. 通道（channel）是一种内置的数据结构，用于在不同的goroutine之间同步发送具有类型的消息. TODO: 测试通道传输的副本和指针功能Page5.
4. Golang提供类无继承的类型系统，这个类型系统支持面向对象开发。
5. Golang的组合（composition）设计模式，只需简单地将一个类型嵌入到另一个类型，比如把类型1嵌入到类型2中，那么类型2就可以复用类型1的所有功能。
一个类型由其他更微小的类型组合而成，避免了传统的基于继承的模型。
6. Golang的接口(interface)实现机制，允许用户对行为进行建模，而不是对类型进行建模.不需要声明某个类型实现了某个接口，编译器会判断一个类型的实例是否符合正在使用的接口.
7. Golang可以在 https://play.golang.org 浏览器中进行调试, 并可以把你的代码Share给其他人.
8. 所有处于同一个文件夹的代码文件，必须使用同一个包名，通常包和文件夹同名
9. 在Golang中，如果main函数返回，整个程序也就终止了。Go程序终止时，还会关闭所有之前启动且还在运行的goroutine。写并发程序时，最佳的做法是，在main函数返回前，清理并终止所有之前启动的goroutine，或者(问题一), 如何在main（）函数结束之前检查此进程所派生了哪些goroutine，然后再尝试终止它们


# 1. 类型系统

## 1.1 基础数据类型

基础数据类型分为：整型，浮点数，复数，布尔型，字符串和常量

12个整型数据类型如下所示，int和uint的位数依赖于CPU的位数(32位或64位) //adm

 int8  | int16| int32<br>(rune)| int64| uint8<br>(byte)| uint16|uint32|uint64|  int  |  uint |
:----: |:----:|:--------------:|:----:|:--------------:|:-----:|:----:|:----:|:-----:|:-----:|

```go
uint8       the set of all unsigned  8-bit integers (0 to 255)
uint16      the set of all unsigned 16-bit integers (0 to 65535)
uint32      the set of all unsigned 32-bit integers (0 to 4294967295)
uint64      the set of all unsigned 64-bit integers (0 to 18446744073709551615)

int8        the set of all signed  8-bit integers (-128 to 127)
int16       the set of all signed 16-bit integers (-32768 to 32767)
int32       the set of all signed 32-bit integers (-2147483648 to 2147483647)
int64       the set of all signed 64-bit integers (-9223372036854775808 to 9223372036854775807)

float32     the set of all IEEE-754 32-bit floating-point numbers
float64     the set of all IEEE-754 64-bit floating-point numbers

complex64   the set of all complex numbers with float32 real and imaginary parts
complex128  the set of all complex numbers with float64 real and imaginary parts

byte        alias for uint8
rune        alias for int32
```
<br>TODO: add a routine to calc the max and min value in each type.

```go
uint     either 32 or 64 bits
int      same size as uint
uintptr  an unsigned integer large enough to store the uninterpreted bits of a pointer value
```

整型的字面量有3种表现形式：10进制，8进制和16进制，以下3个整型字面量均表示十进制的15
```go
15  // 10进制形式(一定不能以"0"开头)
017 //  8进制形式(必须以"0"开头)
0xF // 16进制形式(必须以"0x" 或 "0X"开头)
```
## 1.2 type

type: 类型声明(type declaration)包含2种形式：别名声明(alias declaration)和类型定义(type definition) //adm

**别名声明**是为原类型T绑定一个别名，别名类型与原类型是相同的，形式为：

`type indentifier = T`

**类型定义**是在原类型T的基础上创建一个新的不同类型

`type indentifier  T`

如下例子所示:<br>
IntAlias类型是int类型的**别名声明**，IntAlias和int是两个相同的类型，他们的变量ia和i可以进行比较。<br>
IntDefinition类型是int类型的**类型定义**，IntDefinition和int是两个不同的类型，他们的变量id和i是不能进行比较的。<br>
如果想要id和i进行比较，可以将其中一个变量进行类型转换。IntDefinition到int的类型演变流程为IntDefinition--int，所以IntDefinition类型的id直接就可转换为(int)<br>
```go
type IntAlias = int
type IntDefinition int

var i int = 100
var ia IntAlias = 100
var id IntDefinition = 100

println("i == ia?", i == ia) //true, i 和 ia是相同类型，相当于2个相同类型的变量在进行值的比较。
//println("i == id?", i == id) //invalid operation: i == id (mismatched types int and IntDefinition), 编译出错，i和id是不同的类型。
println("i == id?", i == (int)(id)) //i的类型为int。id的类型演变方式为IntDefinition--int，id从IntDefinition类型到int类型直接转换即可(int)(id)
```

`i == ia? true`<br>
`i == id? true`<br>

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/psyd9W99doe)

byte和rune就是uint8和int32的**别名声明**<br>
```go
// byte is an alias for uint8 and is equivalent to uint8 in all ways. It is
// used, by convention, to distinguish byte values from 8-bit unsigned
// integer values.
type byte = uint8
// rune is an alias for int32 and is equivalent to int32 in all ways. It is
// used, by convention, to distinguish character values from integer values.
type rune = int32
```

如下例子中，T2是t1的别名声明，t1本来是不可导出的，但t1的类型别名T2首字母是大写，所以T2是可以导出的。通常可以直接将t1改为大写的T1，直接导出。<br>
```go
type t1 struct {
	S string
}
type T2 = t1

```
> More reference: https://colobu.com/2017/06/26/learn-go-type-aliases/

定义的类型(Defined Types)和非定义的类型(Non-Defined Types)，也叫命名的类型(named types)和非命名的类型(unamed types) //adm

**定义的类型(Defined Types)**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`1.所有的基础类型都是“定义的类型(Defined Types)”，比如int, string, bool, float等类型`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`2.通过“类型定义”创建出来的类型属于“定义的类型(Defined Types)”，比如type A int，类型A是定义的类型`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`3.通过“类型定义”创建出来的类型的别名也是“定义的类型(Defined Types)”，比如type B=A，类型B也是定义的类型`<br>

**非定义的类型(Non-Defined Types)**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`1.复合数据类型是“非定义的类型(Non-Defined Types)”，比如slice，map，channel和struct定义的结构体类型。`<br>

```go
type A []string //[]string是切片，属于复合数据类型，所以[]string是“非定义的类型(Non-Defined Types)”。 但是A是通过“类型定义”创建出来的类型，属于“定义的类型(Defined Types)”，符合“定义的类型(Defined Types)”的第2点。
type B = A //B是通过“类型定义”创建出来的类型A的别名，所以B也是“定义的类型(Defined Types)”,符合“定义的类型(Defined Types)”的第3点。
type C = []string //[]string是切片，属于复合数据类型，所以[]string是**非定义的类型(Non-Defined Types)**。C不符合“定义的类型(Defined Types)”的3点定义，C是“非定义的类型(Non-Defined Types)”。
```
Pointer指针: 类型T的指针类型为*T，T是*T的基础类型，*T类型的变量我们称之为指针，指针存储的是地址。 //adm

## 1.3 pointer

基础类型的变量可以通过&符号来得到变量的内存地址

```go
var a int = 100
println("Value of a:", a)
println("Address of a:", &a)
```

`Value of a: 100`
`Address of a: 0x41c7ac`

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/UMN5EBhBMi0) <br>

内建函数new(T)可以为*T类型的变量分配内存并返回这块内存的地址，比如<br>

```go
var p *int //p 为*int类型的指针，*int的基础类型是int
println("p: ", p, p == nil)
p = new(int) //new(int)分配一块存储int型数值的内存，并返回这块内存的地址给p
println("p: ", p, p == nil)
```

`p:  0x0 true`
`p:  0x41c7ac false`

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/DaCmuO67au9)<br>

如下例子演示了指针和整型变量的关系

```go
var a int = 100
println("Value of a:", a)
println("Address of a:", &a)

var pa *int
println()
println("Value of pa:", pa)

pa = &a //将变量a的地址赋给pa，pa存储的是变量a的地址，即pa指向变量a。
println("Value of pa:", pa)
println("Value pointed by pa:", *pa) //*pa表示取出pa存储地址上的数据，即a的值。

*pa = 200 //*pa = 200指的是将200赋到pa所指向的地址上，pa存储的是变量a的地址，从而变量a的值从100变为了200
println()
println("Value of pa:", pa)
println("Value pointed by pa:", *pa)

pa = nil //将pa的值赋为nil，则pa不再指向变量a，pa的值变了，但变量a的地址和值均未变
println()
println("Value of pa:", pa)
println("Value of a:", a)
println("Address of a:", &a)
```

`Value of a: 100`<br>
`Address of a: 0x41c7a4`<br>

`Value of pa: 0x0`<br>

`Value of pa: 0x41c7a4`<br>
`Value pointed by pa: 100`<br>

`Value of pa: 0x41c7a4`<br>
`Value pointed by pa: 200`<br>

`Value of pa: 0x0`<br>
`Value of a: 200`<br>
`Address of a: 0x41c7a4`<br>

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/L81O1OBPYrS)<br>

指针类型转换

```go
type DINT int
type PINT *int
type PDINT *DINT

var i int = 200
println("i:", i)

var p *int = new(int)
p = &i
println("p:", p)

var di DINT
//di = i //cannot use i (type int) as type DINT in assignment
di = (DINT)(i) //显示转换;非指针类型的定义类型(DINT)和基础类型(int)的变量不可以互相直接赋值，编译器无法使用隐式转换，代码里需要显示转换。
println(" i to  di:", di)

var pi PINT
pi = p         //隐式转换;pi的类型演变是PINT--*int; p的类型是*int。*int到PINT之间没有其他类型，*int类型的变量p可以直接(隐式转换)赋给PINT类型的变量pi。
pi = (PINT)(p) //显示转换;pi的类型演变是PINT--*int; p的类型是*int。*int到PINT之间没有其他类型，*int类型的变量p也可以显示转换赋给PINT类型的变量pi。
println(" p to  pi:", pi)

var pdi PDINT
//pdi = p //cannot use p (type *int) as type PDINT in assignment
pdi = (*DINT)(p)          //显式+隐式转换;pdi的类型演变是PDINT--*DINT--*int; p的类型是*int。*int到PDINT之间有一个*DINT类型，*int类型的变量p需要先显示转换成*DINT类型，*DINT到PINT之间没有其他类型，*DINT类型的变量p通过隐式转换成PINT，再赋给PINT类型的变量pi。
pdi = (PDINT)((*DINT)(p)) //显示转换     ;pdi的类型演变是PDINT--*DINT--*int; p的类型是*int。*int到PDINT之间有一个*DINT类型，*int类型的变量p需要先显示转换成*DINT类型，*DINT到PINT之间没有其他类型，*DINT类型的变量p也可以显示转换成PINT，再赋给PINT类型的变量pi。
println(" p to pdi:", pdi)

//pdi = pi //cannot use pi (type PINT) as type PDINT in assignment;pi不能直接赋给pdi，因为pi和pdi是不同的类型
pdi = (*DINT)((*int)(pi))          //显式+隐式转换;pdi的类型演变是PDINT--*DINT--*int; pi的类型演变是PINT--*int。PINT类型的变量pi需要先显示转换回*int类型，然后再显示转换成*DINT类型，*DINT到PINT之间没有其他类型，*DINT类型的变量pi通过隐式转换成PDINT，再赋给PDINT类型的变量pdi。
pdi = (PDINT)((*DINT)((*int)(pi))) //显示转换     ;pdi的类型演变是PDINT--*DINT--*int; pi的类型演变是PINT--*int。PINT类型的变量pi需要先显示转换回*int类型，然后再显示转换成*DINT类型，*DINT到PINT之间没有其他类型，*DINT类型的变量pi也可以显示转换成PDINT，再赋给PDINT类型的变量pdi。
println("pi to pdi:", pdi)
```

`i: 200`<br>
`p: 0x41c7a4`<br>
` i to  di: 200`<br>
` p to  pi: 0x41c7a4`<br>
` p to pdi: 0x41c7a4`<br>
`pi to pdi: 0x41c7a4`<br>

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/iMAxkg2sryh)<br>

**go指针与c指针的不同之处**<br>
`*p++`与`*p--`在c语言中分别是将指针的值自加或自减，而在go语言中则指的是将p指针指向的值自加或自减<br>

```go
var p *int = new(int)
println("p addr:", p, "p value:", *p)
*p = 100
println("p addr:", p, "p value:", *p)
*p++
println("p addr:", p, "p value:", *p)
*p--
println("p addr:", p, "p value:", *p)
```

`p addr: 0x41c7ac p value: 0`<br>
`p addr: 0x41c7ac p value: 100`<br>
`p addr: 0x41c7ac p value: 101`<br>
`p addr: 0x41c7ac p value: 100`<br>

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/UnSoFHk_z-E)<br>

c语言中被调函数里声明并定义的指针在被调函数返回时被释放，但go中有垃圾回收机制，被调函数中声明定义的指针可以返回到调用函数。

```go
package main

func pointerTest() *int {
       	var ptr *int = new(int)
       	*ptr = 123456789
       	println("ptr:", ptr, *ptr)
       	return ptr
}

func main() {
       	ret := pointerTest()
       	println("ret:", ret, *ret)
}
```

`ptr: 0x41c7a4 123456789`<br>
`ret: 0x41c7a4 123456789`

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/GSYweaHYZeG)<br>

## 1.4 rune

rune类型: rune类型的变量用于存储Unicode字符的编码 //adm

rune和int32是同一种数据类型，用于存储[**Unicode字符**](https://zh.wikipedia.org/wiki/Unicode字符列表)的编码<br>
rune 的字面量需要用单引号`''`引起来, 单引号`''`中的内容可以是以下6种中的一种：<br>
`1. 除换行符U+000A和未转意的单引号'之外的任意Unicode字符`<br>
`2. \后面跟3位8进制数`<br>
`3. \x后面跟2位16进制数`<br>
`4. \u后面跟4位16进制数`<br>
`5. \U后面跟8位16进制数`<br>
`6. 以下任意一个转意字符:`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\a   U+0007 alert or bell`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\b   U+0008 backspace`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\f   U+000C form feed`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\n   U+000A line feed or newline`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\r   U+000D carriage return`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\t   U+0009 horizontal tab`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\v   U+000b vertical tab`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\\   U+005c backslash`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\'   U+0027 single quote  (valid escape only within rune literals)`<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`\"   U+0022 double quote  (valid escape only within string literals)`<br><br>
以Unicode编码U+0061为例，它对应的十进制数是97，对应的字符是字母a。<br>
以上1-5 rune字面量的的表示方式如下，可以看到5种赋值的方法，最终打印出来的结果均是97<br>

```go
var r rune

r = 'a' //字面量'a'
println(r)

r = '\141' //3位8 进制的字面量表示为\141
println(r)

r = '\x61' //2位16进制的字面量表示为\x61
println(r)

r = '\u0061' //4位16进制的字面量表示为\u0061
println(r)

r = '\U00000061' //8位16进制的字面量表示为\U00000061
println(r)
```
`97`<br>
`97`<br>
`97`<br>
`97`<br>
`97`<br>

[**🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 P L A Y A R O U N D 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓 🍋 🥝 🍓**](https://play.golang.org/p/IMeSrjLnGAz)

## 1.5 bool
bool类型: //adm

## 1.6 byte
byte类型: byte是uint8的别名，可以将byte和uint8看作同一种数据类型 //adm

## 1.7 uintptr
uintptr类型:  //adm

## 1.8 string
string类型:  //adm

## 1.9 float
float32与float64类型:  //adm

浮点型的字面量由一个整数部分，一个小数点部分，一个小数部分和一个exponent部分，某些部分可以省略。
```go
1.23
01.23 // == 1.23
.23
1.
// A "e" or "E" starts the exponent part.
1.23e2  // == 123.0
123E2   // == 12300.0
123.E+2 // == 12300.0
1e-1    // == 0.1
.1e0    // == 0.1
0e+5    // == 0.0
```

## 1.10 complex 
complex64与complex128类型:  //adm

# 2. 数组

## 2.1 数组声明与初始化
声明一个5个元素的数组, 每个元素为int型整数，并为这5个元素赋值 //adm

```go
var array [5]int
array[0] = 10
array[1] = 20
array[2] = 30
array[3] = 40
array[4] = 50
fmt.Println("len:", len(array), "array:", array)
```

`len: 5 array: [10 20 30 40 50]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/uG3hTbW6E_X)

声明一个5个元素的数组, 每个元素为int型整数，并初始化 //adm

```go
array := [5]int{10, 20, 30, 40, 50}
fmt.Println("len:", len(array), "array:", array)
``` 

`len: 5 array: [10 20 30 40 50]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/GNFqU0yYVy8)

[...] 根据初始化元素的数量来确定数组的长度 //adm

```go
array := [...]int{10, 20, 30, 40, 50, 60, 70, 80}
fmt.Println("len:", len(array), "array:", array)
```

`len: 8 array: [10 20 30 40 50 60 70 80]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/tQZZJC4iBsD)

声明一个数组，并将第0个和第10个元素初始化为50 //adm

```go
array := [...]int{0: 50, 10: 50}
fmt.Println("len:", len(array), "array:", array)
```

`len: 11 array: [50 0 0 0 0 0 0 0 0 0 50]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/g5ThOlHRmus)

## 2.2 数组元素为指针

声明一个5个元素的数组, 每个元素为*int整型指针，并用new(int)为这5个元素分配整型指针地址 //adm

```go
var array [5]*int
fmt.Println("declared array:", array)
array[0] = new(int)
array[1] = new(int)
array[2] = new(int)
array[3] = new(int)
array[4] = new(int)
fmt.Println("assigned array:", array)
```

`declared array: [<nil> <nil> <nil> <nil> <nil>]`<br>
`assigned array: [0x416030 0x416034 0x416038 0x41603c 0x416040]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/_rM2W2QkOsY)

声明一个5个元素的数组, 每个元素为*int整型指针，并初始化 //adm

```go
array := [5]*int{new(int), new(int), new(int), new(int), new(int)}
fmt.Println("initial array:", array)
```
`initial array: [0x416020 0x416024 0x416028 0x41602c 0x416030]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/_b5U8Ps2t6M)

&array[0]，array[0] 与 *array[0] //adm

&array[0]表示数组元素array[0]的地址<br>
array[0]存储的是指针的地址<br>
*array[0]表示array[0]指针地址指向的数据<br>

```go
*array[0] = 10
fmt.Println("&array[0] =", &array[0], "array[0] =", array[0], "*array[0] =", *array[0])

*array[1] = 20
fmt.Println("&array[1] =", &array[1], "array[1] =", array[1], "*array[1] =", *array[1])

*array[2] = 30
fmt.Println("&array[2] =", &array[2], "array[2] =", array[2], "*array[2] =", *array[2])

*array[3] = 40
fmt.Println("&array[3] =", &array[3], "array[3] =", array[3], "*array[3] =", *array[3])

*array[4] = 50
fmt.Println("&array[4] =", &array[4], "array[4] =", array[4], "*array[4] =", *array[4])
```

`&array[0] = 0x442260 array[0] = 0x416020 *array[0] = 10`<br>
`&array[1] = 0x442264 array[1] = 0x416024 *array[1] = 20`<br>
`&array[2] = 0x442268 array[2] = 0x416028 *array[2] = 30`<br>
`&array[3] = 0x44226c array[3] = 0x41602c *array[3] = 40`<br>
`&array[4] = 0x442270 array[4] = 0x416030 *array[4] = 50`<br>

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/L9qH_77gX6G)

## 2.3 数组互相赋值
go语言支持将一个数组所有元素的值赋给另一个数组，前提是这2个数组要具有相同的**类型**和**长度**

数组元素为整型类型时，将array2中5个元素的值拷贝到array1 //adm

```go
var array1 [5]int
fmt.Println("declared array1:", array1)

array2 := [5]int{10, 20, 30, 40, 50}
array1 = array2
fmt.Println("assigned array1:", array1)

```
`declared array1: [0 0 0 0 0]`<br>
`assigned array1: [10 20 30 40 50]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/YbSZmdgt_Ob)

数组元素为字符串类型时，将array2中5个元素的值拷贝到array1 //adm

```go
var array1 [5]string
var array2 = [5]string{"desk", "pencil", "ruler", "ink", "book"}

array1 = array2
fmt.Println("len(array1):", len(array1), " array1   :", array1)
fmt.Println("len(array2):", len(array2), " array2   :", array2)

```

`len(array1): 5  array1   : [desk pencil ruler ink book]`<br>
`len(array2): 5  array2   : [desk pencil ruler ink book]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/HuHXZ-c0gv5)

数组元素为指针类型时，将array2中5个元素的值（指针地址)拷贝到array1  //adm

```go
var array1 [5]*int
fmt.Println("declared array1:", array1)

array2 := [5]*int{new(int), new(int), new(int), new(int), new(int)}
array1 = array2
fmt.Println("assigned array1:", array1)
```
`declared array1: [<nil> <nil> <nil> <nil> <nil>]`<br>
`assigned array1: [0x416030 0x416034 0x416038 0x41603c 0x416040]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/rG8UHTVYYNP)

## 2.4 二维数组
一维数组, &nbsp;指的是一行多列的数组。比如var array [5]int, &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是1行5列的一维数组。<br>
二维数组，指的是多行多列的数组。比如var array [2][5]int，是2行5列的二维数组。

声明一个2行5列的二维数组，每个元素为int型整数，并初始化每个元素  //adm

```go
array := [2][5]int {{10, 11, 12, 13, 14},{20, 21, 22, 23, 24}}
fmt.Println("len(array):", len(array), "array:", array)
fmt.Println("len(array[0]):", len(array[0]), "array[0]:", array[0])
fmt.Println("len(array[1]):", len(array[1]), "array[1]:", array[1])
```

`len(array): 2 array: [[10 11 12 13 14] [20 21 22 23 24]]`<br>
`len(array[0]): 5 array[0]: [10 11 12 13 14]`<br>
`len(array[1]): 5 array[1]: [20 21 22 23 24]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/bJqnRfeWlvK)<br>

声明一个2行5列的二维数组，每个元素为int型整数，并初始化第2行第3个元素  //adm
```go
array := [2][5]int{1: {2: 22}}
fmt.Println("len(array):", len(array), "array:", array)
fmt.Println("len(array[0]):", len(array[0]), "array[0]:", array[0])
fmt.Println("len(array[1]):", len(array[1]), "array[1]:", array[1])
```

`len(array): 2 array: [[0 0 0 0 0] [0 0 22 0 0]]`<br>
`len(array[0]): 5 array[0]: [0 0 0 0 0]`<br>
`len(array[1]): 5 array[1]: [0 0 22 0 0]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/ue7to0hPJVP)<br>

声明一个2行5列的二维数组，每个元素为int型整数，并依次为每个元素赋值  //adm

```go
var array [2][5]int
array[0][0] = 10
array[0][1] = 11
array[0][2] = 12
array[0][3] = 13
array[0][4] = 14
array[1][0] = 20
array[1][1] = 21
array[1][2] = 22
array[1][3] = 23
array[1][4] = 24
fmt.Println("len(array):", len(array), "array:", array)
fmt.Println("len(array[0]):", len(array[0]), "array[0]:", array[0])
fmt.Println("len(array[1]):", len(array[1]), "array[1]:", array[1])
```

`len(array): 2 array: [[10 11 12 13 14] [20 21 22 23 24]]`<br>
`len(array[0]): 5 array[0]: [10 11 12 13 14]`<br>
`len(array[1]): 5 array[1]: [20 21 22 23 24]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/F-nSACHOGJn)

二维数组互相赋值  //adm
```go
var array1 [2][5]int
var array2 [2][5]int

array2[0][0] = 10
array2[0][1] = 11
array2[0][2] = 12
array2[0][3] = 13
array2[0][4] = 14
array2[1][0] = 20
array2[1][1] = 21
array2[1][2] = 22
array2[1][3] = 23
array2[1][4] = 24

array1 = array2
fmt.Println("len(array1):", len(array1), "array1:", array1)
fmt.Println("len(array2):", len(array2), "array2:", array2)
```
`len(array1): 2 array1: [[10 11 12 13 14] [20 21 22 23 24]]`<br>
`len(array2): 2 array2: [[10 11 12 13 14] [20 21 22 23 24]]`

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/z82JDO2xVD4)

<a id="在函数间传递数组"/>
在函数间传递数组  //adm

```go
func main() {

	array := [2][5]int{{10, 11, 12, 13, 14}, {20, 21, 22, 23, 24}}
	test1(array)
	test2(&array)
}

func test1(a [2][5]int) { //test1参数传递的是整个二维数组
	fmt.Println("test1 a :", a, "len(a):", len(a), "sizeof(a):", unsafe.Sizeof(a))
}

func test2(a *[2][5]int) { //test2参数传递的是二维数组的指针
	fmt.Println("test2 a:", a, "len(a):", len(a), "sizeof(a):", unsafe.Sizeof(a))
}
```
`test1 a : [[10 11 12 13 14] [20 21 22 23 24]] len(a): 2 sizeof(a): 40`<br>
`test2 a: &[[10 11 12 13 14] [20 21 22 23 24]] len(a): 2 sizeof(a): 4`<br>

可以看到test2的参数a（传递的是二维数组的指针）只占4个字节，比test1的参数a（传递的整个二维数组）的40个字节要节省很多内存空间。<br>

[**🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 P L A Y A R O U N D 🥤  🍔 🍟 🥤 🍔 🍟 🥤 🍔 🍟 🥤 🍔**](https://play.golang.org/p/QeFy6wxv7qW)


# 3. 切片

切片是一个结构体，这个结构体包含了一个指向数组的指针elements，切片当前可用数组元素的个数length，和切片最多可用数组元素的个数capacity.<br>
```go
type _slice struct {
	elements unsafe.Pointer // referencing underlying elements
	len      int            // length
	cap      int            // capacity
}
```

切片相对于数组的好处是切片可以自动增长和缩小空间。<br>

## 3.1 切片声明和初始化

「nil slice声明方式一」用var声明的切片变量是nil slice。  //adm

```go
var s []int //or var s []int = nil

fmt.Println("s is nil?", s== nil)
fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```

`s is nil? true`<br>
`s []int(nil)`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x0 len: 0 cap: 0`<br>

用%#v打印s时表示用go的语法格式来表示变量s。<br>
用%p打印&s时表示的是切片变量s的首地址。<br>
用%p打印s时表示的是切片中elements的值，即切片数组的首地址。<br>
当elements为0值时(指针elements的0值用nil表示)，这种切片称作nil slice。<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/WxZXt2BMEFP)

「nil slice声明方式二」用 := 简短声明一个切片并用(nil)初始化的变量是nil slice。  //adm

```go
s := []int(nil)

fmt.Println("s is nil?", s== nil)
fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```

`s is nil? true`<br>
`s []int(nil)`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x0 len: 0 cap: 0`<br>

s := []int(nil) 和 var s []int 声明的切片打印结果一样，这两种方式均是声明一个nil slice。

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/te0FGkHZJGN)

「blank slice声明方式一」 用 := 简短声明一个切片并用{ }初始化的变量是blank slice。  //adm

```go
s := []int{}

fmt.Println("s is nil?", s== nil)
fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```

`s is nil? false`<br>
`s []int{}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x1c4c64 len: 0 cap: 0`<br>

用{}初始化切片变量s时，elements不是0值(与nil slice的差异)，它含有内存的地址值，但地址上的值为空(0值)，所以这种切片称作blank slice空切片。

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/YMt8Pdj5a7H)

「blank slice声明方式二」 用 make 创建len和cap均为0的切片变量是blank silce。  //adm

```go
s := make([]int, 0)

fmt.Println("s is nil?", s== nil)
fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```
`s is nil? false`<br>
`s []int{}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x1c4c64 len: 0 cap: 0`<br>

s := make([]int, 0) 和 s := []int{} 声明的切片打印结果一样，这两种方式均是创建一个blank slice。

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/z-PYfXziItX)

直接初始化切片  //adm

```go
s := []string{"pen", "ink", "paper", "eraser", "bag"}

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```

`s []string{"pen", "ink", "paper", "eraser", "bag"}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x430150 len: 5 cap: 5`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/K8hjqAlFr6c)

初始化切片的第4个元素  //adm

```go
s := []string{4: "bag"}

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n", s, len(s), cap(s))
```

`s []string{"", "", "", "", "bag"}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x430150 len: 5 cap: 5`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/hzXI06zpVYd)


## 3.2 用make创建切片

在「blank slice声明方式二」时我们用s := make([]int, 0)创建了一个切片数组长度为0的切片s，称作blank slice空切片。<br>
make是go语言的内置函数，可以为slice，map和channel分配空间，这里我们首先了解make是如何创建slice的。

 > func make(t Type, size ...IntegerType) Type

make的第1个参数t是Type类型的变量，它的返回类型是和t类型一样的Type类型，不是一个指针。<br>
对于切片来说，如果只填第1个整型参数size， 则切片的len和cap的大小均为size。<br>
如果再填第2个整型参数(make是可变参的函数)，则第一个整型参数表示切片的len大小，第二个整型参数表示切片的cap大小。

用make给nil slice分配数组空间, len == cap  //adm

nil slice是不能直接进行进行赋值的，因为系统没有为nil slice分配对应的数组（elements为nil）。<br>
要为nil slice赋值的话需要使用make为slice分配数组空间，然后再赋值。
如下例子演示了slice的len与cap相等时的情况：

```go
var s []int //or var s []int = nil

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s = make([]int, 5)

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s[0] = 1
s[1] = 2
s[2] = 3
s[3] = 4
s[4] = 5

fmt.Printf("s %#v\n", s)
```
`s []int(nil)`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x0 len: 0 cap: 0`<br>

`s []int{0, 0, 0, 0, 0}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x450020 len: 5 cap: 5`<br>

`s []int{1, 2, 3, 4, 5}`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/wgDX8PLINU-)

用make给nil slice分配数组空间, len != cap  //adm

如下例子演示了slice的len与cap不相等时的情况：

```go
s:= []int(nil)

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s = make([]int, 4, 5)

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s[0] = 1
s[1] = 2
s[2] = 3
//s[3]不予赋值，因为切片s一共有4个数组元素可以访问，s[3]默认为0
//s[4]不能赋值，因为切片s一共有4个数组元素可以访问，给s[4]赋值，会导致编译错误

fmt.Printf("s %#v\n", s)
```

`s []int(nil)`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x0 len: 0 cap: 0`<br>

`s []int{0, 0, 0, 0}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x450020 len: 4 cap: 5`<br>

`s []int{1, 2, 3, 0}`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/x_eSDliV6Mx)

用make创建切片  //adm

```go
s := make([]int, 4, 5)

s[0] = 1
s[1] = 2
s[2] = 3

fmt.Printf("s %#v\n", s)
```

`s []int{1, 2, 3, 0}`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/ORU4Uo2gOp7)

## 3.3 用append附加切片

> func append(slice []Type, elems ...Type) []Type

append函数用于将新的元素或切片附加到切片的末尾。<br>
如果切片的容量足够容纳新的数据，则新元素或切片将会被附加到切片的末尾;<br>
如果切片的容量不足以容纳新的元素或切片，append函数将会重新分配一个新的切片用于存储原数据和新附加的数据或切片：<br>
如果原来切片的容量小于1000，新的切片容量将会增长到原来的2倍，<br>
如果原来切片的容量大于1000，新的切片容量将会增长到原来的1.25倍。<br>

给blank silce附加元素和切片  //adm

>使用append给切片附加元素和切片的使用方式为：<br>
slice = append(slice, elem1, elem2)<br>
slice = append(slice, anotherSlice...)

```go
s := []int{} //or s := make([]int, 0)
s1 := []int{7,8,9}

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s = append(s, 1, 2, 3)

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))

s = append(s, s1...)

fmt.Printf("s %#v\n", s)
fmt.Printf("s header address: %p\n", &s)
fmt.Printf("s array header address(elements): %p len: %d cap: %d\n\n", s, len(s), cap(s))
```

`s []int{}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x1c4c64 len: 0 cap: 0`<br>

`s []int{1, 2, 3}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x414040 len: 3 cap: 4`<br>

`s []int{1, 2, 3, 7, 8, 9}`<br>
`s header address: 0x40a0e0`<br>
`s array header address(elements): 0x450020 len: 6 cap: 8`<br>

第一段打印结果：此时声明的切片变量s为blank slice空切片, elements为0x1c4c64, len和cap为0。<br>
第二段打印结果：向切片s append数字1, 2, 3时，append函数为切片分配新的数组空间，<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
切片数组首地址变为0x414040, 容量从0增加到4（以2的幂为基数），因为添加<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
了3个数据，所以length为3。<br>
第三段打印结果：继续向切片s append新的切片s1时(注意s1后面的...不能省)，原来的切片数组<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
(0x414040)容量为2，已不足以容纳新的切片s1的3个元素，所以append函数为<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
切片s重新分配了数组(0x450020)，容量从4增长到8并将原来数组(0x414040)的<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
数据拷贝到新的数组(0x450020)里，然后将s1的元素附加s的末尾，s的len变为6。<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/Ut4ZUVlIgFj)

append函数还支持将字符串附加到byte类型的切片上  //adm

```go
slice := append([]byte("hello "), "world"...)

fmt.Printf("elements:%06p, capacity:%d, length:%d, slice: %s\n", slice, len(slice), cap(slice), slice)
```

`elements:0x414030, capacity:11, length:16, slice: hello world`

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/PGxm3M5r7bV)

## 3.4 用copy拷贝切片

copy函数的原型如下，它支持将src切片拷贝到dst切片，同样支持将字符串的字节拷贝到byte的切片中，src和dst可以重叠。<br>
copy函数返回拷贝元素的数量，拷贝元素的数量是src或dst切片len的最小值。

> func copy(dst, src []Type) int

```go
s1 := make([]int, 5)
s2 := make([]int, 15)
s3 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
s4 := make([]byte, 5)

n1 := copy(s1, s3[:])        //s1 len 小于 s3 len
n2 := copy(s2, s3[:])        //s2 len 大于 s3 len
n3 := copy(s3[0:], s3[5:9])  //s3 重叠拷贝
n4 := copy(s4, "blackwhite") //将字符串拷贝到byte切片

fmt.Println("n1:", n1, "s1:", s1)
fmt.Println("n2:", n2, "s2:", s2)
fmt.Println("n3:", n3, "s3:", s3)
fmt.Printf("n4: %d s4: %s\n", n4, s4)
```
`n1: 5 s1: [1 2 3 4 5]`<br>
`n2: 9 s2: [1 2 3 4 5 6 7 8 9 0 0 0 0 0 0]`<br>
`n3: 4 s3: [6 7 8 9 5 6 7 8 9]`<br>
`n4: 5 s4: black`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/tnTRpLAxv2K)

## 3.5 遍历切片

```go
s := []string{"zero", "one", "two", "three", "four", "five", "six", "seven", "eight"}

for i, v := range s {
	fmt.Println(i, v)
}
```
`0 zero`<br>
`1 one`<br>
`2 two`<br>
`3 three`<br>
`4 four`<br>
`5 five`<br>
`6 six`<br>
`7 seven`<br>
`8 eight`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/NcqJAx8keKk)

## 3.6 划分切片

 在切片基础上创建切片  //adm

> 1. 对于底层数组容量为 c 的slice[i:j]切片来说:<br>
slice的切片长度是j - i<br>
slice的切片容量是c - i<br>

> 2. 对于底层数组容量为 k 的slice[i:j:k]切片来说:<br>
slice的切片长度是j - i<br>
slice的切片容量是k - i<br>

```go
slice1 := []string{"pen", "ink", "paper", "eraser", "bag"}
slice2 := slice1[1:3]
slice3 := slice1[1:3:4]
fmt.Println(slice1, len(slice1), cap(slice1))
fmt.Println(slice2, len(slice2), cap(slice2))
fmt.Println(slice3, len(slice3), cap(slice3))
```
`[pen ink paper eraser bag] 5 5`<br>
`[ink paper] 2 4`<br>
`[ink paper] 2 3`<br>

通过公式可以得出slice1的数组容量是5。
slice2的切片长度是 3 - 1 = 2， slice2的切片容量是 5 - 1 = 4.<br>
slice3的切片长度是 3 - 1 = 2， slice2的切片容量是 4 - 1 = 3.<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/YcLWUUnMm55)

 同一数组被多个切片引用时，修改某一切片元素的内容也会改变其他切片元素的内容  //adm

```go
slice1 := []string{"pen", "ink", "paper", "eraser", "bag"}
slice2 := slice1[1:3]
slice2[1] = "elephant"
fmt.Println(slice1, len(slice1), cap(slice1))
fmt.Println(slice2, len(slice2), cap(slice2))
```
`[pen ink elephant eraser bag] 5 5`<br>
`[ink elephant] 2 4`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/w25gBzX7qt5)

## 3.7 二维切片

二维切片的原理是切片成员变量len和cap均为2，elements指向的是一个长度为2，类型为_slice的数组  //adm

感受下面二维切片的地址表示方法
```go
slice := [][]int{{1, 2, 3}, {10, 20, 30}}

fmt.Printf("slice variable address: %p\n\n", &slice) //0x40a0e0：slice变量的首地址

fmt.Printf("slice[0] header address: %p\n\n", slice) //0x43e260：二维切片里第0个切片成员slice[0]的首地址。

fmt.Printf("slice[0] header address: %p\n", &slice[0])           //0x43e260：二维切片里第0个切片成员slice[0]的首地址。
fmt.Printf("slice[0] array header address:  %p\n", slice[0])     //0x414020：slice[0]切片底层数组的首地址。
fmt.Printf("slice[0] array header address:  %p\n", &slice[0][0]) //0x414020：slice[0]切片底层数组的首地址。
fmt.Printf("slice[0] array's addresses:     %p %p %p\n\n", &slice[0][0], &slice[0][1], &slice[0][2])

fmt.Printf("slice[1] header address: %p\n", &slice[1])           //0x43e26c：二维切片里第1个切片成员slice[1]的首地址。
fmt.Printf("slice[1] array header address:  %p\n", slice[1])     //0x414030：slice[1]切片底层数组的首地址。
fmt.Printf("slice[1] array header address:  %p\n", &slice[1][0]) //0x414030：slice[1]切片底层数组的首地址。
fmt.Printf("slice[1] array's addresses:     %p %p %p\n\n", &slice[1][0], &slice[1][1], &slice[1][2])
```
`slice variable address: 0x40a0e0`<br>

`slice[0] header address: 0x43e260`<br>

`slice[0] header address: 0x43e260`<br>
`slice[0] array header address:  0x414020`<br>
`slice[0] array header address:  0x414020`<br>
`slice[0] array's addresses:     0x414020 0x414024 0x414028`<br>

`slice[1] header address: 0x43e26c`<br>
`slice[1] array header address:  0x414030`<br>
`slice[1] array header address:  0x414030`<br>
`slice[1] array's addresses:     0x414030 0x414034 0x414038`<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/q8S9emkRT0_B)


在函数间传递切片  //adm

```go
func main() {

	slice := [][]int{{10, 11, 12, 13, 14}, {20, 21, 22, 23, 24}}
	test1(slice)
	test2(&slice)
}

func test1(a [][]int) { //test1参数传递的是整个二维切片
	//fmt.Println("test1 a :", a, "len(a):", len(a), "sizeof(a):", unsafe.Sizeof(a))
	fmt.Println("test2 a:", a, "sizeof(a):", unsafe.Sizeof(a))
}

func test2(a *[][]int) { //test2参数传递的是二维切片的指针
	//fmt.Println("test2 a:", a, "len(a):", len(a), "sizeof(a):", unsafe.Sizeof(a))
	fmt.Println("test2 a:", a, "sizeof(a):", unsafe.Sizeof(a))
}
```

`test2 a: [[10 11 12 13 14] [20 21 22 23 24]] sizeof(a): 12`<br>
`test2 a: &[[10 11 12 13 14] [20 21 22 23 24]] sizeof(a): 4`<br>

可以看到test2的参数a（传递的是二维切片的指针）只占4个字节，比test1的参数a（传递的整个二维切片）的12个字节要节省很多内存空间。<br>
test1的参数a占12个字节是因为切片的3个成员（elements，length和capacity）各占4各字节。 即使slice为二维切片，elements存储的是指向
一维切片slice[0]的首地址，elements作为指针，在32位平台上只占4个字节。

这段相似功能的代码也同样在<a href="#在函数间传递数组">`在函数间传递数组`</a>的章节实现过，使用数组的输出结果如下：<br>
`test1 a : [[10 11 12 13 14] [20 21 22 23 24]] len(a): 2 sizeof(a): 40`<br>
`test2 a: &[[10 11 12 13 14] [20 21 22 23 24]] len(a): 2 sizeof(a): 4`<br>

可以看到，当在函数间传递的数据量很大时，参数为数组导致的开销最大，参数为切片时开销固定为12各字节，当然传递指针开销只占4个字节，指针是最节省空间的。<br>

[**🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫  P L A Y A R O U N D 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞 🍫 🍰 🥞**](https://play.golang.org/p/L6D2RS2ve5V)


# 4. 映射

go语言的映射(maps)实现了哈希表(hash)，映射和指针、切片一样，都是引用类型，引用类型声明的变量不能直接赋值，
需要使用map先分配并初始化一个哈希表的数据结构，然后返回一个指向这个数据结构的值
go语言中map类型map样式为：[KeyType]ValueType， KeyType可以是任何[可比较的类型](https://golang.org/ref/spec#Comparison_operators)，
ValueType可以是任何类型。

## 4.1 映射声明与初始化
map创建的方式一： 先声明变量，再用make分配空间  //adm

```go
var m map[string]int //声明一个类型为map[string]int的变量m
fmt.Printf("m header address: %06p, m hash header address %06p.\n", &m, m)

m = make(map[string]int) //使用make为m分配类型为map[string]int的哈希表
fmt.Printf("m header address: %06p, m hash header address %06p.\n", &m, m)

m["aaa"] = 111
m["bbb"] = 222
m["ccc"] = 333
fmt.Println("m: ", m)
```
`m header address: 0x40c128, m hash header address 0x000000.`<br>
`m header address: 0x40c128, m hash header address 0x43e260.`<br>
`m:  map[aaa:111 bbb:222 ccc:333]`<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/afhMcDGie9O)

map创建的方式二： 直接用make创建并分配空间  //adm
```go
m := make(map[string]int)

m["aaa"] = 111
m["bbb"] = 222
m["ccc"] = 333

fmt.Printf("m header address: %06p, m hash header address %06p.\n", &m, m)
fmt.Println("m: ", m)
```

`m header address: 0x40c128, m hash header address 0x43e260.`<br>
`m:  map[aaa:111 bbb:222 ccc:333]`<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/Yw9MbkgsaCR)

map创建的方式三： 用空映射来初始化m变量，效果等同于方式二  //adm
```go
m := map[string]int{}

m["aaa"] = 111
m["bbb"] = 222
m["ccc"] = 333

fmt.Printf("m header address: %06p, m hash header address %06p.\n", &m, m)
fmt.Println("m: ", m)
```
`m header address: 0x40c128, m hash header address 0x43e260.`<br>
`m:  map[aaa:111 bbb:222 ccc:333]`

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/aYYAHSajLCn)

map创建的方式四： 用字面量初始化m变量  //adm
```go
m := map[string]int{"aaa":111, "bbb":222, "ccc":333 }

fmt.Printf("m header address: %06p, m hash header address %06p.\n", &m, m)
fmt.Println("m: ", m)
```
`m header address: 0x40c128, m hash header address 0x43e260.`<br>
`m:  map[aaa:111 bbb:222 ccc:333]`

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/LFfogtZ5Zsa)

## 4.2 遍历映射 

用range遍历map  //adm
```go
m := map[string]int{"aaa": 111, "bbb": 222, "ccc": 333}

for k, v := range m {
	fmt.Println("key", k, "value", v)
}
```
`key ccc value 333`<br>
`key aaa value 111`<br>
`key bbb value 222`<br>

没错，map存储的键值对是无序的，所以每次从map中取出的键值对顺序与存入的顺序可能是不一样的。

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/KN82CWihtJN)

## 4.3 有序打印

有序打印map中的键值对  //adm

上面的例子我们看到map中键值对的输出是无序的，但有时候我们需要有序的输出map中的键值对，这里可以对map中的Key进行排序， 然后再取Value的值。

```go
kod := []string{}
m := map[string]int{"aaa": 111, "bbb": 222, "ccc": 333}

for k, _ := range m {
	kod = append(kod, k)
}

sort.Strings(kod)

for _, s := range kod {
	fmt.Println("Key", s, "Value", m[s])
}
```
`Key aaa value 111`<br>
`Key bbb value 222`<br>
`Key ccc value 333`<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/F0HrAVmsNET)

有效的判断map中是否含有你要找的Key  //adm
```go
var v int
var exist bool

m := map[string]int{"aaa": 1, "bbb": 2, "ccc": 3}

//bbb在map中，v为222，exist为true.
v, exist = m["bbb"]
fmt.Println("bbb value", v, exist)

//xyz不在map中，虽然v能打印出值0（实际上是int型变量的默认值），但exist为false，这种为不安全的取值。
v, exist = m["xyz"]
fmt.Println("xyz value", v, exist)

//所以需要先判断exist的布尔值，exist为true说明xyz存在于map中，这时候对应的Value是有效的，否则就是xyz不存在于map中。
if v, exist = m["xyz"]; exist {
	fmt.Println("xyz value", v, exist)

} else {
	fmt.Println("Key xyz doesn't exist in map")
}
```
`bbb value 2 true`<br>
`xyz value 0 false`<br>
`Key xyz doesn't exist in map`<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/HCrPO1n5mlc)

## 4.4 删除键值

删除map中的键值对  //adm
```go
m := map[string]int{"aaa": 111, "bbb": 222, "ccc": 333}
fmt.Println("m: ", m)

delete(m, "aaa")
fmt.Println("m: ", m)

delete(m, "opq")
fmt.Println("m: ", m)
```

`m:  map[aaa:111 bbb:222 ccc:333]`<br>
`m:  map[bbb:222 ccc:333]`<br>
`m:  map[bbb:222 ccc:333]`

delete函数用于删除map中的键值对，它没有返回值，即使要删除的Key不在map中，delete也可以正常执行。

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/_zbYO7zEAw5)

# 5.通道

通过共享内存通信和通过通信来共享内存是2种不同的编程方式。通过共享内存通信需要使用互斥锁之类的同步方式来防止数据竞争，
而通过通信来共享内存则可以通过通道channel的方式来实现，即通过channel(通道)来实现goroutine(并发)之间的共享内存。

## 5.1 什么是通道
通道(channel)的关键字为chan，可以把通道看作FIFO(first in first out)一样的数据队列。同slice，map一样，
chan是Go语言的内置类型，使用时不需要导入包，但是像其他的同步方式（比如WaitGroup）则需要导入sync或sync/atomic包。
chan有不同的类型，chan传输的数据类型要和chan的类型一致，假设T是chan的类型：<br><br>

> 
`chan T` &nbsp;&nbsp;表示一个双向通道，即可以往通道发送T类型的数据，也可以从通道读取T类型的数据。<br>
`chan<- T`表示一个只能往通道发送T类型数据的单向通道。从此通道里读取数据会引起编译错误❌<br>
`<-chan T`表示一个只能从通道读取T类型数据的单向通道。往此通道里发送数据会引起编译错误❌<br>

`chan T`类型的通道值可以隐式的转换为`chan<- T`或`<-chan T`类型的值。<br>
`chan<- T`和`<-chan T`类型的值不能转换为`chan T`类型的值;<br>
`chan<- T`和`<-chan T`类型的值也不能互相转换。<br>

```go
package main

import "fmt"

func main() {

	//声明一个数据类型为int的双向通道变量bidirCh
	var bidirCh chan int // or bidirCh := chan int(nil)

	//声明一个数据类型为int的send only的单向通道变量sendOnlyCh
	var sendOnlyCh chan<- int

	//声明一个数据类型为int的receive only的单向通道变量receiveOnlyCh
	var receiveOnlyCh <-chan int

	fmt.Printf("bidirCh is nil? %t\n", bidirCh == nil)
	fmt.Printf("bidirCh type is: %#v\n\n", bidirCh)

	fmt.Printf("sendOnlyCh is nil? %t\n", sendOnlyCh == nil)
	fmt.Printf("sendOnlyCh type is: %#v\n\n", sendOnlyCh)

	fmt.Printf("receiveOnlyCh is nil? %t\n", receiveOnlyCh == nil)
	fmt.Printf("receiveOnlyCh type is: %#v\n", receiveOnlyCh)

	//chan int 型的双向通道的变量可以隐式的转换为chan<- int或<-chan int型的单向通道的变量
	sendOnlyCh = bidirCh    //也可显式地转换:  sendOnlyCh := (chan<- int)(bidirCh)
	receiveOnlyCh = bidirCh //也可显式地转换:  receiveOnlyCh := (<-chan int)(bidirCh)
}
```

`bidirCh is nil? true`<br>
`bidirCh type is: (chan int)(nil)`<br>

`sendOnlyCh is nil? true`<br>
`sendOnlyCh type is: (chan<- int)(nil)`<br>

`receiveOnlyCh is nil? true`<br>
`receiveOnlyCh type is: (<-chan int)(nil)`<br>

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**]()

## 5.2 创建通道

5.1的例子中我们创建的3个通道bidirCh, sendOnlyCh和receiveOnlyCh的值均为nil，即系统为这3个声明的变量分配了地址，
但变量内部的成员值还均为0值。我们可以用make()函数为这些通道分配缓存大小。<br>

make(chan int, 10) //创建一个容量为10类型为int的通道<br>
make(chan int, 0)  //创建一个容量为0类型为int的通道<br>

make函数的第二个参数是可选的，默认是0，所以make(chan int, 0)也可以写成make(chan int)。容量为0的通道称为无缓冲的通道，否则称之为有缓冲的通道<br>

```go
package main

import "fmt"

func main() {

	//声明一个数据类型为int的双向通道变量bidirCh
	var bidirCh chan int // or bidirCh := chan int(nil)

	//声明一个数据类型为int的send only的单向通道变量sendOnlyCh
	var sendOnlyCh chan<- int

	//声明一个数据类型为int的receive only的单向通道变量receiveOnlyCh
	var receiveOnlyCh <-chan int

	//将bidircCh分配成一个无缓冲的双向通道
	bidirCh = make(chan int, 0) //或者bidirCh = make(chan int)

	//将sendOnlyCh分配成一个有20个整数缓冲的send only的通道
	sendOnlyCh = make(chan<- int, 20)

	//将receiveOnlyCh分配成一个有30个整数缓冲的receive only的通道
	receiveOnlyCh = make(<-chan int, 30)

	fmt.Printf("bidirCh is nil? %t\n", bidirCh == nil)
	fmt.Printf("sendOnlyCh is nil? %t\n", sendOnlyCh == nil)
	fmt.Printf("receiveOnlyCh is nil? %t\n\n", receiveOnlyCh == nil)

	fmt.Printf("bidirCh len %d cap:%d\n", len(bidirCh), cap(bidirCh))
	fmt.Printf("sendOnlyCh len %d cap:%d\n", len(sendOnlyCh), cap(sendOnlyCh))
	fmt.Printf("receiveOnlyCh len %d cap:%d\n\n", len(receiveOnlyCh), cap(receiveOnlyCh))

	//chan int 型的双向通道的变量可以隐式的转换为chan<- int或<-chan int型的单向通道的变量
	sendOnlyCh = bidirCh    //也可显式地转换:  sendOnlyCh := (chan<- int)(bidirCh)
	receiveOnlyCh = bidirCh //也可显式地转换:  receiveOnlyCh := (chan<- int)(bidirCh)

	//注意: 这时的sendOnlyCh和receiveOnlyCh均是由bidirCh转换而来，它们的容量cap已变成bidirCh的容量。
	fmt.Printf("sendOnlyCh len %d cap:%d\n", len(sendOnlyCh), cap(sendOnlyCh))
	fmt.Printf("receiveOnlyCh len %d cap:%d\n", len(receiveOnlyCh), cap(receiveOnlyCh))
}
```

`bidirCh is nil? false`<br>
`sendOnlyCh is nil? false`<br>
`receiveOnlyCh is nil? false`<br>

`bidirCh len 0 cap:0`<br>
`sendOnlyCh len 0 cap:20`<br>
`receiveOnlyCh len 0 cap:30`<br>

`sendOnlyCh len 0 cap:0`<br>
`receiveOnlyCh len 0 cap:0`<br>

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**]()

## 5.3 发送/接收数据

**对通道的标识符<-的说明:<br>**
1. 把<-放在chan的左边或右边表示通道类型是发送还是接收，比如：<br>
`var sch chan<- int`：<-指向通道，代表sch是一个send only通道类型的变量<br>
`var rch <-chan int`:&nbsp;  <-源自通道，代表rch是一个receive only通道类型的变量<br>
2. 把<-放在通道变量的左右表示对通道进行发送或接收，比如：<br>
`sch <- 5`: <-指向sch，表示将数组5发送到sch通道<br>
`i <- sch`: <-源自sch，表示从sch通道里接收一个数字到变量i<br>

下面的程序演示了从一个无缓冲的bidirCh通道发送并接收数据的过程，sender用来向bidirCh发送数据，
receiver用来从bidirCh接收数据。由于bidirCh是无缓冲的通道，sender每发完一个数据后都会block
在bidirCh端等待数据被receiver接收，receiver接收完数据后也会block在bidirCh端等待sender再次
发送数据，依次循环，直到sender和receiver把切片s消耗完为止。<br>
程序的第35行和38行分别用关键字go调用了sender和receiver的函数，这相当于在程序里起了一个“线程”，
这里我们叫goroutine，main函数也是一个goroutine，我们称之为main goroutine。

```go
package main

import (
	"fmt"
	"time"
)

func sender(ch chan<- int, s []int) {
	for _, v := range s {
		//fmt.Println("\nsend block")
		ch <- v
		fmt.Printf("send %d\n", v)
	}
}

func receiver(ch <-chan int) {
	for {
		//fmt.Println("\nrecv block")
		v := <-ch
		fmt.Printf("recv %d\n", v)
	}
}

func main() {

	//声明一个数据类型为int的双向通道变量bidirCh
	var bidirCh chan int // or bidirCh := chan int(nil)

	//为bidircCh分配一个无缓冲的双向通道
	bidirCh = make(chan int, 0) //或者bidirCh = make(chan int)

	s := []int{5, 4, 3, 2, 1, 0}

	//启动sender的goroutine
	go sender(bidirCh, s)

	//启动receiver的goroutine
	go receiver(bidirCh)

	//让main goroutine 延迟5秒退出，以便等待sender和receiver的goroutine完成。
	time.Sleep(time.Second * 5)
}
```
`recv 5`<br>
`send 5`<br>
`send 4`<br>
`recv 4`<br>
`recv 3`<br>
`send 3`<br>
`send 2`<br>
`recv 2`<br>
`recv 1`<br>
`send 1`<br>
`send 0`<br>
`recv 0`<br>

send和recv对数字的发送与接收都是成对出现的，说明无缓冲的通道bidirCh具有同步的效果，
在Go语言中无缓冲的通道也称作**同步通道**。<br>
send和recv在处理不同的数字时它们出现的先后顺序不同，比如数字1，3，5是send在recv前，
数字2，4是recv在send前，对于这种x事件和y事件发生顺序不固定的情况，我们就叫做并发。

在打印中可以看到一些奇怪的现象，对于数字1，3，5的操作均是send在recv前，但是2和4的recv在send前。
以recv2在send2之前为例，这并不是说sender还未发送数字2时receiver就已经接收到了数字2，而是因为在
sender发送完数字1之后`ch <- v`会block住sender的goroutine，继而sender的goroutine从运行状态进入休眠状态，
然后receiver的goroutine被唤醒并接收sender发送到通道的数字1，之后receiver的`v := <-ch`会block住receiver
的goroutine，这个操作发生在sender发送2之前，在sender发送数字2之前receiver就已经block在通道的另一端准备好了接收的操作，

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**]()

## 5.4 关闭通道 

假设通道变量为ch,关闭通道的操作为： close(ch), 使用close()函数需要注意以下几点：<br>

1. close一个值为nil的通道会导致panic。<br>
2. close一个已经close()的通道会导致panic异常。<br>
3. 在向一个已经close()的通道发送数据时会导致panic。<br>
4. close()不能关闭receive-only类型的通道。<br>
5. 从一个已经close()的通道接收数据时会产生无尽的0值。<br>

下例程序的注释标出了以上item1 ～ item4的4种错误操作，item5的无尽0值可以看下例程序的打印输出。<br>

```go
package main

import (
	"fmt"
	"time"
)

func sender(ch chan<- int, s []int) {

	for _, v := range s {
		//fmt.Println("\nsend block")
		ch <- v
		fmt.Printf("send %d\n", v)
	}
	close(ch)
	//close(ch) //item2 ❌ panic: close of closed channel
	//ch <- 99  //item3 ❌ panic: send on closed channel
}

func receiver(ch <-chan int) {
	for {
		//fmt.Println("\nrecv block")
		//close(ch) //item4 ❌ invalid operation: close(ch) (cannot close receive-only channel)
		v := <-ch
		fmt.Printf("recv %d\n", v)
	}
}

func main() {

	//声明一个通道数据类型为int的双向通道bidirCh
	var bidirCh chan int // or bidirCh := chan int(nil)

	//close(bidirCh) //item1 ❌ panic: close of nil channel
	//将bidircCh分配成一个无缓冲的双向通道
	bidirCh = make(chan int, 0) //或者bidirCh = make(chan int)

	s := []int{5, 4, 3, 2, 1, 0}

	//启动sender的goroutine
	go sender(bidirCh, s)

	//启动receiver的goroutine
	go receiver(bidirCh)

	//让main goroutine 延迟5秒退出，以便等待sender和receiver的goroutine完成。
	time.Sleep(time.Second * 5)
}
```

`recv 5`<br>
`send 5`<br>
`send 4`<br>
`recv 4`<br>
`recv 3`<br>
`send 3`<br>
`send 2`<br>
`recv 2`<br>
`recv 1`<br>
`send 1`<br>
`send 0`<br>
`recv 0`<br>
`recv 0`<br>
`recv 0`<br>
`recv 0`<br>
`recv 0`<br>
`recv 0`<br>
`... `<br>

上面的打印出现了很多`recv 0`的打印，我们无法判断究竟哪一个是sender发送的那些是通道close()之后读出来的，
下一节的例子会解决这个问题。

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**](https://play.golang.org/p/kkGOj6tG-sJ)

Go语言中的通道在使用完毕后不必非得使用close()函数来关闭通道，GC会自动检测并回收不再被引用的通道，
那close()函数的作用是什么呢: close()可以通知通道的接收者在接收到某一个数据后后续再接收的数据将不再有效。<br>
事实上从通道接收数据还有一个可选的bool变量，用于判断这个数据是否是从从通道里读取出来的，这样就能解决上面遇到的
不确定哪个`recv 0` 是sender发送的问题。

下面的例子里的接收者将判断从当前通道里读取数据如果不是true的话将终止接收通道的数据。

```go
package main

import (
	"fmt"
	"time"
)

func sender(ch chan<- int, s []int) {

	for _, v := range s {
		//fmt.Println("\nsend block")
		ch <- v
		fmt.Printf("send %d\n", v)
	}
	close(ch)
	//close(ch) //item2 ❌ panic: close of closed channel
	//ch <- 99  //item3 ❌ panic: send on closed channel
}

func receiver(ch <-chan int) {
	for {
		//fmt.Println("\nrecv block")
		//close(ch) //item4 ❌ invalid operation: close(ch) (cannot close receive-only channel)
		v, ok := <-ch
		if ok == true {
			fmt.Printf("recv %d %t\n", v, ok)
		} else {
			break
		}
	}
}

func main() {

	//声明一个通道数据类型为int的双向通道bidirCh
	var bidirCh chan int // or bidirCh := chan int(nil)

	//close(bidirCh) //item1 ❌ panic: close of nil channel
	//将bidircCh分配成一个无缓冲的双向通道
	bidirCh = make(chan int, 0) //或者bidirCh = make(chan int)

	s := []int{5, 4, 3, 2, 1, 0}

	//启动sender的goroutine
	go sender(bidirCh, s)

	//启动receiver的goroutine
	go receiver(bidirCh)

	//让main goroutine 延迟5秒退出，以便等待sender和receiver的goroutine完成。
	time.Sleep(time.Second * 5)
}
```

`recv 5 true`<br>
`send 5`<br>
`send 4`<br>
`recv 4 true`<br>
`recv 3 true`<br>
`send 3`<br>
`send 2`<br>
`recv 2 true`<br>
`recv 1 true`<br>
`send 1`<br>
`send 0`<br>
`recv 0 true`<br>

使用close()函数并配合接收者对接收数据的true/false的判断，可以有效控制通过通道要发送或接收的数据。

对于在本节刚开始提到的以下3种对通道的操作会导致panic的情况，都可以先从通道读取出一个数据并判断其
是否为true的方式再执行以下3种close通道的方式，以避免产生panic:<br>
1. close一个值为nil的通道会导致panic。<br>
2. close一个已经close()的通道会导致panic异常。<br>
3. 在向一个已经close()的通道发送数据时会导致panic。<br>

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**](https://play.golang.org/p/kkGOj6tG-sJ)

## 5.3 通道相关的操作

通道的操作通常涉及：1. 创建，2. 写入数据，3. 获得通道长度和容量，4. 读取数据，5. 关闭通道。<br>

```go
package main

import "fmt"

func main() {

	s := make([]int, 10)

	//1. 创建： 创建一个缓冲为5的通道ch
	ch := make(chan int, 5)

	fmt.Printf("len(ch):%2d  cap(ch):%2d\n", len(ch), cap(ch))//len(ch): 0  cap(ch): 5

	//2. 写入： 往通道ch里发送数据
	ch <- 1
	ch <- 2
	ch <- 3
	ch <- 4
	ch <- 5

	//3.
	// 长度
	// 容量
	fmt.Printf("len(ch):%2d  cap(ch):%2d\n", len(ch), cap(ch))//len(ch): 0  cap(ch): 5

	//❌ ch <- 6 //通道已满(len(ch) == len(cap))，再像通道写入数据会产生错误：fatal error: all goroutines are asleep - deadlock!

	//4. 读取
	s[0] = <-ch
	s[1] = <-ch
	s[2] = <-ch
	s[3] = <-ch
	s[4] = <-ch

	fmt.Printf("len(ch):%2d  cap(ch):%2d\n", len(ch), cap(ch))//len(ch): 0  cap(ch): 5

	//❌ s[5] = <-ch //通道已空(len(ch) == 0)，再从通道读取数据会产生错误：fatal error: all goroutines are asleep - deadlock!

	fmt.Println(s)

	//5. 关闭
	close(ch)
}
```

`len(ch): 0  cap(ch): 5`<br>
`len(ch): 5  cap(ch): 5`<br>
`len(ch): 0  cap(ch): 5`<br>
`[1 2 3 4 5 0 0 0 0 0]`<br>

[**🍏〰️🍎〰️🍐〰️🍊〰️🍋〰️🍌〰️🍉 P L A Y A R O U N D 〰️🍇〰️🍓〰️🍈〰️🍒〰️🍑〰️🥭 〰️🥝**](https://play.golang.org/p/HCrPO1n5mlc)


## 5.4 通道会崩溃的几种情况

# 6. 函数
go语言中函数的声明格式如下:<br>
包含一个函数名name，一个参数列表parameter-list和一个返回结果列表result-list和一个函数体body。

> func name(parameter-list) (result-list) {<br>
	&nbsp;&nbsp;&nbsp;&nbsp;body<br>
}

## 6.1 函数签名

如果2个函数的参数类型相同，返回值类型也相同，则这2个函数具有相同的类型，函数的类型就是函数的签名。  //adm

如下声明了4个函数，它们均有2个int型参数和一个int型返回值，这4个函数的类型相同，签名也相同。

```go

func sig1(x int, y int) int {
	return x + y
}

func sig2(x, y int) int {
	return x + y
}

func sig3(x int, _ int) int {
	return x
}

func sig4(int, int) int {
	return 0
}

func main() {
	fmt.Printf("sig1: %T\n", sig1)
	fmt.Printf("sig2: %T\n", sig2)
	fmt.Printf("sig3: %T\n", sig3)
	fmt.Printf("sig4: %T\n", sig4)
}
```

`sig1: func(int, int) int`<br>
`sig2: func(int, int) int`<br>
`sig3: func(int, int) int`<br>
`sig4: func(int, int) int`<br>

函数声明的参数叫parameter（形参），调用者给函数传递的参数叫argument（实参）。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

如果在go语言中遇到了一个没有函数体的函数声明，只有函数的签名，说明这个函数不是由go语言实现的。  //adm

比如：<br>
```go
package math

func Sin(x float64) float64 // implemented in assembly language.
```

## 6.2 递归调用

递归函数可以自己调用自己，但要设置返回条件，避免递归无限调用。  //adm

```go
func recursion(slice []int) []int {
	slice = append(slice, len(slice)+1)

	fmt.Println(slice)
	if len(slice) < 20 {
		recursion(slice)
	}

	return slice
}

func main() {
	mainslice := recursion(nil)
	fmt.Println("mainslice:", mainslice, "len:", len(mainslice), "cap:", cap(mainslice))
}
```

`[1]`<br>
`[1 2]`<br>
`[1 2 3]`<br>
`[1 2 3 4]`<br>
`[1 2 3 4 5]`<br>
`[1 2 3 4 5 6]`<br>
`[1 2 3 4 5 6 7]`<br>
`[1 2 3 4 5 6 7 8]`<br>
`[1 2 3 4 5 6 7 8 9]`<br>
`[1 2 3 4 5 6 7 8 9 10]`<br>
`[1 2 3 4 5 6 7 8 9 10 11]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19]`<br>
`[1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20]`<br>
`mainslice: [1] len: 1 cap: 1`<br>

以上例子中main函数给recursion函数传递了一个nil slice，之后recursion函数给slice append一个数值（当前slice的长度加1），然后打印出slice的结果，最后在slice length小于20的情况下进行回调。<br>
可以看到recursion函数最后返回给main函数的切片mainslice的元素是1，长度len和容量cap也是1，这是第一次对recursion函数调用return回的结果。<br>
recursion函数对自己再次调用时，每个recursion函数中的slice参数都相当于调用者slice参数的副本，副本相当于这个函数体内的本地变量，函数中对本地变量的更改并不影响调用者里的slice的大小和内容，如果将slice的地址传递给被调函数，则被调函数对slice的更改会影响调用者里slice的大小和内容。<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

## 6.3 函数类型

函数的签名也是一种类型，比如下面main函数中的函数变量f，f的函数类型和add、sub的签名一致，所以add和sub函数均可赋给函数变量f。  //adm
需要注意的是函数和slice、map一样，均属于不可比较的类型，它们的变量只能与nil比较，这些不可比较的类型变量不能用作map的key。<br>

```go
func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

func main() {
	var f func(int, int) int

	fmt.Println(f == nil)
	//fmt.Println(f == f) //invalid operation: f == f (func can only be compared to nil)

	f = add
	fmt.Println("5 + 3: ", f(5, 3))
	f = sub
	fmt.Println("5 - 3: ", f(5, 3))
}
```

`true`<br>
`5 + 3:  8`<br>
`5 - 3:  2`<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

<a id="来自gopl 5.5的例子"/>
来自gopl 5.5的例子  //adm

strings.Map对字符串中的每个字符调用add1 函数，并将每个add1函数的返回值组成一个新的字符串返回给调用者。

```go
func add1(r rune) rune { return r + 1 }
func main() {
	fmt.Println(strings.Map(add1, "HAL-9000")) // "IBM.:111"
	fmt.Println(strings.Map(add1, "VMS"))      // "WNT"
	fmt.Println(strings.Map(add1, "Admix"))    // "Benjy"
}
```

`IBM.:111`<br>
`WNT`<br>
`Benjy`<br>

string.Map的签名如下：<br>
func Map(mapping func(rune) rune, s string) string

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

改自gopl 5.5的例子  //adm

fmt.Printf中的%*s的*表示要打印的空格的个数，空格的数量为i*2个，然后再按%s打印"-"
```go
func main() {

	for i := 0; i < 10; i++ {
		fmt.Printf("%*s\n", i*2, "-")
	}

	for i := 10; i > 0; i-- {
		fmt.Printf("%*s\n", i*2, "-")
	}
}
```

```go
-
 -
   -
     -
       -
         -
           -
             -
               -
                 -
                   -
                 -
               -
             -
           -
         -
       -
     -
   -
 -

```

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

## 6.4 匿名函数

匿名函数，也叫函数字面量，是一个没有函数名的函数  //adm

可以将<a href="#来自gopl 5.5的例子">`来自gopl 5.5的例子`</a>中的add1改为成匿名函数并传给strings.Map<br>

```go
func main() {
	fmt.Println(strings.Map(func(x rune) rune { return x + 1 }, "HAL-9000"))
	fmt.Println(strings.Map(func(x rune) rune { return x + 1 }, "VMS"))
	fmt.Println(strings.Map(func(x rune) rune { return x + 1 }, "Admix"))
}
```

`IBM.:111`<br>
`WNT`<br>
`Benjy`<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

如果匿名函数在另一个函数内，那么匿名函数拥有另一个函数的完整词法环境，匿名函数可以访问另一个函数里的变量  //adm

```go
func add() func() int {
	i := 1
	return func() int {
		i = i + 1
		return i
	}
}

func main() {
	s := add // s是一个指向add函数的指针
	fmt.Println("add addr:", s)
	var f func() int // f是一个func() int函数类型的变量

	/* square()的返回值是函数体内的匿名函数，将匿名函数
		func() int {
			i = i + 1
			return i
		}
	赋给f
	*/
	f = s() //将add()函数的执行结果返回给f，f现在是匿名函数，它能继续引用add函数体内的变量i

	//如下面对f函数的调用f(), 每调用一次i都加1。
	//对于add函数内的变量i来说，这种匿名函数对i的调用一定程度上类似于C语言内的static int i = 1。
	fmt.Println("  f addr:", f, "f():", f()) //匿名函数调用结果 2
	fmt.Println("  f addr:", f, "f():", f()) //匿名函数调用结果 3
	fmt.Println("  f addr:", f, "f():", f()) //匿名函数调用结果 4
	fmt.Println("  f addr:", f, "f():", f()) //匿名函数调用结果 5
}
```

`add addr: 0x10914e0`<br>
`  f addr: 0x1091990 f(): 2`<br>
`  f addr: 0x1091990 f(): 3`<br>
`  f addr: 0x1091990 f(): 4`<br>
`  f addr: 0x1091990 f(): 5`<br>

通过这个例子，我们看到变量的生命周期不由它的作用域决定:add返回后，变量i仍然隐式的存在于f中。
f中存在变量i的引用。这就是函数值属于引用类型和函数值不可比较的原因。
Go使用闭包(closures)技术实现函数值，Go程序员也把函数值叫做闭包。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

匿名函数与for循环连用的例子  //adm

for循环行`for _, s := range slice`的s是一个循环体内可访问的局部变量，匿名函数在for循环内，所以匿名函数可以引用变量s，<br>
考虑下面代码打印都是10的原因<br>

```go
func main() {

	var fs []func()
	slice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

	for _, s := range slice {
		fs = append(fs, func() { fmt.Println(&s, s) })
	}

	for _, f := range fs {
		f()
	}
}
```

`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>
`0xc00006c008 10`<br>

由于s的地址始终都是0xc00006c008，所以最后在调用fs切片的每个函数元素f()时，f()引用的都是同一个s，s存储的是slice切片最后的一个值10。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

通过让匿名函数引用不同的变量s即可解决这个问题，在for循环里加一行 s := s即可在每次for循环时都为为匿名函数新增一个s变量的副本，这个副本在每次for循环时都会具有新的地址，并不影响`for _, s := range slice`这一行s变量的地址。<br>

```go
func main() {

	var fs []func()
	slice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

	for _, s := range slice {
		s := s
		fs = append(fs, func() { fmt.Println(&s, s) })
	}

	for _, f := range fs {
		f()
	}
}
```

`0xc000016080 1`<br>
`0xc000016088 2`<br>
`0xc000016090 3`<br>
`0xc000016098 4`<br>
`0xc0000160a0 5`<br>
`0xc0000160a8 6`<br>
`0xc0000160b0 7`<br>
`0xc0000160b8 8`<br>
`0xc0000160c0 9`<br>
`0xc0000160c8 10`<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

## 6.5 变参函数

在函数参数列表的最后一个类型前加上...，表示这个函数可以接收任意个数量的该类型的参数。  //adm

```go
func add1(i ...int) {
	var sum int
	for _, s := range i {
		sum += s
	}
	fmt.Printf("add1: i type: %T, sum: %d\n", i, sum)
}

func add2(i []int) {
	var sum int
	for _, s := range i {
		sum += s
	}
	fmt.Printf("add2: i type: %T, sum: %d\n", i, sum)
}

func main() {
	fmt.Printf("add1 type:%T\n", add1)
	fmt.Printf("add2 type:%T\n", add2)
	fmt.Println()

	s := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	add1(1, 2, 3, 4, 5, 6, 7, 8, 9) //1. main函数隐式的把add的参数放到一个切片里，然后再传给add函数。
	add1(s...)                      //2. main函数向add1变参函数真的传递一个切片的时候，需要在切片变量后面加入...，以告知add1这回真是个切片。
	add2(s)                         //3. main函数向add2(参数为切片)传参的时候，直接写s即可。
}
```

`add1 type:func(...int)`<br>
`add2 type:func([]int)`<br>

`add1: i type: []int, sum: 45`<br>
`add1: i type: []int, sum: 45`<br>
`add2: i type: []int, sum: 45`<br>

add1函数的参数为可变参数，add2函数的参数是一个切片，前2行打印的是函数的类型，可以看到`func(...int)`和`func([]int)`是2个不同的类型。<br><br>
接下来的3行打印均相同，均为`add1: i type: []int, sum: 45`:<br>
第1行和第2行的打印是main函数向add1分别传入了一些参数(1到9)和一个切片(内容为1到9)，但在add1中打印参数i的类型时，2次均是[int]，这可以证实我们代码注释里说的"1. main函数隐式的把add的参数放到一个切片里，然后再传给add函数。"<br>
第3行参数是main函数直接调用了一个参数为切片的add2，并向add2传递了一个切片s，这行的打印和上2行一致，作为对比用。<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

一个神奇的类型: interface{}  //adm

```go
func anytype(i ...interface{}) {
	fmt.Println(i)
}

func main() {
	anytype(1, 2, 3, 4)
	anytype("abc", "def", "ghi", "jkl")
	anytype(1, "def", true, 3.14)
}
```

`[1 2 3 4]`<br>
`[abc def ghi jkl]`<br>
`[1 def true 3.14]`<br>

interface{}表示能接收任意类型的参数，如果用它做可变参数的类型，那岂不是... 😄 🤩 🤪 

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

## 6.6 延迟调用

当你在编写一个读写文件的程序，为了保证文件正确读取，可能要判断多次是否已读取到想要的大小或是否已到达EOF，
然后在不同的判断位置加入close文件的操作并返回，go中有个defer关键字，defer 后面跟一个函数名，用于在当前函数执行完时，
再执行defer后面的函数，这样我们只需写一个defer close()就可以保证文件一定会关闭，不用在多个地方加入close()的操作。<br>

不论包含defer语句的函数是return返回还是panic异常结束，defer后面的函数都会被最后执行, 当然你要保证在return或者panic
之前系统已执行完你的defer语句，通常在申请完资源后就加上defer释放资源的函数，如果你在return后面加defer释放资源的函数，
那这个defer语句在return之后就执行不到了，defer后面的函数也自然执行不到。<br>
如果你在函数中加入了多条defer语句，那么这些defer的函数会按相反的声明顺序执行。<br>

defer适用于打开、关闭文件；连接、断开连接；加锁、解锁；<br>

摘自gopl 5.8的例子  //adm

```go 
func ReadFile(filename string) ([]byte, error) {
    f, err := os.Open(filename)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    return ReadAll(f)
}
```

来自gopl 5.8的例子  //adm

调试复杂程序时，defer机制也常被用于记录何时进入和退出函数。下例中的 bigSlowOperation函数，
直接调用trace记录函数的被调情况。bigSlowOperation被调时， trace会返回一个函数值，
该函数值会在bigSlowOperation退出时被调用。
通过这种方式， 我们可以只通过一条语句控制函数的入口和所有的出口，甚至可以记录函数的运行时间，
如例子中的start。<br><br>
需要注意一点:不要忘记defer语句后的圆括号:<br>
defer trace("bigSlowOperation")表示在bigSlowOperation函数结束时调用trace函数然后返回trace里的匿名函数，匿名函数不会被执行；<br>
defer trace("bigSlowOperation")()表示要执行trace函数里的匿名函数，会先执行trace函数里匿名函数之前的部分，待bigSlowOperation函数结束时再执行trace函数里的匿名函数；<br>

```go
func bigSlowOperation() {
	defer trace("bigSlowOperation")() // don't forget the extra parentheses
	// ...lots of work...
	time.Sleep(10 * time.Second) // simulate slow operation by sleeping
}
func trace(msg string) func() {
	start := time.Now()
	log.Printf("enter %s", msg)
	return func() {
		log.Printf("exit %s (%s)", msg, time.Since(start))
	}
}

func main() {
	bigSlowOperation()
}
```
`2019/04/03 14:57:39 enter bigSlowOperation`<br>
`2019/04/03 14:57:49 exit bigSlowOperation (10.004776382s)`<br>

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

defer用于return的返回值  //adm

```go
func add(i ...int) int {
	var sum int

	defer func() { fmt.Println("sum: ", sum) }()

	for _, s := range i {
		sum += s
	}

	return sum
}

func main() {

	add(1, 2, 3, 4, 5)
	add(5, 5, 5, 5, 5)
}
```

`sum:  15`
`sum:  25`

add 函数中`defer func() { fmt.Println("sum: ", sum) }()`的意义是:<br>
在return返回之后，defer延迟调用的匿名函数需要访问外部函数的sum变量，<br>
`func() { fmt.Println("sum: ", sum) }`是匿名函数的声明，匿名函数最后加的`()`括号是对匿名函数的调用。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

注意defer用在for循环中的情况  //adm

由于defer后面的语句在函数执行完后才会被调用，像下面的代码如果filenames过多的话，
在// ...process f...之后并没有执行f.Close()，而是在整个for循环结束之后才调用defer的函数执行f.Close()，
这样会导致系统不断分配文件描述符，直到文件描述符耗尽。<br>

```go
func main() {

	for _, filename := range filenames {
		f, err := os.Open(filename)
		if err != nil {
			return err
		}
		defer f.Close() // NOTE: risky; could run out of file descriptors
		// ...process f...
	}
}
```

一个改进的方法是将for循环体里的内容放到一个函数当中，让for循环调用这个函数，这个函数结束时自然就会调用defer
的延迟函数f.Close()进行文件关闭。<br>

```go
func processfile(file string) error {
	f, err := os.Open(filename)
	if err != nil {
		return err
	}
	defer f.Close() // NOTE: risky; could run out of file descriptors
	// ...process f...

}

func main() {

	for _, filename := range filenames {
		if err := processfile(filename); err != nil {
			return err
		}
	}
}
```

## 6.7 错误处理

error是接口类型，error为nil意味着函数运行成功，non-nil表示失败。
对于non-nil的error类型,我们可以通过调用error的Error函数或者输出函数获得字符串类型的错误信息。

错误处理方式一: 调用函数打印被调函数返回的error类型的变量err  //adm

```go
fmt.Println(err)
//or
fmt.Printf("%v", err)
```

错误处理方式二: 被调函数返回error类型的字符串  //adm

```go
return errors.New("Failed to get data")
//or
return fmt.Errorf("Failed to get data, status: %s", status)
```

错误处理方式三: 函数直接打印错误信息，遇到不可恢复错误时使用os.Exit(1)退出；或使用log.Fatalf达到相同效果  //adm

```go
fmt.Fprintf(os.Stderr, "Site is down: %v\n", err)
os.Exit(1)
//or
 log.Fatalf("Site is down: %v\n", err)
```
log包中的函数在打印时都会自带时间前缀信息，也可使用如下方式用自己的标志字符串替换时间信息：<br>
```go
log.SetPrefix("wait: ")
log.SetFlags(0)
```

错误处理方式三: 被调函数直接打印错误信息，遇到不可恢复错误时使用os.Exit(1)退出；或使用log.Fatalf达到相同效果  //adm

```go
fmt.Fprintf(os.Stderr, "Site is down: %v\n", err)
os.Exit(1)
//or
 log.Fatalf("Site is down: %v\n", err)
```

## 6.8 崩溃

Go程序在运行可能会遇到数组访问越界、空指针引用等，这些运行错误会引起panic异常。
panic异常发生时，当前goroutine会中断执行，然后调用当前goroutine中的defer函数，最后再释放当前goroutine的堆栈。

<a id="除数为0引起的panic"/>
除数为0引起的panic  //adm

```go
func main() {

	f(3)

	fmt.Println("main is end")
}

func f(x int) {
	fmt.Printf("f(%d)\n", x+0/x) // panics if x == 0
	f(x - 1)
}
```

`f(3)`<br>
`f(2)`<br>
`f(1)`<br>
`panic: runtime error: integer divide by zero`<br>

`goroutine 1 [running]:`<br>
`main.f(0x0)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67.go:15 +0xcd`<br>
`main.f(0x1)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67.go:16 +0xbe`<br>
`main.f(0x2)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67.go:16 +0xbe`<br>
`main.f(0x3)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67.go:16 +0xbe`<br>
`main.main()`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67.go:9 +0x2a`<br>
`exit status 2`<br>

上述main函数调用了函数f(3)，然后f(3)每次将参数3减1，传给自己进行回调，当参数x为0时，0/x将会引起panic异常，系统输出堆栈的回溯信息。
"main is end"没有打印出来,因为在f(0)时已经发生panic，panic异常中断了当前goroutine的继续运行。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

除数为0引起的panic，并在此之前调用defer函数  //adm

```go
func main() {
	defer printStack()
	f(3)
}

func printStack() {
	var buf [4096]byte
	n := runtime.Stack(buf[:], false)
	os.Stdout.Write(buf[:n])
}

func f(x int) {
	fmt.Printf("f(%d)\n", x+0/x) // panics if x == 0
	f(x - 1)
}
```

`f(3)`<br>
`f(2)`<br>
`f(1)`<br>
`goroutine 1 [running]:`<br>
`main.printStack()`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:16 +0x5b`<br>
`panic(0x10ab4a0, 0x115f530)`<br>
`	/usr/local/go/src/runtime/panic.go:513 +0x1b9`<br>
`main.f(0x0)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:21 +0xcd`<br>
`main.f(0x1)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.f(0x2)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.f(0x3)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.main()`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:11 +0x46`<br>
`panic: runtime error: integer divide by zero`<br>

`goroutine 1 [running]:`<br>
`main.f(0x0)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:21 +0xcd`<br>
`main.f(0x1)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.f(0x2)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.f(0x3)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:22 +0xbe`<br>
`main.main()`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-1.go:11 +0x46`<br>
`exit status 2`<br>

printStack函数用于打印运行时goroutine的堆栈信息，从打印顺序可以看出，
defer函数`main.printStack()`<br>在`panic: runtime error: integer divide by zero`前先输出。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

直接调用panic函数也会引发异常，但须慎用  //adm

```go
func main() {
	f(3)
}

func f(x int) {
	if x == 1 {
		panic(fmt.Sprintf("stop recursion when x is %d", x))
	}

	f(x - 1)
}
```
`panic: stop recursion when x is 1`<br>

`goroutine 1 [running]:`<br>
`main.f(0x1)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-2.go:13 +0xee`<br>
`main.f(0x2)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-2.go:16 +0x38`<br>
`main.f(0x3)`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-2.go:16 +0x38`<br>
`main.main()`<br>
`	/Users/zhuwenjun/work/golang/post_routine/67-2.go:8 +0x2a`<br>
`exit status 2`<br>

有些panic异常也可以使用error机制代替。

[**🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 P L A Y A R O U N D 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗 🌽 🥬  🥗**](-)

## 6.9 恢复

Go语言中panic和recover的函数原型如下。recover函数用于从panic异常中返回，recover函数的返回值就是panic函数的参数。

> func panic(v interface{})<br>
func recover() interface{}

```go
func main() {

	f(3)

	fmt.Println("main is end")
}

func f(x int) {
	defer func() {
		if p := recover(); p != nil {
			fmt.Printf("internal error(x = %d). %v\n", x, p)
		}
	}()

	fmt.Printf("f(%d)\n", x+0/x) // panics if x == 0
	f(x - 1)
}
```

`f(3)`<br>
`f(2)`<br>
`f(1)`<br>
`internal error(x = 0). runtime error: integer divide by zero`<br>
`main is end`<br>

recover函数用于从panic异常中恢复，当f(0)发生异常时，系统先是调用f(0)的defer函数使用recover函数查找错误原因，然后从panic中恢复，可以看到f(0)异常发生时并未中断当前goroutine的继续运行，
`main is end`仍能继续打印。可以与6.8节中的<a href="#除数为0引起的panic">`除数为0引起的panic`</a>进行对比。

# 7. 方法
T是定义的类型(也叫命名类型)，在函数声明的函数名前面加一个类型T，那么这个函数就叫做这个类型的方法，这个类型的变量都能调用（接收）这个方法，T不能为指针或者interface类型:<br>

> func (t T) name(parameter-list) (result-list) {<br>
	&nbsp;&nbsp;&nbsp;&nbsp;body<br>
}

## 7.1 方法声明

方法声明的举例如下  //adm

```go
package geometry

import "math"

type Point struct{ X, Y float64 }

// traditional function
func Distance(p, q Point) float64 {
	return math.Hypot(q.X-p.X, q.Y-p.Y)
}

// same thing, but as a method of the Point type
func (p Point) Distance(q Point) float64 {
	return math.Hypot(q.X-p.X, q.Y-p.Y)
}
```

```go
package main

import "fmt"
import "geometry"

func main() {
	p1 := geometry.Point{3, 5}
	p2 := geometry.Point{3, 100}

	fmt.Println(p1.Distance(p2))           //方法调用
	fmt.Println(geometry.Distance(p1, p2)) //函数调用
}
```

`95`<br>
`95`<br>

`fmt.Println(p1.Distance(p2))`是方法调用，`fmt.Println(geometry.Distance(p1, p2))` 是函数调用。

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](-)

一次求多个点之间的线段之和  //adm

```go
package geometry

import "math"

type Point struct{ X, Y float64 }

// traditional function
func Distance(p, q Point) float64 {
	return math.Hypot(q.X-p.X, q.Y-p.Y)
}

// same thing, but as a method of the Point type
func (p Point) Distance(q Point) float64 {
	return math.Hypot(q.X-p.X, q.Y-p.Y)
}

// A Path is a journey connecting the points with straight lines.
type Path []Point

// Distance returns the distance traveled along the path.
func (path Path) Distance() float64 {
	sum := 0.0
	for i := range path {
		if i > 0 {
			sum += path[i-1].Distance(path[i])
		}
	}
	return sum
}
```
```go
package main

import (
	"fmt"
	"geometry"
)

func main() {
	p := geometry.Path{{2, 10}, {3, 20}, {4, 30}, {5, 40}}
	fmt.Println(p.Distance())
}
```

`30.14962686336267`<br>

虽然Path类型的Distance方法会调用Point类型的Distance方法，但个Distance函数之间没有任何关联。<br>
每个类型的方法之间需要有不同的名字，但不同的类型之间的方法名可以相同，就像Path和Point类型的Distance函数名一样，不同类型的方法可以同名。<br>

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](-)

## 7.2 方法接收者为指针

方法的接收者只有2种类型，T和*T，T或*T类型的变量均可调用接收者为T和*T的方法  //adm

```go
package main

import (
	"fmt"
)

type rbt struct {
	distance, step int
}

func (r rbt) m(num int) {
	r.distance = num * r.step
}

func (r *rbt) move(num int) {
	r.distance = num * r.step
}

func main() {
	r1 := rbt{0, 5} // r1是rbt类型的普通变量（这里rbt类型的普通变量用于区分*rbt类型的指针变量，如后面的指针变量r2这里叫做指针变量）

	fmt.Printf("\nr1.m type: %T r1.move type: %T\n", r1.m, r1.move) //r1.m type: func(int) r1.move type: func(int)
	fmt.Printf("r1 type: %T    &r1 type: %T\n", r1, &r1)            //r1 type: main.rbt    &r1 type: *main.rbt

	r1.m(7)                  //r1是rbt类型的普通变量，方法m的接收者是rbt类型的普通变量，则r1可以直接调用方法m
	fmt.Println(r1.distance) //0 普通变量r1在调用方法m时，会产生一个r1的副本，这和C语言函数的形参是普通变量，函数调用者给函数传实参后，
	// 函数得到的是实参的副本，函数对实参副本的更改并不改变函数调用者的实参值，因此这里普通变量r1的m函数操作的是r1的副本，
	// 并不影响真正的实参r1的成员distance的值，所以打印r1.distance时值为0

	(&r1).move(7)            //r1是rbt类型的普通变量，方法move的接收者是*rbt类型的指针变量，取地址符号&可以将r1变成*rbt类型的指针变量：&r1，因此(&r1)可以调用接收者是*rbt类型的方法move
	fmt.Println(r1.distance) //35 方法的接收者是指针变量，同C语言将实参传递给指针类型的形参一样，调用者和被调函数共享同一个指针变量，所以被调函数改变形参后，实参也跟着改变，r1.distance为35

	r1.move(7)               //r1是rbt类型的普通变量，方法move的接收者是*rbt类型的指针变量，r1也可以直接调用方法move，编译器隐式的使用&r1来调用move
	fmt.Println(r1.distance) //35 方法接收者是指针变量，方法里对变量的更改会影响指针变量的值，方法里将r1.distance改成了35，再打印r1.distance时也是35

	////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

	r2 := &rbt{0, 5} // r2是*rbt类型的指针变量（这里*rbt类型的指针变量用于区分rbt类型的普通变量，如上面的普通变量r1）

	fmt.Printf("\nr2.m type: %T  r2.move type: %T\n", r2.m, r2.move) //r2.m type: func(int)  r2.move type: func(int)
	fmt.Printf("r2 type: %T    *r2 type: %T\n", r2, *r2)             //r2 type: *main.rbt    *r2 type: main.rbt

	r2.m(7)                  //r2是rbt类型的指针变量，方法m的接收者是rbt类型的普通变量，r2也可以直接调用方法m，编译器隐式的使用*r2来调用m
	fmt.Println(r2.distance) //0 方法接收者是普通变量，方法里更改的是实参的副本，函数对副本的更改并不影响实参的值，r2.distance值仍为0

	(*r2).m(7)               //r2是rbt类型的指针变量，方法m的接收者是rbt类型的普通变量，取数据符号*可以将r2变成rbt类型的普通变量:*r2，因此用(*r2)可以调用接收者是rbt类型的方法m
	fmt.Println(r2.distance) //0 方法接收者是普通变量，方法里更改的是实参的副本，函数对副本的更改并不影响实参的值，r2.distance值仍为0

	r2.move(7)               //r2是rbt类型的指针变量，方法move的接收者是*rbt类型的指针变量，则r2可以直接调用方法move
	fmt.Println(r2.distance) //35 方法接收者是指针变量，方法里对变量的更改会影响指针变量的值，方法里将r1.distance改成了35，再打印r1.distance时也是35
}
```

`r1.m type: func(int) r1.move type: func(int)`<br>
`r1 type: main.rbt    &r1 type: *main.rbt`<br>
`0`<br>
`35`<br>
`35`<br>

`r2.m type: func(int)  r2.move type: func(int)`<br>
`r2 type: *main.rbt    *r2 type: main.rbt`<br>
`0`<br>
`0`<br>
`35`<br>

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](-)


函数或接口的参数为slice或map的普通变量（非指针变量）时，函数或接口仍能正常修改slice或map中的值  //adm

当我们向被调函数传递一个slice或map类型的指针变量或接收者也为指针时时，在被调函数中对slice或map值的修改能直接改变调用函数中的slice或map值，因为被调函数和调用函数共享同一个slice或map类型指针变量的首地址;<br>
当我们向被调函数传递一个slice或map类型的普通变量或接收者也为普通变量时，在被调函数中对slice或map值的修改也能改变调用函数中的slice或map值，虽然被调函数得到的是调用函数中slice或map变量的副本，<br>
但这个副本变量里对slice的数组或map的哈希首地址是引用的关系，也就是说副本的地址和调用函数中的变量地址不同，但是副本引用的数组或哈希地址是和调用函数中的相同的。

```go
package main

import "fmt"

type S []int
type M map[int]string

func func_slice(s S) {
	s[3] = 555

	fmt.Println("\nfunc_slice")
	fmt.Printf("&s: %p s: %p %#v\n", &s, s, s)
	s = nil //TODO: 不确定这里给s赋nil值之后，s切片指向底层数组的指针已为nil，但func_slice返回后，仍能打印出s的内容。难道GC机制会判断后面还会用s所以这里s=nil的赋值没生效？
	fmt.Printf("&s: %p s: %p %#v\n", &s, s, s)
}

func func_slice_ptr(s *S) {
	(*s)[3] = 666

	fmt.Println("\nfunc_slice_ptr")
	fmt.Printf("s: %p *s: %p %#v\n", s, *s, s)
	s = nil //TODO: 不确定这里给s赋nil值之后，s切片指向底层数组的指针已为nil，但func_slice_ptr返回后，仍能打印出s的内容。难道GC机制会判断后面还会用s所以这里s=nil的赋值没生效？
	fmt.Printf("s: %p %#v\n", s, s)
}

//[]int是“非定义的类型”，如果method_slice的方法接收者由(s S)改为(s []int)后会导致
//"invalid receiver type []int ([]int is not a defined type)"的错误.
//type S []int用type定义的S是[]int的定义类型。
func (s S) method_slice() {
	s[3] = 888

	fmt.Println("\nmethod_slice")
	fmt.Printf("&s: %p s: %p %#v\n", &s, s, s)
	s = nil //TODO: 不确定这里给s赋nil值之后，s切片指向底层数组的指针已为nil，但method_slice返回后，仍能打印出s的内容。难道GC机制会判断后面还会用s所以这里s=nil的赋值没生效？
	fmt.Printf("&s: %p s: %p %#v\n", &s, s, s)
}

func (s *S) method_slice_ptr() {
	(*s)[3] = 999

	fmt.Println("\nmethod_slice_ptr")
	fmt.Printf("s: %p *s: %p %#v\n", s, *s, s)
	s = nil //TODO: 不确定这里给s赋nil值之后，s切片指向底层数组的指针已为nil，但method_slice_ptr返回后，仍能打印出s的内容。难道GC机制会判断后面还会用s所以这里s=nil的赋值没生效？
	fmt.Printf("s: %p %#v\n", s, s)
}

//##############################################################################################################################

func func_map(m M) {
	m[3] = "ooo"

	fmt.Println("\nfunc_map")
	fmt.Printf("&m: %p m: %p %#v\n", &m, m, m)
	m = nil //TODO: 不确定这里给m赋nil值之后，m切片指向底层数组的指针已为nil，但func_map返回后，仍能打印出m的内容。难道GC机制会判断后面还会用m所以这里m=nil的赋值没生效？
	fmt.Printf("&m: %p m: %p %#v\n", &m, m, m)
}

func func_map_ptr(m *M) {
	(*m)[3] = "ppp"

	fmt.Println("\nfunc_map_ptr")
	fmt.Printf("m: %p *m: %p %#v\n", m, *m, m)
	m = nil //TODO: 不确定这里给m赋nil值之后，m切片指向底层数组的指针已为nil，但func_map_ptr返回后，仍能打印出m的内容。难道GC机制会判断后面还会用m所以这里m=nil的赋值没生效？
	fmt.Printf("m: %p %#v\n", m, m)
}

//map[int]string是“非定义的类型”，如果method_map的方法接收者由(m M)改为(m map[int]string)后会导致
//"invalid receiver type map[int]string (map[int]string is not a defined type)"的错误.
//type M map[int]string用type定义的S是[]int的定义类型。
func (m M) method_map() {
	m[3] = "xxx"

	fmt.Println("\nfunc_map")
	fmt.Printf("&m: %p m: %p %#v\n", &m, m, m)
	m = nil //TODO: 不确定这里给m赋nil值之后，m切片指向底层数组的指针已为nil，但method_map返回后，仍能打印出m的内容。难道GC机制会判断后面还会用m所以这里m=nil的赋值没生效？
	fmt.Printf("&m: %p m: %p %#v\n", &m, m, m)
}

func (m *M) method_map_ptr() {
	(*m)[3] = "yyy"

	fmt.Println("\nfunc_map_ptr")
	fmt.Printf("m: %p *m: %p %#v\n", m, *m, m)
	m = nil //TODO: 不确定这里给m赋nil值之后，m切片指向底层数组的指针已为nil，但method_map_ptr返回后，仍能打印出m的内容。难道GC机制会判断后面还会用m所以这里m=nil的赋值没生效？
	fmt.Printf("m: %p %#v\n", m, m)
}

func main() {

	s := S{0, 1, 2, 3, 4, 5}
	m := M{0: "a", 1: "b", 2: "c", 3: "d", 4: "e", 5: "f"}

	fmt.Println("s:", s)
	fmt.Printf("&s: %p s: %p\n", &s, s)

	func_slice(s)
	fmt.Println("func_slice s:", s)

	func_slice_ptr(&s)
	fmt.Println("func_slice_ptr s:", s)

	s.method_slice()
	fmt.Println("method_slice s:", s)

	(&s).method_slice_ptr()
	fmt.Println("method_slice_ptr s:", s)

	fmt.Println("\n\nm:", m)
	fmt.Printf("&m: %p m: %p\n", &m, m)

	func_map(m)
	fmt.Println("func_map m:", m)

	func_map_ptr(&m)
	fmt.Println("func_map_ptr m:", m)

	m.method_map()
	fmt.Println("method_map m:", m)

	(&m).method_map_ptr()
	fmt.Println("method_map_ptr m:", m)
}
```

`s: [0 1 2 3 4 5]`<br>
`&s: 0x40a0e0 s: 0x450000`<br>

`func_slice`<br>
`&s: 0x40a110 s: 0x450000 main.S{0, 1, 2, 555, 4, 5}`<br>
`&s: 0x40a110 s: 0x0 main.S(nil)`<br>
`func_slice s: [0 1 2 555 4 5]`<br>

`func_slice_ptr`<br>
`s: 0x40a0e0 *s: 0x450000 &main.S{0, 1, 2, 666, 4, 5}`<br>
`s: 0x0 (*main.S)(nil)`<br>
`func_slice_ptr s: [0 1 2 666 4 5]`<br>

`method_slice`<br>
`&s: 0x40a180 s: 0x450000 main.S{0, 1, 2, 888, 4, 5}`<br>
`&s: 0x40a180 s: 0x0 main.S(nil)`<br>
`method_slice s: [0 1 2 888 4 5]`<br>

`method_slice_ptr`<br>
`s: 0x40a0e0 *s: 0x450000 &main.S{0, 1, 2, 999, 4, 5}`<br>
`s: 0x0 (*main.S)(nil)`<br>
`method_slice_ptr s: [0 1 2 999 4 5]`<br>


`m: map[0:a 1:b 2:c 3:d 4:e 5:f]`<br>
`&m: 0x40c128 m: 0x43e260`<br>

`func_map`<br>
`&m: 0x40c168 m: 0x43e260 main.M{0:"a", 1:"b", 2:"c", 3:"ooo", 4:"e", 5:"f"}`<br>
`&m: 0x40c168 m: 0x0 main.M(nil)`<br>
`func_map m: map[0:a 1:b 2:c 3:ooo 4:e 5:f]`<br>

`func_map_ptr`<br>
`m: 0x40c128 *m: 0x43e260 &main.M{0:"a", 1:"b", 2:"c", 3:"ppp", 4:"e", 5:"f"}`<br>
`m: 0x0 (*main.M)(nil)`<br>
`func_map_ptr m: map[0:a 1:b 2:c 3:ppp 4:e 5:f]`<br>

`func_map`<br>
`&m: 0x40c230 m: 0x43e260 main.M{0:"a", 1:"b", 2:"c", 3:"xxx", 4:"e", 5:"f"}`<br>
`&m: 0x40c230 m: 0x0 main.M(nil)`<br>
`method_map m: map[0:a 1:b 2:c 3:xxx 4:e 5:f]`<br>

`func_map_ptr`<br>
`m: 0x40c128 *m: 0x43e260 &main.M{0:"a", 1:"b", 2:"c", 3:"yyy", 4:"e", 5:"f"}`<br>
`m: 0x0 (*main.M)(nil)`<br>
`method_map_ptr m: map[0:a 1:b 2:c 3:yyy 4:e 5:f]`<br>

上面的例子展示了slice和map的函数和方法对参数和接收者为普通变量和指针变量时的修改方法。<br>
加这么多打印主要用来对比修改前后的内容和一个疑惑🤔：在被调函数中对slice或map赋值为nil时，<br>
被调函数内打印的slice值为(*main.S)(nil)或main.S(nil)，被调函数内打印的map值为main.M(nil)或(*main.M)(nil)，<br>
但是从被调函数返回到main函数时，slice或map仍有被赋为nil之前的值，比如main函数里打印的<br>
`func_slice s: [0 1 2 555 4 5]`或`func_map m: map[0:a 1:b 2:c 3:ooo 4:e 5:f]`，<br>
这里有可能是golang的GC将s = ni或m = nil的操作给消除了，待解。

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](https://play.golang.org/p/BET_ATfoimb)


当一个引用类型(slice, map, channel)的变量被赋为nil后，这个变量仍拥有这个类型所持有的方法，但要注意接收者为nil时对方法的调用  //adm
```go
package main

import "fmt"

type M map[int]int

func (m M) get(idx int) int {
	return m[idx]
}

func (m M) set(idx, v int) {
	m[idx] = v
}

func main() {
	m := M{0: 9, 1: 99, 2: 999, 3: 9999}

	m.get(1)
	fmt.Println(m) //map[0:9 1:99 2:999 3:9999]

	m.set(1, 100)
	fmt.Println(m) //map[1:100 2:999 3:9999 0:9]

	m = nil

	ret := m.get(2)
	fmt.Println("ret: ", ret)
	fmt.Println(m) //map[]

	//m.set(2, 200) //panic: assignment to entry in nil map
	//fmt.Println(m)
}
```

`map[0:9 1:99 2:999 3:9999]'<br>
`map[0:9 1:100 2:999 3:9999]'<br>
`ret:  0'<br>
`map[]'<br>

`m = nil`之后，m.get(2)仍能成功调用，因为get方法只是对m的读取，当key(2)在map(m)中不存在时，map(m)将返回0值，，即get(2)返回int型的0.<br>
set(2, 200)尝试向一个nil map写值，这时map还没有为哈希表分配地址，所以这时候向map写值将会导致panic。

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](https://play.golang.org/p/D2g8d_1sglc)


## 7.3 结构体引用成员类型的方法

如果一个类型被一个结构体类型包含，则结构体可以直接引用这个类型的成员，结构体也可以直接引用这个类型所拥有的方法  //adm

```go
package main

import "fmt"

type Arm struct {
	lefthand, righthand int
}

type Leg struct {
	leftleg, rightleg int
}

type Wing struct {
	leftwing, rightwing int
}

func (a *Arm) move(angle int) {
	a.lefthand += angle
	a.righthand += angle
}

func (l *Leg) walk(step int) {
	l.leftleg += step
	l.rightleg += step
}

func (w *Wing) fly(step int) {
	w.leftwing += step
	w.rightwing += step
}

type Body struct {
	Arm
	Leg
	*Wing
	height int
}

func main() {

	b := Body{Arm{1, 1}, Leg{2, 2}, &Wing{50, 50}, 180}
	fmt.Println(b.Arm, b.Leg, *b.Wing, b.height)

	b.move(2)
	b.walk(5)
	fmt.Println(b.Arm, b.Leg, *b.Wing, b.height)

	(&b).fly(100)
	fmt.Println(b.Arm, b.Leg, *b.Wing, b.height)
	fmt.Printf("b.leftwing = %d, b.rightwing = %d\n", b.leftwing, b.rightwing)
}
```

`{1 1} {2 2} {50 50} 180`<br>
`{3 3} {7 7} {50 50} 180`<br>
`{3 3} {7 7} {150 150} 180`<br>
`b.leftwing = 150, b.rightwing = 150`<br>

类型Arm，Leg，*Wing均有自己的方法，分别为move，walk，fly。<br>
1. Body包含了Arm，Leg和*Wing的类型，Body类型的变量b可以直接引用Arm，Leg和*Wing类型的成员，比如最后一行的打印b.leftwing,而不必写成b.*Wing.leftwing,<br>
b.leftwing这种写法在编译时编译器会生成而外的代码来展开并调用到*Wing类型的leftwing变量。<br>
2. Body类型的变量b同样能引用Arm, Leg和*Wing所拥有的方法，比如b.move, b.walk, (&b).fly，这些都是Body成员类型的方法。<br>
当然了，这些成员类型的方法不能重名，否则b调用时会出现歧义。

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](https://play.golang.org/p/D2g8d_1sglc)

## 7.4 方法指针 

方法的指针分为2种，变量方法的指针和类型方法的指针  //adm

变量方法的指针，给指针传的参数和给方法传的参数一样；<br>
类型方法的指针，给指针传的第一个参数是方法的接收类型的变量，后面再传给指针的参数才是指针指向的方法的参数。

```go
package main

import "fmt"

type Arm struct {
	lefthand, righthand int
}

type Leg struct {
	leftleg, rightleg int
}

type Wing struct {
	leftwing, rightwing int
}

func (a *Arm) move(angle int) {
	a.lefthand += angle
	a.righthand += angle
}

func (l *Leg) walk(step int) {
	l.leftleg += step
	l.rightleg += step
}

func (w *Wing) fly(step int) {
	w.leftwing += step
	w.rightwing += step
}

type Body struct {
	Arm
	Leg
	*Wing
	height int
}

func main() {
	a := Arm{1, 1}
	l := Leg{2, 2}
	w := Wing{50, 50}

	b := Body{a, l, &w, 180}
	fmt.Println("Ori", b.Arm, b.Leg, *b.Wing, b.height)

	//b.move(2)

	bmv := b.move //将变量b的方法move(实际上是arm的方法，只是被b引用而已)的地址赋给bmv，bmv其实是b.move的方法指针。
	bmv(2)        //向bmv传入参数2，实际上等同于b.move(2)的表示方式。
	fmt.Println("Arm", b.Arm, b.Leg, *b.Wing, b.height)

	//b.walk(5)

	wk1 := (*Leg).walk //将*Leg类型的方法walk赋给wk1， wk1是(*Leg).walk的*Leg类型walk方法的指针
	wk1(&(b.Leg), 5)   //(*Leg) 类型的方法walk实际上只有一个参数，但这里却给wk1传递了2个参数，因为wk1的第一个参数需要是wk1这个指针指向的方法的接收者类型的变量，即walk方法的接收者*Leg类型的变量&(b.Leg)，后面的5才是传给walk的参数step.
	fmt.Printf("\nwk1 type %T\n", wk1)
	fmt.Println("Leg", b.Arm, b.Leg, *b.Wing, b.height)

	wk2 := (*Body).walk //wk2和wk1都指向walk方法，只不过wk1的walk方法接收者变量是*Leg，wk2的接收者变量类型是*Body
	wk2(&b, 8)          //wk2的第一个参数是*Body类型的变量&b，第二个参数8是传给walk的参数step.
	fmt.Printf("\nwk2 type %T\n", wk2)
	fmt.Println("Leg", b.Arm, b.Leg, *b.Wing, b.height)

	(&b).fly(100)
	fmt.Println("\nWing", b.Arm, b.Leg, *b.Wing, b.height)

	fmt.Printf("b.leftwing = %d, b.rightwing = %d\n", b.leftwing, b.rightwing)
}
```

`Ori {1 1} {2 2} {50 50} 180`<br>
`Arm {3 3} {2 2} {50 50} 180`<br>

`wk1 type func(*main.Leg, int)`<br>
`Leg {3 3} {7 7} {50 50} 180`<br>

`wk2 type func(*main.Body, int)`<br>
`Leg {3 3} {15 15} {50 50} 180`<br>

`Wing {3 3} {15 15} {150 150} 180`<br>
`b.leftwing = 150, b.rightwing = 150`<br>

[**🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯  P L A Y A R O U N D 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰 🍯 🥩 🌰**](https://play.golang.org/p/D2g8d_1sglc)


# 8. 接口

## 8.1 接口类型

接口是一种定义了一组方法的类型，接口类型只定义这组方法，方法由另一种类型来实现，最终接口类型的变量可以指向实现这组方法的类型的变量。接口类型写作:interface  //adm

未定义的接口类型，这个接口类型定义了一个方法`eat() error`
 ```go
interface {
	eat() error
}
 ```

定义的接口类型eater，这个接口类型定义了一个方法`eat() error`
 ```go
type eater interface {
	eat() error
}
 ```

未定义的接口类型的别名eater, 这个接口类型定义了一个方法`eat() error`
```go
type eatalias = interface {
 	eat() error
}
```

Go语言中接口(interface)结构体类型的内部定义如下：  //adm

 ```go
type _interface struct {
	dynamicTypeInfo *struct {
		dynamicType *_type       // the dynamic type
		methods     []*_function // method table
	}
	dynamicValue unsafe.Pointer // the dynamic value
}
 ```

接口类型的结构体成员包含一个dynamicTypeInfo结构体指针变量和一个dynamicValue的unsafe.Pointer指针变量.<br>
dynamicTypeInfo的2个成员：<br>
 &nbsp;&nbsp;dynamicType指向的是实现interface接口方法的类型。<br>
 &nbsp;&nbsp;methods切片指向的是实现interface接口方法的类型方法。<br>
dynamicValue变量指向的是实现interface接口方法类型的变量。<br>

接口类型的使用示例如下  //adm

```go
package main

import "fmt"

//eater是定义的接口类型，这个接口类型定义了一个方法eat()
type eater interface {
	eat(sth string)
}

//kid是定义的结构体类型，这个结构体类型定义了一个string类型的变量name
type kid struct {
	name string
}

//kid类型实现了eat方法
func (k kid) eat(sth string) {
	fmt.Println(k.name, "is eating", sth)
}

//feed函数有2个参数:
//第1个参数是eater接口类型的变量sbd。因为kid类型实现了eater接口要求的eat()方法，
//所以kid类型满足eater接口类型的要求, eater接口类型的变量sbd能指向kid类型的变量。
//第2个参数是string类型的变量sth，代表要吃的东西。
func feed(sbd eater, sth string) {
	sbd.eat(sth)
	//fmt.Printf("sbd type: %T  value: %s\n", sbd, sbd) //sbd type: main.kid  value: {Louis}
}

func main() {
	k := kid{"Louis"}

	feed(k, "bread")
	feed(k, "apple")
}
```

`Louis is eating bread`<br>
`Louis is eating apple`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/HD7BoI2rUye)

## 8.2 空接口类型

如果一个接口类型没有定义任何方法，那么它就是空接口类型(blank interface type)。空接口类型写作interface{}  //adm

Go语言中空接口(blank interface)结构体类型的内部定义如下：
```go
type _interface struct {
	dynamicType  *_type         // the dynamic type
	dynamicValue unsafe.Pointer // the dynamic value
}
```
相比接口类型，空接口类型不定义任何方法，所以空接口的结构体类型成员不包含接口成员的函数指针切片`methods     []*_function // method table`。

因为空接口类型interface{}没有定义方法，不论一个类型有没有自身的方法，空接口类型的2个成员dynamicType和dynamicValue都可以指向任何类型和这个类型的变量。
所以空接口类型interface{}在GO语言中是一种万能类型的存在，interface{}类型的变量可以指向任何类型的变量。

空接口类型的使用示例如下  //adm

```go
package main

import (
	"bytes"
	"fmt"
)

func main() {
	var i interface{}//空接口类型变量i

	i = 1	//i的dynamicType指向int类型，dynamicValue指向int类型值1
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = 1.0 //i的dynamicType指向float类型，dynamicValue指向float类型值1.0
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = "abcdef" //i的dynamicType指向string类型，dynamicValue指向string类型值"abcdef"
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = map[int]string{0: "monday"} //i的dynamicType指向map[int]string类型，dynamicValue指向map[int]string类型值0: "monday"
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = []int{1, 2, 3, 4, 5} //i的dynamicType指向[]int类型，dynamicValue指向[]int类型值1, 2, 3, 4, 5
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = new(bytes.Buffer) //i的dynamicType指向bytes.Buffer类型的指针，dynamicValue指向bytes.Buffer类型变量的default值
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)

	i = nil //interface和interface{}类型的0值是nil
	fmt.Printf("i type: %-14T ", i)
	fmt.Println(" value:", i)
}
```
`i type: int             value: 1`<br>
`i type: float64         value: 1`<br>
`i type: string          value: abcdef`<br>
`i type: map[int]string  value: map[0:monday]`<br>
`i type: []int           value: [1 2 3 4 5]`<br>
`i type: *bytes.Buffer   value:`<br>
`i type: <nil>           value: <nil>`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/ETmmBwnF_HW)

## 8.3 给接口变量赋值
当把另一种类型的变量赋给接口类型的变量时，需要遵循一种规则：另一种类型的方法定义一定要包含接口类型的方法定义。比如接口类型定义了3个方法，<br>
要把另一种类型的变量赋给接口类型的变量时，另一种类型的方法定义可以有10个，但这10个方法一定要包含要被赋值的接口类型的方法定义，<br>
即赋值类型变量的方法与被赋值接口类型的变量的方法是大于等于的关系。<br>
上面的“另一种类型”也包括接口类型，下面的例子解释来上面的描述<br>

给接口变量赋值的例子  //adm

```go
package main

import "fmt"

type human interface {
	swim() error
	walk() error
	talk() error
}

type superhuman interface {
	fly() error
	swim() error
	walk() error
	talk() error
}

type man struct{}

func (man) swim() error {
	fmt.Println("Man can swim")
	return nil
}

func (man) walk() error {
	fmt.Println("Man can walk")
	return nil
}

func (man) talk() error {
	fmt.Println("Man can talk")
	return nil
}

type superman struct{}

func (superman) fly() error {
	fmt.Println("superman can fly")
	return nil
}

func (superman) swim() error {
	fmt.Println("superman can swim")
	return nil
}

func (superman) walk() error {
	fmt.Println("superman can walk")
	return nil
}

func (superman) talk() error {
	fmt.Println("superman can talk")
	return nil
}

func main() {

	var h human      //h代表人类
	var s superhuman //s代表超人类

	h = s //s超人类具有人类h所拥有的所有技能: swim，walk，talk，所以可以把s赋给h。
	//s = h //人类h会的方法没有超人类s多，人类自身没有fly的技能，所以不能把h赋给s。此行编译会出现如下错误：
	//cannot use h (type human) as type superhuman in assignment: human does not implement superhuman (missing fly method)

	var m man //m代表一个人
	h = m     //m这个人实现了h人类的所有技能: swim，walk，talk，所以可以把m赋给h。 h这个接口类型的变量指向了一个实现其3个方法的变量m。

	var sm superman //sm代表一个超人
	s = sm          //sm这个超人实现了s超人类的所有技能: fly，swim，walk，talk，所以可以把sm赋给s。 s这个接口类型的变量指向一个实现了s 4个方法的变量sm.

	//试试h人类指向的这个人m所具有的技能：
	h.swim()
	h.walk()
	h.talk()

	fmt.Println() //打印一个空行

	//试试s超人类指向的这个超人sm所具有的技能：
	s.fly()
	s.swim()
	s.walk()
	s.talk()

	fmt.Println() //打印一个空行

	h = s //s超人类具有人类h所拥有的所有技能: swim，walk，talk，所以可以把s赋给h。这时候h指向的是超人sm。
	h.swim()
	h.walk()
	h.talk()
}
```

`Man can swim`<br>
`Man can walk`<br>
`Man can talk`<br>

`superman can fly`<br>
`superman can swim`<br>
`superman can walk`<br>
`superman can talk`<br>

`superman can swim`<br>
`superman can walk`<br>
`superman can talk`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/D2g8d_1sglc)


## 8.4 类型断言

类型断言(type assertion)就是对接口类型的变量进行类型转换，并检查转换结果的意思，这种转换结果的检查发生在程序运行时。<br>
比如要检查某接口类型的变量i要转换成类型T，写作i.(T)。<br>
T的类型分为2种：一种是非接口类型，一种是接口类型:<br>
如果T是非接口类型：i内部的动态类型(dynamicType)要和T相同，    则i.(T)类型断言（转换结果）成功。<br>
如果T是&nbsp;&nbsp;&nbsp;&nbsp;接口类型：i内部的动态类型(dynamicType)实现了T的方法，则i.(T)类型断言（转换结果）成功。<br>

类型断言的例子  //adm

```go
package main

import "fmt"

type human interface {
	swim() error
	walk() error
	talk() error
}

type superhuman interface {
	fly() error
	swim() error
	walk() error
	talk() error
}

type man struct{}

func (man) swim() error {
	fmt.Println("Man can swim")
	return nil
}

func (man) walk() error {
	fmt.Println("Man can walk")
	return nil
}

func (man) talk() error {
	fmt.Println("Man can talk")
	return nil
}

type superman struct{}

func (superman) fly() error {
	fmt.Println("superman can fly")
	return nil
}

func (superman) swim() error {
	fmt.Println("superman can swim")
	return nil
}

func (superman) walk() error {
	fmt.Println("superman can walk")
	return nil
}

func (superman) talk() error {
	fmt.Println("superman can talk")
	return nil
}

func main() {

	var h human      //h代表人类
	var s superhuman //s代表超人类

	var m man //m代表一个人
	h = m     //m这个人实现了h人类的所有技能: swim，walk，talk，所以可以把m赋给h。 h这个接口类型的变量指向了一个实现其3个方法的变量m, m的方法和m的类型man。

	var sm superman //sm代表一个超人
	s = sm          //sm这个超人实现了s超人类的所有技能: fly，swim，walk，talk，所以可以把sm赋给s。 s这个接口类型的变量指向一个实现了s 4个方法的变量sm，sm的方法和sm的类型superman。

	fmt.Println() //打印一个空行

	fmt.Println("i.(T) type asertion (type conversion check) test")

	fmt.Println("\nT is a non-interface type:") //T是非接口类型的情况, i指向的动态类型一定要和T非接口的类型相同，则断言成功。因为h的动态类型是man，所以h.(superman)是断言失败的。s的动态类型是superman，所以s.(man)也是断言失败的。
	fmt.Printf("h.(man)          type asertion (type conversion check) is %T\n", h.(man))
	//fmt.Printf("h.(superman)   type asertion (type conversion check) is %T\n", h.(superman)) //panic: interface conversion: main.human is main.man, not main.superman

	fmt.Printf("s.(superman)     type assertion (type conversion check) is %T\n", s.(superman))
	//fmt.Printf("s.(man)        type assertion (type conversion check) is %T\n", s.(man)) //impossible type assertion: man does not implement superhuman (missing fly method)

	fmt.Println("\nT is a interface type:") //T是接口类型的情况， i指向的动态类型实现了T接口类型所拥有的方法，则断言成功。h没有实现superhuman类型所定义的fly()方法，所以h.(superhuman)是断言失败的。
	fmt.Printf("h.(human)        type asertion (type conversion check) is %T\n", h.(human))
	//fmt.Printf("h.(superhuman) type asertion (type conversion check) is %T\n", h.(superhuman)) //panic: interface conversion: main.man is not main.superhuman: missing method fly

	fmt.Printf("s.(superhuman)   type assertion (type conversion check) is %T\n", s.(superhuman))
	fmt.Printf("s.(human)        type assertion (type conversion check) is %T\n", s.(human))
}
```

`i.(T) type asertion (type conversion check) test`<br>

`T is a non-interface type:`<br>
`h.(man)          type asertion (type conversion check) is main.man`<br>
`s.(superman)     type assertion (type conversion check) is main.superman`<br>

`T is a interface type:`<br>
`h.(human)        type asertion (type conversion check) is main.man`<br>
`s.(superhuman)   type assertion (type conversion check) is main.superman`<br>
`s.(human)        type assertion (type conversion check) is main.superman`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/JK2vKX4NshX)

利用第2个返回值判断类型断言是否成功，并防止类型断言失败时产生panic  //adm

类型断言是为了我们能更好的检查当前使用的接口变量是否符合我们要求的类型，如果转换类型不符合我们的要求，上述例子中(h.(superhuman))会直接使程序panic，<br>
这样做在程序中不太优雅，幸好Go语言为类型断言的返回值提供了2个结果:<br>
第1个是转换结果，转换成功的话，转换结果是i值的一份副本，转换失败的话，转换结果是转换类型的0值。<br>
第2个是可选的bool类型的true或false转换结果，用于判断转换是否成功，并防止转换失败的程序进入panic<br>

一个interface{}类型的变量，默认值为nil，这个变量要转换成其他类型时都会失败, 如下面程序中的变量j。<br>

```go
package main

import "fmt"

func main() {
	var i interface{} = 100
	fmt.Println("i is", i)

	x := i.(int)               //interface{}类型的i指向100时，会把100的默认类型转换为int整型。
	fmt.Println("i.(int):", x) //100

	x, ok := i.(int)               //在上一步的基础上加入可选变量ok，用于判断类型断言是否成功。
	fmt.Println("i.(int):", x, ok) //100 true

	//y := i.([]int)              //i的默认类型是int，这里用[]int作为断言类型肯定会断言失败，同时这里只用了1个返回结果y，程序会直接进入panic。
	//fmt.Println("i.([]int)", y) //panic: interface conversion: interface {} is int, not []int

	y, ok := i.([]int)               //在上一步的基础上加入可选变量ok，用于判断类型断言是否成功。如果类型断言失败，则程序可以继续执行，不panic。
	fmt.Println("i.([]int):", y, ok) //[] false

	z, ok := i.(interface{})               //i的断言类型是interface{}时，因为interface{}没有任何方法, 可以理解为interface{}类型不用实现，或者i实现了interface{}类型。
	fmt.Println("i.(interface{}):", z, ok) //100 true

	fmt.Println()

	var j interface{} //j为interface{}类型的变量，j的默认值为nil
	fmt.Println("j is", j)

	m, ok := j.(int)               //测试j能否成功转换成int类型
	fmt.Println("j.(int):", m, ok) //0 false 转换失败

	n, ok := j.(interface{})               //测试j能否成功转换成interface{}类型
	fmt.Println("j.(interface{}):", n, ok) //<nil> false 转换失败
}
```

`i is 100`<br>
`i.(int): 100`<br>
`i.(int): 100 true`<br>
`i.([]int): [] false`<br>
`i.(interface{}): 100 true`<br>

`j is <nil>`<br>
`j.(int): 0 false`<br>
`j.(interface{}): <nil> false`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/t5dUd4e8aEx)

## 8.5 type-switch
type-switch语句是类型断言的增强与补充，type-switch与switch-case语句有些相似，type-switch的语句结构如下：

```go
switch aSimpleStatement; v := i.(type) {
case TypeA:
	...
case TypeB, TypeC:
	...
case nil:
	...
default:
	...
}
```

[aSimpleStatement](https://go101.org/article/expressions-and-statements.html#simple-statements)在type-switch中是可选的。<br>

i是一个接口类型的变量，v是i的断言结果，注意i.(type)，与i.(T)中的T可以改成具体类型不同，这里必须写成type, 如下例中的x.(type)。
```go
package main

import "fmt"

func main() {
	values := []interface{}{
		456, "abc", true, 0.33, int32(789),
		[]int{1, 2, 3}, map[int]bool{}, nil,
	}
	for _, x := range values {
		// Here, v is declared once, but it denotes
		// different variables in different branches.
		switch v := x.(type) {

		//case interface{}:
		//fmt.Println("\ninterface{} val: ", v)
		//fmt.Printf("interface{} type: %T\n", v)

		case []int: // a type literal
			fmt.Println("\nint slice:", v)
		case string: // one type name
			fmt.Println("\nstring:", v)
		case int, float64, int32: // multiple type names
			fmt.Println("\nnumber:", v)

		case bool:
			fmt.Println("\nbool:", v)

		case nil:
			fmt.Println("\nnil val: ", v)
			fmt.Printf("nil type: %T\n", v)

		default:
			fmt.Println("\nOthers val: ", v)
			fmt.Printf("Others type: %T\n", v)
		}
	}
}
```

`number: 456`<br>

`string: abc`<br>

`bool: true`<br>

`number: 0.33`<br>

`number: 789`<br>

`int slice: [1 2 3]`<br>

`Others val:  map[]`<br>
`Others type: map[int]bool`<br>

`nil val:  <nil>`<br>
`nil type: <nil>`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/1NYl6BGpQaK)

## 8.6 接口嵌入

一个接口类型里可以嵌入另一个接口类型，但接口类型不能嵌套多个具有相同方法名的接口类型，也不能自己嵌套自己  //adm

比如下面的asian接口类型里嵌入了human接口类型，asian接口类型里同样也声明了fly() error方法，所以接口asian和接口superhuman
在效果上是一样的.

```go
package main

import "fmt"

type human interface {
	swim() error
	walk() error
	talk() error
}

type superhuman interface {
	fly() error
	swim() error
	walk() error
	talk() error
}

type man struct{}

func (man) swim() error {
	fmt.Println("Man can swim")
	return nil
}

func (man) walk() error {
	fmt.Println("Man can walk")
	return nil
}

func (man) talk() error {
	fmt.Println("Man can talk")
	return nil
}

type superman struct{}

func (superman) fly() error {
	fmt.Println("superman can fly")
	return nil
}

func (superman) swim() error {
	fmt.Println("superman can swim")
	return nil
}

func (superman) walk() error {
	fmt.Println("superman can walk")
	return nil
}

func (superman) talk() error {
	fmt.Println("superman can talk")
	return nil
}

//接口类型可以包裹其他接口类型，比如asian接口类型可以包含human接口类型，asian含有human拥有的所有方法。
//如果给asian再加上一个fly()的方法，那asian就成为超级亚洲人了。
type asian interface {
	human
	//superhuman //接口类型不能嵌套多个具有相同方法名的接口类型
	//asian //接口类型不能自己嵌套自己
	fly() error
}

//flyman实现了fly()方法。
type flyman struct{}

func (flyman) swim() error {
	fmt.Println("flyman can swim")
	return nil
}

func (flyman) walk() error {
	fmt.Println("flyman can walk")
	return nil
}

func (flyman) talk() error {
	fmt.Println("flyman can talk")
	return nil
}

func (flyman) fly() error {
	fmt.Println("Believe me I can fly, I am singing in the sky")
	return nil
}

func main() {

	var a asian      //a是超级亚洲人接口类型变量
	var s superhuman //s代表超人类接口类型变量
	var f flyman     //f是一个flyman类型的变量，flyman类型实现了asian接口类型的所有方法，flyman类型的变量f也具有flyman类型的所有方法。

	a = f
	s = f
	_, ok := a.(human) //human是接口类型，a实现了human接口类型的所有方法，所以a.(human)断言成功
	fmt.Println("a.(human): ", ok)

	_, ok = s.(human)  //同上
	fmt.Println("s.(human): ", ok)

	_, ok = a.(superhuman) //superhuman是接口类型，a实现了superhuman接口类型的所有方法(flyman会fly了)，所以a.(superhuman)断言成功
	fmt.Println("a.(superhuman): ", ok)

	_, ok = s.(superhuman)  //同上
	fmt.Println("s.(superhuman): ", ok)

	//_, ok = a.(man) //man是一个非接口类型，a的方法要和man的方法实现完全一致才能断言成功，man没有实现fly接口，所以a.(man)断言失败。 impossible type assertion: man does not implement asian (missing fly method)
	//fmt.Println("a.(man): ", ok)

	//_, ok = s.(man) //同上impossible type assertion:man does not implement superhuman (missing fly method)
	//fmt.Println("s.(man): ", ok)

	_, ok = a.(superman) //superman是一个非接口类型，a的动态类型是flyman，不是superman，所以a.(superman)断言失败
	fmt.Println("a.(superman): ", ok)

	_, ok = s.(superman) //同上
	fmt.Println("s.(superman): ", ok)

	_, ok = a.(flyman) //flyman是一个非接口类型，a的动态类型是flyman，所以a.(flyman)断言成功
	fmt.Println("a.(flyman): ", ok)

	_, ok = s.(flyman) //同上
	fmt.Println("s.(flyman): ", ok)
}
```

`a.(human):  true`<br>
`s.(human):  true`<br>
`a.(superhuman):  true`<br>
`s.(superhuman):  true`<br>
`a.(superman):  false`<br>
`s.(superman):  false`<br>
`a.(flyman):  true`<br>
`s.(flyman):  true`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/CzUcp96U9HR)

## 8.7 接口变量比较

接口变量进行比较，结果相等的条件只有如下2个：<br>
1. 2个接口变量的值均为nil<br>
2. 2个接口变量具有相同的动态类型和动态值<br>

将8.6中的main函数改成如下可以看到接口变量比较结果的例子  //adm

```go
func main() {

	var a asian      //a是超级亚洲人接口类型变量
	var s superhuman //s代表超人类接口类型变量
	var f flyman     //f是一个flyman类型的变量，flyman类型实现了asian接口类型的所有方法，flyman类型的变量f也具有flyman类型的所有方法。
	var sm superman

	fmt.Println("a:", s, "s:", s, " a == s", a == s) //a: <nil> s: <nil>  a == s true. a 和 s的值均为nil, 符合条件1

	a = f
	s = f
	fmt.Println("a:", s, "s:", s, " a == s", a == s) //a: {} s: {}  a == s true. a 和 s均指向同一个变量f，符合条件2

	a = f
	s = sm
	fmt.Println("a:", s, "s:", s, " a == s", a == s) //a: {} s: {}  a == s false. f和sm虽然均有相同的方法实现，但是它们对于a和s来说属于不同的动态类型和动态值，所以比较结果不同， 不符合条件2。
}
```

`a: <nil> s: <nil>  a == s true`<br>
`a: {} s: {}  a == s true`<br>
`a: {} s: {}  a == s false`<br>

[**⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  P L A Y A R O U N D 📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️  🧰  📟 ⚙️**](https://play.golang.org/p/U7_ncGQz_jS)

# 9. 并发


## 9.1. 什么是goroutine

Go语言中的并发主要是通过goroutine来实现，
goroutine是一种超轻量的thread，goroutine由Go程序运行时(runtime)进行调度和维护，而系统thread是由操作系统进行调度和维护。
一个goroutine需要由CPU附加到一个系统的thread上之后才能被执行。在同一时刻，一个goroutine只能附加到一个系统thread，同理
一个系统thread在同一时刻也只能附加到一个goroutine上。当goroutine的时间片使用完毕后，goroutine会从当前的系统thread上分离，
以便其他goroutine能附加到这个系统thread上并被执行。<br>
假设用M代表系统的threads，P代表CPU的逻辑数量，G代表goroutine，上面描述的就是goroutine所用的[M-P-G模型](https://docs.google.com/document/d/1TTj4T2JO42uD5ID9e89oa0sLKhJYD0Y_kqxDv3I3XMw/edit)<br>

Go程序中的main函数就是一个goroutine，如果要运行其他的gotoutine只需在调用的函数前面加上一个go关键字即可，如下示例:

goroutine简单示例  //adm

```go
package main

import (
	"log"
	"time"
)

func routineTest(s string, times int) {
	for i := 0; i < times; i++ {
		log.Println(s)
		time.Sleep(1 * time.Second)
	}
}

func main() {
	log.SetFlags(0)
	go routineTest("watching TV", 5)
	go routineTest("coding", 5)
	time.Sleep(5 * time.Second)
}
```

`watching TV`<br>
`coding`<br>
`coding`<br>
`watching TV`<br>
`watching TV`<br>
`coding`<br>
`coding`<br>
`watching TV`<br>
`watching TV`<br>
`coding`<br>

在main函数的最后加入time.Sleep(5 * time.Second)不利于更多goroutine的调用，所幸同步机制能改变这一现状, 下面的小节将介绍go语言中的多种并发同步机制。

[**⚽️ 🏀 🏈 ⚾️ 🥎  🏐 🏉 🎾 🥏  🎱  P L A Y A R O U N D  🏓 🏸 🏏 🏹 🥊 🥋 🎽 ⛸  🥌 🛷 🛹  ⛳️**](https://play.golang.org/p/_xz8XxNcDIh)

## 9.2 并发同步之WaitGroup

WaitGroup有3个方法：Add, Done和Wait<br>
Add方法的参数为要执行的goroutine数量<br>
Done方法用于通知当前goroutine已经执行完毕<br>
Wait方法用于goroutine的调用者等待goroutine执行完成，goroutine的执行完成由Done方法通知给Wait<br>

```go
package main

import (
	"log"
	"sync"
	"time"
)

var wg sync.WaitGroup

func routineTest(s string, times int) {
	for i := 0; i < times; i++ {
		log.Println(s)
		time.Sleep(1 * time.Second)
	}
	wg.Done()
}

func main() {
	log.SetFlags(0)
	wg.Add(2)
	go routineTest("watching TV", 5)
	go routineTest("coding", 5)
	wg.Wait()
}
```
`coding`<br>
`watching TV`<br>
`watching TV`<br>
`coding`<br>
`coding`<br>
`watching TV`<br>
`watching TV`<br>
`coding`<br>
`coding`<br>
`watching TV`<br>

上述程序中main函数开始执行时处于running的状态，当main函数执行完2个"go routineTest"语句后，这2个routineTest的goroutine处于running状态，
之后main函数执行wg.Wait()，wg.Wait()使main函数进入block状态。当routineTest执行到time.Sleep()语句时，goroutine处在staying in running
的状态(也可以理解为处在queuing状态)，当2个routineTest都执行到wg.Done()的时候，routineTest从running状态退出，然后回到main函数，
main函数从block状态恢复到running状态，然后再从running状态退出。goroutine只能从running状态退出，main程序也是goroutine。

[**⚽️ 🏀 🏈 ⚾️ 🥎  🏐 🏉 🎾 🥏  🎱  P L A Y A R O U N D  🏓 🏸 🏏 🏹 🥊 🥋 🎽 ⛸  🥌 🛷 🛹  ⛳️**](https://play.golang.org/p/0LuRQas0CTa)

## 9.2 并发同步之channel


[**⚽️ 🏀 🏈 ⚾️ 🥎  🏐 🏉 🎾 🥏  🎱  P L A Y A R O U N D  🏓 🏸 🏏 🏹 🥊 🥋 🎽 ⛸  🥌 🛷 🛹  ⛳️**](https://play.golang.org/p/0LuRQas0CTa)





# 10. 总结

## 可以比较的类型
以下5种类型在go语言中可以比较  //adm

比如boolean, numeric, string, pointer, channel, and interface types, and structs or arrays that contain only those types.
但像切片、映射和函数是不可比较的类型，所以不能用作map的KeyType。

## 不可比较的类型
以下5种类型在go语言中不可比较  //adm
1. slice<br>
2. map<br>
3. 函数<br>
4. 含有不可比较类型元素的结构体<br>
5. 元素类型为不可比较的数组<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/_zbYO7zEAw5)

## 零值为nil的类型
go语言中以下几种类型的0值用nil表示  //adm
1. pointer<br>
2. slice<br>
3. map<br>
4. function<br>
5. channel<br>
6. interface<br>

[**🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 P L A Y A R O U N D 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣 🥨 🍒 🍣**](https://play.golang.org/p/_zbYO7zEAw5)

## Printf打印参数

```go
package main

import "fmt"

func main() {
	pm := &map[string]int{"C": 1972, "Go": 2009}
	ps := &[]string{"break", "continue"}
	pa := &[...]bool{false, true, true, false}
	fmt.Printf("%T\n", pm) // *map[string]int
	fmt.Printf("%T\n", ps) // *[]string
	fmt.Printf("%T\n", pa) // *[4]bool
}
```

## 可寻址和不可寻址
addressable & unaddressable
[参考链接](https://cloud.tencent.com/developer/article/1187626)

## 参考资料
https://go101.org <br>
https://tour.golang.org/methods/16 <br>
https://golang.org/ref/spec#Method_sets <br>
