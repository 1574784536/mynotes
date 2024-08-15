# JDK8新特性

Java 8 (又称为 jdk 1.8) 是 Java 语言开发的一个主要版本。 Oracle 公司于 2014 年 3 月 18 日发布 Java 8 ，它支持函数式编程，新的 JavaScript 引擎，新的日期 API，新的Stream API 等。

> 目前本文记录jdk 5之后的所有jdk版本的一些较常用的新特性,不一定局限于jdk 8这一个版本.

Java 8是自Java  5（2004年）发布以来Java语言最大的一次版本升级，Java 8带来了很多的新特性，比如编译器、类库、开发工具和JVM（Java虚拟机）。在这篇教程中我们将会学习这些新特性，并通过真实例子演示说明它们适用的场景。

# 时间与日期

Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。

在旧版的 Java 中，日期时间 API 存在诸多问题，其中有：

- **非线程安全** − java.util.Date 是非线程安全的，所有的日期类都是可变的，这是Java日期类最大的问题之一。
- **设计很差** − Java的日期/时间类的定义并不一致，在java.util和java.sql的包中都有日期类，此外用于格式化和解析的类在java.text包中定义。java.util.Date同时包含日期和时间，而java.sql.Date仅包含日期，将其纳入java.sql包并不合理。另外这两个类都有相同的名字，这本身就是一个非常糟糕的设计。
- **时区处理麻烦** − 日期类并不提供国际化，没有时区支持，因此Java引入了java.util.Calendar和java.util.TimeZone类，但他们同样存在上述所有的问题。



新的`java.time`包涵盖了所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作。

新的时间相关API主要在java.time包以及其4个子包下

- java.time : 所有核心的类都在此包下
- java.time.chrono: 此包下的类型都是为了处理非默认的iso-8601日历系统的
- java.time.format: 此包下的类型用来解析日志与时间类的
- java.time.temporal:扩展API,主要给框架与库作者使用.
- java.time.zone: 支持时区的相关类

Java 8 在 **java.time** 包下提供了很多新的 API。以下为两个比较重要的 API：

- **Local(本地)** − 简化了日期时间的处理，没有时区的问题。
- **Zoned(时区)** − 通过制定的时区处理日期时间。

下面的Local下的示例

```java
// 获取当前的日期时间
      LocalDateTime currentTime = LocalDateTime.now();
      System.out.println("当前时间: " + currentTime);
        
      LocalDate date1 = currentTime.toLocalDate();
      System.out.println("date1: " + date1);
        
      Month month = currentTime.getMonth();
      int day = currentTime.getDayOfMonth();
      int seconds = currentTime.getSecond();
        
      System.out.println("月: " + month +", 日: " + day +", 秒: " + seconds);
        
      LocalDateTime date2 = currentTime.withDayOfMonth(10).withYear(2012);
      System.out.println("date2: " + date2);
        
      // 12 december 2014
      LocalDate date3 = LocalDate.of(2014, Month.DECEMBER, 12);
      System.out.println("date3: " + date3);
        
      // 22 小时 15 分钟
      LocalTime date4 = LocalTime.of(22, 15);
      System.out.println("date4: " + date4);
        
      // 解析字符串
      LocalTime date5 = LocalTime.parse("20:15:30");
      System.out.println("date5: " + date5);
```

下面是时区下的示例

```java
// 获取当前时间日期
      ZonedDateTime date1 = ZonedDateTime.parse("2015-12-03T10:15:30+05:30[Asia/Shanghai]");
      System.out.println("date1: " + date1);
        
      ZoneId id = ZoneId.of("Europe/Paris");
      System.out.println("ZoneId: " + id);
        
      ZoneId currentZone = ZoneId.systemDefault();
      System.out.println("当期时区: " + currentZone);
```



> 参考这些文章看看即可
>
> https://juejin.im/post/5addc7a66fb9a07aa43bd2a0
>
> https://www.liaoxuefeng.com/article/991339711823296

# 接口

## 默认方法

Java 8 新增了接口的默认方法。简单说，默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。我们只需在方法名前面加个 default 关键字即可实现默认方法。

```java
public interface A {
   default void a(){
      System.out.println("aaa");
   }
}
```

> *首先，之前的接口是个双刃剑，好处是面向抽象而不是面向具体编程，缺陷是，当需要修改接口时候，需要修改全部实现该接口的类，目前的 java 8 之前的集合框架没有 foreach 方法，通常能想到的解决办法是在JDK里给相关的接口添加新的方法及实现。然而，对于已经发布的版本，是没法在给接口添加新方法的同时不影响已有的实现。所以引进的默认方法。他们的目的是为了解决接口的修改与现有的实现不兼容的问题*

默认方法只能作用于接口的方法定义上,不能用在类里面.

如果一个类实现了多个接口,这些接口中有同名的默认方法,就需要进行处理,否则会报错,处理的方法就是完全改写接口的实现或者指定用哪一个接口的实现.

完全改写:

```java
public interface A {
   default void a(){
      System.out.println("aaa");
   }
}
 
public interface B {
   default void a(){
      System.out.println("aaaaaaa");
   }
}

public class C implements A,B {

	@Override
	 public default void a() {
		System.out.println("完全改写");
	}
}
```

指定某一个接口的实现:

```java
public interface A {
   default void a(){
      System.out.println("aaa");
   }
}
 
public interface B {
   default void a(){
      System.out.println("aaaaaaa");
   }
}

public class C implements A,B {

	@Override
	 public default void a() {
		A.super.a(); //或者B.super.a();
	}
}
```



## 静态方法

默认方法也可以是静态的,比如下面:

```java
public interface A {
    // 静态方法
   static void b(){
      System.out.println("aaaa");
   }
}
```

只需要加个static关键字就可以了,不需要加default关键字.

## 函数接口

函数式接口(Functional Interface)就是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。比如下面的接口就是一个函数接口,只有一个抽象方法c

```java
public interface A {
	default void a() {
		System.out.println("a in A");
	}
	
	static  void b() {
		System.out.println("static b in A");
	}
	void c();
}
```

如果你的接口满足函数接口的定义,你***可以***在此接口上加一个注解`@FunctionalInterface`.加了此注解后,如果你的接口不是函数接口,编译器会报错.

函数接口除了可以像普通接口一样使用外,主要是与lambda一起结合使用.JDK 1.8 之前已有的函数式接口:

- java.lang.Runnable
- java.util.concurrent.Callable
- java.security.PrivilegedAction
- java.util.Comparator
- java.io.FileFilter
- java.nio.file.PathMatcher
- java.lang.reflect.InvocationHandler
- java.beans.PropertyChangeListener
- java.awt.event.ActionListener
- javax.swing.event.ChangeListener

JDK 1.8 新增加的函数接口主要在`java.util.function`包下.这个package中的接口大致分为了以下四类：

- Function: 接收参数，并返回结果，主要方法 `R apply(T t)`
- Consumer: 接收参数，无返回结果, 主要方法为 `void accept(T t)`
- Supplier: 不接收参数，但返回结果，主要方法为 `T get()`
- Predicate: 接收参数，返回boolean值，主要方法为 `boolean test(T t)`

此包下所有类的分类情况如下图:

![image-20190624170240932](http://ww1.sinaimg.cn/large/006tNc79gy1g4ccs1pbg3j30sq18m40p.jpg)

比如下面的代码就使用了function包下的Predicate接口,此接口接受一个参数,返回一个逻辑值.案例是输出所有的偶数.

```java
public static void main(String[] args) {
		List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
		System.out.println("输出所有数据:");

		filter(list, new Predicate<Integer>() {
			@Override
			public boolean test(Integer t) {
				return t % 2 == 0;
			}
		});

	}

	public static void filter(List<Integer> list, Predicate<Integer> predicate) {
		for (Integer n : list) {

			if (predicate.test(n)) {
				System.out.println(n + " ");
			}
		}
	}
```



# try with resource

在JDK 7 之前,使用一些需要关闭的资源对象时,是必须要关闭的,而且一般的写法如下:

```java
BufferedWriter writer = null;
try {
    writer = Files.newBufferedWriter(file, charset);
    writer.write(s, 0, s.length());
} finally {
    if (writer != null) writer.close();
}
```

try-with-resources 是 JDK 7 推出的一种新的资源访问操作方式,它能够很容易地关闭在 try-catch 语句块中使用的资源。所谓的资源（resource）是指在程序完成后，必须关闭的对象。try-with-resources 语句确保了每个资源在语句结束时关闭。

所有实现了 [java.lang.AutoCloseable](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html) 接口（其中，它包括实现了 [java.io.Closeable](https://docs.oracle.com/javase/8/docs/api/java/io/Closeable.html) 的所有对象,因为Closeable接口是AutoCloseable接口的子类），可以使用作为资源。

try with Resources的基本语法如下:

```java
try(Resource resource = new Resource()){
  resource.doSth();
}
```

> 这种写法如果在try代码块以及try with resources(就是隐藏的finally块)都抛出异常的话,try块的异常会被抛出,try with resources抛出的异常会被抑制(suppress)

这种写法等价于下面的代码:

```java
Resource resource = null;
try{
  resource = new Resource();
  resource.doSth();
}finally {
  resource.close();
}
```

> 这种写法如果try块与finally块都抛出异常,那么finally块的异常会被抛出,而try块的异常会被抑制(suppress),这点与try with resources相反

如果有多个资源对象,可以像下面这样写

```java
try(Resource1 resource1 = new Resource1();
   Resource2 resource2 = new Resource2()){
  
}
```

如果在try里面有多个资源,那么在finally块的close方法的调用顺序刚好与try里面变量的顺序相反.意思是先resources2.close后resource1.close().

那么上面的BufferredWriter的例子就可以转换为下面的写法:

```java
BufferedWriter writer = null;
try( writer = Files.newBufferedWriter(file, charset)) {  
    writer.write(s, 0, s.length());
} 
```

如果你在try之前已经声明了一个资源类的变量,想使用try with resource这种写法,需要下面这样写

```java
//try之前声明的变量,一个有final修饰,一个无
final Resource resource1 = new Resource("resource1");
Resource resource2 = new Resource("resource2");
try (Resource r1 = resource1;
     Resource r2 = resource2) {
    
}
```

而在JDK 9 之后针对这种写法已经有了改进,只要try之前声明的变量是final或等价于final形式的变量(比如上面例子的resource2对象,赋值之后没有改变其值),那么就可以像下面这样写:

```java
try (resource1;
     resource2) {
    
}
```

当然在使用try with resources时,你可以直接加上catch语句,它仍然有try with resources的使用效果(也就是说仍然会自动调用close方法)

```java
try (resource1;) {
    
}catch(Exception e){
  
}
```

当然,这种情况下执行的顺序是try块,finally块,catch块

# lambda

lambda是jdk 8 推出的一个非常有用的新特性,允许把一段代码作为一个方法参数值传递给被调用方法.

## 什么是lambda

lambda表达式(lambda expression)是一段代码,此段代码可以赋值给一个变量也可以作为参数值传递给其它函数.而这段代码的类型是一个函数接口

比如你声明了一个函数接口

```java
interface MyLambdaInf {
	void doSth(String name);
}
```

你就可以这样声明一个变量,其值赋值一个lambda表达式

```java
MyLambdaInf lambda = (s)->System.out.println(s);
lambda.doSth("cj");//注意:不是写成lambda("cj");
```

lambda表达式可以理解成把代码当成了数据一样使用.类似int a = 5中的数字5一样使用.

lambda的主要作用如下:

- 让代码简洁,易读:相比以前创建一个实现类或者用匿名类来实现接口来说简单太多
- 让函数式编程的代码易写,易读

## lambda的语法

完成的语法格式如下:

```java
(参数列表)->{语句块}
```

参数列表间用逗号分隔,语句块的写法与函数体写法一样,比如下面的代码

```java
(int a,int b)-> {
  int result = a + b;
  return result;
}
```

基于编译器的类型推断能力,参数的类型是可以省略掉的,所以上面的代码可以改进为:

```java
(a,b)-> {
  int result = a + b;
  return result;
}
```

如果你的语句块只有一行代码,那么大括号是可以省略的,并会自动把表达式的结果返回,所以上面的代码可以简化为:

```java
(a,b)-> a + b;
```

## 方法引用

方法引用通过方法的名字来指向一个方法。它是一种特殊的lambda表达式.方法引用使用一对冒号 **::** 。

比如上面的MyLambdaInf接口的赋值可以采用下面的形式

```java
MyLambdaInf lambda = System.out::print;
```

当然print的方法签名必须与接口中的方法签名兼容.

方法引用有如下4种引用形式

- 构造器引用: 它的语法是Class::new
- 静态方法引用:它的语法是Class::static_method
- 特定对象的方法引用:它的语法是instance::method
- 特定类的任意对象的方法引用:它的语法是Class::method,这种情况与上面类似,但不需要额外先创建一个对象出来.

方法引用的基本使用方法如下:

```java
List<String> names = new ArrayList();       
names.add("a");
names.add("b");    
names.forEach(System.out::println);
```

### 特定类的任意对象的方法引用

特定类的任意对象的方法引用的案例如下:

```java
String[] stringArray = { "Barbara", "James", "Mary", "John",
    "Patricia", "Robert", "Michael", "Linda" };
Arrays.sort(stringArray, String::compareToIgnoreCase);
```

上面的代码等价于下面的代码 

```java
Arrays.sort(stringArray, (a,b) -> a.compareToIgnoreCase(b));
```

其实compareToIgnoreCase这个实例方法的签名如下:

```java
public int compareToIgnoreCase(String str)
```

而sort方法的第二个参数类型Comparator接口中的compare方法签名如下:

```java
int compare(T o1, T o2);
```

这二者并不匹配,本不能这样直接把String::compareToIgnoreCase传递给sort方法的.但由于此是实例方法,你可以认为编译器自动帮你创建了一个Comparator接口的实现类,代码如下:

```java
class Foo implements Comparator<Integer>{
 int compare(Integer s1, Integer s2) {
     return s1.compareToIgnoreCase(s2);
 }
}
```

这个类中的compare方法实现体就是你通过方法引用指定的内容.此方法第一个参数s1作为调用compareToIgnoreCase的对象,第二个参数作为compareToIgnoreCase的参数.

下面的例子更能说明这种方法引用的情况.有一个类的代码如下:

```java
class Person {
	private String name;
	private boolean gender; // 性别 true表示男生
	private int age;
	
	public void setNameAndAge(String name,int age) {
		this.name = name;
		this.age = age;
	}
  //省略掉getter,setter toString等
}
```

一个函数接口如下:

```java
@FunctionalInterface
interface LambdaInterface
{
    // 注意：入参比Person类的setNameAndScore方法多1个Person对象，除第一个外其它入参类型一致
    public void set(Person d, String name, int age);
}
```

在使用时可以像下面这样使用

```java
LambdaInterface lambdaInterface = Person::setNameAndAge;
Person person = new Person();
lambdaInterface.set(person, "abc", 22);

System.out.println(person);
```

函数接口的set方法的第一个参数是一个Person类型,后面的2个参数与Person类的setNameAndAge的参数个数,顺序与类型是一样的.两个方法签名本来不匹配的,但编译器可以通过set方法识别出可能赋值一个Person对象的方法,而可能赋值的Person对象的方法的参数与set方法除了第一个参数外的其它参数情况一样.

## 常见使用场景

lambda表达式最常用的使用场景就是调用某个方法时,作为参数值传递给被调用的方法.

假定有一个Person类与一个Person类的List集合以及一个依据各种过滤规则找出符合条件的所有人的方法

```java
class Person {
  private String name;
  private boolean gender; //性别 true表示男生
  private int age;
  //getter setter 各种构造函数 省略
}
interface Filter {
  boolean doFilter(Person p);
}
class Main {
  public static void main(String[] args) {
    List<Person> people = Arrays.asList(
      new Person("a",true,20),
      new Person("b",false,20),
      new Person("abc",false,20),
      new Person("def",true,20)
    );
    //找出所有的男生
    List<Person> matched = find(people,p->p.isGender()==true);
  }
  static List<Person> find(List<Person> people,Filter filter) 	{
    List<Person> result = new ArrayList<>();
    for(Person p: people){
      if(filter.doFilter(p)) {
        result.add(p);
      }
    }
    return result;
  }
}

```

当然,你也可以去掉Filter接口,直接使用function包下的类型Predicate,那么find方法的代码可以改为如下的形式,其它的不变

```java
static List<Person> find2(List<Person> people,Predicate<Person> filter) 	{
    List<Person> result = new ArrayList<>();
    for(Person p: people){
      if(filter.test(p)) {
        result.add(p);
      }
    }
    return result;
}
```

# Optional

下面的代码我们想得到一个版本号:

```java
String version = computer.getSoundcard().getUSB().getVersion();
```

但为了防止空引用异常,我们通常会这么写代码

```java
String version = "UNKNOWN";
if(computer != null){
  Soundcard soundcard = computer.getSoundcard();
  if(soundcard != null){
    USB usb = soundcard.getUSB();
    if(usb != null){
      version = usb.getVersion();
    }
  }
}
```

这种代码简直丑出天际,但为了代码的健壮性你通常都需要这么写,Optional类的出现就是为了缓解这种问题的.

java.util包下的Optional<T> 类可以被认为是一个单值的容器,它要么包含一个值,要么不包含(Empty)

上面的代码可以用下面的代码来取代

```java
Optional<Computer> computer = Optional.of(new Computer());
		String name = computer.flatMap(Computer::getSoundcard).flatMap(Soundcard::getUSB).map(USB::getVersion)
				.orElse("UNKNOWN");


class Computer {
	public Optional<Soundcard> getSoundcard() {
		// return Optional.of(new Soundcard());
		return Optional.ofNullable(null);
		// return null; // 不要返回null;
	}
}

class Soundcard {
	public Optional<USB> getUSB() {
		return Optional.of(new USB());
	}
}

class USB {
	public String getVersion() {
		return "1.0";
	}
}
```



## 创建

Optional对象的创建主要利用此类的下面3个方法来实现

- empty
- of
- ofNullable

```java
Optional<Soundcard> sc = Optional.empty(); 

SoundCard soundcard = new Soundcard();
Optional<Soundcard> sc = Optional.of(soundcard); 

Optional<Soundcard> sc = Optional.ofNullable(soundcard); 
```

empty方法表明Optional没有包含值,而of方法表明包含有值,如果你传递一个null给of,执行到这行代码时立即抛出异常,而不用等到后续使用对象时才抛出异常.ofNullable表明可能包含值也可能不包含值.

## 包含值的处理

isPresent可以用来检查Optional类型是否包含值,比如

```java
if(soundcard.isPresent()){
  System.out.println(soundcard.get());
}
```

其中get方法在Optional包含值时返回值,否则抛出`NoSuchElementException`,此异常是一个RuntimeException.但这种写法代码不精练，体现不出Optional类型的优点，更多的时候会使用ifPresent方法来处理包含值的情况

如果Optional包含有值的话,你可以利用ifPresent方法做一些事情,比如:

```java
Optional<Soundcard> soundcard = ...;
//如果soundcar不包含值,不报错,什么都不干
soundcard.ifPresent(System.out::println); 
```

而不用像以前的写法:

```java
SoundCard soundcard = ...;
if(soundcard != null){
  System.out.println(soundcard);
}
```



## 不包含值的处理

如果Optional不包含值,你可能想给一个默认值或者干些其它的处理,比如以前的代码可能这么写

```java
Soundcard soundcard = 
  maybeSoundcard != null ? maybeSoundcard 
            : new Soundcard("basic_sound_card");
```

现在就可以直接这么写

```java
Soundcard soundcard = maybeSoundcard.orElse(new Soundcard("defaut"));
```

orElse有值时返回值,无值时返回orElse参数指定的值.

类似的，orElseThrow方法在无值的时候会抛出异常,比如:

```java
Optional<Soundcard> soundcard = Optional.ofNullable(null);
Soundcard result = 
  soundcard.orElseThrow(IllegalStateException::new);
```

## filter

通常情况下,你会检查某个对象的某个属性或方法返回值是否满足某个条件,满足之后再干一些事情,比如

```java
USB usb = ...;
if(usb != null && "3.0".equals(usb.getVersion())){
  System.out.println("ok");
}
```

现在用Optional你就把上面的代码改写为下面的形式

```java
Optional<USB> maybeUSB = ...;
maybeUSB.filter(usb -> "3.0".equals(usb.getVersion())
                    .ifPresent(() -> System.out.println("ok"));
```

如果有值filter才会执行参数中的Predicate,如果Predicate返回true就会返回一个包含值的Optional,否则返回empty的Optional.

## map

通常情况下,你判断一个对象是否为null,如果不为null就调用其方法得到一个值,比如下面的代码

```java
if(soundcard != null){
  USB usb = soundcard.getUSB(); //得到soundcard对象的USB对象
  if(usb != null && "3.0".equals(usb.getVersion()){
    System.out.println("ok");
  }
}
```

这种情况就可以利用map方法来实现,比如

```java
maybeSoundcard.map(Soundcard::getUSB)
      .filter(usb -> "3.0".equals(usb.getVersion())
      .ifPresent(() -> System.out.println("ok"));
```

map方法的主要作用就是检查是否为null并提取一个值出来的场景

```java
Optional<USB> usb = maybeSoundcard.map(Soundcard::getUSB);
```

## flatMap

有时会出现Optional类嵌套其它Optional对象的情况,比如下面的代码

```java
Optional<Computer> computer = Optional.ofNullable(null);

Optional<USB> usb =computer
	.map(Computer::getSoundcard)
  .map(Soundcard::getUSB)
```

computer调用map方法时是可以的,但调用之后由于getSoundcard方法本身返回的就是Optional类型,然后map返回的也是Optional类型,这样相当于返回了一个Optional<Optional<Soundcar>>类型.所以上面的会编译出错.

这个时候需要通过flatMap来拉平这种嵌套的情况,所以上面的代码可以改进为下面这样

```java
Optional<Computer> computer = Optional.ofNullable(null);
Optional<USB> usb = computer
	.flatMap(Computer::getSoundcard)
  .flatMap(Soundcard::getUSB);
```

第一个flatMap方法返回Optional<Soundcar>,而不是Optional<Optional<Soundcard>>,第二个flatMap类似.

因此我们可以利用Optional提供的功能,达成我们最开始希望达成的功能,就是让下面的代码不会出现空引用异常的问题.

```java
String version = computer.getSoundcard().getUSB().getVersion();
//改写为:
String version = computer.map(Computer::getSoundcard)
                  .map(Soundcard::getUSB)
                  .map(USB::getVersion)
                  .orElse("UNKNOWN");
```



> flatmap方法的参数如果传递的是个方法，那么此方法的返回类型必须是Optional的
>
> map方法的参数如果传递的是个方法，那么此方法的返回类型就不必是Optional的，可以是也可以不是

## OptionalInt、OptionalDouble、OptionalLong

为了避免不必要的装箱与拆箱导致的性能损耗，对于int，double，long这3个基本数据类型推荐用对应的可选类型，用法与通用的Optional是类似的。

```java
OptionalInt price = OptionalInt.of(10);
System.out.println(price.orElse(-1));//输出10

OptionalInt price2 = OptionalInt.empty();
System.out.println(price2.orElse(-1)); //输出-1
```



## 错误使用示例

Brian Goetz和Steward Marks（Java语言架构师）聚在一起写下了以下段落：

> Our intention was to provide a limited mechanism for library method return types where there needed to be a clear way to represent “no result”, and using null for such was overwhelmingly likely to cause errors.
>
> 我们的目的是为库方法的返回类型提供一种有限的机制，其中需要一种明确的方式来表示“无结果”，并且对于这样的方法使用null绝对可能导致错误。

Optional主要用于作为***返回类型***（主要解决的问题是臭名昭著的空指针异常（NullPointerException）），并将其与流(或返回可选的方法)相结合以构建连贯API。

在这个网址，博主自己总结了26个错误的Optional使用示例，值得阅读。https://blog.csdn.net/summer_lyf/article/details/102975055

简言之，记住2个准则：

- Optional只用在方法返回类型
- 方法不允许返回null的Optional类型,不要让Optional自己的null影响它设计之初作为其它对象容器方便null处理的目标

```java
public Optional<String> doSth(){
  return Optional.empty();
}//ok

public Optional<String> doSth(){
  return null;
}//不允许
```



# stream

java 8 引入了流式操作（Stream),它可以让你像sql语句一样以声明的方式来处理数据。它的定义为:`a sequence of elements from a source that supports aggregate operations.` 意思就是说从数据源得到的支持聚合操作的元素序列.比如:

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
List<Integer> twoEvenSquares = 
    numbers.stream()
           .filter(n -> {
                    System.out.println("filtering " + n); 
                    return n % 2 == 0;
                  })
           .map(n -> {
                    System.out.println("mapping " + n);
                    return n * n;
                  })
           .limit(2)
           .collect(toList());
```

## 简介

根据操作返回的结果不同，流式操作分为中间操作(**Intermediate**)和最终操作(**Terminal**)两种。最终操作返回一特定类型的结果，而中间操作返回流本身，这样就可以将多个操作依次串联起来。

根据流的并发性，流又可以分为串行和并行两种。流式操作实现了集合的过滤、排序、映射等功能。

## 基本使用流程

当我们使用一个流的时候，通常包括三个基本步骤：

- 获取一个数据源（source）
- 数据转换
- 执行操作获取想要的结果

每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道，如下图所示。

![image-20190625213852666](http://ww1.sinaimg.cn/large/006tNc79ly1g4dqdo2fu2j30ai080dfy.jpg)

比如上面的代码中的`numbers`,它是一个数据源,通过调用stream方法变为一个流,然后进行一系列的转换操作,比如filter,map,最后通过collect完成最终的操作.

像filter,map这些都是中间操作,并不会真正去操作数据源,相当于是对数据源的一个操作描述,只有类似collect的最终操作发起时,才会真正去操作数据源.总而言之,stream的中间操作是延迟操作的形式.

中间操作可以链在一起形成一个管道(pipeline),因为每一个中间操作返回的都是Stream,而最终操作会关闭管道并生成一个最终的结果.

上面代码最终的输出如下,它并没有遍历每一个元素,因为limit是一个短路(short-circuiting)操作,只要得到所需的2条记录后结束运算了.

```
filtering 1
filtering 2
mapping 2
filtering 3
filtering 4
mapping 4
```

## 流的创建

流的创建主要通过以下方式实现

- Stream.empty(): 创建一个空流,以避免返回null
- 通过集合的stream()来创建
- 通过数组创建流
  - Stream.of
  - Arrays.stream
- Stream的builder方法
- Stream的generate方法
- Stream的iterate方法
- 基本类型的流
  - IntStream
  - LongStream
  - DoubleStream
- 字符串流:利用字符串的chars方法
- 通过文件创建Stream

### empty流

此方法常用来创建一个空的流而避免空引用异常

```java
Stream<String> streamEmpty = Stream.empty();

public Stream<String> streamOf(List<String> list) {
    return list == null || list.isEmpty() ? Stream.empty() : list.stream();
}
```

### 从集合创建流

流也可以从任何集合(Collection)类型中创建出来,比如List,Set

```java
Collection<String> collection = Arrays.asList("a", "b", "c");
Stream<String> streamOfCollection = collection.stream();
```

### 从数组创建流

数组也可以作为流的数据源(source),一般利用Stream.of和Arrays.stream来创建

```java
Stream<String> streamOfArray = Stream.of("a", "b", "c");
```

如果已经有一个数组类型,你可以通过下面的方法创建一个流

```java
String[] arr = new String[]{"a", "b", "c"};
Stream<String> streamOfArrayFull = Arrays.stream(arr);
```

或者从数组中的部分元素中创建流

```java
Stream<String> streamOfArrayPart = Arrays.stream(arr, 1, 3);
```

> 在java 8,你既可以用Arrays.stream也可以用Stream.of来转换一个数组为流.对于对象数组来说,这2个方法是一样的,比如下面的代码
>
> ```java
> String[] array = {"a", "b", "c", "d", "e"};
> 
> //Arrays.stream
> Stream<String> stream1 = Arrays.stream(array);
> stream1.forEach(x -> System.out.println(x));
> 
> //Stream.of
> Stream<String> stream2 = Stream.of(array);
> stream2.forEach(x -> System.out.println(x));
> ```
>
> 但对于基本类型的数组来说,两者是不一样的,比如下面的代码
>
> ```java
> int[] intArray = {1, 2, 3, 4, 5};
> 
>    // 1. Arrays.stream -> IntStream 
>    IntStream intStream1 = Arrays.stream(intArray);
>    intStream1.forEach(x -> System.out.println(x));
> 
>    // 2. Stream.of -> Stream<int[]>
>    Stream<int[]> temp = Stream.of(intArray);
> 
>    // 不能直接打印输出Stream<int[]>,需要转换为IntStream
>    IntStream intStream2 = temp.flatMapToInt(x -> Arrays.stream(x));
>    intStream2.forEach(x -> System.out.println(x));
> 
> ```
>
> 对于基本类型的数组来说,建议用Arrays.stream的方式来转换.因为其返回固定大小的IntStream.也便于操作

### 利用String创建流

String类型也可以作为流的数据源,通过chars方法得到的是IntStream.

```java
IntStream streamOfChars = "abc".chars();
```



### 基本类型流

需要注意的是，对于基本数值型，目前有三种对应的包装类型 Stream：

IntStream、LongStream、DoubleStream。当然我们也可以用 Stream<Integer>、Stream<Long> >、Stream<Double>，但是 boxing 和 unboxing 会很耗时，所以特别为这三种基本数值型提供了对应的 Stream。

Java 8 中还没有提供其它数值型 Stream，因为这将导致扩增的内容较多。而常规的数值型聚合运算可以通过上面三种 Stream 进行。

数值流的构造方法如下:

```java
IntStream.of(new int[]{1, 2, 3}).forEach(System.out::println);
IntStream.range(1, 3).forEach(System.out::println);
IntStream.rangeClosed(1, 3).forEach(System.out::println);
```

### 利用builder来创建流

调用Stream类型的builder方法也可以创建流,调用builder泛型方法时需要指定创建流的类型,否则就是Stream<Object>类型的流

```java
Stream<String> stream = Stream.<String>builder().add("aaa").add("bbb").build();
```

### 利用generate来创建流

Stream类型的generate方法可以生成一个无限的流,使用此方法时需要提供Supplier<T>的参数用来确定流中的内容.

```java
		Stream<String> streamGenerated =
				  Stream.generate(() -> "element").limit(10);
```

### 利用iterate来创建流

Stream类型的generate方法可以生成一个无限的流,iterate方法的第一个参数表示开始值,第二个参数是利用iterate的第一个作为初始值来生成值,比如下面的代码就会得到1,3,5这三个数组成的流

```java
Stream<Integer> streamIterated = Stream.iterate(1, n -> n + 2).limit(3);
```

### 从Files创建流

java NIO的Files类的lines方法可以创建一个Stream<String>类型的流,代表着文件中的所有行.

```java
Path path = Paths.get("C:\\file.txt");
Stream<String> streamOfStrings = Files.lines(path);
Stream<String> streamWithCharset = 
  Files.lines(path, Charset.forName("UTF-8"));
```



## stream的常见操作

对流的操作从整体上进行分类的话,它分为不产生最终结果的中间操作(*intermediate*)与可以产生最终结果的最终操作(*terminal*)

### 中间操作

该操作会保持 stream 处于中间状态，允许做进一步的操作。中间操作是延迟执行的,它返回的还是 Stream,允许更多的链式操作。常见的中间操作有：

- filter()：对元素进行过滤；

- sorted()：对元素排序；

- map()：元素的映射

- distinct()：去除重复元素；
- limit:限制元素的数量
- skip:跳过元素的数量
- peek:返回一个流,对流中的每个元素执行Consumer<T>操作
- parallel:获取并行流
- sequential:获得串行流
- unordered:返回无序流

- subStream()：获取子 Stream 等。



> Intermediate operations return a new stream. They are always lazy; executing an intermediate operation such as filter() does not actually perform any filtering, but instead creates a new stream that, when traversed, contains the elements of the initial stream that match the given predicate. Traversal of the pipeline source does not begin until the terminal operation of the pipeline is executed.
>
> 每一个延迟操作都会创建一个新的流并且记录下操作，在最后调用立即执行的方法时会把这些记录的操作一个个的执行。可以理解为类似下面的代码
>
> ```java
> public Stream filter(Predicate p) {
>     this.filter = p; // just store it, don't apply it yet
>     return this; // in reality: return a new stream
> }
> public List collect() {
>     for (Object o : stream) {
>         if (filter.test(o)) list.add(o);
>     }
>     return list;
> }
> ```
>
> 

#### filter

下面是对一个字符串集合进行过滤，返回以“s”开头的字符串集合，并将该集合依次打印出来：

```java
list.stream()
.filter((s) -> s.startsWith("s"))
.forEach(System.out::println);
```

这里的 filter(...) 就是一个中间操作，该中间操作可以链式地应用其他 Stream 操作。

#### map

以下代码片段使用 map 输出了元素对应的平方数：

```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
// 获取对应的平方数
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

#### flatmap

从上面例子可以看出，map 生成的是个 1:1 映射，每个输入元素，都按照规则转换成为另外一个元素。还有一些场景，是一对多映射关系的，这时需要 flatMap。

```java
Stream<List<Integer>> inputStream = Stream.of(
 Arrays.asList(1),
 Arrays.asList(2, 3),
 Arrays.asList(4, 5, 6)
 );
Stream<Integer> outputStream = inputStream.
flatMap((childList) -> childList.stream());
```

flatMap 把 input Stream 中的层级结构扁平化，就是将最底层元素抽出来放到一起，最终 形成的新Stream里面已经没有 List 了,都是直接的数字。

#### limit

limit 方法用于获取指定数量的流。 以下代码片段使用 limit 方法打印出 10 条数据：

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 终止操作

该操作必须是流的最后一个操作，一旦被调用，Stream 就到了一个终止状态，而且不能再使用了。常见的终止操作有：

- forEach()：对每个元素做处理；
- forEachOrdered:有序的遍历方式

- toArray()：把元素导出到数组；

- findFirst()：返回第一个匹配的元素；
- findAny
- iterator

- anyMatch()：是否有匹配的元素等。
- allMatch
- noneMatch
- collect: 规约操作
- reduce
- min
- max
- count

#### forEach

例如，下面是对一个字符串集合进行过滤，返回以“s”开头的字符串集合，并将该集合依次打印出来：

```java
list.stream() //获取列表的 stream 操作对象
.filter((s) -> s.startsWith("s"))//对这个流做过滤操作
.forEach(System.out::println);
```

这里的 forEach(...) 就是一个终止操作，该操作之后不能再链式的添加其他操作了。

#### collect

Collectors 类实现了很多归约操作,例如将流转换成集合和聚合元素。Collectors 可用于返回列表或字符串

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream()
  .filter(string -> !string.isEmpty())
  .collect(Collectors.toList());
 
System.out.println("筛选列表: " + filtered);

String mergedString = strings.stream()
  .filter(string -> !string.isEmpty())
  .collect(Collectors.joining(", "));
System.out.println("合并字符串: " + mergedString);
```

##### summaryStatistics

一些产生统计结果的收集器也非常有用。它们主要用于int、double、long等基本类型上，它们可以用来产生类似如下的统计结果

```java
IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();
		 
		System.out.println("列表中最大的数 : " + stats.getMax());
		System.out.println("列表中最小的数 : " + stats.getMin());
		System.out.println("所有数之和 : " + stats.getSum());
		System.out.println("平均数 : " + stats.getAverage());
```

## 并行流与串行流

流有串行和并行两种，串行流上的操作是在一个线程中依次完成，而并行流则是在多个线程上同时执行。并行与串行的流可以相互切换：通过 stream.sequential() 返回串行的流，通过 stream.parallel() 返回并行的流。相比串行流，并行流可以很大程度上提高程序的执行效率。

下面是用串行的方式对集合进行排序:

```java
List<String> list = new ArrayList<String>();
for(int i=0;i<1000000;i++){
  double d = Math.random()*1000;
  list.add(d+"");
}
long start = System.nanoTime();//获取系统开始排序的时间点
int count= (int) ((Stream) list.stream().sequential()).sorted().count();
long end = System.nanoTime();//获取系统结束排序的时间点
long ms = TimeUnit.NANOSECONDS.toMillis(end-start);//得到串行排序所用的时间
System.out.println(ms+”ms”);
```

下面是用并行的方式对集合进行排序:

```java
List<String> list = new ArrayList<String>();
for(int i=0;i<1000000;i++){
  double d = Math.random()*1000;
  list.add(d+"");
}
long start = System.nanoTime();//获取系统开始排序的时间点
int count = (int)((Stream) list.stream().parallel()).sorted().count();
long end = System.nanoTime();//获取系统结束排序的时间点
long ms = TimeUnit.NANOSECONDS.toMillis(end-start);//得到并行排序所用的时间
System.out.println(ms+”ms”);
//串行输出为 1200ms，并行输出为 800ms。可见，并行排序的时间相比较串行排序时间要少不少。
```



# 参考资料

https://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html

https://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html

https://www.ibm.com/developerworks/cn/java/j-lo-jdk8newfeature/index.html

https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/index.html

https://www.runoob.com/java/java8-new-features.html

https://www.baeldung.com/java-8-streams

https://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html

https://www.oracle.com/technetwork/articles/java/architect-streams-pt2-2227132.html

https://stackify.com/streams-guide-java-8/（几乎有所有方法的例子）

https://jaxenter.com/java-8-streams-lazy-136183.html (说明了flatmap不是延迟执行的，这可能是jdk 8的一个bug，我在mac的jdk 1.8.0_201版本测试出现了作者说的问题)





