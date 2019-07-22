---
title:  "几种常见的设计模式"
permalink: /design-pattern/
date: 2019-07-22

categories: [基础, 设计模式]

tags: [design, pattern, basic]
---

[TOC]

# 几种常见的设计模式

GoF 的著作《设计模式-可复用面向对象软件的基础》讲述了 23 个设计模式。这里我只对几种常见的设计模式进行记录和总结。包括 单例、工厂、策略、适配器、代理、装饰者、观察者、模板 等模式。

## 简单工厂

简单工厂不是一个标准的设计模式，但其非常常用。**其作用是提供一个创建对象实例的功能**，以封装创建对象的接口或实现类。被创建实例的类型可以是接口、抽象类，也可以是具体的类。

```java
/**
* 接口的定义，该接口可以通过简单工厂来创建
*/
public interface Api {
	public void operation (String s) ;
}

/**
* 接口的具体实现对象A
*/
public class ImplA implements Api {
	public void operation (String s) {
		//实现功能的代码，示意一下
		System.out.println ("ImplA s=="+s) ;
	}
}

/**
* 接口的具体实现对象B
*/
public class ImplB implements Api {
	public void operation (String s) {
    //实现功能的代码，示意一下
    System.out.println("ImplB s=="+s) ;
	}
}

/**
*工厂类，用来创建Api对象
*/
public class Factory {
  /**
  *具体创建Api对象的方法
  @param condition 示意，从外部传入的选择条件
  @return创建好的Api对象
  */
  public static Api createApi (int condition) {
    //应该根据某些条件去选择究竟创建哪-一个 具体的实现对象，
    //这些条件可以从外部传入，也可以从其他途径来获取。
    //如果只有一个实现，可以省略条件，因为没有选择的必要。
    //示意使用条件
    Api api = null;
    if (condition == 1) {
    	api new ImplA() ;
    }else if (condition == 2) {
    	api = new ImplB() ;
    }
    return api;
  }
}

/**
*客户端，使用Api接口
*/
public class Client{
  public static void main(String[] args) {
    //通过简单工厂来获取接口对象
    Api api = Factory.createApi (1) ;
    api. operation("正在使用简单工厂") ;
  }
}
```

这个时候，客户端就不用再自己去创建接口的对象了，可以使用工厂来获取，即由

```java
Api api = new Impl ()
```

变为

```java
Factory.createApi (1)
```

**对对象的创建方法进行了封装**。同时也实现了解耦。

上例是通过客户端传递参数的方法实现简单工厂内部的选择实现，还可以通过可配置的简单工厂，利用发射，来对客户端需要的对象进行创建。这样的话如果有新的实现方法，就可以不用重写简单工厂中的代码了。

配置文件：

```properties
ImplClass=cn.javass.dp.simplefactory.example5.Impl
```

### 利用发射机制的简单工厂

```java
/**
*工厂类，用来创建Api对象
*/
public class Factory {
  /**
  *具体创建Api的方法，根据配置文件的参数来创建接口
  *@return创建好的Api对象
  */
  public static Api createApi () {
    //直接读取配置文件来获取需要创建实例的类
    //至于如何读取Properties,还有如何反射在这里就不解释了
    Properties P = new Properties () ;
    InputStream in = null;
    try {
      in = Factory.class .getResourceAsStream ("FactoryTest.properties") ;
      p.load(in) ;
    } catch (IOException e) {
      System.out.println ("装载工厂配置文件出错了，具体的堆栈信息如下: ");
      e.printStackTrace() ;
    } finally{
      try {
        in.close() ;
      } catch (IOException e) {
        e.printStackTrace () ;
      }
    }
     //用反射去创建，那些例外处理等完善的工作这里就不做了
    Api api = null;
    try {
      api = (Api)Class. forName (P. getProperty (" ImplClass") )
      newIns tance ( ) ;
    } catch (InstantiationException e) {
      e.printStackTrace() ;
    } catch (IllegalAccessException e) {
      e.printStackTrace() ;
    } catch (ClassNotFoundException e) {
      e.printStackTrace() ;
    }
  	return api;
  }
}

/**
* 利用配置文件，客户端即不用再传入参数，实现了解耦和可复用
*/
public class Client {
	public static void main(String[] args) {
		Api api = Factory.createApi() ;
		api.test1 ("测试利用反射改写的简单工厂! ");
  }
}
```

### 与其他工厂模式的区别

#### 简单工厂和抽象工厂模式

**简单工厂是用来选择实现的，可以选择任意接口的实现**。一个简单工厂可以有多个用于选择并创建对象的方法，多个方法创建的对象可以有关系也可以没有关系。
**抽象工厂模式是用来选择产品簇的实现的，也就是说一般抽象工厂里面有多个用于选择并创建对象的方法**，但是这些方法所创建的对象之间通常是有关系的，这些被创建的对象通常是构成一个产品簇所需要的部件对象。所以从某种意义上来说，简单工厂和抽象工厂是类似的，**如果抽象工厂退化成为只有一个实现，不分层次，那么就相当于简单工厂了**。

#### 简单工厂和工厂方法模式
简单工厂和工厂方法模式也是非常类似的。工厂方法的本质也是用来选择实现的，跟简单工厂的区别在于**工厂方法是把选择具体实现的功能延迟到子类去实现**。
如果把工厂方法中选择的实现放到父类直接实现，那就等同于简单工厂。

#### 简单工厂和能创建对象实例的模式
简单工厂的本质是选择实现，所以它可以跟其他任何能够具体的创建对象实例的模式配合使用，比如:单例模式、原型模式、生成器模式等。

## 抽象工厂

