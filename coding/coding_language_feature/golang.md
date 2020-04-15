# Go语言特性

----
|概念|说明|
|--|--|
|进程|一个程序对应一个独立程序空间|
|线程|一个执行空间，一个进程可以有多个线程|
|逻辑处理器|执行创建的goroutine，绑定一个线程|
|调度器|Go运行时中的，分配goroutine给不同的逻辑处理器|
|全局运行队列|所有刚创建的goroutine都会放到这里|
|本地运行队列|逻辑处理器的goroutine队列|

## goroutine
go语言中并发指的是让某个函数独立于其他函数运行的能力，一个goroutine就是一个独立的工作单元，Go的runtime（运行时）会在逻辑处理器上调度这些goroutine来运行，一个逻辑处理器绑定一个操作系统线程，所以说goroutine不是线程，它是一个协程，也是这个原因，它是由Go语言运行时本身的算法实现的。

```go
func main() {
    var wg sync.WaitGroup // 用于计数的信号量
    wg.Add(2)
    go func() {
        defer wg.Done() // 计数减一
        for (i := 1; i <= 100; i++) {
            fmt.Println("First:", i)
        }
    }
    go func() {
        defer wg.Done() // 计数减一
        for (i := 1; i <= 100; i++) {
            fmt.Println("Second:", i)
        }
    }
    wg.Wait() // 如果计数器大于0，此处会阻塞
}
```

## channal
```go
chan T // 可用来接收和发送类型为T的数据
chan<- T // 只可用来发送类型为T的数据
<-chan T // 只可用来接收类型为T的数据

make(chan T, 10) // 最多缓存10个Channel
```
若未设置容量，或者容量设置为0, 说明`Channel`没有缓存，只有`sender`和`receiver`都准备好了后它们的通讯才会发生。如果设置了缓存，就有可能不发生阻塞，只有`buffer`满了后`sender`才会阻塞，而只有缓存空了后`receive`才会阻塞。  
一个`nil channel`不会通信。  
可以通过内建的`close`方法可以关闭`Channel`。  

```go
ch := make(chan T, 10)
v, ok := <-ch // 若ok是false，表明接收的v是产生的零值，这个channel被关闭了或者为nil
```

### select语句
在`select`语句中，若有同时多个`case`去处理，如同时有多个channel可以接收数据，那么Go会伪随机的选择一个`case`处理(`pseudo-random`)。若无`case`需要处理，且default case存在的情况下，则会选择`default`去处理。如果没有`default case`，则`select`语句会阻塞，直到某个`case`需要处理。

```go
func main() {
    c := make(chan strinf, 1)
    go func() {
        time.Sleep(time.Second * 2);
        c <- "Result"
    }

    select {
    case ret := <- c:
        fmt.Println(ret)
    case <- time.After(time.Second * 1): // time.After会返回一个类型为<-chan Time的单向的channel，在达到指定时间时发送一个当前时间给返回的channel中。
        fmt.Println("Timeout")
    }
}
```


## 锁
### 互斥锁
```go
var mutex sync.Mutex // 初始化互斥锁

mutex.Lock() // 上锁

mutex.Unlock() // 解锁
```

当执行`mutex.Lock()`操作时，如果有另外一个`goroutine`又已执行了上锁操作，却又还未解锁，那么该操作被被阻塞，直到该互斥锁恢复到解锁状态。  


### 读写锁
```go
var mutex sync.RWMutex // 初始化互斥锁

mutex.Lock() // 写锁定

mutex.Unlock() // 写解锁

mutex.RLock() // 读锁定

mutex.RUnlock() // 读解锁
```

其中`Lock()`即“写锁定”，调用了“写锁定”后，不能有其他`goroutine`进行读或者写操作。`Unlock()`即“写解锁”，调用了“写解锁”后会唤醒所有因为要进行“读锁定（即:`RLock()`）” 而被阻塞的 goroutine。  

`RLock()`为“读锁定”，调用“读锁定”后，不能有其他`goroutine`进行写操作，但是可以进行读操作。`RUnlock()`为“读解锁”，调用“读解锁”后，会唤醒一个因为要进行“写锁定”而被阻塞的`goroutine`。  

## go mod
可以通过`go env`命令返回值的`GOMOD`字段是否为空来判断是否已经开启了`gomod`。  
在`v1.11`和`v1.12`的`Go`版本中，可以通过设置环境变量`export GO111MODULE=on`开启。  

```go
go mod init

go mod tidy
```

除了`go run`命令以外，`go build`、`go test`等命令也能自动下载相关依赖包。

**安装依赖**  
如果要想先下载依赖，那么可以直接像以前那样`go get`即可，不过`gomod`下可以跟语义化版本号，比如`go get dependmod@v1.2.3`，也可以跟`git`的分支或`tag`，比如`go get dependmod@master`，当然也可以跟`git`提交哈希，比如`go get dependmod@e3702bed2`。  
需要特别注意的是，`gomod`除了遵循语义化版本原则外，还遵循最小版本选择原则，也就是说如果当前版本是`v1.1.0`，只会下载不超过这个最大版本号。如果使用`go get dependmod@master`，下次再下载只会和第一次的一样，无论 `master`分支是否更新了代码，如下所示，使用包含当前最新提交哈希的虚拟版本号替代直接的`master`版本号。  

```go
require (
    golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a // indirect
)
```

  * 若要下载所有依赖可以使用`go mod download`命令。

**升级依赖**  
```go
 go list -u -m all // 查看所有可升级依赖版本

 go get -u golang.org/x/crypto // 升级次级或补丁版本号
 
 go get -u=patch golang.org/x/crypto // 仅升级补丁版本号
 
 go get dependmod@'<v1.2.2' // 升降级版本号
```

**移除依赖**  
```go
// 删除引用的代码后，命令行运行
go mod tidy

// 移除对应path的依赖模块
go mod edit --droprequire=path
```