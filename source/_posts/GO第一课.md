---
title: GO第一课
date: 2019-11-12 14:19:42
tags: [Go]
---

[TOC]

## 安装与配置

### 换源

1.13 以上的 go:支持全局 go 代理
`go env -w GOPROXY=https://goproxy.cn,direct`

1. 换源以后,所有包都会走代理, 有些包是自己引入的第三方或者自建的, 走代理的话有时会超时, 导致安装不了, 所以可以过滤不想走代理的域名(也叫私有域名)`go env -w GOPRIVATE="*.gitlab.com"`

### 升级 GO, 两种方法

1. `brew upgrade go`有点慢
2. 官网直接下载安装包, 会覆盖安装

### vscode 配置

1. 下载 go 插件
2. cmd+shift+P, 框里输入 go: install/update, 全选安装(把所有用到的工具都装好)
3. 配置里加上

   ```json
   "go.formatTool": "goimports",
   "prettier.disableLanguages": [
     "vue",
     "go"
   ],
   "go.useLanguageServer": true,
   "editor.formatOnSave": true,
   "editor.formatOnSaveTimeout": 2500,
   "go.coverOnTestPackage": false,
   "go.lintOnSave": "off",
   "go.lintTool": "golangci-lint",
   "go.vetOnSave": "off",
   "gopls": {
       "completeUnimported": true  // 不导入就可以用一些内置包的api, 然后就可以自动导入了, 比如直接fmt.就会有提示, 然后保存就会自动把import语句加进去
   },
   "go.buildOnSave": "off",
   ```

4. debug 运行服务时, vscode 为例 配置 json

   ```json
   {
     // 使用 IntelliSense 了解相关属性。
     // 悬停以查看现有属性的描述。
     // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Launch",
         "type": "go",
         "request": "launch",
         "mode": "debug",
         "program": "${fileDirname}",
         "env": {},
         "args": ["-env", "dev"] // 这个很重要 不同的项目有不同的参数约定
       }
     ]
   }
   ```

## GO 的基础

1.  发请求的时候, 如何打断点(或者可以试试):
    1. debug 模式把服务跑起来
2.  跳转不畅
    1. 包结构构建起来就没问题了
3.  \* & 地址传值… 1.
4.  继承, 组合 1.
5.  数据类型
    1. `b bool := true`
    2. 数字类型
       1. int
          1. uint/int 8/16/32/64
          2. 这里有个小知识点: 有符号位的算法是: 第一位是符号位, 0 是正数, 1 是负数, 正数的值是其余位正常算, 负数的值是所有位取反后加一后的值, 所以举个例子, int8 的范围应该是: 01111111(+(2^7-1)=+127) ~ 10000000(-(2^7)=-128)
       2. float32 / float64 /complex 64/128
       3. 其他: byte≈uint8 rune≈int32 uint/int:32 位或者 64 位 uintptr(无符号整型存指针)
    3. 字符串,格式化输出
        * %v	按值的本来值输出
        * %+v	在 %v 基础上，对结构体字段名和值进行展开
        * %#v	输出 Go 语言语法格式的值
        * %T	输出 Go 语言语法格式的类型和值
        * %%	输出 % 本体
        * %b	整型以二进制方式显示
        * %o	整型以八进制方式显示
        * %d	整型以十进制方式显示
        * %x	整型以十六进制方式显示
        * %X	整型以十六进制、字母大写方式显示
        * %U	Unicode 字符
        * %f	浮点数
        * %p	指针，十六进制方式显示
        * %s: 普通字符串
        * %q: 引号包含字符串
        * %x, %o, %b: 十六进制，8进制，2进制
        * %t: bool值
    4. 其他类型(派生类型)
       1. 指针(pointer)
       2. 数组
          1. 必须相同类型
          2. `var arr [10]int`
          3. 赋值 `= [10]int{1,2,3,4}`
          4. 做函数参数: `([]int arr)`
        6. slice(切片)
          7. `var slice []int`
          1. make([]int, len, cap)函数创建切片,
          2. 赋值`=数组变量[10:20] `
          3. `arr[0:2]`

       3. struct(结构体)
          1. 定义: 不同类型数据集合(类似对象)
          2. 定义 type xxx struct {xxxxx xxxx}
          3. 使用 xx:=xxx {a,b...}
          4. 标签, 每行后可以有标签,通过反射可以拿到, 形如: `"the name of student"`;`\`json:"name"\``
       4. channel, 并发时多线程通信, `ch:=make(chan int)`
          ```text
            ch <- v    // 把 v 发送到通道 ch
            v := <-ch  // 从 ch 接收数据
            // 并把值赋给 v
          ```
       5. 函数
       7. interface (接口)
          1. 所有共性的方法定义到一起
          2. 类似类 class
          3. 方便理解

              ```go
                package main
     
                import "fmt"
                 
                type Person struct {
                    Name string
                    Age  int
                }
                
                func (p Person) String() string {
                    return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
                }
                 
                func main() {
                    a := Person{"Arthur Dent", 42}
                    z := Person{"Zaphod Beeblebrox", 9001}
                    fmt.Println(a, z)
                }
                 
              ```
       8. Map
          1. var countryCapitalMap map[string]string /_创建集合 _/
          2. countryCapitalMap = make(map[string]string)
6.  变量声明不赋值,默认值:

    1. 数值 0
    2. string""
    3. 布尔 false
    4. 指针/数组/map/chan/func/interface(比如 error) 都是 nil

7.  `:=`只能在函数体中出现; 左边一定要至少有一个未声明过的变量
8.  类型

    1. 值类型
       1. 变量指向值本身
       2. 例: 数值, 布尔值, 字符串这样的基本类型
       3. 值类型的变量在赋值给新变量的时候, 是拷贝了一个值
       4. 可以通过&xxx 来拿到内存地址, 值类型存在栈中(stack)
    2. 引用类型
       1. 存一些复杂的数据
       2. 变量存的是内存地址(指针)

9.  常量

    1. 只能是字符串 数字 布尔值
    2. 也可以用表达式,但是处理函数必须是内置函数
    3. 实现接口以后, 就可以被别的逻辑隐形调用, 也就是说, 有别的逻辑依赖这个接口, 新的结构实现这个接口以后 就可以直接使用已有的某些api(隐形支持了这个api的调用)

       ```go
       import "unsafe"
       const (
           a = "abc"
           b = len(a)
           c = unsafe.Sizeof(a) // 应该是用来查看占用内存大小的
       )
       ```

    3. iota 可被编译器修改的常量, 逐行编译的时候,会不停的变化, const 出现初始化为 0 每新出现一个常量声明 iota+=1

       ```go

       const (
       a = iota //0
       b //1
       c //2
       d = "ha" //独立值，iota += 1
       e //"ha" iota += 1
       f = 100 //iota +=1
       g //100 iota +=1
       h = iota //7,恢复计数
       i //8
       )

       ```

10. 关系运算符只能比较布尔值
11. 位运算符很有意思, 按位逐个比较产生最后的值(用的时候查就可以)
12. \* \&

    1.  \&返回变量内存地址
    2.  \*指针变量, 可解析地址为变量值

13. 条件语句: go 没有三目; select 类似 switch, 会随机执行一个 case
14. 函数 vs 方法: 一个方法就是包含了接受者的函数https://www.runoob.com/go/go-method.html
15. 类型转换
    1.  `type_name(expression)`

### 一些困惑

1. 为什么有些地方使用`_.e = xxx`
   1. _\_是用来抛弃值的_, 因为用不到, 但是返回了, 而且是第一位, go 要求只要取出来必须使用, 所以用来解决这个问题
2. 字符串格式化输出 `fmt.Printf("%v %v %v %q\n", i, f, b, s)`
3. struct
4. interface
5. main 包
6. 文件名, 文件夹名, 包名没有直接关系
7. 同一个文件夹下的文件只能同一个包名
8. 直接调函数名只能是当前包(暂时是所在文件夹)的方法
9. 包, 层,的概念
10. gin 和 MVC
11. gin 的 基本结构
12. 常用 api
13. fmt.print
    1. fmt.print("123\n") 等价于 fmt.println("123")
14. 标识符大写开头代表是公开(public)方法, 这种写法叫做导出(小写就私有 protected)

### 支持多返回值、支持命名返回值

```go
func sumProductDiff(i, j int) (int, int, int) {    // 多返回值

return i+j, i\*j, i-j

}

func Split(sum int) (x, y int) {    // 命名返回

x = sum \* 4 / 9

y = sum - x

return

}
```

作者：laigw
链接：https://www.jianshu.com/p/7d8c98e7dee6
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

如果方法是公开访问的，建议最好命名返回值，因为不命名返回值，虽然使得代码更加简洁了，但是会造成生成的文档可读性差。

### 不定参数

func myfunc (arg ...int) {}

arg ...int 告诉 Go 这个函数接受不定数量的参数。注意，这些参数的类型全部是 int。arg 是一个 int 的 slice

## 传值与传指针

// 简单的一个函数，实现了函数+1 的操作

func add1(a \*int) int {

*a = *a + 1    // 改变了 a 值

return \*a    // 返回新值

}

func main () {

x := 3

fmt.Println("x=", x)    // 输出"x=3"

x1 := add1(&x)

fmt.Println("x+1=", x1)    // 输出"x+1=4"

fmt.Println("x=", x)    // 输出"x=4"

}

这样，我们就达到了修改 x 的目的。

那么到底传指针有什么好处呢？

1）传指针使得多个函数能操作同一个对象；

2）传指针比较轻量级 (8bytes)，只是传内存地址，我们可以用指针传递体积大的结构体。如果用参数值传递的话, 在每次 copy 上面就会花费相对较多的系统开销（内存和时间）。所以当要传递大的结构体的时候，用指针是一个明智的选择。

3）Go 语言中 channel，slice，map 这三种类型的实现机制类似指针，所以可以直接传递，而不用取地址后传递指针。（注：若函数需改变 slice 的长度，则仍需要取地址传递指针）

作者：laigw
链接：https://www.jianshu.com/p/7d8c98e7dee6
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 函数作为值、类型

在 Go 中函数也是一种变量，我们可以通过 type 来定义它，它的类型就是所有拥有相同的参数，相同的返回值的一种类型。

type typeName func(input1 inputType1, input2 inputType2 [, ...]) (result1 resultType1 [, ...])

函数作为类型到底有什么好处呢？那就是可以把这个类型的函数当做值来传递。

type testInt func(int) bool    // 声明了一个函数类型

func isOdd(integer int) bool {

if integer%2 == 0 {

return false

}

return true

}

func isEven(integer int) bool {

if integer%2 == 0 {

return true

}

return false

}

// 声明的函数类型作为一个参数

func filter(slice []int, f testInt) []int {

var result []int

for \_, value := range slice {

if f(value) {

result = append(result, value)

}

return result

}

func main() {

slice := []int {1,2,3,4,5,6,7}

fmt.Println("slice=", slice)

odd := filter(slice, isOdd)    // 函数作为值传递

fmt.Println("odd elements of slice are:", odd)

even := filter(slice, isEven)    // 函数作为值传递

fmt.Println("even elements of slice are:", even)

}

函数当做值和类型在我们写一些通用接口的时候非常有用，通过上面例子我们看到 testInt 这个类型是一个函数类型，然后两个 filter 函数的参数和返回值与 testInt 类型是一样的，但是我们可以实现很多种的逻辑，这样使得我们的程序变得非常的灵活。

作者：laigw
链接：https://www.jianshu.com/p/7d8c98e7dee6
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

1. go module 才能模块化开发

`go mod init 项目名`

2. 单元测试

每个测试文件必须以 \_test.go 结尾，不然 go test 不能发现测试文件
每个测试文件必须导入 testing 包
功能测试函数必须以 Test 开头，然后一般接测试函数的名字，这个不强求

关于 go test 命令
go test [-c][-i] [build flags][packages] [flags for test binary]
参数解读
-c : 编译 go test 成为可执行的二进制文件，但是不运行测试。

-i : 安装测试包依赖的 package，但是不运行测试。

关于 build flags，调用 go help build，这些是编译运行过程中需要使用到的参数，一般设置为空

关于 packages，调用 go help packages，这些是关于包的管理，一般设置为空

关于 flags for test binary，调用 go help testflag，这些是 go test 过程中经常使用到的参数

-test.v : 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。

-test.run pattern: 只跑哪些单元测试用例

-test.bench patten: 只跑那些性能测试用例

-test.benchmem : 是否在性能测试的时候输出内存情况

-test.benchtime t : 性能测试运行的时间，默认是 1s

-test.cpuprofile cpu.out : 是否输出 cpu 性能分析文件

-test.memprofile mem.out : 是否输出内存性能分析文件

-test.blockprofile block.out : 是否输出内部 goroutine 阻塞的性能分析文件

-test.memprofilerate n : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是 profile 中一个 sample 代表的内存大小。默认是设置为 512 \* 1024 的。如果你将它设置为 1，则每分配一个内存块就会在 profile 中有个打点，那么生成的 profile 的 sample 就会非常多。如果你设置为 0，那就是不做打点了。

你可以通过设置 memprofilerate=1 和 GOGC=off 来关闭内存回收，并且对每个内存块的分配进行观察。

-test.blockprofilerate n: 基本同上，控制的是 goroutine 阻塞时候打点的纳秒数。默认不设置就相当于-test.blockprofilerate=1，每一纳秒都打点记录一下

-test.parallel n : 性能测试的程序并行 cpu 数，默认等于 GOMAXPROCS。

-test.timeout t : 如果测试用例运行时间超过 t，则抛出 panic

-test.cpu 1,2,4 : 程序运行在哪些 CPU 上面，使用二进制的 1 所在位代表，和 nginx 的 nginx_worker_cpu_affinity 是一个道理

-test.short : 将那些运行时间较长的测试用例运行时间缩短

作者：MicoCube
链接：https://www.jianshu.com/p/cb47fe8f295c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

经过上面简单的例子的演示和操作，现在我们大概可以了解到路由需要传入两个参数，一个为路径，另一个为路由执行的方法，我们叫做它处理器 Handler ，而且，该参数是可变长参数。也就是说，可以传入多个 handler，形成一条 handler chain 。

====

结论已经很明显，string(int) 会将整数直译为 ASCII 编码，
而 strconv.Itoa(int) 才会转换成对应的数字字符在 ASACII 编码。

int to a(a 代表字符串)

静态资源(css)需要两个要素 1. router.static 2. link 标签引入

a = *b 中 *b 基础地址的值 就相当于 b 本身的值

a=&b &b 相当于指针的地址，是个指针
