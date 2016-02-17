title: Node内存管理
date: 2016-02-17 17:16:12
tags: [Node,内存管理]
---

## Node使用V8的方式管理内存 ##

Node中通过JS使用内存时只能使用部分内存（64位系统约为1.4GB，32位系统约为0.7GB），限制了Node无法直接操作大内存对象。主要原因是Node基于V8构建，在Node中使用JS对象基本上都是通过V8自己的方式进行分配和管理。

## V8中的对象 ##

V8中所有JS对象都是通过堆来进行分配，

#### 查看Node程序内存使用量 ####

```
C:\Users\NCIT>node
> process.memoryUsage()
{ rss: 26599424, heapTotal: 7442512, heapUsed: 4500616 }
>
```