## 引用

java中的引用，类似C语言中的指针，java的数据类型分为两大类，基本数据类型和引用数据类型

基本数据类型：编程语言中内置最小细粒度的数据类型，它包含四大类八种类型：

4种整数类型：byte,short,int,long

2种浮点数类型:float,double

1种字符类型:char

1中布尔类型:boolean

引用类型:引用类型指向一个对象，不是原始值，指向对象的变量是引用变量，在java里，除了基本类型，其他类型都是引用类型，他主要包括：类，数组，枚举，注解

每种编程语言都有自己的数据处理方式。有些时候，程序员必须注意将要处理的数据类型是什么类型，你是操作元素，还是某种基于特殊语法的间接表示，例如c/c++里的指针，来操作对象，所有这些在java里都得到简化，一切都被视为对象，因此，我们可采用一种统一的语法。尽管将一切看做对象，但操纵的标识符实际是指向一个对象的"引用"。(reference)

### 强引用

在java中最常见的引用就是强引用，把一个对象赋值给一个引用变量，这个引用变量就是一个强引用。类似一个引用变量，这个引用变量就是一个强引用，类似 Object obj=new Object(); 这类的引用。

当一个对象被强引用变量引用时，它处于可达状态，是不可能被垃圾回收器回收的，即使对象永远不会被用到也不会被回收。

当内存不足时，jvm开始垃圾回收，对于强引用的对象，就算是出现OOM也不会对该对象进行回收，因此强引用也有时造成java内存泄漏的原因之一。

对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显示的将相应(强)引用赋值为null，一般认为就是可以被垃圾回收器回收。

```java
Object o1=new Object();
Object o2=o1;
o1=null;
System.out.println(o1); //null
System.out.println(o2);//java.lang.Object@2503dbb3
```



### 软引用

软引用是一种相对强引用弱化了一些引用，需要用"java.lang.ref.SoftReference"类来实现，可以让对象豁免一些垃圾回收

软引用用来描述一些还有用，但并非必须的对象，对于软引用关联的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围中并进行第二次回收，如果这次回收还是没有足够的内存，才会抛出内存溢出异常。

对于只有软引用的对象来说，当系统内存充足是它不会被回收，系统内存不足时才会被回收。

```java
//VM options: -Xms5m -Xmx5m
public class SoftReferenceDemo{
    public static void main(String[] args){
        softRefMemoryEnough();
        System.out.println("---内存不足情况---");
        softRefMemoryNotEnough();
    }
    private static void softRefMemoryEnough(){
        Object o1=new Object();
        SoftReference<Object> s1=new SoftReference<>(o1);
        System.out.println(o1);
        System.out.println(s1.get());
        
        o1=null;
        System.gc();
        
        System.out.println(o1);
        System.out.println(s1.get());
    }
    
    //jvm配置 -Xms5m -Xmx5m  最后故意new一个大对象，是内存不足产生oom看软引用对象的回收情况
    private static void softRefMemoryNotEnough(){
        Object o1=new Object();
        SoftReference<Object> s1=new SoftReference<Object>(o1);
        System.out.println(o1);
        System.out.println(s1.get());
        
        o1=null;
        
        byte[] bytes=new byte[10*1024*1024];
        
        System.out.println(o1);
        System.out.println(s1.get());
    }
}
```



### 弱引用

弱引用也就是用来描述非必须对象的，但是他的强度比软引用更弱一些，被软引用关联的对象只能生存到下一次垃圾回收发生之前，当垃圾收集器工作的时候，无论当前内存是否足够，都会回收掉只被弱引用的对象

弱引用需要用"java.lang.ref.WeakReference"类来实现，它比软引用的生存周期更短。

对于只有弱引用的对象来说，只要垃圾回收机制一运行，不管jvm的内存是否足够，都会回收改对象占用的内存

```java
Object o1=new Object();
WeakReference<Object> w1=new WeakReference<Object>(o1);

System.out.println(o1);
System.out.println(w1.get());

o1=null;
System.gc();

System.out.println(o1);
System.out.println(w1.get());
```

官方文档中这么写的，弱引用常被用来实现规范化映射，jdk中的WeakHashMap就是这样的例子

### 虚引用

虚引用也称为"幽灵引用"或者"幻影引用",它是最弱的一种引用关系

顾名思义，也就是形同虚设，与其他的几种引用都不太一样，一个对象是否有虚引用的存在，完全不会对生存时间构成影响，也无法通过虚引用来取得一个对象的实例

虚引用需要"java.lang.ref.PhantomReference" 来实现

如果一个对象持有虚引用，那么它就和没有任何引用一样，在任何时候都有可能被垃圾回收，他不能单独访问也不能通过它访问对象，虚引用必须和引用队列(ReferenceQueue)联合使用

虚引用的作用主要的作用就是跟踪对象垃圾回收状态，仅仅是提供了一种确保对象被finalize以后，做某些事情的机制

PhantomReference的get方法总是返回null，因此无法访问对应的引用对象，其意义在于说明一个对象已经进入finalization阶段，可以被回收，用来实现finalization机制更灵活的回收操作

换句话说，设置弱引用的唯一目的，就是在这个对象被回收器回收的时候收到一个系统通知，或者后续添加进一步处理



