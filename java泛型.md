# 为什么需要泛型

java的泛型(Generic)是JDK 5推出的一种特性,所谓的泛型从字面意思来理解就是泛化的类型,不确定类型的东西.想了解为什么需要泛型就先看看不使用泛型的情况下的2个典型缺点

## 类型转换问题

```java
List list = new ArrayList();
list.add(new Integer(1)); 
Integer i = list.iterator().next();//编译出错.
```

上面代码的最后一行是会编译出错的,因为ArrayList集合中可以存放Object类型的数据,所以其返回的是Object,虽然你知道里面存放的就是一个Integer,你也不得不进行转型操作.更严重的问题是:如果你往ArrayList中添加了一个不能转换为Integer的类型,在编译时不会出现问题错误,但在运行时会报类型转换异常.

## 重复代码编写问题

想象一下,如果你自己编写ArrayList类的add方法,你如果想让ArrayList支持存放Integer,String,Double等类型,那么你的add方法的参数只能是Object,或者是多个add方法的重载以实现分别添加Integer,String,Double等数据.

这几个重载方法的实现肯定是类似的,这属于重复代码的编写,不利于后期维护.而且还有一个致命的问题是:你无法预先就知道要编写多少个add方法的重载.因为你可能想让ArrayList集合也支持存放用户自定义的类型.

这样的话就无法通过重载实现这个添加数据的功能,你最后唯一的选择可能就是只写一个add方法,其参数类型是Object.但这样写就会出现上面的问题:类型转换问题.

想让ArrayList集合支持多种类型数据的存储,又不想出现类型转换问题与重复代码编写问题几乎就是矛盾的.正是这种情况java才推出了泛型.

# 泛型类型使用

## 泛型类的实例化

下面是泛型类型的使用.其实例化方式在jdk8下有如下几种方式

```java
//第一种方式
ArrayList<String> al = new ArrayList<String>();
//第二种方式,推荐的方式
ArrayList<String> al = new ArrayList<>();
//第三种方式
ArrayList<String> al = new ArrayList();
```

第一种方式是最完整的泛型类型声明方式,而第二种由于编译器类型推断的原因,所以可以省略掉实际类型参数值,这也是建议的泛型类型使用方式.而第三种方式虽然可以编译通过,集合里面也只能放String类型数据.但不建议.因为其把一个原生(raw)类型对象赋值给了一个泛型对象.

上面的方式声明之后,集合中只能放String类型的数据了.这样不存在类型转换方面的问题.

ArrayList类型是个泛型类型,可以实例化ArrayList为存放各种类型数据的对象,比如下面的代码就往集合里面存放了Integer类型的数据

```java
ArrayList<Integer> al = new ArrayList<>();
```

但泛型类型声明时不能指定基本数据类型,比如下面的代码就会报错

```java
ArrayList<int> al = new ArrayList<>();
```

这是因为泛型类型经过泛型擦除后,其内部把类型参数都转换为Object类型或边界类型,而这些类型都是引用类型,基本类型不能直接转换为Object类型,所以报错,不排序后续jdk的功能进行增强,让泛型类型中可以指定基本类型.

## 泛型方法的调用

调用泛型方法的完整语法如下:

```java
<类型实参,...>方法名()
```

但通常由于有编译器的类型推断,调用泛型方法与调用普通方法是类似的,这样上面的语法可以简写为

```java
方法名()
```

在ArrayList类中有个泛型方法toArray,其方法签名如下:

```java
 <T> T[] toArray(T[] a);
```

此方法的完整语法调用方式如下:

```java
ArrayList<Integer> al = new ArrayList<>();
al.add(5);
Integer[] intArray = new Integer[1];
al.<Integer>toArray(intArray);
```

简化的调用方式如下

```java
al.toArray(intArray);
```

## 类型推断

类型推断是java编译器的能力,用来简化泛型类型实例化以及泛型方法调用的写法.让代码更简洁.类型推断主要推断以下几个方面

- 实例化泛型类的类型实参
- 泛型方法调用的实参
- 如果可以的话,推断赋值语句的类型或者方法返回的类型
- 推断算法尽力推断最具体的类型

前面的2点已经在前面讲过,关于最后一点,假设有下面一个泛型方法,经过类型推断后确定T实参类型为Serializable,因为String与ArrayList类型都实现了这个接口.

```java
static <T> T pick(T a1, T a2) { return a2; }
Serializable s = pick("d", new ArrayList<String>());
```

### 泛型构造函数的推断

下面的代码即推断除了X的类型为Integer也推断除了构造函数的类型参数T的类型为String

```java
class MyClass<X> {
  <T> MyClass(T t) {
    // ...
  }
}

MyClass<Integer> myObject = new MyClass<>("");
```

### 目标类型(Target Types)

在调用泛型方法时,编译器会利用目标类型的优势来推断类型参数.java编译器会依据表达式出现的地方来确定表达式的目标类型,比如Collections.emptyList方法的签名如下:

```java
static <T> List<T> emptyList();
```

当你直接调用此方法时,比如`Collections.emptyList()`,编译器是不能推断是T的实参是什么,你只能用List<Object>来接收.但如果编写下面的语句,可以很明确的判断出此语句需要一个List<String>,因而编译器就可以推断出T的实参就是String

```java
List<String> listOne = Collections.emptyList();
```

或者下面这样使用,编译器也可以推断出来(注意:java se 8 之前不能推断出来,会报错)

```java
void processStringList(List<String> stringList) {
    // process stringList
}
//推断出T实参为String
processStringList(Collections.emptyList());
```

## 泛型与子类型

Number类型是Double,Integer等类的父类,所以你可以把Integer类型的变量赋值给Number类型.因为继承就确定了is-a关系,Integer与Double都是Number.

```java
Number num = null;
Integer i = 5;
num = i;
```

如果你实例化一个List<Number>类型的对象出来,能往里添加Double类型的数据与Integer数据吗?

```java
ArrayList<Number> numList = new ArrayList<Number>();
Integer i = 5;
Double d = 5.5;
numList.add(i);
numList.add(d);
```

上面的代码是正确的,因为numList是一个存放Number类型数据的集合,而Integer,Double由于继承关系,他们两就是Number类型,所以可以往集合里面添加Integer与Double数据.

再看看下面的代码,在把整数集合赋值给Number集合的时候是会报错的.错误的信息是不能把ArrayList<Integer>类型转换为ArrayList<Number>的.这是因为ArrayList<Number>类本质上与ArrayList<Integer>类没有任何的关系,也不存在继承关系,所以无法赋值.他们两只不过都是由ArrayList<T>这个工厂类创建出来的2个新类型而已.这两个新的类型的父类就是Object而不是ArrayList类型.

```java
ArrayList<Number> numList = new ArrayList<Number>();
numList = new ArrayList<Integer>();//报错
```

这种情况可以用下图表示

![image-20190726093615214](http://ww2.sinaimg.cn/large/006tNc79gy1g5czpad25gj30bv09rdgj.jpg)

那么实例化之后的泛型类型之间有没有继承关系呢?如果有的话是一个什么原理确定的呢.一个泛型实例(假定A)与另一个泛型实例(假定B)有继承关系由以下几个条件确定

- A的泛型类型继承或实现了B的泛型类型
- 二者实例化时用的是同一个具体的类型

比如ArrayList<T>继承了List<T>,所以List<String>是ArrayList<String>的父类型,List<Integer>是ArrayList<Integer>的父类型,但List<String>与ArrayList<Integer>就不是父子关系.可以用下图表示

![image-20190726095114631](http://ww3.sinaimg.cn/large/006tNc79gy1g5d04v616dj305805qglo.jpg)

所以你可以用下面的方式来使用ArrayList集合

```java
List<String> list = new ArrayList<>();
```

假定你自己创建了额外的泛型类型,其继承了List并且用了同样的类型参数,但比List多了一个类型参数,比如下面这样

```java
interface SubList<E,T> extends List<E>{
  
}
```

那么SubList<String,String>是List<String>的子类型,SubList<String,Exception>也是List<String>的子类型,但SubList<Integer,String>***不是***List<String>的子类型,因为E代表的类型实参在SubList指定的是Integer,在List中指定的是String.可以用下图说明

![image-20190726095959270](http://ww4.sinaimg.cn/large/006tNc79gy1g5d0dz0c0yj30g7050mxn.jpg)

用代码表示是如下的情况

```java
List<String> list = ....;
SubList<String,Integer> subList = ...;
list = subList;
```

## 原始类型

原始类型也称之为原生类型,是raw type的中文翻译.指的是一个泛型类或接口去掉类型参数之后的名字作为原生类型的名字,比如下面的泛型类的原生类型称之为Box

```java
public class Box<T>{
  public void set(T t){}
}
```

这个技术是为了向后兼容的考虑,因为在jdk5之前是没有泛型的.把jdk中的一些类型实现为泛型后会导致过去使用这些类型的程序出现问题,所以就推出了这样一个技术.正因为如此,实例化的泛型类型对象是可以直接赋值给原生类型的.

```java
Box<String> stringBox = new Box<>();
Box<Integer> intBox = new Box<>();
Box rawBox = stringBox;  
rawBox = intBox; 
```

但是反过来你把原生类型赋值给泛型类型就是有问题的,不报错但会有警告,主要的原因是编译器无法确定原生类型与泛型类型的兼容性,可能会导致后续的类型不安全的问题.比如下面的rawBox可能代表一个String的box对象.

```java
Box rawBox = new Box();     
Box<Integer> intBox = rawBox; 
```

同样的道理,通过原生类型调用泛型方法时也会弹出警告.

```java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;
rawBox.set(8);  
```

总而言之,在JDK5之后的程序中要杜绝使用原生类型,除非是修改老版本的程序,因为使用原生类型会导致类型检查从编译时推迟到运行时,给程序带来隐患.



# 泛型类型

泛型类型(Generic Type)是一个参数化的类或者接口.泛型类的声明语法如下:

```java
class 类名<T1, T2, ..., Tn> { /* ... */ }
```

泛型类的声明就是在类名之后紧跟一个尖括号,在尖括号内放置类型参数(Type Parameter).像上面的T1,T2等都是类型参数,这些类型参数也称之为类型形式参数或者类型变量(type variables).

类型参数可以有一个,也可以有多个,多个之间用逗号分隔.下面就创建了一个只有一个类型参数的泛型类

```java
public class Box<T>{
  
}
```

下面的代码是创建了有2个类型参数的类

```java
public class Pair<K,V> {
  
}
```

泛型接口的声明与泛型类的声明是一样的语法规则.比如

```java
public interface Boxable<T>{}
```

## 类型参数命名习惯

类型参数的名字与变量的命名规则是一样的,但通常会用一个大写的字母来命名.下面是一些常用的类型参数名字

- E: Element代表元素的意思,一般放在集合中,表示往集合中添加的数据.
- K:Key,代表键
- V:Value,代表值
- N: number,代表数字
- T:type,代表类型
- S,U,V等:代表第2,3,4个类型参数.

比如下面的代码

```java
public class Box<T,U,V>{}
```

## 实例化泛型类

实例化泛型类也称之为泛型类的实例化(parameterized type),为了声明一个泛型类型的变量,你必须用一个具体的类型替换掉类型形参,这个类型称之为类型实参(type argument),比如

```java
Box<Integer> integerBox; //Integer就是类型实参
```

如果要给integerBox赋值,就需要创建一个对象出来,泛型类对象的创建与普通类的对象创建是类似的.只是需要在类名与括号之间添加尖括号,在尖括号内部填上类型实参即可.比如

```java
Box<Integer> integerBox = new Box<Integer>();
```

由于JDK 8 增强的编译器推断能力,上面的代码可以简写为

```java
Box<Integer> integerBox = new Box<>();
```

泛型类有多个类型参数,其实例化也是类似的,比如

```java
Pair<Integer,String> pair = new Pair<>();
//或
Pair<Integer,String> pair = new Pair<Integer,String>();
```

下面的box泛型类用来装数据的,可以是String数据,也可以是Integer数据,同时还可以从这个盒子(box)泛型类中取数据.

```java
public class Box<T> {  
  private T t;
  public void add(T t) {
    this.t = t;
  }
 
  public T get() {
    return t;
  }
}
```

使用方法如下:

```java
Box<Integer> integerBox = new Box<Integer>();
Box<String> stringBox = new Box<String>();

integerBox.add(new Integer(10));
stringBox.add(new String("abc"));

System.out.printf("整型值为 :%d\n\n", integerBox.get());
System.out.printf("字符串为 :%s\n", stringBox.get());
```

从上面的介绍来看,你可以把泛型类看做是一个类型工厂,声明了一个泛型类等价于声明了无数个类型! 

下面是存放字符串的盒子类

```java
public class Box {  
  private String t;
  public void add(String t) {
    this.t = t;
  }
 
  public String get() {
    return t;
  }
}
```

下面是存放Integer类型的盒子类

```java
public class Box {  
  private Integer t;
  public void add(Integer t) {
    this.t = t;
  }
 
  public Integer get() {
    return t;
  }
}
```

所有这种存放数据的盒子类都统一可以用一个泛型类来代表.这也是T这种符号被称之为类型形参的意思,T只是某个具体类型的代表,是一个占位符一样的东西.

# 泛型方法

泛型方法是方法声明的时候声明了类型参数的方法,这些方法可以在一个非泛型类中声明也可以在一个泛型类中声明,这些泛型方法可以是静态的也可以是非静态的.泛型方法中声明的类型参数只能在此方法范围内使用,不能用在别的方法里.

泛型方法的类型参数可以有一个,也可以有多个,之间用逗号分隔,这些类型参数也放在尖括号内,而这个尖括号放置在方法返回类型之前.比如下面

```java
public <T>  T doSth(){}
```

类型参数可以作为方法的参数类型

```java
public <T>  void doSth(T t){}
```

也可以作为方法的返回类型

```java
public <T>  T doSth(String t){}
```

也可以完全不用它,虽然语法是可以的,但这种情况就没有必要声明为一个泛型方法

```java
//没有必要声明为一个泛型方法
public <T>  Integer doSth(String t){} 
```

比如下面的一个泛型方法是用来打印输出任意引用类型数组中的数据

```java
public class ArrayUtil
{                      
   public static <E> void printArray(E[] inputArray )
   {          
         for ( E element : inputArray ){        
            System.out.println(  element );
         }
    }
}
```

此方法使用方法如下:

```java
Integer[] intArray = { 1, 2 };
Double[] doubleArray = { 1.1, 2.2 };  

System.out.println( "整型数组元素为:" );
//由于intArray是个整形数组,并且printArray方法的参数利用类型参数
// 这样编译器就可以推断出类型参数的实参就是Integer类型,所以可省略实参
ArrayUtil.printArray( intArray  ); 

System.out.println( "\n双精度型数组元素为:" );
ArrayUtil.<Double>printArray( doubleArray ); 
```



# 类型参数边界

假定你要编写一个方法用来比较2个对象,你可能会这样写:

```java
	public static <T> int compare(T x, T y){
		return x.compareTo(y);
	}
```

但上面的代码是不会编译通过的,因为T只是一个类型形参,你预先是无法知道传递过来的类型实参是什么类型的,有可能是一个实现了Comparable接口的类型,有可能不是.碰到这种情况你就需要给类型参数加上约束,比如传递过来的类型实参都必须是实现了Comparable接口的类型

给类型参数添加限定就称之为给类型参数设定边界,设定边界的语法是

```java
<T extends 边界类型>
```

这个边界类型可以是一个类也可以是一个接口.这个边界类型称之为上边界(upper bound).这样就限定了类型实参必须是这个边界类型或者是其子类型或实现类.比如下面的情况

```java
class A{	}
class B extends A{}
class C extends B{}

public static <T extends B> void doSth(T x){
}

doSth(new A());//报错
doSth(new B());//ok
doSth(new C());//ok
```

所以上面的compare方法就可以改写为下面的情形.

```java
public static <T extends Comparable<T>> int compare(T x, T y){
  return x.compareTo(y);
}
```

下面的方法指定的上边界是Number类.

```java
 public <U extends Number> void inspect(U u){
    System.out.println("U: " + u.getClass().getName());
}
```

## 多个边界

给类型参数指定边界的时候是可以指定多个限制条件的,这些边界之间用`&`分隔,比如

```java
<T extends B1 & B2 & B3>
```

指定多个边界之后,类型参数就是这3个边界的子类型,但由于java是单继承的语言,所以多个边界最多只能有一个是类,如果有一个边界是类,那么它必须是第一个,比如

```java
Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }
// A必须是第一个出现在extends关键字后的
class D <T extends A & B & C> { /* ... */ }
```

# 类型参数通配符

假定你实现一个功能,需要对List<Number>,List<Integer>这种***数字(Number)列表***类型的数据进行处理(比如求和).方法的签名该怎么设计呢?设计成如下3种情况都是不行的

```java
void doSth(List<Number> list){} //第一种
void doSth(List<Integer> list){} //第二种
<T> void doSth(List<T> list){} //第三种
```

第一种与第二种不行的原因是一样的,都是只能处理一种类型,而第三种可以处理多种类型,但没有任何限定,List<String>类型的数据也可以传递给方法.但下面这种方法设计是可行的

```java
<T extends Number> void doSth(List<T> list){} //第四种
```

第四种方式可行,但声明方法过于麻烦,我们本质的目标是想对数字列表类型进行处理,但List<Number>与List<Integer>没有父子关系,导致没有一个合适的父类型作为方法的参数.如果有的话就可以简化泛型的使用.这也就是通配符推出的作用.

## 通配符的作用

在泛型代码中,通配符用`?`表示,表示某一个未知的类型.它主要的目标是给泛型实例确立父子关系,比如

```java
List<?> commonList = null;
List<Integer> intList = null;
List<String> strList = null ;
commonList = intList;
commonList = strList;
```

用图表示为如下:

![image-20190729145630850](http://ww4.sinaimg.cn/large/006tNc79gy1g5gptha72ej30aj049dfx.jpg)

所以List<?>代表着所有的List泛型类的实例,是所有List实例的父类型.通配符是一个实参层面的概念,不是泛型类型中形参层面的概念.比如下面所有出现通配符的代码都是错误的

```java
class SomeClass<?>{  
  public <?> doSth(){}
  public <T extends ?> doSth2(){}
  public void doSth3(? param){}
  public <?> doSth4(? param){}
}
```



通配符通常作为参数,字段或局部变量的类型,有时也作为返回类型(但不推荐).比如下面的代码

```java
class SomeClass{
  private List<?> data = null; // 作为字段
  public void doSth(List<?> list){ //作为方法的参数
    List<?> anotherList = list; //作为局部变量
  }
}
```

但通配符不能作为泛型类的实例化时指定的具体类型,因为?代表着某个未知类型,实例化一个泛型类时必须是一个确定的类型,比如

```java
ArrayList<String> al = new ArrayList<String>(); // 正确
ArrayList<?> al = new ArrayList<?>(); //错误
```

通配符也不能作为类型参数用在泛型方法的调用上,比如

```java
List<?> list = null;
list.<String>get(0); //编译不报错
list.<?>get(0); // 编译报错
```

> java数组有协变性,而泛型默认没有协变性与逆变性,它具有不变性,而通过通配符以及上下界来达成类似协变与逆变的效果.https://www.jianshu.com/p/2bf15c5265c5这个网址有关于逆变与协变的通俗讲解

## 通配符上边界

List<?>代表着所有的List泛型类的实例,Box<?>代表着所有的Box泛型的实例,通配符的上下边界让其只代表同泛型类的部分实例.

通配符的上边界是靠extends表示的,基本写法如下

```java
<? extends 边界类型>
```

比如List<? extends Number> 就代表着所有的List实例,其中实例的具体类型是Number类型或者其子类型.代码示例如下

```java
List<?> commonList ;
List<? extends Number> list ;
list = new ArrayList<Integer>();
list = new ArrayList<Double>();
list = new ArrayList<Number>();
list = new ArrayList<String>();// 错误
commonList = list;
commonList = new ArrayList<String>();
```

回到最开始讲通配符的案例,doSth方法就可以改写为下面的形式来实现目的

```java
 void doSth(List<? extends Number> list){
   double s = 0.0;
    for (Number n : list)
      //有上边界限制就可以使用Number类型中的doubleValue方法
        s += n.doubleValue(); 
    return s;
 }
```

这种写法明显相比类型参数(第四种)的写法要简单一些.方法使用时就可以接受各种数字类型的List了.比如

```java
doSth(new ArrayList<Number>());
doSth(new ArrayList<Integer>());
doSth(new ArrayList<Double>());
```

上边界特别适合从此泛型实例中取数据的情况,比如上面的读取doubleValue值.

## 通配符下边界

上边界是限定未知类型为指定类型或是其子类型,下边界刚好相反,它限制未知类型为指定类型或者是其父类型.通配符上界的基本语法如下

```java
<? super 边界类型>
```

你可以给通配符指定上界也可以指定下界,但不能同时指定上下界.确定上边界之后,就只代表此泛型类的部分实例了.比如List<? super Number>包含List<Number>,List<Object> 但不包含List<Integer>.代码如下

```java
List<?> commonList ;
List<? super Number> list ;
list = new ArrayList<Integer>();//错误
list = new ArrayList<Number>();
list = new ArrayList<Object>();
commonList = list;
commonList = new ArrayList<Integer>();
```

如果你想写一个方法,此方法的功能是往List集合中添加Integer值,但可以针对List<Integer>,List<Number>,List<Object>这3个list集合,那么方法设计就可以利用上界来完成,比如

```java
public  void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 10; i++) {
        list.add(i);
    }
}
```

上界特别适合往泛型实例中添加数据.有了通配符的上下界之后,整个泛型实例的父子关系图如下

![image-20190729162215483](http://ww3.sinaimg.cn/large/006tNc79gy1g5gsantl4oj30aw08a74z.jpg)

> 因为同时指定上下界相当于取交集,而这个交集通常为空或者就是指定的边界类型,这样还不如不用通配符.

## 无边界的通配符

无边界的通配符就是既不指定上界也不指定下界的通配符.比如List<?>,Box<?>都是无界通配符的例子.无界通配符通常用在如下两种场景下

- 如果你的方法使用了一个通配符的泛型实例,而你的方法实现只需要Object类提供的功能即可完成,比如下面的代码

```java
public  void printList(List<?> list) {
    for (Object elem : list)
        System.out.println(elem + " ");
    System.out.println();
}
```

- 当你使用了泛型类的方法,但这些方法并不依赖类型参数,比如List类型的size方法,clear方法.比如下面的代码

```java
public int count(List<?> list){
  return list.size();
}
```

### List<?> 与List< Object >

List<Object>表示一个List集合,此集合里面可以放Object及其子类,但List<Object>不能赋值为List<String> ,因为2者没有关系.比如下面的代码

```java
List<Object> objList ;
objList.add(new Object());
objList.add("abc");
objList = new ArrayList<String>();//报错
```

而List<?>代表所有的List集合,它可以代表List<Object> 也可以代表List<String>等.所以下面的代码是正确的

```java
List<?> commonList ;
commonList = new ArrayList<String>();
commonList = new ArrayList<Object>();
```

但List<?>除了插入null值外,什么也不能插入,比如

```java
List<?> commonList = new ArrayList<Object>();
commonList.add(null);
commonList.add("abc");//错误
commonList.add(new Object()); //错误
```

因为List<?>代表的任何的List,具体是哪一个是不确定的,所以什么都不能插入,只有所有引用类型都支持的null值可以插入.

## 通配符的捕获

在某些情况下,编译器会推断通配符的类型,比如声明为List<?>的变量,当编译器评估关于它的表达式的时候,会把?评估为某一个特定的具体类型,这称之为通配符捕获.比如下面的代码

```java
List<?> commonList ;
commonList.add("abc");//错误,字符串类型与捕获类型不匹配.
```

当编译器评估调用add方法这个表达式时,编译器推断add需要某个类型的数据,编译器用`Cap#1`这样的符号表示这个具体类型,而这个符号虽然代表某个具体的类型,但具体是哪一个是不确定,既不是String,也不是Integer,也不会是Foo,就是`Cap#1`.所以add方法你插入任何数据都不行,除了null.

下面看看一个更令人惊奇的例子.

```java
public class WildcardError {
    void foo(List<?> i) {
        i.set(0, i.get(0));//报错
    }
}
```

提示的错误信息是

```
The method set(int, capture#1-of ?) in the type List<capture#1-of ?> is not applicable for the arguments (int, capture#2-of ?)
```

出现这个错误的原因是编译器无法预先知道i.get(0)方法返回什么类型的数据,所以推断会返回`Capture#2`这样的类型,但这个类型能放到list集合吗?编译器也不知道,它单独推断set方法需要一个`Capture#1`这样类型的数据.而这两者明显是不兼容的,所以报错了.

如果要解决这个问题,就需要有明显的信息告诉编译器get与set用的是同样的类型.可以利用下面的方式解决

```java
public class WildcardFixed {
    void foo(List<?> i) {
        fooHelper(i);
    }
  
    private <T> void fooHelper(List<T> l) {
        l.set(0, l.get(0));
    }
}
```

通过定义一个泛型帮助(helper)方法,通过类型参数确定通配符就是具体的类型T,这样get与set都是同样的类型,就不会报错了.

> 个人看法:出现这种错误,我觉得是编译器不够强大或者实现不出错的情况,编译器实现太复杂导致的.因为如果编译器推断时充分考虑代码的上下文,上面的代码不应该编译出错.但很明显编译器推断的时候很难做到这样,因为List<?>的声明与set与get方法是在不同的地方的(set与get在List泛型类里,List<?>在foo方法的参数里).合起来统筹考虑十分困难.

### List<?> 与List< T >

粗看两者貌似是一样的,都"可以"接受任何类型的List泛型实例.比如下面的代码

```java
class WildcardError {
  void foo(List<?> list) {

  }

  <T> void foo2(List<T> list) {

  }
}

public static void main(String[] args) throws Exception {
		
		WildcardError wild = new WildcardError();
		wild.foo(new ArrayList<String>());
		wild.foo(new ArrayList<Integer>());
		
		wild.foo2(new ArrayList<String>());
		wild.foo2(new ArrayList<Integer>());
}
```

当调用foo或者foo2方法时都可以传递任何的List泛型实例.但两者本质上是不同的,比如下面的代码

```java
void bar(List<?> list1,List<?> list2) {
  list2.set(0,list1.get(0));//编译报错
}
<T> void bar2(List<T> list1,List<T> list2) {
    list2.set(0,list1.get(0));
}
```

上面bar直接编译出错,这是因为list1代表任意的list实例,list2也是,但两者没有关系,有可能list1你传递进来的是ArrayList<String>,而list2你传递进来的是ArrayList<Integer>,很明显不能把字符串设置到Integer列表中.但List<T>代表的是两个同类型的List集合,所以可以互相赋值,比如

```java
//编译不报错
wild.bar(new ArrayList<String>(),new ArrayList<Integer>());
//编译报错
wild.bar2(new ArrayList<Integer>(),new ArrayList<String>());
//编译不报错
wild.bar2(new ArrayList<Integer>(),new ArrayList<Integer>());
```

上面举的例子虽然是List这种泛型实例,但?与T的区别也可以用在其它的泛型类上,比如Box<?>,Box<T>这种.

## 通配符的使用指南

当使用通配符的时候,一个重要点是什么时候使用上界,什么时候使用下界.在了解使用上下界的时候,有必要先把变量分个类,以便后续原则的讲述.

- in变量:提供数据给代码,假定一个copy(src,dest)方法,此拷贝方法从src里提取数据然后复制到dest里,src就是in变量
- out变量:out变量使用数据,在上面copy的例子中,dest参数接收数据,所以它是out变量.

可以用下面的图形来表示:

![image-20190729221839431](http://ww3.sinaimg.cn/large/006tNc79ly1g5h2lh1z0oj30ia01kmx7.jpg)

通配符的使用指南如下

- in变量用上界
- out变量用下界
- in变量如果只需要使用Object类的方法,用无界通配符
- 如果一个变量既有in的功能也有out的功能,不要用通配符

前2个指南缩写为PECS(Produce Extend Consume Super).为什么有这2个原则呢?看看下面的代码:

```java
List<? extends Integer> list1 ;		
list1.add(new Integer(5)); //报错
Integer num = list1.get(0);

List<? super Integer> list2 ;
list2.add(new Integer(5)); 
Integer num2 = list2.get(0);//报错
```

上面的list1声明的时候用了extends,遵循了原则1,也就是PE原则,所以可以取值,list2声明的时候使用了super,遵循了原则2,也就是CS原则,所以可以往里面存数据.

list1往里添加数据出错的原因是List<? extends Integer>代表的是所有的List<Integer>以及List<Integer的子类型>,假定list1你赋值为一个List<Integer的子类型>,然后你往里添加一个属于父类型的Integer值,这很明显是不允许的.只能往里添加`Integer子类型`自身以及`Integer子类型`的子类型.相应的为什么能取出值呢,因为list1里面存放的数据一定是Integer或者其子类型的数据,所以可以用Integer或者Object接收返回的数据都是可以的.

list2为什么往里可以添加数据呢?原因就是list2代表的是所有的List<Integer>以及List<Integer的父类型> ,所以你往里面添加最底层的Integer数据是没有问题的.取出来的数据为什么不能用Integer接收,是因为list2可能指向一个List<Integer的父类型>的泛型实例,那么get就返回`Integer的父类型`,用一个Integer接收需要进行类型转换才行.

正式有这些问题,所以使用通配符时的上下界时尽量遵循PECS原则,至于第三个原则与第四个原则在前面已经讲述过了,这里不再赘述.

# 泛型擦除

泛型技术的引入是为了在编译时进行更严格的检查,以便提前发现错误.而不是等到运行时才发现错误.但java的泛型只是编译器层面的技术,经过编译之后,把泛型的相关信息擦除了(erasure).其擦除的逻辑如下

- 把泛型类型中的类型参数替换为它的边界类型,如果没有指定边界就替换为Object.
- 为了保留类型安全性,必要时会插入相关的类型转换的代码
- 扩展泛型类时,为了保留多态的特性,会生成桥接方法(bridge methods)

总而言之,泛型擦除是为了在实例化泛型类型时不会产生新的类,这样就不会给运行时代理额外的负担,所以才说泛型是编译器层面的技术,运行时是不知道泛型的信息的.

## 类型擦除

下面的泛型类没有设置边界,经过擦除后会把类型参数替换为Object

```java
public class Node<T> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}

//替换为:
public class Node {

    private Object data;
    private Node next;

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() { return data; }
    // ...
}
```

下面是有边界的泛型类,会把类型参数替换为边界类型

```java
public class Node<T extends Comparable<T>> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
//替换为
public class Node {

    private Comparable data;
    private Node next;

    public Node(Comparable data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Comparable getData() { return data; }
    // ...
}
```

正式由于类型擦除,所以下面的字符串节点类与整数节点类其实是同一个类

```java
Node<String> strNode = new Node<>("aa") ;
Node<Integer> intNode = new Node<>(5) ;
//下面的结果输出true
System.out.println(strNode.getClass() == intNode.getClass());
		
```



## 方法擦除

编译器也会擦除泛型方法参数中指定的类型参数信息,如果类型参数没有边界,就把类型参数替换为Object,比如

```java
public static <T> int count(T[] anArray, T elem) {
    int cnt = 0;
    for (T e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
//
public static int count(Object[] anArray, Object elem) {
    int cnt = 0;
    for (Object e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
```

如果类型参数有边界,就把类型参数替换为边界类型,比如

```java
public  <T extends Number> void doSth(T num) {  }
//替换为
public   void doSth(Number num) {  }
```

## 泛型对多态的支持

考虑下下面的2个类

```java
class Node<T> {
    public T data;
    public Node(T data) { this.data = data; }

    public void setData(T data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

 class MyNode extends Node<Integer> {
    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}
```

两个类经过擦除之后变成下面的类型

```java
class Node {
    public Object data;
    public Node(Object data) { this.data = data; }

    public void setData(Object data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

class MyNode extends Node {
    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}
```

这样MyNode类里面就有2个重载的setData方法,所以你执行下面的代码编译是不会报错的

```java
MyNode mn = new MyNode(5);
Node n = mn;       
n.setData("Hello");     
Integer x = mn.data;  
```

其中`n.setData("Hello")` 这行代码会调用参数类型为Object的setData方法,然后你取出数据用Integer接收,运行时就会产生类型转换异常.

本质上MyNode类继承Node类之后是想重写Node类中的setData方法,而不是重载,所以编译器碰到这种情况会在MyNode中插入一个桥接方法,把对参数类型为Object的setData的调用桥接到调用参数为Integer的方法上,以达到重写的效果,比如

```java
class MyNode extends Node {
  	public MyNode(Integer data) { super(data); }
    // 编译器生成的桥接方法
    public void setData(Object data) {
        setData((Integer) data);
    }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}
```

通常情况下,编译器编译一个继承参数化的泛型类或实现参数化泛型接口的类与接口时,编译器就可能创建一个桥接方法,桥接方法是合成(synthetic)方法的一种.主要的目标就是保留多态性.一般情况下这些都是编译器默默完成,你不用理会.这种桥接方法一般在反射或者异常堆栈跟踪信息时会考虑.平时使用根本需要知道桥接方法的存在.

# 泛型的限制

由于java泛型只是一个编译器层面的技术,经过擦除之后,在jvm层面没有泛型的相关信息,这也会给泛型带来一些使用限制.

TODO:下面的链接讲的都是限制,待删除

https://www.cnblogs.com/ixenos/p/5645851.html

https://blog.csdn.net/hanchao5272/article/details/79352321

## 不能使用基本类型

因为java的基本类型根本就不是一个类型,不具有面向对象的特性,虽然它叫"类型".而且泛型类经过擦除后,内部可能都是用Object替换掉了类型形参,实例化时需要的是类型作为实参.所以不允许.比如下面的代码是不允许的

```java
ArrayList<int> al = new ArrayList<int>();
```

> c# 支持值类型作为类型实参,因为c#中的基本类型都是结构,它是一个类型.而且c#的泛型实现不是靠擦除完成的
>
> 目前java的这个限制,我个人觉得没什么道理,也不排除后续的jdk能提供泛型对基本类型的支持.

## 不能创建类型参数的实例

比如下面的类的变量a是不能直接实例化的,因为下面的类经过类型擦除后直接变成了`Object a = new Object()`,这样a规定的调用Object的构造函数了,而如果我们传递一个Integer的实参,我们期望a的实例化代码应该是`Integer a = new Integer()`,两种根本不是一回事.所以java泛型禁止这样操作.

```java
public class A<T>{
  T a = new T(); //错误
}
```

从另外一个层面考虑,假定如果没有这个限制,可以实例化类型参数,那么你怎么预先知道T代表的类型有这样的构造函数,它的构造函数参数个数与类型你怎么预先知道呢?构造函数你一定能访问到吗?(比如private修饰).所以类型参数实例化是不允许的.

但是可以有一个变通的方法来实现,比如

```java
class A<T>{
	 T a ;
	 void set(Class<T> cls) throws Exception {
		 a = cls.newInstance();
	}
}
```

上面的方式是听过反射来创建一个类型的实例,下面就可以像下面这样调用set方法了.

```java
A<String> ins = new A<>();
ins.set(String.class);
```

## 不能声明类型为类型参数的静态字段.

```java
public class Container<T>{
  private static T data;//错误
}
```

因为静态是属于类的,而泛型类可以理解为一个类型工厂,它可以创建很多个具体的类型,到时候可以创建很多个不同实参的泛型类实例,那么T到底指的是哪一个实例呢?这是无法明确的,所以不允许.比如

```java
Container<Number> num = new Container<Number>();
Container<String> str = new Container<String>();
```

静态字段data是被num与str共享的,那data到底是Number类型还是String类型?data也不可能同时是Number类型与String类型.

## 不能在实例化的泛型类型间进行类型转换(cast)

因为两个实例化的类型间没有任何关系,所以不能进行类型转换

```java
List<Integer> li = new ArrayList<>();
List<Number> ln = li; //错误
List<Number> ln2 = (List<Number>)li; // 错误
```

但2个泛型类之间有父子关系,那么他们之间是可以类型转换的,比如下面的代码编译时不会出错的,只要li赋值就是一个ArrayList<String>类型,那么运行时就能类型转换成功,不会抛出异常

```java
List<String> l1 ;
ArrayList<String> l2 = (ArrayList<String>)l1;
```

那么相应的下面的代码也可以类型转换成功,因为List<?> 是所有List泛型实例的父类型.

```java
List<?> l1 ;
ArrayList<String> l2 = (ArrayList<String>)l1;
```

## 不能用instanceof来判断变量是一个实例化的泛型类型

由于泛型擦除的原因,所有的类型参数都不会在jvm中有任何信息.而instancof操作符右边的类型必须是具体化的(reifiable),所以不能编写`instanceof ArrayList<String>`这样的代码.

```java
List<String> list = new ArrayList<>();
if(list instanceof ArrayList<String>) { //错误

}
```

反过来考虑,假设instanceof操作符支持实例化的泛型类型,那么上面的list变量你可以赋值为ArrayList<Integer>.而且上面的instanceof语句应该返回true.因为在运行时是无法区分ArrayList<String>与ArrayList<Integer>有什么区别的,都是ArrayList.所以instanceof禁止用实例化的泛型类型,防止出现问题.

但下面的代码是可以的,而且instanceof操作符返回的结果是true.因为通配符会有通配符捕获,在运行时有信息,而ArrayList<?>是所有ArrayList实例化类型的父类型,所以返回true.

```java
List<String> list = new ArrayList<>();
if(list instanceof ArrayList<?>) {
  System.out.println("---");
}
```

## 不能创建参数化类型的数组

不能创建实例化泛型类的数组,比如下面的代码是会编译报错的

```java
List<Integer>[] arrayOfLists = new List<Integer>[2];
```

主要的原因是java的数组是协变的,而泛型不是,所以如果支持参数化类型数组,那么下面的代码就是合法的.

```java
Object[] stringLists = new List<String>[];  
stringLists[0] = new ArrayList<String>();  
stringLists[1] = new ArrayList<Integer>(); 
```

这样就会导致List<String>数组中有List<Integer>,这与泛型的初衷是违背的,所以java的泛型禁止创建参数化类型的数组

## 不能创建,捕获(catch)或抛出参数化的类型

不能创建泛型异常类,比如下面的代码

```java
class MyEx<T> extends Exception { /* ... */ }
class MyEx<T> extends Throwable {}
```

原因也能简单,因为泛型类你可以认为是类的工厂,但由于异常使用的特殊性与泛型擦除的原因会导致下面的代码是没有意义的.

```java
try{
  
}catch(MyEx<String> strEx){
  
}catch(MyEx<Integer> strEx){
  
}
```

上面的代码经过擦除后变为下面的代码,很明显不符合异常使用的语法

```java
try{
  
}catch(MyEx strEx){
  
}catch(MyEx strEx){
  
}
```

而且即使没有擦除,MyEx<String>与MyEx<Integer>也没有父子关系,也不符合异常使用语法,所以不能够声明泛型的异常类.

类型参数不能用在异常的catch块中,比如下面的代码,由于T的类型是不确定的,T如果传递一个Exception的子类过来,下面的代码是没有问题,但如果就是Exception,那么下面的代码就不符合语法规范.异常的catch处理必须是具体的类型,而不能是不确定的类型,是类型参数T天生就是不确定的,所以不允许出现在catch块中.

```java
public <T extends Exception> void doSth(){
  try{
  
  }catch(T ex){ //报错

  }catch(Exception ex){
    
  }
}
```

但类型参数可以出现在throws语句中,比如下面的代码,因为有上界的限制,可以确保T就是一个异常类型.

```java
class SomeClass<T extends Exception> {
    public void parse(File file) throws T {     // OK
    }
}
```

## 重载不支持形参擦除后是一样的

重载的基本要求是方法名相同,参数类型或个数不一样,很明显如果方法的形参采用参数化的泛型类型,经过擦除后可能就是一样的,这就不符合重载的定义,编译也不通过,比如下面的代码

```java
public void print(Set<String> strSet) { }
public void print(Set<Integer> intSet) { }
//擦除后
public void print(Set strSet) { }
public void print(Set intSet) { }
```



# 参考资料

https://docs.oracle.com/javase/tutorial/java/generics/index.html

https://www.tutorialspoint.com/java/java_generics

https://www.baeldung.com/java-generics

https://www.zhihu.com/question/20400700

https://stackoverflow.com/questions/4343202/difference-between-super-t-and-extends-t-in-java

http://bayou.io/draft/Capturing_Wildcards.html#Capture_Conversion

[https://zh.wikipedia.org/wiki/%E5%8D%8F%E5%8F%98%E4%B8%8E%E9%80%86%E5%8F%98](https://zh.wikipedia.org/wiki/协变与逆变)

https://www.jianshu.com/p/2bf15c5265c5(讲逆变与协变)

