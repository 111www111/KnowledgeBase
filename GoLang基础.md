​                            

# Golang基础教程                                                 

# Go语言

Go（又称 Golang）是 Google 的 Robert Griesemer，Rob Pike 及 Ken Thompson 开发的一种静态强类型、编译型语言。Go 语言语法与 C 相近，但功能上有：内存安全，GC（垃圾回收），结构形态及 CSP-style 并发计算。

## 简介

Go（又称Golang）是Google开发的一种静态强类型、编译型、并发型，并具有垃圾回收功能的编程语言。

罗伯特·格瑞史莫（Robert Griesemer），罗勃·派克（Rob Pike）及肯·汤普逊（Ken Thompson）于2007年9月开始设计Go，稍后Ian Lance Taylor、Russ Cox加入项目。Go是基于Inferno操作系统所开发的。Go于2009年11月正式宣布推出，成为开放源代码项目，并在Linux及Mac OS X平台上进行了实现，后来追加了Windows系统下的实现。在2016年，Go被软件评价公司TIOBE 选为“TIOBE 2016 年最佳语言”。 目前，Go每半年发布一个二级版本（即从a.x升级到a.y）。

## 描述

Go的语法接近C语言，但对于变量的声明有所不同。Go支持垃圾回收功能。Go的并行模型是以东尼·霍尔的通信顺序进程（CSP）为基础，采取类似模型的其他语言包括Occam和Limbo，但它也具有Pi运算的特征，比如通道传输。在1.8版本中开放插件（Plugin）的支持，这意味着现在能从Go中动态加载部分函数。

与C++相比，Go并不包括如枚举、异常处理、继承、泛型、断言、虚函数等功能，但增加了 切片(Slice) 型、并发、管道、垃圾回收、接口（Interface）等特性的语言级支持。Go 2.0版本将支持泛型，对于断言的存在，则持负面态度，同时也为自己不提供类型继承来辩护。

不同于Java，Go内嵌了关联数组（也称为哈希表（Hashes）或字典（Dictionaries）），就像字符串类型一样。

## 撰写风格

在Go中有几项规定，当不匹配以下规定时编译将会产生错误。

1. 每行程序结束后不需要撰写分号（;）。

1. 大括号（{）不能够换行放置。

1. if判断式和for循环不需要以小括号包覆起来。

Go亦有内置gofmt工具，能够自动整理代码多余的空白、变量名称对齐、并将对齐空格转换成Tab。

## 编译器

当前有两个Go编译器分支，分别为官方编译器gc和gccgo。官方编译器在初期使用C写成，后用Go重写从而实现自举。Gccgo是一个使用标准GCC作为后端的Go编译器。

官方编译器支持跨平台编译（但不支持CGO），允许将源代码编译为可在目标系统、架构上执行的二进制文件。               

# go语言开发环境搭建

## windows平台

### 下载安装并配置环境变量

下载地址：https://golang.google.cn/dl/，这里提供了不同平台的go版本，根据自己的平台选择下载。

这里注意，安装路径选择一个比较好找的路径，例如：c:/go，其他安装都选择”下一步“即可。

安装完成后，把C:\go\bin目录添加到环境变量，这里就可以使用go了，在命令行输入 go version查看版本，输出结果如下所示。

```
go version go1.16.6 windows/amd64

```

### 配置go环境

```
go env -w GO111MODULE=on 
go env -w GOPROXY=https://goproxy.cn,https://goproxy.io,direct

```

> 
使用使用go mod 管理库，需要科学上网


### 安装配置git

```
https://www.git-scm.com/download/

```

配置环境变量，命令行输入git测试

## goroot和gopath

goroot就是go安装的根目录，gopath就是go项目所在的路径，高版本go项目已经不再依赖gopath来管理项目，使用go mod来管理项目。

## Linux平台

如果不是要在Linux平台敲go代码就不需要在Linux平台安装Go，我们开发机上写好的go代码只需要跨平台编译（详见文章末尾的跨平台编译）好之后就可以拷贝到Linux服务器上运行了，这也是go程序跨平台易部署的优势。

我们在版本选择页面选择并下载好go1.14.1.linux-amd64.tar.gz文件：

```bash
wget https://dl.google.com/go/go1.14.1.linux-amd64.tar.gz

```

将下载好的文件解压到/usr/local目录下：

```bash
tar -zxvf go1.14.1.linux-amd64.tar.gz -C /usr/local  # 解压

```

如果提示没有权限，加上sudo以root用户的身份再运行。执行完就可以在/usr/local/下看到go目录了。

配置环境变量： Linux下有两个文件可以配置环境变量，其中/etc/profile是对所有用户生效的；$HOME/.profile是对当前用户生效的，根据自己的情况自行选择一个文件打开，添加如下两行代码，保存退出。

```bash
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin

```

修改/etc/profile后要重启生效，修改$HOME/.profile后使用source命令加载$HOME/.profile文件即可生效。 检查：

```bash
~ go version
go version go1.16.6 linux/amd64

```

## Mac平台

下载可执行文件版，直接点击下一步安装即可，默认会将go安装到/usr/local/go目录下。

上一步安装过程执行完毕后，可以打开终端窗口，输入go version命令，查看安装的Go版本。

```
go version go1.16.6 darwin/amd64
```

​               

# 开发工具简介

golang的开发工具有很多，例如：

1. vim

1. sublime

1. atom

1. LiteIDE

1. eclipse

1. goland

1. vscode

## 使用goland开发go应用

goland是一款付费的、功能强大的golang集成开发环境，是Jetbrain公司的产品。下载地址：https://www.jetbrains.com/go/ goland非常智能，几乎不需要配置，安装即用。

## 使用vscode开发go应用

1. 下载安装vscode 

https://code.visualstudio.com/

1. 下载安装插件，参考文档：

https://marketplace.visualstudio.com/items?itemName=golang.go

1. 安装code runner运行脚本         



# go常用命令

## 查看可用命令

直接在终端中输入 go help 即可显示所有的 go 命令以及相应命令功能简介，主要有下面这些:

- build: 编译包和依赖

- clean: 移除对象文件

- doc: 显示包或者符号的文档

- env: 打印go的环境信息

- bug: 启动错误报告

- fix: 运行go tool fix

- fmt: 运行gofmt进行格式化

- generate: 从processing source生成go文件

- get: 下载并安装包和依赖

- install: 编译并安装包和依赖

- list: 列出包

- run: 编译并运行go程序

- test: 运行测试

- tool: 运行go提供的工具

- version: 显示go的版本

- vet: 运行go tool vet   

  ​                          

# golang标识符、关键字、命名规则

## 标识符

标识符的英文是identifier，通俗的讲，就是给变量、常量、函数、方法、结构体、数组、切片、接口起名字。

### 标识符的组成

1. 标识符由数字、字母和下划线(

_)组成。123 abc _

1. 只能以字母和下划线(

_)开头。abc123 _sysVar  123abc

1. 标识符区分大小写。 name Name NAME

### 举例说明标识符的命名

正确的命名

```go
package main

import "fmt"

func main() {
	var name string
	var age int
	var _sys int
}

```



错误的标识符

```go
package main

import "fmt"

func main() {
	var 1name string
	var &age int
	var !email
}

```



## go语言关键字

go语言提供了25个关键字，如下所示。

| break | default | func | interface | select |
| - | - | - | - | - |
| case | defer | go | map | struct |
| chan | else | goto | package | switch |
| const | fallthrough | if | range | type |
| continue | for | import | return | var |




除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符，其中包含了基本类型的名称和一些基本的内置函数，见下表：

| append | bool | byte | cap | close | complex | complex64 | complex128 | uint16 |
| - | - | - | - | - | - | - | - | - |
| copy | false | float32 | float64 | imag | int | int8 | int16 | uint32 |
| int32 | int64 | iota | len | make | new | nil | panic | uint64 |
| print | println | real | recover | string | true | uint | uint8 | uintptr |




## go语言命名规范

### Go是一门区分大小写的语言

命名规则涉及变量、常量、全局函数、结构、接口、方法等的命名。 Go语言从语法层面进行了以下限定：任何需要对外暴露的名字必须以大写字母开头，不需要对外暴露的则应该以小写字母开头。

当命名（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：GetUserName，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）； 命名如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）

### 包名称

保持package的名字和目录保持一致，尽量采取有意义的包名，简短，有意义，尽量和标准库不要冲突。包名应该为小写单词，不要使用下划线或者混合大小写。

```go
package dao
package service

```



### 文件命名

尽量采取有意义的文件名，简短，有意义，应该为小写单词，使用下划线分隔各个单词。

```go
customer_dao.go

```



### 结构体命名

采用驼峰命名法，首字母根据访问控制大写或者小写

struct 申明和初始化格式采用多行，例如下面：

```go
type CustomerOrder struct {
    Name string 
    Address string
}
order := CustomerOrder{"tom", "北京海淀"}

```



### 接口命名

命名规则基本和上面的结构体类型

单个函数的结构名以 “er” 作为后缀，例如 Reader , Writer 。

```go
type Reader interface {
     Read(p []byte) (n int, err error)
}

```



### 变量命名

和结构体类似，变量名称一般遵循驼峰法，首字母根据访问控制原则大写或者小写，但遇到特有名词时，需要遵循以下规则：

如果变量为私有，且特有名词为首个单词，则使用小写，如 appService 若变量类型为 bool 类型，则名称应以 Has, Is, Can 或 Allow 开头

```go
var isExist bool
var hasConflict bool
var canManage bool
var allowGitHook bool

```



### 常量命名

常量均需使用全部大写字母组成，并使用下划线分词

```go
const APP_URL = "https://www.abc360.com"

```



如果是枚举类型的常量，需要先创建相应类型：

```go
type Scheme string

const (
    HTTP  Scheme = "http"
    HTTPS Scheme = "https"
)

```



### 错误处理

错误处理的原则就是不能丢弃任何有返回err的调用，不要使用 _ 丢弃，必须全部处理。接收到错误，要么返回err，或者使用log记录下来尽早return：一旦有错误发生，马上返回，尽量不要使用panic，除非你知道你在做什么，错误描述如果是英文必须为小写，不需要标点结尾，采用独立的错误流进行处理

```go
// 错误写法
if err != nil {
    // 错误处理
} else {
    // 正常代码
}

// 正确写法
if err != nil {
    // 错误处理
    return // 或者继续
}
// 正常代码

```

### 单元测试

单元测试文件名命名规范为 example_test.go 测试用例的函数名称必须以 Test 开头，例如：TestExample 每个重要的函数都要首先编写测试用例，测试用例和正规代码一起提交方便进行回归测试 。           

# golang变量

变量是计算机语言中能储存计算结果或能表示值的抽象概念。不同的变量保存的数据类型可能会不一样。

## 声明变量

Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。 并且Go语言的变量声明后必须使用。

声明变量的语法

```go
var identifier type

```

var：声明变量关键字

identifier：变量名称

type：变量类型

例如

```go
package main

import "fmt"

func main() {
	var name string
	var age int
	var ok bool
}

```

### 批量声明

使用一个var关键字，把一些变量写在一个括号()里

```go
package main
func main() {
	var (
		name string
		age  int
		ok   bool
	)
}

```

## 变量的初始化

Go语言在声明变量的时候，会自动对变量对应的内存区域进行初始化操作。每个变量会被初始化成其类型的默认值，例如： 整型和浮点型变量的默认值为0。 字符串变量的默认值为空字符串“”。 布尔型变量默认为false。 切片、函数、指针变量的默认为nil。

变量初始化语法

```go
var 变量名 类型 = 表达式

```

例如

```go
package main
func main() {
	var name string = "王某人"
	var site string = "www.baidu.com"
	var age int = 30
}

```

类型推导

我们在声明变量时，可以根据初始化值进行类型推导，从而省略类型。

```go
package main
func main() {
	var name = "王某人"
	var site = "www.baidu.com"
	var age = 30
}

```

初始化多个变量

可以一次初始化多个变量，中间用逗号分隔。

```go
package main
func main() {
	var name, site, age = "王某", "www.baidu.com", 30
}

```

## 短变量声明

在函数内部，可以使用 :=运算符对变量进行声明和初始化。

```go
package main

func main() {
	name := "王某"
	site := "www.baidu.com"
	age := 20
}

```

> 
注意：这种方法只适合在函数内部，函数外面不能使用。


## 匿名变量

如果我们接收到多个变量，有一些变量使用不到，可以使用下划线_表示变量名称，这种变量叫做匿名变量。例如：

```go
package main

import "fmt"

func getNameAndAge() (string, int) {
	return "王某", 30
}

func main() {
	name, _ := getNameAndAge()
	fmt.Printf("name: %v\n", name)
}

```

​             

# go语言常量

常量，就是在程序编译阶段就确定下来的值，而程序在运行时则无法改变该值。在Go程序中，常量可以是数值类型（包括整型、浮点型和复数类型）、布尔类型、字符串类型等。

## 定义常量的语法

定义一个常量使用const关键字，语法格式如下：

```go
const constantName [type]= value

```

const：定义常量关键字

constantName：常量名称

type：常量类型

value：常量的值

实例

```go
package main

func main() {
	const PI float64 = 3.14
	const PI2 = 3.1415 // 可以省略类型

	const (
		width  = 100
		height = 200
	)

	const i, j = 1, 2 // 多重赋值
	const a, b, c = 1, 2, "foo"
}

```

const同时声明多个常量时，如果省略了值则表示和上面一行的值相同

```go
package main

import "fmt"

func main() {
	const (
		a1 = 100
		a2
		a3
	)

	fmt.Printf("a1: %v\n", a1)
	fmt.Printf("a2: %v\n", a2)
	fmt.Printf("a3: %v\n", a3)
}

```

运行结果

```
a1: 100
a2: 100
a3: 100

```

## iota

iota 比较特殊，可以被认为是一个可被编译器修改的常量，它默认开始值是0，每调用一次加1。遇到 const 关键字时被重置为 0。

实例

```go
package main

import "fmt"

func main() {
	const (
		a1 = iota
		a2 = iota
		a3 = iota
	)

	fmt.Printf("a1: %v\n", a1)
	fmt.Printf("a2: %v\n", a2)
	fmt.Printf("a3: %v\n", a3)
}

```

运行结果

```
a1: 0
a2: 1
a3: 2

```

使用_跳过某些值

```go
package main

import "fmt"

func main() {
	const (
		a1 = iota
		_
		a2 = iota
	)

	fmt.Printf("a1: %v\n", a1)
	fmt.Printf("a2: %v\n", a2)
}

```

运行结果

```
a1: 0
a2: 2

```

iota声明中间插队

```go
package main

import "fmt"

func main() {
	const (
		a1 = iota
		a2 = 100
		a3 = iota
	)

	fmt.Printf("a1: %v\n", a1)
	fmt.Printf("a2: %v\n", a2)
	fmt.Printf("a3: %v\n", a3)
}

```

运行结果

```
a1: 0
a2: 100
a3: 2
```

# go语言数据类型

在 Go 编程语言中，数据类型用于声明函数和变量。

数据类型的出现是为了把数据分成所需内存大小不同的数据，编程的时候需要用大数据的时候才需要申请大内存，就可以充分利用内存。

Go 语言按类别有以下几种数据类型：

| 序号 | 类型和描述 |
| - | - |
| 1 | 布尔型<br> 布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。 |
| 2 | 数字类型<br> 整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。 |
| 3 | 字符串类型:<br> 字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。 |
| 4 | 派生类型:<br> 包括：(a) 指针类型（Pointer）(b) 数组类型(c) 结构化类型(struct)(d) Channel 类型(e) 函数类型(f) 切片类型(g) 接口类型（interface）(h) Map 类型 |


## 数字类型

Go 也有基于架构的类型，例如：int、uint 和 uintptr。

| 序号 | 类型和描述 |
| - | - |
| 1 | uint8<br> 无符号 8 位整型 (0 到 255) |
| 2 | uint16<br> 无符号 16 位整型 (0 到 65535) |
| 3 | uint32<br> 无符号 32 位整型 (0 到 4294967295) |
| 4 | uint64<br> 无符号 64 位整型 (0 到 18446744073709551615) |
| 5 | int8<br> 有符号 8 位整型 (-128 到 127) |
| 6 | int16<br> 有符号 16 位整型 (-32768 到 32767) |
| 7 | int32<br> 有符号 32 位整型 (-2147483648 到 2147483647) |
| 8 | int64<br> 有符号 64 位整型 (-9223372036854775808 到 9223372036854775807) |


### 浮点型

| 序号 | 类型和描述 |
| - | - |
| 1 | float32<br> IEEE-754 32位浮点型数 |
| 2 | float64<br> IEEE-754 64位浮点型数 |
| 3 | complex64<br> 32 位实数和虚数 |
| 4 | complex128<br> 64 位实数和虚数 |


## 其他数字类型

以下列出了其他更多的数字类型：

| 序号 | 类型和描述 |
| - | - |
| 1 | byte<br> 类似 uint8 |
| 2 | rune<br> 类似 int32 |
| 3 | uint<br> 32 或 64 位 |
| 4 | int<br> 与 uint 一样大小 |
| 5 | uintptr<br> 无符号整型，用于存放一个指针 |


# go语言布尔类型

go语言中的布尔类型有两个常量值：true和false。布尔类型经常用在条件判断语句，或者循环语句。也可以用在逻辑表达式中。

## 布尔类型

```go
package main

import "fmt"

func main() {
	var b1 bool = true
	var b2 bool = false
	var b3 = true
	var b4 = false

	b5 := true
	b6 := false

	fmt.Printf("b1: %v\n", b1)
	fmt.Printf("b2: %v\n", b2)
	fmt.Printf("b3: %v\n", b3)
	fmt.Printf("b4: %v\n", b4)
	fmt.Printf("b5: %v\n", b5)
	fmt.Printf("b6: %v\n", b6)
}

```

运行结果

```
b1: true
b2: false
b3: true
b4: false
b5: true
b6: false

```

## 用在条件判断中

```go
package main

import "fmt"

func main() {
	age := 18
	ok := age >= 18
	if ok {
		fmt.Println("你已经成年")
	} else {
		fmt.Println("你还未成年")
	}
}

```

运行结果

```
你已经成年

```

## 用在循环语句中

```go
package main

import "fmt"

func main() {
	count := 10
	for i := 0; i < count; i++ {
		fmt.Printf("i: %v\n", i)
	}
}

```

## 用在逻辑表达式中

```go
package main

import "fmt"

func main() {
	age := 18
	gender := "男"

	if age >= 18 && gender == "男" {
		fmt.Println("你是成年男子")
	}
}

```

> 
注意：不能使用0和非0表示真假


```go
package main

func main() {
	i := 1
	if i {
		// 编译错误
	}
}

```

#                             

# go语言数字类型

Go 语言支持整型和浮点型数字，并且原生支持复数，其中位的运算采用补码。

Go 也有基于架构的类型，例如：int、uint 和 uintptr。

这些类型的长度都是根据运行程序所在的操作系统类型所决定的：

- int 和 

uint 在 32 位操作系统上，它们均使用 32 位（4 个字节），在 64 位操作系统上，它们均使用 64 位（8 个字节）。

- uintptr 的长度被设定为足够存放一个指针即可。

Go 语言中没有 float 类型。（Go语言中只有 float32 和 float64）没有double类型。

与操作系统架构无关的类型都有固定的大小，并在类型的名称中就可以看出来：

整数：

- int8（-128 -> 127）

- int16（-32768 -> 32767）

- int32（-2,147,483,648 -> 2,147,483,647）

- int64（-9,223,372,036,854,775,808 -> 9,223,372,036,854,775,807）

无符号整数：

- uint8（0 -> 255）

- uint16（0 -> 65,535）

- uint32（0 -> 4,294,967,295）

- uint64（0 -> 18,446,744,073,709,551,615）

浮点型（IEEE-754 标准）：

- float32（+- 1e-45 -> +- 3.4 * 1e38）

- float64（+- 5 * 1e-324 -> 107 * 1e308）

int 型是计算最快的一种类型。

整型的零值为 0，浮点型的零值为 0.0。

## 实例

下面实例演示了，各个数字类型的长度和取值范围

```go
package main

import (
	"fmt"
	"math"
	"unsafe"
)

func main() {
	var i8 int8
	var i16 int16
	var i32 int32
	var i64 int64
	var ui8 uint8
	var ui16 uint16
	var ui32 uint32
	var ui64 uint64

	fmt.Printf("%T %dB %v~%v\n", i8, unsafe.Sizeof(i8), math.MinInt8, math.MaxInt8)
	fmt.Printf("%T %dB %v~%v\n", i16, unsafe.Sizeof(i16), math.MinInt16, math.MaxInt16)
	fmt.Printf("%T %dB %v~%v\n", i32, unsafe.Sizeof(i32), math.MinInt32, math.MaxInt32)
	fmt.Printf("%T %dB %v~%v\n", i64, unsafe.Sizeof(i64), math.MinInt64, math.MaxInt64)

	fmt.Printf("%T %dB %v~%v\n", ui8, unsafe.Sizeof(ui8), 0, math.MaxUint8)
	fmt.Printf("%T %dB %v~%v\n", ui16, unsafe.Sizeof(ui16), 0, math.MaxUint16)
	fmt.Printf("%T %dB %v~%v\n", ui32, unsafe.Sizeof(ui32), 0, math.MaxUint32)
	fmt.Printf("%T %dB %v~%v\n", ui64, unsafe.Sizeof(ui64), 0, uint64(math.MaxUint64))

	var f32 float32
	var f64 float64

	fmt.Printf("%T %dB %v~%v\n", f32, unsafe.Sizeof(f32), -math.MaxFloat32, math.MaxFloat32)
	fmt.Printf("%T %dB %v~%v\n", f64, unsafe.Sizeof(f64), -math.MaxFloat64, math.MaxFloat64)

	var ui uint
	ui = uint(math.MaxUint64) //再+1会导致overflows错误
	fmt.Printf("%T %dB %v~%v\n", ui, unsafe.Sizeof(ui), 0, ui)

	var imax, imin int
	imax = int(math.MaxInt64) //再+1会导致overflows错误
	imin = int(math.MinInt64) //再-1会导致overflows错误

	fmt.Printf("%T %dB %v~%v\n", imax, unsafe.Sizeof(imax), imin, imax)
}

```



运行结果

```
int8 1B -128~127
int16 2B -32768~32767
int32 4B -2147483648~2147483647
int64 8B -9223372036854775808~9223372036854775807
uint8 1B 0~255
uint16 2B 0~65535
uint32 4B 0~4294967295
uint64 8B 0~18446744073709551615
float32 4B -3.4028234663852886e+38~3.4028234663852886e+38
float64 8B -1.7976931348623157e+308~1.7976931348623157e+308
uint 8B 0~18446744073709551615
int 8B -9223372036854775808~9223372036854775807

```



## 以二进制、八进制或十六进制浮点数的格式定义数字

```go
package main

import "fmt"

func main() {
	// 十进制
	var a int = 10
	fmt.Printf("%d \n", a) // 10
	fmt.Printf("%b \n", a) // 1010  占位符%b表示二进制

	// 八进制  以0开头
	var b int = 077
	fmt.Printf("%o \n", b) // 77

	// 十六进制  以0x开头
	var c int = 0xff
	fmt.Printf("%x \n", c) // ff
	fmt.Printf("%X \n", c) // FF
}

```



运行结果

```
10 
1010 
77 
ff 
FF 

```



## 浮点型

Go语言支持两种浮点型数：float32和float64。这两种浮点型数据格式遵循IEEE 754标准： float32 的浮点数的最大范围约为 3.4e38，可以使用常量定义：math.MaxFloat32。 float64 的浮点数的最大范围约为 1.8e308，可以使用一个常量定义：math.MaxFloat64。

打印浮点数时，可以使用fmt包配合动词%f，代码如下：

```go
package main
import (
        "fmt"
        "math"
)
func main() {
        fmt.Printf("%f\n", math.Pi)
        fmt.Printf("%.2f\n", math.Pi)
}

```



## 复数

complex64和complex128

```go
var c1 complex64
c1 = 1 + 2i
var c2 complex128
c2 = 2 + 3i
fmt.Println(c1)
fmt.Println(c2)

```



复数有实部和虚部，complex64的实部和虚部为32位，complex128的实部和虚部为64位。        

​      

# golang字符串

一个Go语言字符串是一个任意字节的常量序列。[] byte

## go语言字符串字面量

在Go语言中，字符串字面量使用双引号 "" 或者反引号 ' 来创建。双引号用来创建可解析的字符串，支持转义，但不能用来引用多行；反引号用来创建原生的字符串字面量，可能由多行组成，但不支持转义，并且可以包含除了反引号外其他所有字符。双引号创建可解析的字符串应用最广泛，反引号用来创建原生的字符串则多用于书写多行消息，HTML以及正则表达式。

实例

```go
package main

import "fmt"

func main() {
	var str1 string = "hello world"
	var html string = 
		<html>
			<head><title>hello golang</title>
		</html>
	`

	fmt.Printf("str1: %v\n", str1)
	fmt.Printf("html: %v\n", html)
}

```



运行结果

```go
str1: hello world
html: 
<html>
	<head><title>hello golang</title>
</html>

```



## go语言字符串连接

使用加号

虽然Go语言中的字符串是不可变的，但是字符串支持 + 级联操作和+=追加操作，例如：

```go
package main

import "fmt"

func main() {
	name := "tom"
	age := "20"
	msg := name + " " + age
	fmt.Printf("msg: %v\n", msg)
	fmt.Println("-------------")
	msg = ""
	msg += name
	msg += " "
	msg += age
	fmt.Printf("msg: %v\n", msg)
}

```



> 
golang 里面的字符串都是不可变的，每次运算都会产生一个新的字符串，所以会产生很多临时的无用的字符串，不仅没有用，还会给 gc 带来额外的负担，所以性能比较差




使用fmt.Sprintf()函数

```go
package main

import "fmt"

func main() {
	name := "tom"
	age := "20"
	msg := fmt.Sprintf("%s,%s", name, age)
	fmt.Printf("msg: %v\n", msg)
}

```



运行结果

```go
msg: tom,20

```



> 
内部使用 []byte 实现，不像直接运算符这种会产生很多临时的字符串，但是内部的逻辑比较复杂，有很多额外的判断，还用到了 interface，所以性能也不是很好




strings.Join()

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	name := "tom"
	age := "20"
	msg := strings.Join([]string{name, age}, ",")
	fmt.Printf("msg: %v\n", msg)
}

```



运行结果

```
msg: tom,20

```



> 
join会先根据字符串数组的内容，计算出一个拼接之后的长度，然后申请对应大小的内存，一个一个字符串填入，在已有一个数组的情况下，这种效率会很高，但是本来没有，去构造这个数据的代价也不小




buffer.WriteString()

```go
package main

import (
	"bytes"
	"fmt"
)

func main() {
	var buffer bytes.Buffer
	buffer.WriteString("tom")
	buffer.WriteString(",")
	buffer.WriteString("20")
	fmt.Printf("buffer.String(): %v\n", buffer.String())
}

```



> 
这个比较理想，可以当成可变字符使用，对内存的增长也有优化，如果能预估字符串的长度，还可以用 buffer.Grow() 接口来设置 capacity




## go语言字符串转义字符

Go 语言的字符串常见转义符包含回车、换行、单双引号、制表符等，如下表所示。

| 转义符 | 含义 |
| - | - |
| \\r | 回车符（返回行首） |
| \\n | 换行符（直接跳到下一行的同列位置） |
| \\t | 制表符 |
| \\' | 单引号 |
| \\" | 双引号 |
| \\\\ | 反斜杠 |




实例

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Print("hello\tworld\n")
	fmt.Print("\"c:\\test\\\"")
}

```



运行结果

```
hello	world
"c:\test\"

```



## go语言字符串切片操作

```go
package main

import (
	"fmt"
)

func main() {
	str := "hello world"
	n := 3
	m := 5
	fmt.Println(str[n])   //获取字符串索引位置为n的原始字节
	fmt.Println(str[n:m]) //截取得字符串索引位置为 n 到 m-1 的字符串
	fmt.Println(str[n:])  //截取得字符串索引位置为 n 到 len(s)-1 的字符串
	fmt.Println(str[:m])  //截取得字符串索引位置为 0 到 m-1 的字符串
}

```



运行结果

```
108
lo
lo world
hello

```



## go语言字符串常用方法

| 方法 | 介绍 |
| - | - |
| len(str) | 求长度 |
| +或fmt.Sprintf | 拼接字符串 |
| strings.Split | 分割 |
| strings.contains | 判断是否包含 |
| strings.HasPrefix,strings.HasSuffix | 前缀/后缀判断 |
| strings.Index(),strings.LastIndex() | 子串出现的位置 |
| strings.Join(a[]string, sep string) | join操作 |




实例

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	s := "hello world！"
	fmt.Printf("len(s): %v\n", len(s))
	fmt.Printf("strings.Split(s, \"\"): %v\n", strings.Split(s, " "))
	fmt.Printf("strings.Contains(s, \"hello\"): %v\n", strings.Contains(s, "hello"))
	fmt.Printf("strings.HasPrefix(s, \"hello\"): %v\n", strings.HasPrefix(s, "hello"))
	fmt.Printf("strings.HasSuffix(s, \"world！\"): %v\n", strings.HasSuffix(s, "world！"))
	fmt.Printf("strings.Index(s, \"l\"): %v\n", strings.Index(s, "l"))
	fmt.Printf("strings.LastIndex(s, \"l\"): %v\n", strings.LastIndex(s, "l"))
}

```



运行结果

```
len(s): 14
strings.Split(s, ""): [hello world！]
strings.Contains(s, "hello"): true
strings.HasPrefix(s, "hello"): true
strings.HasSuffix(s, "world！"): true
strings.Index(s, "l"): 2
strings.LastIndex(s, "l"): 9

```



## byte和rune类型

组成每个字符串的元素叫做“字符”，可以通过遍历或者单个获取字符串元素获得字符。 字符用单引号（’）包裹起来，如：

```go
package main

import "fmt"

func main() {
	var a = '华'
	var b = 'a'
	fmt.Printf("a: %v,%c\n", a, a)
	fmt.Printf("b: %v,%c\n", b, b)
}

```



运行结果

```
a: 21326,华
b: 97,a

```



Go 语言的字符有以下两种：

1. uint8类型，或者叫 byte 型，代表了

ASCII码的一个字符。

1. rune类型，代表一个 

UTF-8字符。

当需要处理中文、日文或者其他复合字符时，则需要用到rune类型。rune类型实际是一个int32。

Go 使用了特殊的 rune 类型来处理 Unicode，让基于 Unicode 的文本处理更为方便，也可以使用 byte 型进行默认字符串处理，性能和扩展性都有照顾。    



# golang格式化输出

下面实例使用到的结构体

```go
type Website struct {
	    Name string
}
// 定义结构体变量
var site = Website{Name:"abc"}

```

## 占位符

普通占位符

```go
占位符						说明						举例										输出
%v		相应值的默认格式。								Printf("%v", site)，Printf("%+v", site)	{abcabc}，{Name:abcabc}
		在打印结构体时，“加号”标记（%+v）会添加字段名
%#v		相应值的Go语法表示							Printf("#v", site)						main.Website{Name:"abcabc"}
%T		相应值的类型的Go语法表示						Printf("%T", site)						main.Website
%%		字面上的百分号，并非值的占位符					Printf("%%")							%

```

布尔占位符

```go
占位符						说明						举例										输出
%t		单词 true 或 false。							Printf("%t", true)						true

```

整数占位符

```go
占位符						说明						举例									输出
%b		二进制表示									Printf("%b", 5)						101
%c		相应Unicode码点所表示的字符					Printf("%c", 0x4E2D)				中
%d		十进制表示									Printf("%d", 0x12)					18
%o		八进制表示									Printf("%o", 10)					12
%q		单引号围绕的字符字面值，由Go语法安全地转义		Printf("%q", 0x4E2D)				'中'
%x		十六进制表示，字母形式为小写 a-f				Printf("%x", 13)					d
%X		十六进制表示，字母形式为大写 A-F				Printf("%x", 13)					D
%U		Unicode格式：U+1234，等同于 "U+%04X"			Printf("%U", 0x4E2D)				U+4E2D

```

浮点数和复数的组成部分（实部和虚部）

```go
占位符						说明												举例									输出
%b		无小数部分的，指数为二的幂的科学计数法，与 strconv.FormatFloat	
		的 'b' 转换格式一致。例如 -123456p-78
%e		科学计数法，例如 -1234.456e+78									Printf("%e", 10.2)							1.020000e+01
%E		科学计数法，例如 -1234.456E+78									Printf("%e", 10.2)							1.020000E+01
%f		有小数点而无指数，例如 123.456									Printf("%f", 10.2)							10.200000
%g		根据情况选择 %e 或 %f 以产生更紧凑的（无末尾的0）输出				Printf("%g", 10.20)							10.2
%G		根据情况选择 %E 或 %f 以产生更紧凑的（无末尾的0）输出				Printf("%G", 10.20+2i)						(10.2+2i)

```

字符串与字节切片

```go
占位符						说明												举例									输出
%s		输出字符串表示（string类型或[]byte)							Printf("%s", []byte("多课网"))		多课网
%q		双引号围绕的字符串，由Go语法安全地转义							Printf("%q", "多课网")				"多课网"
%x		十六进制，小写字母，每字节两个字符								Printf("%x", "golang")						676f6c616e67
%X		十六进制，大写字母，每字节两个字符								Printf("%X", "golang")						676F6C616E67

```

指针

```go
占位符						说明												举例									输出
%p		十六进制表示，前缀 0x											Printf("%p", &site)							0x4f57f0

```

## 实例

```go
type user struct {
	name string
}

func main() {
	u := user{"guo"}
	//Printf 格式化输出
	fmt.Printf("% + v\n", u)     //格式化输出结构
	fmt.Printf("%#v\n", u)       //输出值的 Go 语言表示方法
	fmt.Printf("%T\n", u)        //输出值的类型的 Go 语言表示
	fmt.Printf("%t\n", true)     //输出值的 true 或 false
	fmt.Printf("%b\n", 1024)     //二进制表示
	fmt.Printf("%c\n", 11111111) //数值对应的 Unicode 编码字符
	fmt.Printf("%d\n", 10)       //十进制表示
	fmt.Printf("%o\n", 8)        //八进制表示
	fmt.Printf("%q\n", 22)       //转化为十六进制并附上单引号
	fmt.Printf("%x\n", 1223)     //十六进制表示，用a-f表示
	fmt.Printf("%X\n", 1223)     //十六进制表示，用A-F表示
	fmt.Printf("%U\n", 1233)     //Unicode表示
	fmt.Printf("%b\n", 12.34)    //无小数部分，两位指数的科学计数法6946802425218990p-49
	fmt.Printf("%e\n", 12.345)   //科学计数法，e表示
	fmt.Printf("%E\n", 12.34455) //科学计数法，E表示
	fmt.Printf("%f\n", 12.3456)  //有小数部分，无指数部分
	fmt.Printf("%g\n", 12.3456)  //根据实际情况采用%e或%f输出
	fmt.Printf("%G\n", 12.3456)  //根据实际情况采用%E或%f输出
	fmt.Printf("%s\n", "wqdew")  //直接输出字符串或者[]byte
	fmt.Printf("%q\n", "dedede") //双引号括起来的字符串
	fmt.Printf("%x\n", "abczxc") //每个字节用两字节十六进制表示，a-f表示
	fmt.Printf("%X\n", "asdzxc") //每个字节用两字节十六进制表示，A-F表示
	fmt.Printf("%p\n", 0x123)    //0x开头的十六进制数表示
}

```

​      

# golang运算符

Go 语言内置的运算符有：

1. 算术运算符

1. 关系运算符

1. 逻辑运算符

1. 位运算符

1. 赋值运算符

## 算术运算符

| 运算符 | 描述 |
| - | - |
| + | 相加 |
| - | 相减 |
| \* | 相乘 |
| / | 相除 |
| % | 求余 |


注意： ++（自增）和--（自减）在Go语言中是单独的语句，并不是运算符。

实例

```go
package main

import "fmt"

func main() {
	a := 100
	b := 10

	fmt.Printf("(a + b): %v\n", (a + b))
	fmt.Printf("(a - b): %v\n", (a - b))
	fmt.Printf("(a * b): %v\n", (a * b))
	fmt.Printf("(a / b): %v\n", (a / b))
	fmt.Printf("(a %% b): %v\n", (a % b))

	a++
	fmt.Printf("a: %v\n", a)
	b--
	fmt.Printf("b: %v\n", b)
}

```

## 关系运算符

| 运算符 | 描述 |
| - | - |
| == | 检查两个值是否相等，如果相等返回 True 否则返回 False。 |
| != | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。 |
| &gt; | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。 |
| &gt;= | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 |
| &lt; | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。 |
| &lt;= | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 |


实例

```go
package main

import "fmt"

func main() {
	a := 1
	b := 2

	fmt.Printf("(a > b): %v\n", (a > b))
	fmt.Printf("(a < b): %v\n", (a < b))
	fmt.Printf("(a >= b): %v\n", (a >= b))
	fmt.Printf("(a <= b): %v\n", (a <= b))
	fmt.Printf("(a == b): %v\n", (a == b))
	fmt.Printf("(a != b): %v\n", (a != b))
}

```

## 逻辑运算符

| 运算符 | 描述 |
| - | - |
| &amp;&amp; | 逻辑 AND 运算符。 如果两边的操作数都是 True，则为 True，否则为 False。 |
| || | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则为 True，否则为 False。 |
| ! | 逻辑 NOT 运算符。 如果条件为 True，则为 False，否则为 True。 |


实例

```go
package main

import "fmt"

func main() {
	a := true
	b := false

	fmt.Printf("(a && b): %v\n", (a && b))
	fmt.Printf("(a || b): %v\n", (a || b))
	fmt.Printf("(!a): %v\n", (!a))
	fmt.Printf("(!b): %v\n", (!b))
}

```

## 位运算符

位运算符对整数在内存中的二进制位进行操作。

| 运算符 | 描述 |
| - | - |
| &amp; | 参与运算的两数各对应的二进位相与。 （两位均为1才为1） |
| | | 参与运算的两数各对应的二进位相或。 （两位有一个为1就为1） |
| ^ | 参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1。 （两位不一样则为1） |
| &lt;&lt; | 左移n位就是乘以2的n次方。 &quot;a&lt;&lt;b”是把a的各二进位全部左移b位，高位丢弃，低位补0。 |
| &gt;&gt; | 右移n位就是除以2的n次方。 &quot;a&gt;&gt;b”是把a的各二进位全部右移b位。 |


实例

```go
package main

import "fmt"

func main() {
	a := 4 // 二进制 100
	fmt.Printf("a: %b\n", a)
	b := 8 // 二进制 1000
	fmt.Printf("b: %b\n", b)

	fmt.Printf("(a & b): %v, %b \n", (a & b), (a & b))
	fmt.Printf("(a | b): %v, %b\n", (a | b), (a | b))
	fmt.Printf("(a ^ b): %v, %b\n", (a ^ b), (a ^ b))
	fmt.Printf("(a << 2): %v, %b\n", (a << 2), (a << 2))
	fmt.Printf("(b >> 2): %v, %b\n", (b >> 2), (b >> 2))
}

```

## 赋值运算符

| 运算符 | 描述 |
| - | - |
| = | 简单的赋值运算符，将一个表达式的值赋给一个左值 |
| += | 相加后再赋值 |
| -= | 相减后再赋值 |
| \*= | 相乘后再赋值 |
| /= | 相除后再赋值 |
| %= | 求余后再赋值 |
| &lt;&lt;= | 左移后赋值 |
| &gt;&gt;= | 右移后赋值 |
| &amp;= | 按位与后赋值 |
| |= | 按位或后赋值 |
| ^= | 按位异或后赋值 |


实例

```go
package main

import "fmt"

func main() {
	var a int
	a = 100
	fmt.Printf("a: %v\n", a)
	a += 1 // a = a + 1
	fmt.Printf("a: %v\n", a)
	a -= 1 // a = a -1
	fmt.Printf("a: %v\n", a)
	a *= 2 // a = a * 2
	fmt.Printf("a: %v\n", a)
	a /= 2 // a = a / 2
	fmt.Printf("a: %v\n", a)
}
```

​                   

# if elseif switch 略

# fori forr（fore） 略

# golang数组

数组是相同数据类型的一组数据的集合，数组一旦定义长度不能修改，数组可以通过**下标（或者叫索引）**来访问元素。

## go语言数组的定义

数组定义的语法如下：

```go
var variable_name [SIZE] variable_type

```

variable_name：数组名称

SIZE：数组长度，必须是常量

variable_type：数组保存元素的类型

实例

```go
package main

import "fmt"

func main() {
	var a [3]int    // 定义一个int类型的数组a，长度是3
	var s [2]string // 定义一个字符串类型的数组s，长度是2

	fmt.Printf("a: %T\n", a)
	fmt.Printf("s: %T\n", s)
}

```

运行结果

```
a: [3]int
s: [2]string

```

从上面运行结果，我们可以看出，数组和长度和元素类型共同组成了数组的类型。

## go语言数组的初始化

初始化，就是给数组的元素赋值，没有初始化的数组，默认元素值都是零值，布尔类型是false，字符串是空字符串。

### 没有初始化的数组

实例

```go
package main

import "fmt"

func main() {
	var a [3]int    // 定义一个int类型的数组a，长度是3
	var s [2]string // 定义一个字符串类型的数组s，长度是2
	var b [2]bool

	fmt.Printf("a: %v\n", a)
	fmt.Printf("s: %v\n", s)
	fmt.Printf("b: %v\n", b)
}

```

运行结果

```
a: [0 0 0]
s: ["" ""]
b: [false false]

```

### 使用初始化列表

实例

```go
package main

import "fmt"

func main() {
	var a = [3]int{1, 2, 3}
	var s = [2]string{"tom", "kite"}
	var b = [2]bool{true, false}

	a1 := [2]int{1, 2} // 类型推断

	fmt.Printf("a: %v\n", a)
	fmt.Printf("s: %v\n", s)
	fmt.Printf("b: %v\n", b)
	fmt.Printf("a1: %v\n", a1)
}

```

运行结果

```
a: [1 2 3]
s: [tom kite]
b: [true false]
a1: [1 2]

```

使用初始化列表，就是将值写在大括号里面。

### 省略数组长度

数组长度可以省略，使用...代替，更加初始化值得数量自动推断，例如：

```go
package main

import "fmt"

func main() {
	var a = [...]int{1, 2, 3}
	var s = [...]string{"tom", "kite"}
	var b = [...]bool{true, false}

	a1 := [...]int{1, 2} // 类型推断

	fmt.Printf("a: %v\n", a)
	fmt.Printf("s: %v\n", s)
	fmt.Printf("b: %v\n", b)
	fmt.Printf("a1: %v\n", a1)
}

```

运行结果

```
a: [1 2 3]
s: [tom kite]
b: [true false]
a1: [1 2]

```

### 指定索引值的方式来初始化

可以通过指定所有的方式来初始化，未指定所有的默认未零值。

实例

```go
package main

import "fmt"

func main() {
	var a = [...]int{0: 1, 2: 2}
	var s = [...]string{1: "tom", 2: "kite"}
	var b = [...]bool{2: true, 5: false}

	a1 := [...]int{1, 2} // 类型推断

	fmt.Printf("a: %v\n", a)
	fmt.Printf("s: %v\n", s)
	fmt.Printf("b: %v\n", b)
	fmt.Printf("a1: %v\n", a1)
}

```

运行结果

```
a: [1 0 2]
s: [ tom kite]
b: [false false true false false false]
a1: [1 2]
```

# go语言访问数组元素

可以通过下标的方式，来访问数组元素。数组的最大下标为数组长度-1，大于这个下标会发生数组越界。

## 访问数组元素

实例

```go
package main

import "fmt"

func main() {
	var a [2]int
	a[0] = 100
	a[1] = 200

	fmt.Printf("a[0]: %v\n", a[0])
	fmt.Printf("a[0]: %v\n", a[1])

	// 修改 a[0]  a[1]
	a[0] = 1
	a[1] = 2

	fmt.Println("-----------")

	fmt.Printf("a[0]: %v\n", a[0])
	fmt.Printf("a[0]: %v\n", a[1])
}

```

运行结果

```
a[0]: 100
a[0]: 200
-----------
a[0]: 1
a[0]: 2

```

## 根据数组长度遍历数组

可以根据数组长度，通过for循环的方式来遍历数组，数组的长度可以使用len函数获得。

实例

```go
package main

import "fmt"

func main() {
	a := [...]int{1, 2, 3, 4, 5, 6}

	for i := 0; i < len(a); i++ {
		fmt.Printf("a[%d]: %v\n", i, a[i])
	}
}

```

运行结果

```
a[0]: 1
a[1]: 2
a[2]: 3
a[3]: 4
a[4]: 5
a[5]: 6

```

## 使用for range数组

还可以使用for range循环来遍历数组，range返回数组下标和对应的值

实例

```go
package main

import "fmt"

func main() {
	a := [...]int{1, 2, 3, 4, 5, 6}

	for i, v := range a {
		fmt.Printf("a[%d]: %v\n", i, v)
	}
}

```

运行结果

```go
a[0]: 1
a[1]: 2
a[2]: 3
a[3]: 4
a[4]: 5
a[5]: 6

```

# golang切片

前面我们学习了数组，数组是固定长度，可以容纳相同数据类型的元素的集合。当长度固定时，使用还是带来一些限制，比如：我们申请的长度太大浪费内存，太小又不够用。

鉴于上述原因，我们有了go语言的切片，可以把切片理解为，可变长度的数组，其实它底层就是使用数组实现的，增加了自动扩容功能。切片（Slice）是一个拥有相同类型元素的可变长度的序列。

## go语言切片的语法

声明一个切片和声明一个数组类似，只要不添加长度就可以了

```go
var identifier []type

```

切片是引用类型，可以使用make函数来创建切片：

```go
var slice1 []type = make([]type, len)

也可以简写为

slice1 := make([]type, len)

```

也可以指定容量，其中capacity为可选参数。

```go
make([]T, length, capacity)

```

这里 len 是数组的长度并且也是切片的初始长度。

## go语言切片实例

```go
package main

import "fmt"

func main() {
	var names []string
	var numbers []int
	fmt.Printf("names: %v\n", names)
	fmt.Printf("numbers: %v\n", numbers)
	fmt.Println(names == nil)
	fmt.Println(numbers == nil)
}

```

运行结果

```
names: []
numbers: []
true
true

```

## go语言切片的长度和容量

切片拥有自己的长度和容量，我们可以通过使用内置的len()函数求长度，使用内置的cap()函数求切片的容量。

实例

```go
package main

import "fmt"

func main() {
	var names = []string{"tom", "kite"}
	var numbers = []int{1, 2, 3}

	fmt.Printf("len: %d cap: %d\n", len(names), cap(names))
	fmt.Printf("len: %d cap: %d\n", len(numbers), cap(numbers))

	var s1 = make([]string, 2, 3)
	fmt.Printf("len: %d cap: %d\n", len(s1), cap(s1))
}

```

运行结果

```
len: 2 cap: 2
len: 3 cap: 3
len: 2 cap: 3

```

# golang切片的初始化

切片的初始化方法很多，可以直接初始化，也可以使用数组初始化等。

## 切片如何切分

```go
// 切片
func test1() {
	var s1 = []int{1, 2, 3, 4, 5, 6}
	s2 := s1[0:3] // [)
	fmt.Printf("s2: %v\n", s2)
	s3 := s1[3:]
	fmt.Printf("s3: %v\n", s3)
	s4 := s1[2:5]
	fmt.Printf("s4: %v\n", s4)
	s5 := s1[:]
	fmt.Printf("s5: %v\n", s5)
}

```

## 直接初始化

```go
package main

import "fmt"

func main() {
	s := []int{1, 2, 3}
	fmt.Printf("s: %v\n", s)
}

```

运行结果

```
s: [1 2 3]

```

## 使用数组初始化

```go
package main

import "fmt"

func main() {
	arr := [...]int{1, 2, 3}
	s1 := arr[:]
	fmt.Printf("s1: %v\n", s1)
}

```

运行结果

```
s1: [1 2 3]

```

## 使用数组的部分元素初始化（切片表达式）

切片的底层就是一个数组，所以我们可以基于数组通过切片表达式得到切片。 切片表达式中的low和high表示一个索引范围（左包含，右不包含），得到的切片长度=high-low，容量等于得到的切片的底层数组的容量。

```go
package main

import "fmt"

func main() {
	arr := [...]int{1, 2, 3, 4, 5, 6}
	s1 := arr[2:5]
	fmt.Printf("s1: %v\n", s1)
	s2 := arr[2:]
	fmt.Printf("s2: %v\n", s2)
	s3 := arr[:3]
	fmt.Printf("s3: %v\n", s3)
}

```

运行结果

```
s1: [3 4 5]
s2: [3 4 5 6]
s3: [1 2 3]

```

## 空(nil)切片

一个切片在未初始化之前默认为 nil，长度为 0，容量为0.

```go
package main

import "fmt"

func main() {
	var s1 []int
	fmt.Println(s1 == nil)
	fmt.Printf("len: %d, cap: %d\n", len(s1), cap(s1))
}

```

运行结果

```
true
len: 0, cap: 0
```

# go语言切片的遍历

切片的遍历和数组的遍历非常类似，可以使用for循环索引遍历，或者for range循环。

## for循环索引遍历

```go
package main

import "fmt"

func main() {
	s1 := []int{1, 2, 3, 4, 5}
	for i := 0; i < len(s1); i++ {
		fmt.Printf("s1[%d]: %v\n", i, s1[i])
	}
}

```

运行结果

```
s1[0]: 1
s1[1]: 2
s1[2]: 3
s1[3]: 4
s1[4]: 5

```

## for range循环

```go
package main

import "fmt"

func main() {
	s1 := []int{1, 2, 3, 4, 5}
	for i, v := range s1 {
		fmt.Printf("s1[%d]: %v\n", i, v)
	}
}

```

运行结果

```
s1[0]: 1
s1[1]: 2
s1[2]: 3
s1[3]: 4
s1[4]: 5

```

#                             

# go语言切片元素的添加和删除copy

切片是一个动态数组，可以使用append()函数添加元素，go语言中并没有删除切片元素的专用方法，我们可以使用切片本身的特性来删除元素。由于，切片是引用类型，通过赋值的方式，会修改原有内容，go提供了copy()函数来拷贝切片

db CRUD

## 添加元素

```go
package main

import "fmt"

func main() {
	s1 := []int{}
	s1 = append(s1, 1)
	s1 = append(s1, 2)
	s1 = append(s1, 3, 4, 5) // 添加多个元素
	fmt.Printf("s1: %v\n", s1)

	s3 := []int{3, 4, 5}
	s4 := []int{1, 2}
	s4 = append(s4, s3...) // 添加另外一个切片
	fmt.Printf("s4: %v\n", s4)
}

```



运行结果

```
s1: [1 2 3 4 5]
s4: [1 2 3 4 5]

```



## 删除元素

```go
package main

import "fmt"

func main() {
	s1 := []int{1, 2, 3, 4, 5}
	// 删除索引为2的元素
	s1 = append(s1[:2], s1[3:]...)
	fmt.Printf("s1: %v\n", s1)
}

```



运行结果

```
s1: [1 2 4 5]

```



公式：要从切片a中删除索引为index的元素，操作方法是a = append(a[:index], a[index+1:]...)

## 拷贝切片

```go
package main

import "fmt"

func main() {
	s1 := []int{1, 2, 3}
	s2 := s1
	s1[0] = 100
	fmt.Printf("s1: %v\n", s1)
	fmt.Printf("s2: %v\n", s2)
	fmt.Println("----------")

	s3 := make([]int, 3)

	copy(s3, s1)

	s1[0] = 1

	fmt.Printf("s1: %v\n", s1)
	fmt.Printf("s3: %v\n", s3)
}

```



运行结果

```
s1: [100 2 3]
s2: [100 2 3]
-------------
s1: [1 2 3]
s3: [100 2 3]

```



从运行结果，我们看到赋值的情况下，原来的变量被修改了，使用copy函数，原来的变量没有被修改。                    



# golang map

map是一种key:value键值对的数据结构容器。map内部实现是哈希表(hash)。

map 最重要的一点是通过 key 来快速检索数据，key 类似于索引，指向数据的值。

map是引用类型的。

## map的语法格式

可以使用内建函数 make 也可以使用 map 关键字来定义 map

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type
/* 使用 make 函数 */
map_variable = make(map[key_data_type]value_data_type)

```

map_variable：map变量名称

key_data_type：key的数据类型

value_data_type：值得数据类型

实例

下面声明一个保存个人信息的map

```go
package main

import "fmt"

func main() {
	m1 := make(map[string]string)
	m1["name"] = "tom"
	m1["age"] = "20"
	m1["email"] = "tom@gmail.com"

	fmt.Printf("m1: %v\n", m1)

	m2 := map[string]string{
		"name":  "kite",
		"age":   "20",
		"email": "kite@gmail.com",
	}

	fmt.Printf("m2: %v\n", m2)
}

```

运行结果

```
m1: map[age:20 email:tom@gmail.com name:tom]
m2: map[age:20 email:kite@gmail.com name:kite]

```

## 访问map

可以通过下标key获得其值，例如：

```go
package main

import "fmt"

func main() {
	m1 := make(map[string]string)
	m1["name"] = "tom"
	m1["age"] = "20"
	m1["email"] = "tom@gmail.com"

	name := m1["name"]
	age := m1["age"]
	email := m1["email"]
	fmt.Printf("name: %v\n", name)
	fmt.Printf("age: %v\n", age)
	fmt.Printf("email: %v\n", email)
}

```

## 判断某个键是否存在

go语言中有个判断map中键是否存在的特殊写法，格式如下:

```go
value, ok := map[key]

```

如果ok为true，存在；否则，不存在。

实例

```go
package main

import "fmt"

func main() {
	m1 := make(map[string]string)
	m1["name"] = "tom"
	m1["age"] = "20"
	m1["email"] = "tom@gmail.com"

	v, ok := m1["address"]
	if ok {
		fmt.Println("键存在")
		fmt.Println(v)
	} else {
		fmt.Println("键不存在")
	}
}

```

运行结果

```go
键不存在

```

# go语言遍历map

可以使用for range循环进行map遍历，得到key和value值。

## 遍历key

```go
package main

import "fmt"

func main() {
	 m := make(map[string]string)
	 m["name"] = "tom"
	 m["age"] = "20"
	 m["email"] = "tom@gmail.com"

	 for key := range m{
		 fmt.Println(key)
	 }
}

```

运行结果

```
name
age
email

```

## 遍历key和value

```go
package main

import "fmt"

func main() {
	 m := make(map[string]string)
	 m["name"] = "tom"
	 m["age"] = "20"
	 m["email"] = "tom@gmail.com"

	 for key, value := range m{
		 fmt.Println(key + ":" +value)
	 }
}

```

运行结果

```
name:tom
age:20
email:tom@gmail.com

```

# golang 函数

## golang函数简介

函数的go语言中的一级公民，我们把所有的功能单元都定义在函数中，可以重复使用。函数包含函数的名称、参数列表和返回值类型，这些构成了函数的签名（signature）。

### go语言中函数特性

1. go语言中有3种函数：普通函数、匿名函数(没有名称的函数)、方法(定义在struct上的函数)。receiver

1. go语言中不允许函数重载(overload)，也就是说不允许函数同名。

1. go语言中的函数不能嵌套函数，但可以嵌套匿名函数。

1. 函数是一个值，可以将函数赋值给变量，使得这个变量也成为函数。

1. 函数可以作为参数传递给另一个函数。

1. 函数的返回值可以是一个函数。

1. 函数调用的时候，如果有参数传递给函数，则先拷贝参数的副本，再将副本传递给函数。

1. 函数参数可以没有名称。

## go语言中函数的定义和调用

函数在使用之前必须先定义，可以调用函数来完成某个任务。函数可以重复调用，从而达到代码重用。

### go语言函数定义语法

```go
func function_name( [parameter list] ) [return_types]
{
   函数体
}

```

语法解析：

- func：函数由 

func 开始声明

- function_name：函数名称，函数名和参数列表一起构成了函数签名。

- [parameter list]：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，这个值被称为实际参数。参数列表指定的是参数类型、顺序、及参数个数。参数是可选的，也就是说函数也可以不包含参数。

- return_types：返回类型，函数返回一列值。

return_types 是该列值的数据类型。有些功能不需要返回值，这种情况下 

return_types 不是必须的。

- 函数体：函数定义的代码集合。

### go语言函数定义实例

定义一个求和函数

```go
func sum(a int, b int) (ret int) {
	ret = a + b
	return ret
}

```

定义一个比较两个数大小的函数

```go
func compare(a int, b int) (max int) {
	if a > b {
		max = a
	} else {
		max = b
	}
	return max
}

```

### go语言函数调用

当我们要完成某个任务时，可以调用函数来完成。调用函数要传递参数，如何有返回值可以获得返回值。

```go
func main() {
	s := sum(1, 2)
	fmt.Printf("s: %v\n", s)

	max := compare(1, 2)
	fmt.Printf("max: %v\n", max)
}

```

运行结果

```go
s: 3
max: 2

```

# golang函数的返回值

函数可以有0或多个返回值，返回值需要指定数据类型，返回值通过return关键字来指定。

return可以有参数，也可以没有参数，这些返回值可以有名称，也可以没有名称。go中的函数可以有多个返回值。

1. return关键字中指定了参数时，返回值可以不用名称。如果

return省略参数，则返回值部分必须带名称

1. 当返回值有名称时，必须使用括号包围，逗号分隔，即使只有一个返回值

1. 但即使返回值命名了，

return中也可以强制指定其它返回值的名称，也就是说

return的优先级更高

1. 命名的返回值是预先声明好的，在函数内部可以直接使用，无需再次声明。命名返回值的名称不能和函数参数名称相同，否则报错提示变量重复定义

1. return中可以有表达式，但不能出现赋值表达式，这和其它语言可能有所不同。例如

return a+b是正确的，但

return c=a+b是错误的。

## go语言函数返回值实例

没有返回值

```go
func f1() {
	fmt.Printf("我没有返回值，只是进行一些计算")
}

```

有一个返回值

```go
func sum(a int, b int) (ret int) {
	ret = a + b
	return ret
}

```

多个返回值，且在return中指定返回的内容

```go
func f2() (name string, age int) {
	name = "name"
	age = 30
	return name, age
}

```

多个返回值，返回值名称没有被使用

```
func f3() (name string, age int) {
	name = "name"
	age = 30
	return // 等价于return name, age
}

```

return覆盖命名返回值，返回值名称没有被使用

```go
func f4() (name string, age int) {
	n := "name"
	a := 30
	return n, a
}

```

> 
Go中经常会使用其中一个返回值作为函数是否执行成功、是否有错误信息的判断条件。例如return value,exists、return value,ok、return value,err等。


> 
当函数的返回值过多时，例如有4个以上的返回值，应该将这些返回值收集到容器中，然后以返回容器的方式去返回。例如，同类型的返回值可以放进slice中，不同类型的返回值可以放进map中。


> 
但函数有多个返回值时，如果其中某个或某几个返回值不想使用，可以通过下划线_来丢弃这些返回值。例如下面的f1函数两个返回值，调用该函数时，丢弃了第二个返回值b，只保留了第一个返回值a赋值给了变量a。


```go
package main

import "fmt"

func f1() (int, int) {
	return 1, 2
}
func main() {
	_, x := f1()
	fmt.Printf("x: %v\n", x)
}

```

运行结果

```
x: 2
```

# golang函数类型与函数变量

可以使用type关键字来定义一个函数类型，语法格式如下：

```go
type fun func(int, int) int

```

上面语句定义了一个fun函数类型，它是一种函数类型，这种函数接收两个int类型的参数，并且返回一个int类型的返回值。

下面我们定义两个这样结构的两个函数，一个求和，一个比较大小：

```go
func sum(a int, b int) int {
	return a + b
}

func max(a int, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

```

下面定义一个fun函数类型，把sum和max赋值给它

```go
package main

import "fmt"

type fun func(int, int) int

func sum(a int, b int) int {
	return a + b
}

func max(a int, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

func main() {
	var f fun
	f = sum
	s := f(1, 2)
	fmt.Printf("s: %v\n", s)
	f = max
	m := f(3, 4)
	fmt.Printf("m: %v\n", m)
}

```

运行结果

```
s: 3
m: 4

```

# golang高阶函数

go语言的函数，可以作为函数的参数，传递给另外一个函数，可以可以作为，另外一个函数的返回值返回。

## go语言函数作为参数

```go
package main

import "fmt"

func sayHello(name string) {
	fmt.Printf("Hello,%s", name)
}

func f1(name string, f func(string)) {
	f(name)
}

func main() {
	f1("tom", sayHello)
}

```

运行结果

```
Hello,tom

```

## go语言函数作为返回值

```
package main

import "fmt"

func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

func cal(s string) func(int, int) int {
	switch s {
	case "+":
		return add
	case "-":
		return sub
	default:
		return nil
	}
}

func main() {
	add := cal("+")
	r := add(1, 2)
	fmt.Printf("r: %v\n", r)

	fmt.Println("-----------")

	sub := cal("-")
	r = sub(100, 50)
	fmt.Printf("r: %v\n", r)
}

```

运行结果

```go
r: 3
-----------
r: 50

```

# golang匿名函数

go语言函数不能嵌套，但是在函数内部可以定义匿名函数，实现一下简单功能调用。

所谓匿名函数就是，没有名称的函数。

语法格式如下：

```
func (参数列表)(返回值)

```

> 
当然可以既没有参数，可以没有返回值


## 匿名函数实例

```go
func main() {
	max := func (a int, b int) int {
		if a > b {
			return a
		} else {
			return b
		}
	}

	i := max(1, 2)
	fmt.Printf("i: %v\n", i)
}

```

运行结果

```
i: 2

```

自己执行

```go
func main() {
	// 自己执行
	func(a int, b int) {
		max := 0
		if a > b {
			max = a
		} else {
			max = b
		}
		fmt.Printf("max: %v\n", max)
	}(1, 2)
}

```

运行结果

```
max: 2
```

# golang闭包

闭包可以理解成定义在一个函数内部的函数。在本质上，闭包是将函数内部和函数外部连接起来的桥梁。或者说是函数和其引用环境的组合体。

闭包指的是一个函数和与其相关的引用环境组合而成的实体。简单来说，闭包=函数+引用环境。 首先我们来看一个例子：

```go
package main

import "fmt"

// 返回一个函数
func add() func(int) int {
	var x int
	return func(y int) int {
		x += y
		return x
	}
}

func main() {
	var f = add()
	fmt.Println(f(10))
	fmt.Println(f(20))
	fmt.Println(f(30))
	fmt.Println("-----------")
	f1 := add()
	fmt.Println(f1(40))
	fmt.Println(f1(50))
}

```

运行结果

```
10
30
60
-----------
40
90

```

变量f是一个函数并且它引用了其外部作用域中的x变量，此时f就是一个闭包。 在f的生命周期内，变量x也一直有效。 闭包进阶示例1：

```go
package main

import "fmt"

func add(x int) func(int) int {
	return func(y int) int {
		x += y
		return x
	}
}
func main() {
	var f = add(10)
	fmt.Println(f(10))
	fmt.Println(f(20))
	fmt.Println(f(30))

	fmt.Println("----------")

	f1 := add(20)
	fmt.Println(f1(40))
	fmt.Println(f1(50))
}

```

运行结果

```
20
40
70
----------
60
110

```

闭包进阶示例2：

```go
func makeSuffixFunc(suffix string) func(string) string {
	return func(name string) string {
		if !strings.HasSuffix(name, suffix) {
			return name + suffix
		}
		return name
	}
}

func main() {
	jpgFunc := makeSuffixFunc(".jpg")
	txtFunc := makeSuffixFunc(".txt")
	fmt.Println(jpgFunc("test")) 
	fmt.Println(txtFunc("test")) 
}

```

运行结果

```
test.jpg
test.txt

```

闭包进阶示例3：

```go
func calc(base int) (func(int) int, func(int) int) {
	add := func(i int) int {
		base += i
		return base
	}

	sub := func(i int) int {
		base -= i
		return base
	}
	return add, sub
}

func main() {
	f1, f2 := calc(10)
	fmt.Println(f1(1), f2(2)) 
	fmt.Println(f1(3), f2(4)) 
	fmt.Println(f1(5), f2(6)) 
}

```

运行结果

```
11 9
12 8
13 7
```

# golang递归

函数内部调用函数自身的函数称为递归函数。

使用递归函数最重要的三点：

1. 递归就是自己调用自己。

1. 必须先定义函数的退出条件，没有退出条件，递归将成为死循环。

1. go语言递归函数很可能会产生一大堆的goroutine，也很可能会出现栈空间内存溢出问题。

## go语言递归实例

阶乘

```go
package main

import "fmt"

func a(n int) int {
	// 返回条件
	if n == 1 {
		return 1
	} else {
		// 自己调用自己
		return n * a(n-1)
	}
}

func main() {
	n := 5
	r := a(n)
	fmt.Printf("r: %v\n", r)
}

```

运行结果

```
r: 120

```

斐波那契数列

它的计算公式为f(n)=f(n-1)+f(n-2)且f(2)=f(1)=1

```go
package main

func f(n int) int {
	// 退出点判断
	if n == 1 || n == 2 {
		return 1
	}
	// 递归表达式
	return f(n-1) + f(n-2)
}

func main() {
	r := f(5)
	fmt.Printf("r: %v\n", r)
}

```

运行结果

```
r: 5
```

# golang defer语句

go语言中的defer语句会将其后面跟随的语句进行延迟处理。在defer归属的函数即将返回时，将延迟处理的语句按defer定义的逆序进行执行，也就是说，先被defer的语句最后被执行，最后被defer的语句，最先被执行。stack

## defer特性

1. 关键字 

defer 用于注册延迟调用。

1. 这些调用直到 

return 前才被执。因此，可以用来做资源清理。

1. 多个

defer语句，按先进后出的方式执行。

1. defer语句中的变量，在

defer声明时就决定了。

## defer用途

1. 关闭文件句柄

1. 锁资源释放

1. 数据库连接释放

## go语言defer语句实例

查看执行顺序

```go
func main() {
	fmt.Println("start")
	defer fmt.Println("step1")
	defer fmt.Println("step2")
	defer fmt.Println("step3")
	fmt.Println("end")
}

```

运行结果

```
start
end
step3
step2
step1

```

# golang init函数

golang有一个特殊的函数init函数，先于main函数执行，实现包级别的一些初始化操作。

## init函数的主要特点

- init函数先于main函数自动执行，不能被其他函数调用；

- init函数没有输入参数、返回值；

- 每个包可以有多个init函数；

- 包的每个源文件也可以有多个init函数，这点比较特殊；

- 同一个包的init执行顺序，golang没有明确定义，编程时要注意程序不要依赖这个执行顺序。

- 不同包的init函数按照包导入的依赖关系决定执行顺序。

## golang 初始化顺序

初始化顺序：变量初始化->init()->main()

实例

```go
package main

import "fmt"

var a int = initVar()

func init() {
	fmt.Println("init2")
}

func init() {
	fmt.Println("init")
}

func initVar() int {
	fmt.Println("init var...")
	return 100
}

func main() {
	fmt.Println("main...")
}

```

运行结果

```
init var...
init2
init
main...
```

# golang指针

Go语言中的函数传参都是值拷贝，当我们想要修改某个变量的时候，我们可以创建一个指向该变量地址的指针变量。传递数据使用指针，而无须拷贝数据。

类型指针不能进行偏移和运算。

Go语言中的指针操作非常简单，只需要记住两个符号：&（取地址）和*（根据地址取值）。

### 指针地址和指针类型

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用&字符放在变量前面对变量进行取地址操作。 Go语言中的值类型（int、float、bool、string、array、struct）都有对应的指针类型，如：*int、*int64、*string等。

## 指针语法

一个指针变量指向了一个值的内存地址。（也就是我们声明了一个指针之后，可以像变量赋值一样，把一个值的内存地址放入到指针当中。）

类似于变量和常量，在使用指针前你需要声明指针。指针声明格式如下：

```go
var var_name *var-type

```

var-type ：为指针类型

var_name ：为指针变量名

* ：用于指定变量是作为一个指针。

## 指针声明实例

```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */

```

## 指针使用实例

```go
package main

import "fmt"

func main() {
	var a int= 20   /* 声明实际变量 */
	var ip *int        /* 声明指针变量 */
	ip = &a  /* 指针变量的存储地址 */
	fmt.Printf("a 变量的地址是: %x\n", &a  )
	/* 指针变量的存储地址 */
	fmt.Printf("ip 变量储存的指针地址: %x\n", ip )
	/* 使用指针访问值 */
	fmt.Printf("*ip 变量的值: %d\n", *ip )
}

```

运行结果

```
a 变量的地址是: c00000a0a8
ip 变量储存的指针地址: c00000a0a8
*ip 变量的值: 20

```

# golang指向数组的指针

## 定义语法

```go
var ptr [MAX]*int; 表示数组里面的元素的类型是指针类型

```

## 实例演示

```go
package main

import "fmt"

const MAX int = 3

func main() {
	a := []int{ 1, 3, 5}
	var i int
	var ptr [MAX]*int;
	fmt.Println(ptr)   //这个打印出来是[<nil> <nil> <nil>]
	for  i = 0; i < MAX; i++ {
		ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
	}
	for  i = 0; i < MAX; i++ {
		fmt.Printf("a[%d] = %d\n", i,*ptr[i] )   //*ptr[i]就是打印出相关指针的值了。
	}
}

```

运行结果

```
[<nil> <nil> <nil>]
a[0] = 1
a[1] = 3
a[2] = 5

```

​                            

# golang类型定义和类型别名

在介绍结构体之前，我们先来看看什么是类型定义和类型别名。

# go语言类型定义

类型定义的语法

```go
type NewType Type

```



实例

```go
package main

import "fmt"

func main() {
	// 类型定义
	type MyInt int
	// i 为MyInt类型
	var i MyInt
	i = 100
	fmt.Printf("i: %v i: %T\n", i, i)
}

```



运行结果

```
i: 100 i: main.MyInt

```



## go语言类型别名

类型别名的语法

```go
type NewType = Type

```



实例

```go
package main

import "fmt"

func main() {
	// 类型别名定义
	type MyInt2 = int
	// i 其实还是int类型
	var i MyInt2
	i = 100
	fmt.Printf("i: %v i: %T\n", i, i)
}

```



## go语言类型定义和类型别名的区别

1. 类型定义相当于定义了一个全新的类型，与之前的类型不同；但是类型别名并没有定义一个新的类型，而是使用一个别名来替换之前的类型

1. 类型别名只会在代码中存在，在编译完成之后并不会存在该别名

1. 因为类型别名和原来的类型是一致的，所以原来类型所拥有的方法，类型别名中也可以调用，但是如果是重新定义的一个类型，那么不可以调用之前的任何方法                    

# golang 结构体

go语言没有面向对象的概念了，但是可以使用结构体来实现，面向对象编程的一些特性，例如：继承、组合等特性。

## go语言结构体的定义

上一节我们介绍了类型定义，结构体的定义和类型定义类似，只不过多了一个struct关键字，语法结构如下：

```go
type struct_variable_type struct {
   member definition;
   member definition;
   ...
   member definition;
}

```

type：结构体定义关键字

struct_variable_type：结构体类型名称

struct：结构体定义关键字

member definition;：成员定义

实例

下面我们定义一个人的结构体Person

```go
type Person struct {
    id    int
    name  string
    age   int
    email string
}

```

以上我们定义一个Person结构体，有四个成员，来描述一个Person的信息。

形同类型的可以合并到一行，例如：

```go
type Person struct {
    id, age     int
    name, email string
}

```

## 声明一个结构体变量

声明一个结构体变量和声明一个普通变量相同，例如：

```go
var tom Person
fmt.Printf("tom: %v\n", tom)
kite := Person{}
fmt.Printf("kite: %v\n", kite)

```

也行结果

```go
tom: {0 0  }
kite: {0 0  }

```

结构体成员，在没有赋值之前都是零值。

## 访问结构体成员

可以使用点运算符(.)，来访问结构体成员，例如：

```go
package main

import "fmt"

func main() {
	type Person struct {
		id, age     int
		name, email string
	}

	var tom Person
	tom.id = 1
	tom.name = "tom"
	tom.age = 20
	tom.email = "tom@gmail.com"
	fmt.Printf("tom: %v\n", tom)
}

```

运行结果如下

```go
tom: {1 20 tom tom@gmail.com}

```

## 匿名结构体

如果结构体是临时使用，可以不用起名字，直接使用，例如：

```go
package main

import "fmt"

func main() {
	var dog struct {
		id   int
		name string
	}
	dog.id = 1
	dog.name = "花花"
	fmt.Printf("dog: %v\n", dog)
}

```

# golang结构体的初始化

未初始化的结构体，成员都是零值  int 0 float 0.0 bool false string nil nil

实例

```go
package main

import "fmt"

func main() {
	type Person struct {
		id, age     int
		name, email string
	}

	var tom Person
	fmt.Printf("tom: %v\n", tom)
}

```

运行结果

```
tom: {0 0 "" ""}

```

## 使用键值对对结构体进行初始化

实例

```go
package main

import "fmt"

func main() {
	type Person struct {
		id, age     int
		name, email string
	}

	kite := Person{
		id:    1,
		name:  "kite",
		age:   20,
		email: "kite@gmail.com",
	}
	fmt.Printf("kite: %v\n", kite)
}

```

运行结果

```
kite: {1 20 kite kite@gmail.com}

```

### 使用值的列表初始化

实例

```go
package main

import "fmt"

func main() {
	type Person struct {
		id, age     int
		name, email string
	}

	kite := Person{
		1,
		20,
		"kite",
		"kite@gmail.com",
	}
	fmt.Printf("kite: %v\n", kite)
}

```

运行结果

```
kite: {1 20 kite kite@gmail.com}

```

> 
注意：

必须初始化结构体的所有字段。
初始值的填充顺序必须与字段在结构体中的声明顺序一致。
该方式不能和键值初始化方式混用。



## 部分成员初始化

用不到的成员，可以不进行初始化

```go
package main

import "fmt"

func main() {
	type Person struct {
		id, age     int
		name, email string
	}

	kite := Person{
		id:   1,
		name: "kite",
	}
	fmt.Printf("kite: %v\n", kite)
}

```

运行结果

```
kite: {1 0 kite "" }
```

# golang结构体指针

结构体指针和普通的变量指针相同，我先来回顾一下普通变量的指针，例如：

```go
package main

import "fmt"

func main() {
	var name string
	name = "tom"
    // p_name 指针类型
	var p_name *string
    // &name 取name地址
	p_name = &name
	fmt.Printf("name: %v\n", name)
    // 输出指针地址
	fmt.Printf("p_name: %v\n", p_name)
    // 输出指针指向的内容值
	fmt.Printf("*p_name: %v\n", *p_name)
}

```

运行结果

```
name: tom
p_name: 0xc00010e120
*p_name: tom

```

## go结构体指针

实例

```go
package main

import "fmt"

func main() {
	type Person struct {
		id   int
		name string
	}

	var tom = Person{1, "tom"}

	var p_person *Person
	p_person = &tom
	fmt.Printf("tom: %v\n", tom)
	fmt.Printf("p_person: %p\n", p_person)
	fmt.Printf("*p_person: %v\n", *p_person)
}

```

运行结果

```
tom: {1 tom}
p_person: 0xc000004078
*p_person: {1 tom}

```

## 使用new关键字创建结构体指针

我们还可以通过使用new关键字对结构体进行实例化，得到的是结构体的地址，例如：

```go
package main

import "fmt"

func main() {
	type Person struct {
		id   int
		name string
	}

	var p_person = new(Person)
	fmt.Printf("p_person: %T\n", p_person)
}

```

运行结果

```
p_person: *main.Person

```

从运行结果，我们发现p_person为指针类型

## 访问结构体指针成员

访问结构体指针成员，也使用点运算符(.)，例如：

```go
package main

import "fmt"

func main() {
	type Person struct {
		id   int
		name string
	}

	var p_person = new(Person)
	fmt.Printf("p_person: %T\n", p_person)

	p_person.id = 1
	p_person.name = "tom"
	fmt.Printf("*p_person: %v\n", *p_person)
}

```

运行结果

```
p_person: *main.Person
*p_person: {1 tom}

```

# golang结构体作为函数参数

go结构体可以像普通变量一样，作为函数的参数，传递给函数，这里分为两种情况：

1. 直接传递结构体，这是是一个副本（拷贝），在函数内部不会改变外面结构体内容。

1. 传递结构体指针，这时在函数内部，能够改变外部结构体内容。

## 直接传递结构体

实例

```go
package main

import "fmt"

type Person struct {
	id   int
	name string
}

func showPerson(person Person) {
	person.id = 1
	person.name = "kite"
	fmt.Printf("person: %v\n", person)
}

func main() {
	person := Person{1, "tom"}
	fmt.Printf("person: %v\n", person)
	fmt.Println("----------------")
	showPerson(person)
	fmt.Println("----------------")
	fmt.Printf("person: %v\n", person)
}

```

运行结果

```
person: {1 tom}
----------------
person: {1 kite}
----------------
person: {1 tom}

```

从运行结果可以看出，函数内部改变了结构体内容，函数外面并没有被改变。

## 传递结构体指针

实例

```go
package main

import "fmt"

type Person struct {
	id   int
	name string
}

func showPerson(person *Person) {
	person.id = 1
	person.name = "kite"
	fmt.Printf("person: %v\n", person)
}

func main() {
	person := Person{1, "tom"}
	fmt.Printf("person: %v\n", person)
	fmt.Println("----------------")
	showPerson(&person)
	fmt.Println("----------------")
	fmt.Printf("person: %v\n", person)
}

```

运行结果

```
person: {1 tom}
----------------
person: &{1 kite}
----------------
person: {1 kite}

```

# golang嵌套结构体

go语言没有面向对象编程思想，也没有继承关系，但是可以通过结构体嵌套来实现这种效果。

下面通过实例演示如何实现结构体嵌套，加入有一个人Person结构体，这个人还养了一个宠物Dog结构体。

下面我们来看一下：

Dog结构体

```go
type Dog struct {
	name  string
	color string
	age   int
}

```

Person结构体

```go
type person struct {
	dog  Dog
	name string
	age  int
}

```

访问它们

```go
package main

import "fmt"

type Dog struct {
	name  string
	color string
	age   int
}

type person struct {
	dog  Dog
	name string
	age  int
}

func main() {
	var tom person
	tom.dog.name = "花花"
	tom.dog.color = "黑白花"
	tom.dog.age = 2

	tom.name = "tom"
	tom.age = 20

	fmt.Printf("tom: %v\n", tom)
}

```

运行结果

```
tom: {{花花 黑白花 2} tom 20}
```



​                            

# golang方法

go语言没有面向对象的特性，也没有类对象的概念。但是，可以使用结构体来模拟这些特性，我们都知道面向对象里面有类方法等概念。我们也可以声明一些方法，属于某个结构体。

## go语言方法的语法

Go中的方法，是一种特殊的函数，定义于struct之上(与struct关联、绑定)，被称为struct的接受者(receiver)。

通俗的讲，方法就是有接收者的函数。

语法格式如下：

```go
type mytype struct{}

func (recv mytype) my_method(para) return_type {}
func (recv *mytype) my_method(para) return_type {}

```



mytype：定义一个结构体

recv：接受该方法的结构体(receiver)

my_method：方法名称

para：参数列表

return_type：返回值类型

从语法格式可以看出，一个方法和一个函数非常相似，多了一个接受类型。

实例

```go
package main

import "fmt"

type Person struct {
	name string
}

func (per Person) eat() {
	fmt.Println(per.name + " eating....")
}

func (per Person) sleep() {
	fmt.Println(per.name + " sleep....")
}

func main() {
	var per Person
	per.name = "tom"
	per.eat()
	per.sleep()
}

```



运行结果

```
tom eating....
tom sleep....

```



## go语言方法的注意事项

1. 方法的receiver type并非一定要是struct类型，type定义的类型别名、slice、map、channel、func类型等都可以。

1. struct结合它的方法就等价于面向对象中的类。只不过struct可以和它的方法分开，并非一定要属于同一个文件，但必须属于同一个包。

1. 方法有两种接收类型：

(T Type)和

(T *Type)，它们之间有区别。

1. 方法就是函数，所以Go中没有方法重载(overload)的说法，也就是说同一个类型中的所有方法名必须都唯一。

1. 如果receiver是一个指针类型，则会自动解除引用。

1. 方法和type是分开的，意味着实例的行为(behavior)和数据存储(field)是分开的，但是它们通过receiver建立起关联关系。



# golang接口

接口像是一个公司里面的领导，他会定义一些通用规范，只设计规范，而不实现规范。

go语言的接口，是一种新的类型定义，它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。

语法格式和方法非常类似。

## 接口的语法格式

```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}

```

在接口定义中定义，若干个空方法。这些方法都具有通用性。

## 接口实例

下面我定义一个USB接口，有读read和写write两个方法，再定义一个电脑Computer和一个手机Mobile来实现这个接口。

USB接口

```go
type USB interface {
	read()
	write()
}

```

Computer结构体

```go
type Computer struct {
}

```

Mobile结构体

```go
type Mobile struct {
}

```

Computer实现USB接口方法

```go
func (c Computer) read() {
	fmt.Println("computer read...")
}

func (c Computer) write() {
	fmt.Println("computer write...")
}

```

Mobile实现USB接口方法

```go
func (c Mobile) read() {
	fmt.Println("mobile read...")
}

func (c Mobile) write() {
	fmt.Println("mobile write...")
}

```

测试

```go
func main() {
	c := Computer{}
	m := Mobile{}

	c.read()
	c.write()
	m.read()
	m.write()
}

```

运行结果

```go
func main() {
	c := Computer{}
	m := Mobile{}

	c.read()
	c.write()
	m.read()
	m.write()
}

```

## 实现接口必须实现接口中的所有方法

下面我们定义一个OpenClose接口，里面有两个方法open和close，定义个Door结构体，实现其中一个方法。

```go
package main

import "fmt"

type OpenClose interface {
	open()
	close()
}

type Door struct {
}

func (d Door) open() {
	fmt.Println("open door...")
}

func main() {
	var oc OpenClose
	oc = Door{} // 这里编译错误，提示只实现了一个接口
}

```

​          

# golang接口值类型接收者和指针类型接收者

这个话题，本质上和方法的值类型接收者和指针类型接收者，的思考方法是一样的，值接收者是一个拷贝，是一个副本，而指针接收者，传递的是指针。

## 实例演示

定义一个Pet接口

```go
type Pet interface {
	eat()
}

```

定义一个Dog结构体

```
type Dog struct {
	name string
}

```

实现Pet接口（接收者是值类型）

```go
func (dog Dog) eat() {
	fmt.Printf("dog: %p\n", &dog)
	fmt.Println("dog eat..")
	dog.name = "黑黑"
}

```

测试

```go
func main() {
	dog := Dog{name: "花花"}
	fmt.Printf("dog: %p\n", &dog)
	dog.eat()
	fmt.Printf("dog: %v\n", dog)
}

```

运行结果

```
dog: 0xc000046240
dog: 0xc000046250
dog eat..
dog: {花花}

```

从运行结果，我们看出dog的地址变了，说明是复制了一份，dog的name没有变说明，外面的dog变量没有被改变。

将Pet接口改为指针接收者

```go
func (dog *Dog) eat() {
	fmt.Printf("dog: %p\n", dog)
	fmt.Println("dog eat..")
	dog.name = "黑黑"
}

```

再测试

```go
func main() {
	dog := &Dog{name: "花花"}
	fmt.Printf("dog: %p\n", dog)
	dog.eat()
	fmt.Printf("dog: %v\n", dog)
}

```

运行结果

```go
dog: 0xc00008c230
dog: 0xc00008c230
dog eat..
dog: &{黑黑}

```

​          