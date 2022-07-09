# Go技术相关（做什么的，怎么做，为什么这样做）
## 1. 引用类型有哪些？
slice map channel interface 指针

## 2. 数组和切片的区别？（数组定义后cap不能再改变）
## 3. 切片的底层实现？high

### 1. 扩增算法

### 2. for range 的时候它的地址会发生变化么

## 4. defer，多个 defer 的顺序，defer 在什么时机会修改返回值？
### 1. defer recover

## 5. uint 类型溢出
uint 0-255，如果当到256位时会溢出导致编译失败
### 1. 介绍 rune 类型
rune是int32的别称，用于处理go的unicode和utf-8编码问题

## 6. map的底层实现？（哈希查找表，使用链表解决哈希冲突）high
Go 语言采用的是哈希查找表，并且使用链表解决哈希冲突。

- 哈希查找表用一个哈希函数将 key 分配到不同的桶（bucket，也就是数组的不同 index）。这样，开销主要在哈希函数的计算以及数组的常数访问时间。**哈希查找表的平均查找效率是 O(1)，最差搜索效率是 O(N)。**
- 哈希查找表一般会存在“碰撞”的问题，就是说不同的 key 被哈希到了同一个 bucket。解决办法：链表法将一个 bucket 实现成一个链表，落在同一个 bucket 中的 key 都会插入这个链表。

源码 /src/runtime/map.go

```go
// A header for a Go map.
type hmap struct {
    // 元素个数，调用 len(map) 时，直接返回此值
    count     int
    flags     uint8
    // buckets 的对数 log_2
    B         uint8
    // overflow 的 bucket 近似数
    noverflow uint16
    // 计算 key 的哈希的时候会传入哈希函数
    hash0     uint32
    // 指向 buckets 数组，大小为 2^B
    // 如果元素个数为0，就为 nil
    buckets    unsafe.Pointer
    // 扩容的时候，buckets 长度会是 oldbuckets 的两倍
    oldbuckets unsafe.Pointer
    // 指示扩容进度，小于此地址的 buckets 迁移完成
    nevacuate  uintptr
    extra *mapextra // optional fields
}
```

`B` 是 buckets 数组的长度的对数，也就是说 buckets 数组的长度就是 2^B。

`buckets` 是一个指针，最终它指向的是一个结构体：

```go
type bmap struct {
    tophash [bucketCnt]uint8
}

// 但这只是表面(src/runtime/hashmap.go)的结构，编译期间会给它加料，动态地创建一个新的结构：
type bmap struct {
    topbits  [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr
}
```

`bmap` 就是我们常说的“桶”，桶里面会最多装 8 个 key，这些 key 之所以会落入同一个桶，是因为它们经过哈希计算后，哈希结果是“一类”的。在桶内，又会根据 key 计算出来的 hash 值的高 8 位来决定 key 到底落入桶内的哪个位置（一个桶内最多有8个位置）。

###  1. map并发安全吗？解决办法
map 并发不安全 

解决办法：1、加锁；2、使用sync.Map，3、换成channel结构
### 2. map 中删除一个 key，它的内存会释放么
golang的map在key被删除之后，并不会立即释放内存，所以随着程序的运行，实际上map占用的内存只会越来越大。此外，GC会在标记阶段访问map中的每一个元素，当map非常大时这会对程序性能带来非常大的开销。不过go 1.5版本之后，如果map的key和value中都不包含指针，那么GC会忽略这个map。

golang释放map内存的方法：首先删除map中的所有key，map占用内存仍处于【使用状态】；然后删除map所有的key，将map设置为nil，map占用的内存处于【空闲状态】；最后处于空闲状态内存，一定时间内在下次申请的可重复被使用，不必再向操作系统申请即可。
### 3. map 取一个 key，然后修改这个值，原 map 数据的值会不会变化
map2 := map1, 如果map2改变其中的值，map1也会跟着改变。（指向的地址不变）

### 4. nil map 和空 map 有何不同
map初始化有两种方式：map[string]string{}或make(map[string]string)

未初始化的map是nil，它与一个空map基本等价，只是nil的map不允许往里面添加值，否则会报错`panic: assignment to entry in nil map`。

断定map是空还是nil，可使用 map == nil 进行判断

### 5. map承载量有多大，什么情况下扩容，怎么扩容
扩容条件：
- 负载因子(loadFactorNum/loadFactorDen)超过阈值6.5
- overflow 的 bucket 数量过多，即当 B 小于 15（bucket 总数 2^B 小于 2^15 ）时，overflow 的 bucket 数量超过 2^B；当 B >= 15（bucket 总数 2^B 大于等于 2^15），overflow 的 bucket 数量超过 2^15。

扩容策略：
- 根据需扩容的原因不同（overLoadFactor/tooManyOverflowBuckets），分为两类容量规则方向，为等量扩容（不改变原有大小）或双倍扩容
- 新申请的扩容空间（newbuckets/newoverflow）都是预分配，等真正使用的时候才会初始化
- 扩容完毕后（预分配），不会马上就进行迁移。而是采取增量扩容的方式，当有访问到具体 bukcet 时，才会逐渐的进行迁移（将 oldbucket 迁移到 bucket）

### 6. sync.Map怎么实现线程安全的，怎么实现的
https://colobu.com/2017/07/11/dive-into-sync-Map/

底层结构：
1. 空间换时间。 通过冗余的两个数据结构(read、dirty),实现加锁对性能的影响。
2. 使用只读数据(read)，避免读写冲突。
3. 动态调整，miss次数多了之后，将dirty数据提升为read。
4. double-checking。
5. 延迟删除。 删除一个键值只是打标记，只有在提升dirty的时候才清理删除的数据。
6. 优先从read读取、更新、删除，因为对read的读取不需要锁。

```go
type Map struct {
	// 当涉及到dirty数据的操作的时候，需要使用这个锁
	mu Mutex
	// 一个只读的数据结构，因为只读，所以不会有读写冲突。
	// 所以从这个数据中读取总是安全的。
	// 实际上，实际也会更新这个数据的entries,如果entry是未删除的(unexpunged), 并不需要加锁。如果entry已经被删除了，需要加锁，以便更新dirty数据。
	read atomic.Value // readOnly
	// dirty数据包含当前的map包含的entries,它包含最新的entries(包括read中未删除的数据,虽有冗余，但是提升dirty字段为read的时候非常快，不用一个一个的复制，而是直接将这个数据结构作为read字段的一部分),有些数据还可能没有移动到read字段中。
	// 对于dirty的操作需要加锁，因为对它的操作可能会有读写竞争。
	// 当dirty为空的时候， 比如初始化或者刚提升完，下一次的写操作会复制read字段中未删除的数据到这个数据中。
	dirty map[interface{}]*entry
	// 当从Map中读取entry的时候，如果read中不包含这个entry,会尝试从dirty中读取，这个时候会将misses加一，
	// 当misses累积到 dirty的长度的时候， 就会将dirty提升为read,避免从dirty中miss太多次。因为操作dirty需要加锁。
	misses int
}
```

它的数据结构很简单，值包含四个字段：`read`、`mu`、`dirty`、`misses`。

它使用了冗余的数据结构`read`、`dirty`。`dirty`中会包含`read`中为删除的entries，新增加的entries会加入到`dirty`中。

`read`的数据结构是：

```go
type readOnly struct {
	m       map[interface{}]*entry
	amended bool // 如果Map.dirty有些数据不在中的时候，这个值为true
}
```

`amended`指明`Map.dirty`中有`readOnly.m`未包含的数据，所以如果从`Map.read`找不到数据的话，还要进一步到`Map.dirty`中查找。

`readOnly.m`和`Map.dirty`存储的值类型是`*entry`,它包含一个指针p, 指向用户存储的value值。

```go
type entry struct {
	p unsafe.Pointer // *interface{}
}
```

p有三种值：

- nil: entry已被删除了，并且m.dirty为nil
- expunged: entry已被删除了，并且m.dirty不为nil，而且这个entry不存在于m.dirty中
- 其它： entry是一个正常的值


### 7. sync.Map的优缺点和使用场景
sync.Map 保证并发安全，但有一些性能损失，没有len函数

## 7. channel的用法（使用通信进行共享内存），channel是如何使用的？
### 1. 有无缓存的channel的区别
### 2. channel的底层实现
### 3. 向 channel 发送数据和从 channel 读数据的流程会怎么样
## 8. 锁的用法，实现原理，锁有哪些类，使用场景分别是什么？
### 1. Mutex 是悲观锁还是乐观锁？悲观锁、乐观锁是什么
### 2. Mutex 有几种模式
### 3. 锁的饥饿模式和自旋锁
### 4. 读写锁底层是怎么实现的
## 9. make和new的区别？
## 10. sync中context的用法？
### 1. 有哪些函数，使用场景
## 11. sync中waitGroup的使用？
## 12. sync.Pool的使用场景？不同版本的变化1.12->1.13
## 13. 空的struct{}有什么作用？
## 14. interface{}的用法，断言的使用？
## 15. 反射原理
### 1. 解析tag怎么实现
## 16. go协程和线程的区别？（进程、线程、协程）
## 17. GMP调度模型 g0 m0 全局队列
### 1. 全局队列、本地队列、调度原理
### 2. gmp中的阻塞有哪些
### 3. 各个调度模型的版本及解决的问题
## 18. GC的实现
### 1. 什么时候发生回收
### 2. gc中stw的时机
### 3. 各个阶段是如何解决的
## 19. go的内存区间
### 1. 内存管理 Channel 分配在栈上还是堆上？哪些对象分配在堆上，哪些对象分配在栈上？
### 2. 大对象小对象，为什么小对象多了会造成 gc 压力？
### 3. 堆和栈的区别？
### 4. 内存泄露，什么情况下内存会泄露？怎么定位排查内存泄漏问题？怎么处理
### 5. golang 的内存逃逸吗？什么情况下会发生内存逃逸？
### 6. 如何手动对其内存
### 7. 栈扩容和栈缩容，连续栈的缺点 https://segmentfault.com/a/1190000019570427
## 20. 协程池怎么实现
## 21. 主协程如何等其余协程完再操作
## 22. 利用 golang 特性，设计一个 QPS 为 500 的服务器
## 23. var _ io.Writer = (*myWriter)(nil) 写法的原因 （定义为nil的myWriter指针类型的变量使用_进行接收）
## 24. 问题排查 pprof trace
