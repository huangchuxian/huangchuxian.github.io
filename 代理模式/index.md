# 代理模式


**摘要：**

​		**代理模式就是为其他对象提供一种代理以控制对这个对象的访问，包括静态代理和动态代理两种实现方式。实现代理模式的有Spring AOP、RPC框架等**

#### 一. 代理模式

###### 1. 简介

* 在程序运行前就已经编译好的代理类就是静态代理，在程序运行时根据需要动态创建的代理类就是动态代理
* 代理对象可以对真实业务对象进行拦截操作、消息预处理和后处理操作
* 代理对象实现真实业务对象的共同接口或者继承相同的父类
* 代理类是隔离客户端和委托类的中介

###### 2. 结构

   代理模式主要包含三个角色：抽象角色、委托角色、代理角色

​		抽象角色：可以是类或者接口

​		委托角色：真实的业务对象，业务逻辑的执行者

​		代理角色：里面包含对真实业务对象的调用，还包含预处理和后处理

<p align="center"><img src="/images/image-20210413161324259.png"></p>

###### 3. 原则

​		使用代理类可以在不修改原有代码的前提下增加功能，符合开闭原则（对拓展开放，对修改封闭）

#### 二. 静态代理

###### 1. 实现

​		静态代理在程序运行前生成.class文件

实现步骤：

1. 定义业务接口
2. 实现业务接口
3. 定义代理类并实现业务接口
4. 客户端调用

###### 2. 代码

* 抽象接口

  ```java
  public interface HelloService {
      String hello(String name);
      String hi(String msg);
  }
  ```

* 委托类

  ```java
  public class HelloServiceImpl implements HelloService{
      @Override
      public String hello(String name) {
          return "Hello " + name;
      }
  
      @Override
      public String hi(String msg) {
          return "Hi, " + msg;
      }
  }
  ```

* 代理类

  ```java
  public class HelloServiceProxy implements HelloService {
  
      private HelloService helloService;
  
      public HelloServiceProxy(HelloService helloService) {
          this.helloService = helloService;
      }
  
      @Override
      public String hello(String name) {
          System.out.println("预处理...");
          String result = helloService.hello(name);
          System.out.println(result);
          System.out.println("后处理...");
          return result;
      }
  
      @Override
      public String hi(String msg) {
          System.out.println("预处理...");
          String result = helloService.hi(msg);
          System.out.println(result);
          System.out.println("后处理...");
          return result;
      }
  }
  ```

* 客户端

  ```java
  public class Main {
      public static void main(String[] args){
          HelloService helloService = new HelloServiceImpl();
          HelloServiceProxy helloServiceProxy = new HelloServiceProxy(helloService);
          helloServiceProxy.hello("Panda");
          helloServiceProxy.hi("Panda");
      }
  }
  /** Output
   预处理...
  Hello Panda
  后处理...
  预处理...
  Hi, Panda
  后处理...
  **/
  ```

#### 三. 动态代理

###### 1. 简介

* 一个代理类往往不止代理一个委托类，因此是一对多的关系，不需要关注传入的是哪个委托类
* 动态代理分为jdk动态代理和cglib动态代理

###### 2. jdk动态代理

* 步骤

1. 创建委托接口和委托类
2. 实现InvocationHandler接口，对目标接口中声明的所有方法进行统一处理
3. 调用Proxy的静态方法，创建代理类并生成相应的代理对象
4. 使用代理

* 代码

  * 创建委托接口、类

  ```java
  public interface HelloService {
  
      String hello(String name);
  
      String hi(String msg);
  }
  public class HelloServiceImpl implements HelloService{
      @Override
      public String hello(String name) {
          return "Hello " + name;
      }
  
      @Override
      public String hi(String msg) {
          return "Hi, " + msg;
      }
  }
  ```

  * 实现InvocationHandler接口

  ```java
  public class MyInvocationHandler implements InvocationHandler{
      // 真实业务对象
      private Object target;
  
      public MyInvocationHandler(Object target){
          this.target = target;
      }
  
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          // 增强逻辑
          System.out.println("PROXY : " + proxy.getClass().getName());
          // 反射调用，目标方法
          Object result = method.invoke(target, args);
          // 增强逻辑
          System.out.println(method.getName() + " : " + result);
          return result;
      }
  }
  ```

  * 创建代理类并生成相应的代理对象

  ```java
  // 方法一
  // 生成代理类的class对象
  Class<?> clazz = Proxy.getProxyClass(helloService.getClass().getClassLoader(), helloService
              .getClass().getInterfaces());
  // 创建InvocationHandler
  InvocationHandler myInvocationHandler = new MyInvocationHandler(helloService);
  // 获取代理类的构造器对象
  Constructor constructor = clazz.getConstructor(new Class[] {InvocationHandler.class});
  // 反射创建代理对象
  HelloService proxy = (HelloService)constructor.newInstance(myInvocationHandler);				
  ```

  ```java
  //方法二
  HelloService proxy = (HelloService)Proxy.newProxyInstance(HelloService.class.getClassLoader(),
  helloService.getClass().getInterfaces(), new MyInvocationHandler(helloService));
  ```

  * 使用代理

  ```java
  proxy.hello("rico");
  proxy.hi("panda");
  
  /** Output
  PROXY : com.sun.proxy.$Proxy0
  hello : Hello rico
  PROXY : com.sun.proxy.$Proxy0
  hi : Hi, panda
  **/
  ```

###### 3. cglib动态代理



#### 四. 引用

* 深入理解代理模式：静态代理与JDK动态代理：<https://blog.csdn.net/justloveyou_/article/details/79407248>

* 【Spring基础】JDK动态代理实现原理(jdk8)：<https://blog.csdn.net/yhl_jxy/article/details/80586785>

* Spring基础】CGLIB动态代理实现原理：https://blog.csdn.net/yhl_jxy/article/details/80633194


