Go设置ENV

设置GO111MODULE
go env -w GO111MODULE=on

设置GOPROXY
主要是设置Go模块代理，作用是使Go在后续拉取模块版本时直接使用镜像站点来拉取
阿里云：https://mirrors.aliyun.com/goproxy/
七牛云：https://goproxy.cn,direct
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/

#### 关联数组

map是Go内置关联数据类型（其他类型语言称为哈希或字典）

要创建一个空map，需要使用内建的make:make(map[key-type]val-type)

使用典型的make[key] = val 语法来设置键值对

使用例如Println来打印一个map讲会输出所有的键值对

使用name[key]来获取一个键的值

当对一个map调用内建的len时，返回的是键值对数目内建的delete可以从一个map中移除键值对

当从一个map中取值时，可选的第二返回值指示这个键是在这个map中，这可以用来消除不存在的键和零值，像0或者""而产生歧义

你也可以通过语法在同一行申明和初始化一个新的map

注意一个map在使用fmt.Println打印的时候，是以map[k:v k:v]的格式输出的

```go
package main

import "fmt"

func main(){
	var myMap1 map[string]string
	var myMap2 map[string]string

	myMap1 = make(map[string]string,10)

	myMap1["name"] = "吴中生"
	myMap1["age"] = "23"
	myMap1["address"] = "珠海"

	fmt.Println(myMap1)

	myMap2 = make(map[string]string)

	myMap2["name"] = "王进喜"
	myMap2["age"] = "233"
	myMap2["address"] = "北京"

	fmt.Println(myMap2)

	 myMap3 := map[string]string {
		"name":"王八蛋",
		"age":"666",
		"address":"中山",
	}
	fmt.Println(myMap3)

	 for k,v := range myMap3{
	 	fmt.Println("k:",k,"v:",v)
	 }
}

```

#### 变参函数

可变参数函数，可以用任意数量的参数调用

这个函数使用任意数目的int作为参数

变参函数使用常规的调用方式，除了参数比较特殊

如果你的slice已经有了多个值，想把它们作为变参使用，你要这样调用func(slice...)

```go
package main

import "fmt"

func sum(nums ...int){
	fmt.Print(nums," ")
	total:=0
	for _,num:=range nums{
		total+=num
	}
	fmt.Println(total)
}
func main(){
	sum(1,2)
	sum(1,2,3)

	nums:=[]int{1,2,3}
	sum(nums...)
}
```

#### 闭包

Go支持通过闭包来使用匿名函数。匿名函数在你想定义一个不需要命名的内联函数时很实用的。

这个intSeq函数返回另一个在intSeq函数体内定义的匿名函数。这个的函数使用闭包的方式隐藏变量i

我们在调用intSeq函数，将返回值(也是一个函数)赋给nextInt。这个函数的值包含了自己的值i，这样在每次调用nextInt时都会更新i的值

```go
package main

import "fmt"

func main(){
	nextInt:=intSeq()
	fmt.Println(nextInt())
	fmt.Println(nextInt())
	fmt.Println(nextInt())

	newInts:=intSeq()
	fmt.Println(newInts())
}

func intSeq() func() int {
	i:=0
	return func() int {
		i+=1
		return i
	}
}

```

#### 方法

Go支持在结构体类型中定义方法

这里的area方法有一个接收器类型react

可以为值类型或者指针类型的接收器定义方法。这里是一个值类型接收器的例子

这里我们调用上面为结构体定义的两个方法。

Go自动处理方法调用时的值和指针之间的转化，你可以使用指针来调用方法来避免在方法调用时产生一个拷贝，或者让方法能够改变接收的数据。

```go
package main

import "fmt"

type react struct {
	width, height int
}

func (r *react) area() int {
	return r.width * r.height
}
func (r react) perim() int {
	return 2*r.width * 2*r.height
}

func main() {
	r := react{
		width:  10,
		height: 20,
	}
	fmt.Println("area:",r.area())
	fmt.Println("perim:",r.perim())

	rp:=&r
	fmt.Println("area:",rp.area())
	fmt.Println("perim:",rp.perim())
}

```

#### 接口

接口是方法特征的命名集合

要在Go中实现一个接口，我们只需要实现接口中所有方法

如果一个变量是一个接口类型，那么我们可以调用这个被命名的接口中的方法。

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	r := rect{width: 3, height: 3}
	c := circle{radius: 5}
	
	measure(r)
	measure(c)
}

type geometry interface {
	area() float64
	perim() float64
}

type rect struct {
	width, height float64
}

type circle struct {
	radius float64
}

func (r rect) area() float64 {
	return r.width * r.height
}

func (r rect) perim() float64 {
	return 2*r.width + 2*r.height
}

func (c circle) area() float64 {
	return math.Pi * c.radius * c.radius
}

func (c circle) perim() float64 {
	return 2 * math.Pi * c.radius
}

func measure(g geometry) {
	fmt.Println(g)
	fmt.Println(g.area())
	fmt.Println(g.perim())
}

```

#### 错误处理

Go语言使用一个独立的，明确的返回值来传递错误信息，这与使用异常的Java和Ruby以及C语言中常见的超重的单返回值/错误值相比，Go语言的处理方式能清楚的知道哪一个函数返回了错误，并能像调用那些没有出错的函数一样调用

按照惯例，错误通常是最后一个返回值并且是error类型，一个内建的接口

errors.New构造一个使用给定的错误信息的基本error值，返回错误值为nil代表没有错误

通过实现Error方法来自定义error类型是可以的，这里使用自定义错误类型来表示上面的参数错误

我们使用&argError语法来建立一个新的结构体，并提供了arg和prob这两个字段的值

#### 协程

Goroutinue是Go运行时管理的轻量级线程，主线程结束时，协程或被中断，需要有效的阻塞机制

数据竞争

多个线程同时对同一个内存空间进行写操纵会导致数据竞争，sunc包可以解决此问题，互斥锁Mutex，但在Go中不常用，因为Go中有更高效的信道channel来解决这个问题

#### Channel

声明与存储

channel，官方翻译为信道，是一种带有类型的管道，引用类型，使用前需要make(Type,(缓冲区容量))

不带缓冲区的管道必须结合协程使用

可以查看长度len(channel)或容量cap(channel)

存入:channel <- value

取出:value,(ok) <- channel

丢弃: <- channel

#### 随机数

注意："math/rand" 包生成的随机数是容易预测的，安全等级要求比较高的随机数参考"crypto/rand"包，

Rand.Seed(int64)

Unix时间：UTC(世界时间标准)1970年1月1日0时0分0秒起至现在所 经过的所有时间

Go获取Unix纳秒:time.Now().UnixNano()

#### 字符串类型转换

fmt包

| fmt.Sprintf(fmtstr,...)    | 其他类型转字符串，用法同fmt.Printf()    |
| -------------------------- | --------------------------------------- |
| Fmt.Sscanf(str,fmtstr,...) | 字符串解析为其他类型，用法同fmt.Scanf() |

strconv包

| Strconv.ltoa()      | 整数转字符串     |
| ------------------- | ---------------- |
| Strconv.Atoi()      | 字符串转整数     |
| Strconv.Format...() | 基本类型转字符串 |

#### 字符串常见操作

| Strings.Contains(str,substr) bool      | 是否存在子串                            |
| -------------------------------------- | --------------------------------------- |
| Strings.Count(str,substr) int          | 子串出现的次数，没有返回-1              |
| strings.Index(str,substr int)          | 子串第一次出现的index，没有则返回-1     |
| Strings.LastIndex(str,substr) int      | 子串最后一次出现时的index，没有则返回-1 |
| strings.Replace(str,old,new,n) string  | 替换前m个子串，n<0为全部                |
| strings.RaplaceAll(str,old,new) string | 替换全部子串                            |
| strings.Repeat(str,n) string           | 重复n次str                              |
| strings.HasPerfix(str,perstr) bool     | 是否存在前缀                            |
| strings.HasSuffix(str,substr) bool     | 是否存在后缀                            |

