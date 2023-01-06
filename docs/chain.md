
```java
public class A{
  public void a() {
    // dosomething
    return;
  }
}

public class B {
  public void a() {
    return;
  }
}

A a = new A();
B b = new B();

// 串联,ab没有关系
a.a();
b.a();

// a中调b b中调c,链条一样的,动态代理
a -> b 


```