# Singleton 单例设计模式

单例设计模式的实现关键在于将类的构造函数设置成private已到达阻止外部实例化该类的目的，
在由内部实例化一个静态实例以供外部使用。从实例化的方式来分，可分为饿汉模式和懒汉模式，饿汉模式既是不管该类是否需要被使用都将在类初始化的时候直接实例化内部静态实例，而懒汉模式则是在该类第一次需要被使用时才实例化内部静态实例。

需要注意的是，由于懒汉模式使用的是延迟加载，所以存在线程安全问题，需要使用同步机制


Eagerly initialized - 饿汉模式
``` java
public class Singleton {
    // 类加载的时候直接进行初始化，线程安全
    private static final Singleton INSTANCE = new Singleton();
    
    private Singleton(){
        
    }
    public static Singleton getInstance(){
        return INSTANCE;
    }
}
```    

JDK中的`java.lang.Runtime`中使用的既是这种模式

lazily initialized - 懒汉模式

``` java
public class Singleton {
    private static Singleton instance ;

    private Singleton(){
        if(instance == null){
            instance = this;
        }else{
            throw new IllegalStateException("Already initialize.");
        }
    }

    // 直接对方法使用synchronized，保证Singleton不会被重复创建
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }

}
```
双重检查版本
``` java
public class Singleton {
    private static volatile Singleton instance ;

    private Singleton(){
        if(instance != null){
            throw new IllegalStateException("Already initialize.");
        }
    }

    public static Singleton getInstance(){
        // 使用局部变量可提高性能 -- Effective Java
        Singleton result = instance;                // (1)
        if(result == null){
            synchronized (Singleton.class){         // (2)
                // 由于从(1)到(2)的过程中存在其他线程已经完成实例化的可能
                // 因此需要再次同步result和instance值
                result  = instance;
                if(result == null){
                    instance = result = new Singleton();
                }
            }
        }
        return result;

    }
}

```

Effective Java中基于enum-枚举的单例模式
``` java
public enum  Singleton {
    INSTANCE;
    
    private Singleton(){
        initDate = new Date();
    }
    
    private Date initDate;
    
    public Date getInitDate(){
        return initDate;
    }
    
}
```
此处直接将枚举当做一个类来使用，利用枚举本身即是基于单例实现的特点来实现单例模式
对于上面这个类的使用如下
``` java
Singleton singleton = Singleton.INSTANCE;
Date date = singleton.getInitDate();
```

