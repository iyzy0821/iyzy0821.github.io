title: Node内存管理
date: 2016-02-17 17:16:12
tags: [Node,内存管理]
---

*《深入浅出Node.js》读书笔记*

## Node使用V8的方式管理内存 ##

Node中通过JS使用内存时只能使用部分内存（64位系统约为1.4GB，32位系统约为0.7GB），限制了Node无法直接操作大内存对象。主要原因是Node基于V8构建，在Node中使用JS对象基本上都是通过V8自己的方式进行分配和管理。

## V8中的对象 ##

V8中所有JS对象都是通过堆来进行分配，但是V8会限制最大的堆内存。

#### 查看Node程序内存使用量 ####

```
C:\Users\NCIT>node
> process.memoryUsage()
{ rss: 26599424, heapTotal: 7442512, heapUsed: 4500616 }
>
```
rss：
heapTotal：以申请到的堆内存  
heapUsed：当前使用量  

#### 大内存垃圾回收时间花销大 ####

V8最初为浏览器设计，不大可能遇到使用大量内存的场景。  

V8在1.5GB的堆内存上进行垃圾回收，会消耗50毫秒以上，做一次非增量式的垃圾回收甚至要1秒以上。垃圾回收时会造成JS线程暂停，在这样的时间花销下，应用的性能和响应能力会直线下降。

#### 使用更多的内存 ####

Node在启动时传递“--max-old-space-size”和“--max-new-space-size”来调整内存限制，单位：MB。

```
C:\Users\NCIT>node --max-old-space-size=1700
C:\Users\NCIT>node --max-new-space-size=1024
```
**--max-new-space-size在我使用的版本中不存在了**
```
C:\Users\NCIT>node --max-new-space-size=1024
node: bad option: --max-new-space-size=1024
```

查看Node的V8参数
```
node --v8-options 
```

## V8的垃圾回收机制 ##

V8的垃圾回收机制主要基于分代式回收机制。

#### V8的内存分代 ####

V8中将内存分为新生代和老生代两代，