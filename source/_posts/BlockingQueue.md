---
title: BlockingQueue
date: 2018-11-05 20:28:13
tags:
---

1. BlockingQueue用法
2. BlockingQueue实现
3. Java BlockingQueue示例

java.util.concurrent包中BlockingQueue是一个可以线程安全往里面存取实例的接口。本文将介绍如何使用BlockingQueue。本文将不会讨论如何去自己实现一个BlockingQueue。

##### BlockingQueue 用法

​	BlockingQueue通常用来生成另一个线程消费的对象，请看下图。
<span><div style="text-align: center;">
{% asset_img blocking-queue.png one %}
</div></span>

一个BlockingQueue，一个生产线程放入其中，一个消费线程从中获取。

Thread 1作为生成线程将继续生成新的对象放入到BlockingQueue中，知道队列达到它的某个上限，如果阻塞队列达到其上限，Thread 1在尝试插入新的对象时会阻止放入。Thread 1将一直被阻塞，直到Thread 2将一个对象从队列中取出。

Thread 2作为消费线程不断的从阻塞队列中取出对象，并对它进行处理。如果Thread 2消费线程将尝试从一个空的队列中消费数据，Thread 2将被阻塞，直到生产线程将对象放入队列。

##### BlockingQueue 方法

BlockingQueue针对插入，删除，检查队列中是否有元素有四种不同的方法。每种不同的方法有不同的行为。

|           | 抛出异常      | 特殊值      | 阻塞     | 超时                        |
| --------- | --------- | -------- | ------ | ------------------------- |
| 插入        | add(o)    | offer(o) | put(o) | offer(o,timeout,timeunit) |
| 移除        | remove(o) | poll()   | take() | poll()timeout,timeunit)   |
| remove(o) | element() | peek()   |        |                           |

四种不同的方法的含义：

- 抛出异常：如果无法立即尝试操作，则抛出异常。


- 特殊值：如果无法立即进行操作，将返回特殊值(true/false)。


- 阻塞：如果无法立即进行操作，方法将阻塞，直到可以为止。


- 超时：如果操作无法立即执行，则方法将阻塞一段时间，时间则由timeout和timeunit控制，然后返回特殊值(true/false)。

无法将null插入到BlockingQueue,如果尝试插入null，将抛出NullPointerException。

你可以访问BlockingQueue中的所有数据，而不仅仅是头结点和尾部结点，比如你想删除队列中的某个对象，你可以通过remove(o)，移除指定的对象，但是一般情况下不会这么干。

##### BlockingQueue 实现

既然BlockingQueue是接口，那么在使用的时候肯定需要使用它的实现类，下面是它的一些实现类。

- ArrayBlockingQueue


- DelayQueue


- LinkedBlockingQueue


- PriorityBlocingQueue


- SynchrnoursQueue

##### BlockingQueue  示例

下面是BlockingQueue的实例，这个例子中使用ArrayBlockingQueue作为实现类。

首先BlockingQueueExample 在两个线程中启动一个Producer和一个Comsumer线程。

Producer向BlockingQueue中插入数据，Comsumer从中取数据。

```
public class BlockingQueueExample {
    public static void main(String[] args) throws Exception {
        BlockingQueue queue = new ArrayBlockingQueue(1024);
        Producer producer = new Producer(queue);
        Consumer consumer = new Consumer(queue);
        new Thread(producer).start();
        new Thread(consumer).start();
        Thread.sleep(4000);
    }
}
```

下面是Producer类，它将在每次put后sleep一秒，这将导致Comsumer阻塞。

```
public class Producer implements Runnable{
    protected BlockingQueue queue = null;
    public Producer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
            queue.put("1");
            Thread.sleep(1000);
            queue.put("2");
            Thread.sleep(1000);
            queue.put("3");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

下面是Comsumer类，从队列中取数据，并打印。

```
public class Consumer implements Runnable{
    protected BlockingQueue queue = null;
    public Consumer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
            System.out.println(queue.take());
            System.out.println(queue.take());
            System.out.println(queue.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

