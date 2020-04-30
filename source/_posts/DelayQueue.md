---
title: DelayQueue
date: 2018-11-05 20:28:13
tags:
---

DelayQueue实现了BlockingQueue接口，更多信息可以阅读我之前的文章。

DelayQueue将会阻塞元素直到某个延迟过期，而这个元素必须实现java.util.concurrent.Delayed接口。

```
public interface Delayed extends Comparable<Delayed< {
 public long getDelay(TimeUnit timeUnit);
}
```

getDelay()返回的是到过期的剩余时间，如果是0或者负数，那么就认为过期了，这个元素将被释放，使用take()就能够从DelayQueue中获得这个元素。

TimeUnit是时间单位，告诉getDelay()延迟应该以什么单位进行返回，TimeUnit是一个Enum.

可以取以下值：

```
DAYS
HOURS
MINUTES
SECONDS
MILLISECONDS
MICROSECONDS
NANOSECONDS
```

Delayed接口也继承了Java.lang.Comparable接口，意味着Delayed对象能相互比较，因此在DelayQueue内部用来进行元素排序。根据过期时间进行排序。

下面是一个例子

```
public class DelayQueueExample {
    public static void main(String[] args) {
        DelayQueue queue = new DelayQueue();
        Delayed element1 = new DelayedElement();
        queue.put(element1);
        Delayed element2 = queue.take();
    }
}
```

DelayElement是一个实现Delayed接口的实现类，这根据你需要，创建不同的实现。

用途：在异步通知时，用作间隔重发场景，比如订单回调失败后，1分钟，5分钟，10分钟..重发。