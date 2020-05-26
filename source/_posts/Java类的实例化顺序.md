---
title: Java类的实例化顺序
date: 2020-05-24 17:17:11
tags:
---

话不多说，show you code
## 主类测试
```
package corejava;

/**
 * @author robin
 */
public class TestInitialOrder {
    public static void main(String[] args) {
        InitailOrder order = new SubClassInitialOrder();
    }
}
```
## 定义父类
```
class InitailOrder{
    static {
        System.out.println("静态代码块");
    }
    static String a = verify("静态变量初始化");
    String nonStatic = verify("成员变量初始化");
    public InitailOrder() {
        System.out.println("构造函数");
    }
    static String verify(String s) {
        System.out.println(s);  return s;
    }
}
```
## 定义子类
```
class SubClassInitialOrder extends InitailOrder{
    static String c = verify("子类静态变量初始化");
    String d = verify("子类成员变量初始化");
    static {
        System.out.println("子类静态代码块");
    }
    static String verify(String s) {
        System.out.println(s);
        return s;
    }
    public SubClassInitialOrder() {
        System.out.println("子类构造函数执行");
    }
}

```
### 输出:
```
静态代码块
静态变量初始化
子类静态变量初始化
子类静态代码块
成员变量初始化
构造函数
子类成员变量初始化
子类构造函数执行
```
## 静态代码块和静态变量哪究竟谁先初始化？，我来调整一下顺序
```
class InitailOrder{
    static String a = verify("静态变量初始化");
    static {
        System.out.println("静态代码块");
    }
    String nonStatic = verify("成员变量初始化");
    public InitailOrder() {
        System.out.println("构造函数");
    }
    static String verify(String s) {
        System.out.println(s);  return s;
    }
}
```
### 输出：
```
静态变量初始化
静态代码块
子类静态变量初始化
子类静态代码块
成员变量初始化
构造函数
子类成员变量初始化
子类构造函数执行
```
可以看到静态变量初始化先于静态代码块，所以这里与静态元素在代码中的先后顺序有关。


## 看一个高级点的例子，看一下main方法的代码执行时机
```
package corejava;
class Parent {
    String p = verify("4  - Parent's non-static class variable p initialized.");
    static String pstatic = verify("1a - Parent's static class variable pstatic initialized.");
    static {
        System.out.println("1b - Parent's static initialization block ran.");
    }
    static String verify(String s) {
        System.out.println(s);  return s;
    }
    Parent( ) {
        System.out.println("5  - Parent constructor ran.");
    }
}



public class Child extends Parent {
    static {
        System.out.println("2a - Child's static initialization block ran.");
    }
    String c = verify("6  - Child's non-static class variable c initialized.");
    static String cstatic = verify("2b - Child's static class variable cstatic initialized.");
    Child( ) {
        super( );   //providing super( ) won't change anything here
        System.out.println ("7  - Child constructor ran.");
    }
    public static void main(String[ ] args) {
        System.out.println("3  - Child's main(..) method ran. Invoking Child with new.");
        Child C = new Child( );
        System.out.println("8  - The rest of main(..) was run.");  }
}

```
### 输出：
```
1a - Parent's static class variable pstatic initialized.
1b - Parent's static initialization block ran.
2a - Child's static initialization block ran.
2b - Child's static class variable cstatic initialized.
3  - Child's main(..) method ran. Invoking Child with new.
4  - Parent's non-static class variable p initialized.
5  - Parent constructor ran.
6  - Child's non-static class variable c initialized.
7  - Child constructor ran.
8  - The rest of main(..) was run.
```


### 下面总结一下：
首先是父类的静态变量或者静态代码块先执行，然后是子类静态变量或者子类静态代码块，完了是父类成员变量初始化，父类构造函数，然后是子类成员变量初始化，子类构造函数执行。
- 静态代码块
- 静态变量初始化
- 子类静态变量初始化
- 子类静态代码块
- 成员变量初始化
- 构造函数
- 子类成员变量初始化
- 子类构造函数执行
静态元素先后，由静态元素在代码中的先后位置有关。



参考文献：http://1001javatips.com/initializationorder.htm