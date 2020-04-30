---
title: LinkedBlockingQueue
date: 2018-11-05 20:45:52
tags:
---

LinkedBlockingQueue类也实现了BlockingQueue接口，可阅读之前发表的文章进行查看。

LinkedBlockingQueue内部元素是一个链式结构，一个个链接起来的节点，这个链式接口可以指定一个上界，也就是最多能有多少个节点或者元素，如果没有指定的那上界就是Integer.MAX_VALUE。

LinkedBlocking内存存储元素遵循FIFO的顺序，队列head是在队列里待的时间最长的元素，队列tail是在队列里待的时间最短的元素。

下面看看如何初始化和使用LinkedBlockingQueue

```
BlockingQueue unbounded = new BlockingQueue<String>();
BlockingQueue bounded = new BlockingQueue<String>(1024);
bounded.put("Value");
String value = bounded.take();
```