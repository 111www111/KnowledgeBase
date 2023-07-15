# golang并发编程之协程

Golang 中的并发是函数相互独立运行的能力。Goroutines 是并发运行的函数。Golang 提供了 Goroutines 作为并发处理操作的一种方式。

创建一个协程非常简单，就是在一个任务函数前面添加一个go关键字：

```go
go task()

```

## 实例1

```go
package main

import (
	"fmt"
	"time"
)

func show(msg string) {
	for i := 1; i < 5; i++ {
		fmt.Printf("msg: %v\n", msg)
		time.Sleep(time.Millisecond * 100)
	}
}

func main() {
	go show("java")
	show("golang") // 在main协程中执行，如果它前面也添加go，程序没有输出
	fmt.Println("end...")
}

```

> 
查看 go 关键字去掉的运行效果


## 实例2

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"time"
)

func responseSize(url string) {
	fmt.Println("Step1: ", url)
	response, err := http.Get(url)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("Step2: ", url)
	defer response.Body.Close()

	fmt.Println("Step3: ", url)
	body, err := ioutil.ReadAll(response.Body)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Step4: ", len(body))
}

func main() {
	go responseSize("https://www.abc.com")
	go responseSize("https://baidu.com")
	go responseSize("https://jd.com")
	time.Sleep(10 * time.Second)
}
```

# golang并发编程之通道channel

Go 提供了一种称为通道的机制，用于在 goroutine 之间共享数据。当您作为 goroutine 执行并发活动时，需要在 goroutine 之间共享资源或数据，通道充当 goroutine 之间的管道（管道）并提供一种机制来保证同步交换。

需要在声明通道时指定数据类型。我们可以共享内置、命名、结构和引用类型的值和指针。数据在通道上传递：在任何给定时间只有一个 goroutine 可以访问数据项：因此按照设计不会发生数据竞争。

根据数据交换的行为，有两种类型的通道：无缓冲通道和缓冲通道。无缓冲通道用于执行 goroutine 之间的同步通信，而缓冲通道用于执行异步通信。无缓冲通道保证在发送和接收发生的瞬间执行两个 goroutine 之间的交换。缓冲通道没有这样的保证。

通道由 make 函数创建，该函数指定 chan 关键字和通道的元素类型。

### 这是创建无缓冲和缓冲通道的代码块：

### 语法

```
Unbuffered := make(chan int) // 整型无缓冲通道
buffered := make(chan int, 10)	// 整型有缓冲通道

```

使用内置函数make创建无缓冲和缓冲通道。make的第一个参数需要关键字chan，然后是通道允许交换的数据类型。

### 这是将值发送到通道的代码块需要使用 <- 运算符：

### 语法

```
goroutine1 := make(chan string, 5) // 字符串缓冲通道
goroutine1 <- "Australia" // 通过通道发送字符串

```

一个包含 5 个值的缓冲区的字符串类型的 goroutine1 通道。然后我们通过通道发送字符串“Australia”。

### 这是从通道接收值的代码块：

### 语法

```
data := <-goroutine1 // 从通道接收字符串

```

<- 运算符附加到通道变量（goroutine1）的左侧，以接收来自通道的值。

## 无缓冲通道

在无缓冲通道中，在接收到任何值之前没有能力保存它。在这种类型的通道中，发送和接收 goroutine 在任何发送或接收操作完成之前的同一时刻都准备就绪。如果两个 goroutine 没有在同一时刻准备好，则通道会让执行其各自发送或接收操作的 goroutine 首先等待。同步是通道上发送和接收之间交互的基础。没有另一个就不可能发生。

## 缓冲通道

在缓冲通道中，有能力在接收到一个或多个值之前保存它们。在这种类型的通道中，不要强制 goroutine 在同一时刻准备好执行发送和接收。当发送或接收阻塞时也有不同的条件。只有当通道中没有要接收的值时，接收才会阻塞。仅当没有可用缓冲区来放置正在发送的值时，发送才会阻塞。

## 通道的发送和接收特性

1. 对于同一个通道，发送操作之间是互斥的，接收操作之间也是互斥的。

1. 发送操作和接收操作中对元素值的处理都是不可分割的。

1. 发送操作在完全完成之前会被阻塞。接收操作也是如此。

### 实例

```
package main

import (
	"fmt"
	"math/rand"
	"time"
)

// 创建int类型通道，只能传入int类型值
var values = make(chan int)

func send() {
	rand.Seed(time.Now().UnixNano())
	value := rand.Intn(10)
	fmt.Printf("send: %v\n", value)
	// time.Sleep(time.Second * 5)
	values <- value
}

func main() {
	// 从通道接收值
	defer close(values)
	go send()
	fmt.Println("wait...")
	value := <-values
	fmt.Printf("receive: %v\n", value)
	fmt.Println("end...")
}

```

# golang并发编程之WaitGroup实现同步

实例演示

查看添加WaitGroup和不添加WaitGroup的区别

```go
var wg sync.WaitGroup
 
func hello(i int) {
    defer wg.Done() // goroutine结束就登记-1
    fmt.Println("Hello Goroutine!", i)
}
func main() {
    for i := 0; i < 10; i++ {
        wg.Add(1) // 启动一个goroutine就登记+1
        go hello(i)
    }
    wg.Wait() // 等待所有登记的goroutine都结束
}
```

# golang并发编程之runtime包

runtime包里面定义了一些协程管理相关的api

## runtime.Gosched()

让出CPU时间片，重新等待安排任务

```go
package main

import (
	"fmt"
	"runtime"
)

func show(s string) {
	for i := 0; i < 2; i++ {
		fmt.Println(s)
	}
}

func main() {
	go show("java")
	// 主协程
	for i := 0; i < 2; i++ {
		// 切一下，再次分配任务
		runtime.Gosched() // 注释掉查看结果
		fmt.Println("golang")
	}
}

```

## runtime.Goexit()

退出当前协程

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func show() {
	for i := 0; i < 10; i++ {
		if i >= 5 {
			runtime.Goexit()
		}
		fmt.Printf("i: %v\n", i)
	}
}

func main() {
	go show()
	time.Sleep(time.Second)
}

```

## runtime.GOMAXPROCS

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func a() {
	for i := 1; i < 10; i++ {
		fmt.Println("A:", i)
	}
}

func b() {
	for i := 1; i < 10; i++ {
		fmt.Println("B:", i)
	}
}

func main() {
	fmt.Printf("runtime.NumCPU(): %v\n", runtime.NumCPU())
	runtime.GOMAXPROCS(2) // 修改为1查看效果
	go a()
	go b()
	time.Sleep(time.Second)
}
```

# golang并发编程之Mutex互斥锁实现同步

除了使用channel实现同步之外，还可以使用Mutex互斥锁的方式实现同步。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var m int = 100

var lock sync.Mutex

var wt sync.WaitGroup

func add() {
	defer wt.Done()
	lock.Lock()
	m += 1
	time.Sleep(time.Millisecond * 10)
	lock.Unlock()
}

func sub() {
	defer wt.Done()
	lock.Lock()
	time.Sleep(time.Millisecond * 2)
	m -= 1
	lock.Unlock()
}

func main() {

	for i := 0; i < 100; i++ {
		go add()
		wt.Add(1)
		go sub()
		wt.Add(1)
	}

	wt.Wait()
	fmt.Printf("m: %v\n", m)
}

```

#                        

# golang并发编程之channel的遍历

## 方法1 for循环+if判断

```go
package main

import "fmt"

func main() {

	c := make(chan int)

	go func() {
		for i := 0; i < 10; i++ {
			c <- i
		}
		close(c)
	}()

	for {
		if data, ok := <-c; ok {
			fmt.Printf("data: %v\n", data)
		} else {
			break
		}
	} 
}

```



## 方法2 for range

```
package main

import "fmt"

func main() {

	c := make(chan int)

	go func() {
		for i := 0; i < 10; i++ {
			c <- i
		}
		close(c)
	}()

	for v := range c {
		fmt.Printf("v: %v\n", v)
	}
}


```



> 
注意：如果通道关闭，读多写少，没有了就是默认值，例如，int 就是0，如果没有关闭就会死锁。




# golang并发编程之select

1. select是Go中的一个控制结构，类似于

switch语句，用于处理异步IO操作。select会监听case语句中channel的读写操作，当case中channel读写操作为非阻塞状态（即能读写）时，将会触发相应的动作。

> 
select中的case语句必须是一个channel操作
select中的default子句总是可运行的。


1. 如果有多个

case都可以运行，select会随机公平地选出一个执行，其他不会执行。

1. 如果没有可运行的

case语句，且有default语句，那么就会执行default的动作。

1. 如果没有可运行的

case语句，且没有default语句，select将阻塞，直到某个case通信可以运行

## 实例

```go
package main

import (
	"fmt"
	"time"
)

var chanInt = make(chan int)
var chanStr = make(chan string)

func main() {

	go func() {
		chanInt <- 100
		chanStr <- "hello"
		close(chanInt)
		close(chanStr)
	}()

	for {
		select {
		case r := <-chanInt:
			fmt.Printf("chanInt: %v\n", r)
		case r := <-chanStr:
			fmt.Printf("chanStr: %v\n", r)
		default:
			fmt.Println("default...")
		}
		time.Sleep(time.Second)
	}
}
```

​           

# golang并发编程之Timer

Timer顾名思义，就是定时器的意思，可以实现一些定时操作，内部也是通过channel来实现的。

实例演示

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timer1 := time.NewTimer(time.Second * 2)
	t1 := time.Now()
	fmt.Printf("t1:%v\n", t1)

	t2 := <-timer1.C
	fmt.Printf("t2:%v\n", t2)

	//如果只是想单纯的等待的话，可以使用 time.Sleep 来实现
	timer2 := time.NewTimer(time.Second * 2)
	<-timer2.C
	fmt.Println("2s后")

	time.Sleep(time.Second * 2)
	fmt.Println("再一次2s后")

	<-time.After(time.Second * 2) //time.After函数的返回值是chan Time
	fmt.Println("再再一次2s后")

	timer3 := time.NewTimer(time.Second)
	go func() {
		<-timer3.C
		fmt.Println("Timer 3 expired")
	}()

	stop := timer3.Stop() //停止定时器
	////阻止timer事件发生，当该函数执行后，timer计时器停止，相应的事件不再执行
	if stop {
		fmt.Println("Timer 3 stopped")
	}

	fmt.Println("before")
	timer4 := time.NewTimer(time.Second * 5) //原来设置5s
	timer4.Reset(time.Second * 1)            //重新设置时间,即修改NewTimer的时间
	<-timer4.C
	fmt.Println("after")
}

```

​         

# golang并发编程之Ticker

Timer只执行一次，Ticker可以周期的执行。

实例

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	ticker := time.NewTicker(time.Second)
	counter := 1
	for _ = range ticker.C {
		fmt.Println("ticker 1")
		counter++
		if counter >= 5 {
			break
		}
	}
	ticker.Stop()
}

```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	chanInt := make(chan int)

	ticker := time.NewTicker(time.Second)

	go func() {
		for _ = range ticker.C {
			select {
			case chanInt <- 1:
			case chanInt <- 2:
			case chanInt <- 3:
			}
		}
	}()

	sum := 0
	for v := range chanInt {
		fmt.Printf("接收: %v\n", v)
		sum += v
		if sum >= 10 {
			fmt.Printf("sum: %v\n", sum)
			break
		}
	}
}

```

# golang并发编程之原子变量的引入

先看一个实例

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var i = 100

var lock sync.Mutex

func add() {
	lock.Lock()
	i++
	lock.Unlock()
}

func sub() {
	lock.Lock()
	i--
	lock.Unlock()
}

func main() {
	for i := 0; i < 100; i++ {
		go add()
		go sub()
	}

	time.Sleep(time.Second * 3)

	fmt.Printf("i: %v\n", i)
}

```

> 
这是我们之前的写法，使用锁实现协程的同步


下面使用原子操作

```go
package main

import (
	"fmt"
	"sync/atomic"
	"time"
)

var i int32 = 100

func add() {
	atomic.AddInt32(&i, 1)
}

func sub() {
	atomic.AddInt32(&i, -1)
}

func main() {
	for i := 0; i < 100; i++ {
		go add()
		go sub()
	}

	time.Sleep(time.Second * 3)
	fmt.Printf("i: %v\n", i)
}
```

#                             

# golang并发编程之原子操作详解

atomic 提供的原子操作能够确保任一时刻只有一个goroutine对变量进行操作，善用 atomic 能够避免程序中出现大量的锁操作。

atomic常见操作有：

- 增减

- 载入 read

- 比较并交换 cas

- 交换

- 存储 write

下面将分别介绍这些操作。

## 增减操作

atomic 包中提供了如下以Add为前缀的增减操作:

```go
- func AddInt32(addr *int32, delta int32) (new int32)
- func AddInt64(addr *int64, delta int64) (new int64)
- func AddUint32(addr *uint32, delta uint32) (new uint32)
- func AddUint64(addr *uint64, delta uint64) (new uint64)
- func AddUintptr(addr *uintptr, delta uintptr) (new uintptr)

```



## 载入操作

atomic 包中提供了如下以Load为前缀的增减操作:

```go
- func LoadInt32(addr *int32) (val int32)
- func LoadInt64(addr *int64) (val int64)
- func LoadPointer(addr *unsafe.Pointer) (val unsafe.Pointer)
- func LoadUint32(addr *uint32) (val uint32)
- func LoadUint64(addr *uint64) (val uint64)
- func LoadUintptr(addr *uintptr) (val uintptr)

```



> 
载入操作能够保证原子的读变量的值，当读取的时候，任何其他CPU操作都无法对该变量进行读写，其实现机制受到底层硬件的支持。




## 比较并交换

该操作简称 CAS(Compare And Swap)。 这类操作的前缀为 CompareAndSwap :

```go
- func CompareAndSwapInt32(addr *int32, old, new int32) (swapped bool)
- func CompareAndSwapInt64(addr *int64, old, new int64) (swapped bool)
- func CompareAndSwapPointer(addr *unsafe.Pointer, old, new unsafe.Pointer) (swapped bool)
- func CompareAndSwapUint32(addr *uint32, old, new uint32) (swapped bool)
- func CompareAndSwapUint64(addr *uint64, old, new uint64) (swapped bool)
- func CompareAndSwapUintptr(addr *uintptr, old, new uintptr) (swapped bool)

```



> 
该操作在进行交换前首先确保变量的值未被更改，即仍然保持参数 old 所记录的值，满足此前提下才进行交换操作。CAS的做法类似操作数据库时常见的乐观锁机制。




## 交换

此类操作的前缀为 Swap：

```go
- func SwapInt32(addr *int32, new int32) (old int32)
- func SwapInt64(addr *int64, new int64) (old int64)
- func SwapPointer(addr *unsafe.Pointer, new unsafe.Pointer) (old unsafe.Pointer)
- func SwapUint32(addr *uint32, new uint32) (old uint32)
- func SwapUint64(addr *uint64, new uint64) (old uint64)
- func SwapUintptr(addr *uintptr, new uintptr) (old uintptr)

```



> 
相对于CAS，明显此类操作更为暴力直接，并不管变量的旧值是否被改变，直接赋予新值然后返回背替换的值。




## 存储

此类操作的前缀为 Store：

```go
- func StoreInt32(addr *int32, val int32)
- func StoreInt64(addr *int64, val int64)
- func StorePointer(addr *unsafe.Pointer, val unsafe.Pointer)
- func StoreUint32(addr *uint32, val uint32)
- func StoreUint64(addr *uint64, val uint64)
- func StoreUintptr(addr *uintptr, val uintptr)

```



> 
此类操作确保了写变量的原子性，避免其他操作读到了修改变量过程中的脏数据。


​                    