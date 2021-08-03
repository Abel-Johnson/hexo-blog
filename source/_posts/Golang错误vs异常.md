---
title: Golang错误vs异常
date: 2021-08-02 15:14:54
tags: [go]
---

> 错误和异常如何区分？  
> 错误处理的方式有哪几种？  
> 什么时候需要使用异常终止程序？  
> 什么时候需要捕获异常？  
> ...


- 错误 指的是可能出现问题的地方出现了问题，  
    - 比如打开一个文件时失败，  
    - 这种情况在人们的意料之中 ；  
- 而异常指的是不应该出现问题的地方出现了问题，  
    - 比如引用了空指针，  
    - 这种情况在人们的意料之外。  

可见，错误是业务过程的一部分，而异常不是 。

## 错误
套件包括: error内置对象

如果函数要返回错误，则返回值类型列表中肯定包含error。error处理过程类似C语言中的错误码，可逐层返回，直到被处理。

## 异常
套件包括: panic/recover   defer

- 一直等到包含defer语句的函数执行完毕时，延迟函数（defer后的函数）才会被执行，而不管包含defer语句的函数是通过return的正常结束，还是由于panic导致的异常结束。你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反。(后入先出)  
- 当程序运行时，如果遇到引用空指针、下标越界或显式调用panic函数等情况，则先触发panic函数的执行，然后调用延迟函数。调用者继续传递panic，因此该过程一直在调用栈中重复发生：函数停止执行，调用延迟执行函数等。如果一路在延迟函数中没有recover函数的调用，则会到达该协程的起点，该协程结束，然后终止其他所有协程，包括主协程（类似于C语言中的主线程，该协程ID为1）。


panic类似throw

## 关系

Golang错误和异常是可以互相转换的：

错误转异常，比如程序逻辑上尝试请求某个URL，最多尝试三次，尝试三次的过程中请求失败是错误，尝试完第三次还不成功的话，失败就被提升为异常了。

异常转错误，比如panic触发的异常被recover恢复后，将返回值中error类型的变量进行赋值，以便上层函数继续走错误处理流程。

## go官方包里的函数示例

regexp包中有两个函数Compile和MustCompile，它们的声明如下：


func Compile(expr string) (*Regexp, error)
func MustCompile(str string) *Regexp

同样的功能，不同的设计：



Compile函数基于错误处理设计，将正则表达式编译成有效的可匹配格式，适用于用户输入场景。当用户输入的正则表达式不合法时，该函数会返回一个错误。

MustCompile函数基于异常处理设计，适用于硬编码场景。当调用者明确知道输入不会引起函数错误时，要求调用者检查这个错误是不必要和累赘的。我们应该假设函数的输入一直合法，当调用者输入了不应该出现的输入时，就触发panic异常。


于是我们得到一个启示：什么情况下用错误表达，什么情况下用异常表达，就得有一套规则，否则很容易出现一切皆错误或一切皆异常的情况。 


在这个启示下，我们给出异常处理的作用域（场景）：



空指针引用

下标越界

除数为0

不应该出现的分支，比如default

输入不应该引起函数错误


其他场景我们使用错误处理，这使得我们的函数接口很精炼。对于异常，我们可以选择在一个合适的上游去recover，并打印堆栈信息，使得部署后的程序不会终止。


说明： Golang错误处理方式一直是很多人诟病的地方，有些人吐槽说一半的代码都是"if err != nil { / 打印 && 错误处理 / }"，严重影响正常的处理逻辑。当我们区分错误和异常，根据规则设计函数，就会大大提高可读性和可维护性。

## 正确姿势

### 错误处理的正确姿势

#### 姿势一：失败的原因只有一个时，不使用error

我们看一个案例：

```golang
func (self *AgentContext) CheckHostType(host_type string) error {
    switch host_type {
    case "virtual_machine":
        return nil
    case "bare_metal":
        return nil
    }
    return errors.New("CheckHostType ERROR:" + host_type)
}
```

我们可以看出，该函数失败的原因只有一个，所以返回值的类型应该为bool，而不是error，重构一下代码：

```go
func (self *AgentContext) IsValidHostType(hostType string) bool {
    return hostType == "virtual_machine" || hostType == "bare_metal"
}
```

说明：大多数情况，导致失败的原因不止一种，尤其是对I/O操作而言，用户需要了解更多的错误信息，这时的返回值类型不再是简单的bool，而是error。

#### 姿势二：没有失败时，不使用error

error在Golang中是如此的流行，以至于很多人设计函数时不管三七二十一都使用error，即使没有一个失败原因。
我们看一下示例代码：


func (self *CniParam) setTenantId() error {
    self.TenantId = self.PodNs
    return nil
}

对于上面的函数设计，就会有下面的调用代码：


err := self.setTenantId()
if err != nil {
    // log
    // free resource
    return errors.New(...)
}

根据我们的正确姿势，重构一下代码：


func (self *CniParam) setTenantId() {
    self.TenantId = self.PodNs
}

于是调用代码变为：


self.setTenantId()

#### 姿势三：error应放在返回值类型列表的最后

对于返回值类型error，用来传递错误信息，在Golang中通常放在最后一个。

```go
resp, err := http.Get(url)
if err != nil {
    return nil, err
}
```

bool作为返回值类型时也一样。

```go
value, ok := cache.Lookup(key) 
if !ok {
    // ...cache[key] does not exist… 
}
```

#### 姿势四：错误值统一定义，而不是跟着感觉走

很多人写代码时，到处return errors.New(value)，而错误value在表达同一个含义时也可能形式不同，比如“记录不存在”的错误value可能为：



"record is not existed."

"record is not exist!"

"###record is not existed！！！"

...


这使得相同的错误value撒在一大片代码里，当上层函数要对特定错误value进行统一处理时，需要漫游所有下层代码，以保证错误value统一，不幸的是有时会有漏网之鱼，而且这种方式严重阻碍了错误value的重构。


于是，我们可以参考C/C++的错误码定义文件，**在Golang的每个包中增加一个错误对象定义文件**，如下所示：

```go
var ERR_EOF = errors.New("EOF")
var ERR_CLOSED_PIPE = errors.New("io: read/write on closed pipe")
var ERR_NO_PROGRESS = errors.New("multiple Read calls return no data or error")
var ERR_SHORT_BUFFER = errors.New("short buffer")
var ERR_SHORT_WRITE = errors.New("short write")
var ERR_UNEXPECTED_EOF = errors.New("unexpected EOF")
```

说明：笔者对于常量更喜欢C/C++的“全大写+下划线分割”的命名方式，读者可以根据团队的命名规范或个人喜好定制。


姿势五：**错误逐层传递时，一定要层层都加上日志**

根据笔者经验，层层都加日志非常方便故障定位。


说明：至于通过测试来发现故障，而不是日志，目前很多团队还很难做到。如果你或你的团队能做到，那么请忽略这个姿势:)

#### 姿势六：错误处理使用defer

我们一般通过判断error的值来处理错误，如果当前操作失败，需要将本函数中已经create的资源destroy掉，示例代码如下：

```go
func deferDemo() error {
    err := createResource1()
    if err != nil {
        return ERR_CREATE_RESOURCE1_FAILED
    }
    err = createResource2()
    if err != nil {
        destroyResource1()
        return ERR_CREATE_RESOURCE2_FAILED
    }

    err = createResource3()
    if err != nil {
        destroyResource1()
        destroyResource2()
        return ERR_CREATE_RESOURCE3_FAILED
    }

    err = createResource4()
    if err != nil {
        destroyResource1()
        destroyResource2()
        destroyResource3()
        return ERR_CREATE_RESOURCE4_FAILED
    }
    return nil
}
```

当Golang的代码执行时，如果遇到defer的闭包调用，则压入堆栈。当函数返回时，会按照后进先出的顺序调用闭包。
对于闭包的参数是值传递，而对于外部变量却是引用传递，所以闭包中的外部变量err的值就变成外部函数返回时最新的err值。
根据这个结论，我们重构上面的示例代码：

```go
func deferDemo() error {
    err := createResource1()
    if err != nil {
        return ERR_CREATE_RESOURCE1_FAILED
    }
    defer func() {
        if err != nil {
            destroyResource1()
        }
    }()
    err = createResource2()
    if err != nil {
        return ERR_CREATE_RESOURCE2_FAILED
    }
    defer func() {
        if err != nil {
            destroyResource2()
        }
    }()

    err = createResource3()
    if err != nil {
        return ERR_CREATE_RESOURCE3_FAILED
    }
    defer func() {
        if err != nil {
            destroyResource3()
        }
    }()

    err = createResource4()
    if err != nil {
        return ERR_CREATE_RESOURCE4_FAILED
    }
    return nil
}
```

#### 姿势七：当尝试几次可以避免失败时，不要立即返回错误

如果错误的发生是偶然性的，或由不可预知的问题导致。一个明智的选择是重新尝试失败的操作，有时第二次或第三次尝试时会成功。在重试时，我们需要限制重试的时间间隔或重试的次数，防止无限制的重试。


两个案例：

我们平时上网时，尝试请求某个URL，有时第一次没有响应，当我们再次刷新时，就有了惊喜。

团队的一个QA曾经建议当Neutron的attach操作失败时，最好尝试三次，这在当时的环境下验证果然是有效的。


#### 姿势八：当上层函数不关心错误时，建议不返回error

对于一些资源清理相关的函数（destroy/delete/clear），如果子函数出错，打印日志即可，而无需将错误进一步反馈到上层函数，因为一般情况下，上层函数是不关心执行结果的，或者即使关心也无能为力，于是我们建议将相关函数设计为不返回error。


#### 姿势九：当发生错误时，不忽略有用的返回值

通常，当函数返回non-nil的error时，其他的返回值是未定义的(undefined)，这些未定义的返回值应该被忽略。然而，有少部分函数在发生错误时，仍然会返回一些有用的返回值。比如，当读取文件发生错误时，Read函数会返回可以读取的字节数以及错误信息。对于这种情况，应该将读取到的字符串和错误信息一起打印出来。


说明：对函数的返回值要有清晰的说明，以便于其他人使用。 


### 异常处理的正确姿势

#### 姿势一：在程序开发阶段，坚持速错(比如主动调用panic)

去年学习Erlang的时候，建立了速错的理念，简单来讲就是“让它挂”，只有挂了你才会第一时间知道错误。在早期开发以及任何发布阶段之前，最简单的同时也可能是最好的方法是调用panic函数来中断程序的执行以强制发生错误，使得该错误不会被忽略，因而能够被尽快修复。


#### 姿势二：在程序部署后，应恢复异常避免程序终止

在Golang中，虽然有类似Erlang进程的Goroutine，但需要强调的是Erlang的挂，只是Erlang进程的异常退出，不会导致整个Erlang节点退出，所以它挂的影响层面比较低，而Goroutine如果panic了，并且没有recover，那么整个Golang进程（类似Erlang节点）就会异常退出。所以，**一旦Golang程序部署后，在任何情况下发生的异常都不应该导致程序异常退出**，我们在上层函数中加一个延迟执行的recover调用来达到这个目的，并且是否进行recover需要根据环境变量或配置文件来定，默认需要recover。
这个姿势类似于C语言中的断言，但还是有区别：一般在Release版本中，断言被定义为空而失效，但需要有if校验存在进行异常保护，尽管契约式设计中不建议这样做。在Golang中，recover完全可以终止异常展开过程，省时省力。


我们在调用recover的延迟函数中以最合理的方式响应该异常：



**打印**堆栈的异常调用信息和关键的业务信息，以便这些问题保留可见；

将异常**转换**为错误，以便调用者让程序恢复到健康状态并继续安全运行。


我们看一个简单的例子：

```go
func funcA() error {
    defer func() {
        if p := recover(); p != nil {
            fmt.Printf("panic recover! p: %v", p)
            debug.PrintStack()
        }
    }()
    return funcB()
}

func funcB() error {
    // simulation
    panic("foo")
    return errors.New("success")
}

func test() {
    err := funcA()
    if err == nil {
        fmt.Printf("err is nil\\n")
    } else {
        fmt.Printf("err is %v\\n", err)
    }
}
```

我们期望test函数的输出是：


err is foo

实际上test函数的输出是：


err is nil

原因是panic异常处理机制不会自动将错误信息传递给error，所以要在funcA函数中进行显式的传递，代码如下所示：


func funcA() (err error) {
    defer func() {
        if p := recover(); p != nil {
            fmt.Println("panic recover! p:", p)
            str, ok := p.(string)
            if ok {
                err = errors.New(str)
            } else {
                err = errors.New("panic")
            }
            debug.PrintStack()
        }
    }()
    return funcB()
}

#### 姿势三：对于不应该出现的分支，使用异常处理

当某些不应该发生的场景发生时，我们就应该调用panic函数来触发异常。比如，当程序到达了某条逻辑上不可能到达的路径：

```go
switch s := suit(drawCard()); s {
    case "Spades":
    // ...
    case "Hearts":
    // ...
    case "Diamonds":
    // ... 
    case "Clubs":
    // ...
    default:
        panic(fmt.Sprintf("invalid suit %v", s))
}
```

#### 姿势四：针对入参不应该有问题的函数，使用panic设计

入参不应该有问题一般指的是硬编码，我们先看“一个启示”一节中提到的两个函数（Compile和MustCompile），其中MustCompile函数是对Compile函数的包装：

```go
func MustCompile(str string) *Regexp {
    regexp, error := Compile(str)
    if error != nil {
        panic(`regexp: Compile(` + quote(str) + `): ` + error.Error())
    }
    return regexp
}
```

所以，对于同时支持用户输入场景和硬编码场景的情况，一般支持硬编码场景的函数是对支持用户输入场景函数的包装。
对于只支持硬编码单一场景的情况，函数设计时直接使用panic，即返回值类型列表中不会有error，这使得函数的调用处理非常方便（没有了乏味的"if err != nil {/ 打印 && 错误处理 /}"代码块）。

