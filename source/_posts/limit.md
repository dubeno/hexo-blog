---
title: 限流 
---



使用信号量实现并发量限制
``` bash
public class Test{
public static void main(int argc,String[] argv){
    Semaphore semaphore = new Semaphore(10,true);
    semaphore.acquire();
    //do business
    semaphore.realease();
    }
}
```
速率限制
使用Google的Guava实现速率限制
``` bash
    public void class Test{
        public static void main(int argc,String [] argv){
            ReteLimiter limiter = ReteLimiter.create(10.0);
            limiter.acquire();
       }


    }
```
