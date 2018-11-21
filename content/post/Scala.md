---
title: "Scala"
date: 2018-11-18T22:45:00+08:00
draft: false
---

Scala是SCAlable Language（可扩展语言）的缩写，由Martin Odersky教授和他的EPFL团队于2003年创建。
Scala用来为Java虚拟机(JVM)平台上的函数式编程和面向对象编程提供一个高性能的开发环境。

## 1. Scala环境搭建

### 1.1. 安装JDK
安装JAVA SE标准环境并检查版本
```
   java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```
***

### 1.2. 安装Scala
OSX 安装Scala
```
   brew install scala
==> Downloading https://downloads.lightbend.com/scala/2.12.7/scala-2.12.7.tgz
######################################################################## 100.0%
==> Caveats
To use with IntelliJ, set the Scala home to:
  /usr/local/opt/scala/idea
==> Summary
🍺  /usr/local/Cellar/scala/2.12.7: 42 files, 20.8MB, built in 20 minutes 36 seconds
```

安装完后在终端输入scala命令，看到如下Welcome to Scala...和scala>提示说明你已经在Scala REPL（READ-Eval-Print-Loop）**交互解释器环境**
```
   scala
Welcome to Scala 2.12.7 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_191).
Type in expressions for evaluation. Or try :help.

scala>
```
***

### 1.3 Scala REPL
#### 1.3.1 帮助
在Scala的REPL中输入:help会显示帮助系统

```
scala> :help
All commands can be abbreviated, e.g., :he instead of :help.
:completions <string>    output completions for the given string
:edit <id>|<line>        edit history
:help [command]          print this summary or command-specific help
:history [num]           show the history (optional num is commands to show)
:h? <string>             search the history
:imports [name name ...] show import history, identifying sources of names
:implicits [-v]          show the implicits in scope
:javap <path|class>      disassemble a file or class name
:line <id>|<line>        place line(s) at the end of history
:load <path>             interpret lines in a file
:paste [-raw] [path]     enter paste mode or paste a file
:power                   enable power user mode
:quit                    exit the interpreter
:replay [options]        reset the repl and replay all previous commands
:require <path>          add a jar to the classpath
:reset [options]         reset the repl to its initial state, forgetting all session entries
:save <path>             save replayable session to a file
:sh <command line>       run a shell command (result is implicitly => List[String])
:settings <options>      update compiler options, if possible; see reset
:silent                  disable/enable automatic printing of results
:type [-v] <expr>        display the type of an expression without evaluating it
:kind [-v] <type>        display the kind of a type. see also :help kind
:warnings                show the suppressed warnings from the most recent line which had any

scala>
```
***

#### 1.3.2 Hello, World
```
scala> println("Hello, World")
Hello, World

scala>
```
在scala> 中输入println("Hello, World")并回车时:
<br>
REPL先读(**R**) println("Hello, World")的输入内容
<br>
&ensp;&ensp;然后计算(**E**) println("Hello, World")函数的返回结果
<br>
&ensp;&ensp;之后显示(**P**) 到stdout标准输出(屏幕)上
<br>
&ensp;&ensp;最后循环(**L**) 到scala>提示符
***


#### 1.3.3 算数运算
```
scala> 9 * 9
res4: Int = 81

scala> println(res4)
81
scala>
```
在REPL 中输入9 * 9时， REPL显示结果为81，但这个81被赋给一个Int（整型）的变量res4,之后用println打印(res4)，可以看到res4变量的值为81。
***


#### 1.3.4 值(val)
>定义值: val \<identifier\>\[: \<type>\] = \<data\>

```
scala> val x:Int = 5
x: Int = 5

scala> x = 6
<console>:12: error: reassignment to val
       x = 6
         ^

scala>
```

根据字面量(Literal)赋值自动推导**值**的类型，即可省略 \[: \<type>\] 

```
scala> val a = 10
a: Int = 10

scala> val b = "desk"
b: String = desk

scala> val c = '$'
c: Char = $

scala>
```

**注意**⚠️   :如果将val显示的声明某一类型时(比如Int)，其赋值也须是相同的类型(比如Int)，否则会出现类型不匹配
```
scala> val x: Int = "floor"
<console>:11: error: type mismatch;
 found   : String("floor")
 required: Int
       val x: Int = "floor"
                    ^

scala>
```
***


#### 1.3.5 变量(var)

>定义变量:var \<identifier\>\[: \<type>\] = \<data\>

```
scala> var x:Int = 5
x: Int = 5

scala> x = 6
x: Int = 6

scala>
```
根据字面量(Literal)赋值推导变量类型，即可省略\[: \<type>\] 
```
scala> var a = 100
a: Int = 100

scala> var b = "table"
b: String = table

scala> var c = '@'
c: Char = @

scala>
```

**注意**⚠️   :如果将var显示的声明某一类型时(比如Int)，其赋值也须是相同的类型(比如Int)，否则会出现类型不匹配
```
scala> var x: Int = "roof"
<console>:11: error: type mismatch;
 found   : String("roof")
 required: Int
       var x: Int = "roof"
                    ^

scala>
```
**但是**, 一个Double类型的变量可以被赋值为一个Int值，因为Int数可以自动转换为Double数
```
scala> var y: Double = 3.1415926
y: Double = 3.1415926

scala> y = 1234
y: Double = 1234.0

scala>
```
***

#### 1.3.6 命名

Scala中的名字命名可以使用字母、数字和一些特殊的操作符（operator）字符，比如 *, :或者+等。
**注意**⚠️ ：特殊操作符不包括点号. 和中括号[] 
Scala合法标识符命名规则:

1.一个字母后面跟任意个字母或数字

```
scala> val a = 10
a: Int = 10

scala> val ab = 11
ab: Int = 11

scala> val a1 = 12
a1: Int = 12

scala> val a1b = 13
a1b: Int = 13

scala> val ab1 = 14
ab1: Int = 14

scala>
```

2.一个字母后面跟任意个字母或数字, 然后是一个下划线(_)，后面是任意个字母和数字或操作符字符

```
scala> val a_b = 20
a_b: Int = 20

scala> val a_1 = 21
a_1: Int = 21

scala> val a_* = 22
a_*: Int = 22

scala> val a* = 23
<console>:1: error: illegal start of simple pattern
       val a* = 23
              ^

scala>
```
3.一个或多个操作符字符
```
scala> val * = 30
*: Int = 30

scala> val ** = 31
**: Int = 31

scala> val *: = 32
*:: Int = 32

scala> val *:+ = 33
*:+: Int = 33

scala>
```

4.一个或多个除反引号\`\`之外的任意字符，所有这些字符需要包围在一对反引号中,
这些字符可以是Scala的保留字，比如true, while, = 和var

a*的命名违反规则2， 使用反引号\`\`包围a*使其命名合法化
```
scala> val a* = 40
<console>:1: error: illegal start of simple pattern
       val a* = 40
              ^

scala> val `a*` = 40
a*: Int = 40

```

Scala命名规则里面不包含点号.,  使用反引号\`\` 使命名合法化
```
scala> val . = 41
<console>:1: error: illegal start of simple pattern
       val . = 41
           ^

scala> val `.` = 41
.: Int = 41
```

```
scala> val a.. = 42
<console>:1: error: identifier expected but '.' found.
       val a.. = 42
             ^
<console>:1: error: identifier expected but '=' found.
       val a.. = 42
               ^

scala> val `a..` = 42
a..: Int = 42

scala>
```

Scala命名规则里面不包中括号[].,  使用反引号\`\` 使命名合法化
```
scala> val [] = 43
<console>:1: error: illegal start of simple pattern
       val [] = 43
           ^

scala> val `[]` = 43
[]: Int = 43

scala>
```

Scala命名规则里面不包含数字开头的规则，所以这种即使是加上反引号\`\`也是不合法的
```
scala> var `1abc` = 44
<console>:5: error: Invalid literal number
         lazy val $result = 1abc
                                                 ^
<console>:9: error: Invalid literal number
       ""  + "\u001B[1m\u001B[34m1abc\u001B[0m: \u001B[1m\u001B[32mInt\u001B[0m = " + _root_.scala.runtime.ScalaRunTime.replStringOf(1abc, 1000)
```


Scala采用驼峰式命名法,值和变量名首字母小写，其余后面单词的首字母大写。类型和类的首字母和后面的单词首字母均采用大写的方式，以便和值和变量做区分。
***

