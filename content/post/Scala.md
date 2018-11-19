---
title: "Scala"
date: 2018-11-18T22:45:00+08:00
draft: true
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

如下是一个类似的例子，可以看到8 * 8的结果被赋给了Int型的变量res6。
```
scala> 8 * 8
res6: Int = 64

scala> println(res6)
64

scala>
```
***


