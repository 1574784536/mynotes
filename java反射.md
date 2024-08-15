# 简介

**什么是反射？**

Java反射说的是在运行状态中，对于任何一个类，我们都能够知道这个类有哪些方法和属性。对于任何一个对象，我们都能够对它的方法和属性进行调用。我们把这种动态获取对象信息和调用对象方法的功能称之为反射机制。



反射(Reflection) 是一种运行时检视(Inspect)类型信息并且可以动态操作类中的一切成员的一种机制。它常常用于如下一些场景中：

- 运行时实例化类的对象,比如实例化一个Servlet类,此Servlet类的名字容器预先是不知道的
- 运行时调用方法,比如调用Servlet对象的service方法
- 运行时修改属性值.比如Apache DbUtils工具库可以把数据库的一条记录默认依据名称相同的策略赋值一个对象的字段.

JDK中关于反射的相关类型都在`java.lang.reflect`包下,并不需要额外的第三方包来完成反射的工作.

# Class对象

当jvm加载一个class文件到内存后，会为其创建一个唯一的Class对象（注意：同一个类加载器的前提）。所以我们可以理解为，Class对象就是一个class文件被加载到jvm中后的运行时表现。而反射的首要任务就是得到Class对象.得到Class对象的方法如下：

- Class.forName("类的全称")
- Class clazz=Class.forName("com.entity.Users");
- 类的class字段
- Class clazz=Users.class;
- 对象的getClass方法
- Users user=new Users();
- Class clazz= user.getClass();

## Class.forName()

这种方法适合只知道类的字符串表示,也就是全称的情况.如果类还没有加载它还会加载此类,如果已经加载了就会返回已加载的class对象给你,比如:

```java
Class personCls = Class.forName("com.Person");
```

## 类的class字段

这种方式是此类已经预先知道的情况下就非常合适,比如下面的代码

```java
Class personCls = Person.class;
Class integerCls = Integer.class;
```

对于8个基本类型想获取其class对象信息除了可以用class字段的方式以外还可以利用其对应包装类型的TYPE字段来获取,比如:

```java
Class intCls = int.class;
Class intCls = Integer.TYPE;
```

## 对象的getClass方法

这种方式适合于已经有此类对象的情况下来获取类的class对象信息,比如下面的代码

```java
Person p = new Person();
Class personCls = p.getClass();
```

## Void类对象

java方法的返回类型中有一个特殊的值就是void,使用java.lang.Void类来表示。所以获取此特殊的Class对象就用Void的class字段或TYPE字段来实现

```java
Class<Void> clazz = Void.class;
Class<Void> clazz = Void.TYPE;
```

# Class对象的基本操作

有了Class对象之后,我们就可以利用它来获取类中各种各样的信息，主要可以获取的信息有如下一些

- 获取类的名字
- 类的父类
- 类的修饰符
- 类实现的所有接口
- 类的构造函数
- 类的所有字段
- 类的所有方法

## 获取类的名字

类的名字分为简称和全称,比如下面的类其简称为Person,全称为com.Person

```java
package com;
public class Person{}
```

通过反射获取类的名称方法如下:

```java
Class clazz = ...;
String simpleName = clazz.getSimpleName();
String fullQualifiedName = clazz.getName();
```

## 获取类的修饰符

通过Class对象的getModifiers方法获取类的修饰符,此方法返回的是一个整数,然后依赖Modifier类的一系列方法来分析getModifiers方法返回整数的含义.

```java
int modifier = clazz.getModifiers();
boolean isPublic = Modifier.isPublic(modifier);
boolean isAbs = Modifier.isAbstract(modifier);
```

## 获取包的信息

可以通过Class对象对的getPackage()方法获取类的包信息,此方法返回的是Package类型,通过此Package类型就可以得到包的名字,比如:

```java
Package pkg = clazz.getPackage();
String pkgName = pkg.getName();
```

## 获取父类信息

获取父类信息主要是靠getSuperClass()方法实现,如果当前的Class对象代表的是Object类型,接口类型,void类型,基本类型,那么此方法返回null值

```java
Class<?> superClazz = clazz.getSuperclass();
String superClassName = superClazz.getSimpleName();
```

## 获取实现的接口

可以通过getInterfaces方法获取实现或继承的接口信息.如果当前的Class对象代表的是一个类,那么此方法得到是此类声明实现的所有接口信息,不包含其父类实现的接口信息.返回的数组中按声明的顺序排序.如果没有实现接口就返回长度为0的数组.

如果当前的Class对象代表的是一个接口,那么此方法返回的是此接口extends的所有接口信息,返回的数组中按照声明的接口顺序排序.如果没有继承任何接口,返回的数组是一个长度为0的数组.

如果当前的Class对象代表的是void或者基本类型,此方法返回长度为0的数组.

如果当前的Class对象代表的是数组类型,那么返回的是`Cloneable`和`Serializable`

```java
Class<?>[] personInterfaces = clazz.getInterfaces();
```

## 获取构造函数

通过getConstructors方法可以获取类的所有构造函数.比如下面的方法

```java
Constructor<?>[] constructors = clazz.getConstructors();
```

## 获取字段

字段分为类自己声明(Declare)的和从父类型继承过来的字段,想得到所有的字段(不区分是继承的还是自己声明的)就通过`getFields`方法,通过getDeclaredFields()方法可以获取此Class对象代表的类声明的所有字段,不包括继承过来的字段.比如下面的代码

```java
Field[] fields = clazz.getDeclaredFields();
```

上面的方法可以得到任意修饰符的字段,静态与实例字段可以得到,public,private等访问修饰符的字段也可以得到.如果想得到某一个具体名字的字段可以通过`getDeclaredFields(String name)`方法获取

```java
Field f = clazz.getDeclaredFields("字段名");
```

如果Class对象代表的类型没有字段或者代表的是数组类型,基本数据类型或者void类型会返回一个长度为0的数组.

返回数组中的元素并没有进行排序,并且并没有特定的顺序,比如按照声明的顺序,所以你的代码不能依赖反射中得到的字段的顺序来编写逻辑.

而getFields方法会返回自身和继承过来的所有***public***字段.并不包含其它修饰符的字段.

## 获取方法

方法也分为类自己声明(Declare)的和从父类型继承过来的.可以通过`getDeclaredMethods()`方法获取此Class对象代表的类声明的所有方法,通`过getMethods()`方法获取所有的方法.比如下面的代码

```java
Method[] methods = clazz.getDeclaredMethods();
```

如果想获得特定的方法,就需要传递方法名与参数类型,因为方法有重载.比如下面的代码表示取得只有一个String类型参数的方法doSth.

```java
Method method = clazz.getDeclaredMethods("doSth",String.class)
```

getDeclaredMethods的第二个参数是可变长度的,因为方法的参数可以有多个.

如果一个类只有静态代码块,那么getDeclaredMethods返回的数组中不包括这个静态代码块.

如果Class对象代表的类型没有方法或者代表一个数组,基本类型,void类型,那么返回的是长度为0的数组.

返回数组中的元素并没有进行排序,并且并没有特定的顺序,比如按照声明的顺序,所以你的代码不能依赖反射中得到的方法的顺序来编写逻辑.

而getMethods方法会返回自身和继承过来的所有***public***方法.并不包含其它修饰符的方法.

## 实例化对象

可以通过Class对象直接实例化一个类的对象出来.比如下面的代码

```java
Class<Person> clazz = ...;
Person p = clazz.newInstance();
```

这个方法必须要求类有一个`nullary constructor`,也就是无参的构造函数.默认构造就属于`nullary constructor`.如果没有这样的构造函数,那么调用newInstance()方法时会抛出异常.

如果没有无参的构造函数,就不能这样实例化对象了,只能靠反射先获取Constructor对象,然后通过Constructor来创建对象.

> 默认构造函数与无参构造函数这2个术语有一点点的区别.默认构造函数比较强调由编译器生成(c++领域不强调编译器生成,自己写的也算默认构造函数)以及是public修饰符的,无参构造函数不强调是public的,也不强调是编译器生成/
>
> https://en.wikipedia.org/wiki/Default_constructor
>
> https://en.wikipedia.org/wiki/Nullary_constructor

## isInstance()

Class对象的isInstance方法的作用等价于instanceof操作符.比如下面的代码会返回true

```java
Person person = new Person();
Class<Person> clazz = Person.class;
System.out.println(clazz.isInstance(person));
```

# 构造函数

由于一个类可以有多个构造函数,所以反射API提供了getConstructors()方法得到所有的构造函数,用getConstructor(Class<?>… parameterTypes)来得到某一个具体的构造函数.比如下面的代码

```java
Constructor<?>[] constructors = clazz.getConstructors();
Constructor<?> cons1 = clazz.getConstructor();
    Constructor<?> cons2 = birdClass.getConstructor(String.class);
```

如果没有对应参数类型的构造函数会抛出`NoSuchMethodException`异常.

有了Constructor对象之后,我们就可以调用调用其newInstance方法来实例化对象,其作用等价于new一个类的对象时指定调用这个构造函数.

```
cons1.newInstance();
cons2.newInsance("hello");
```

# 方法

获得了Method对象后,我们可以获取方法的名字,可访问性以及调用方法等操作.比如下面的代码获取了方法的名字,可访问性

```java
Method m = ...;
boolean access = m.isAccessible();//获取可访问性
String name = m.getName();//得到方法名字
int parameterCount = m.getParameterCount();//得到方法参数个数
Parameter[] ps = m.getParameters();//获取所有参数信息
```

## 调用方法

反射的方式调用方法主要是靠Method对象的`invoke(Object obj,Object…args)`方法来完成.其第一个参数是此方法所属类的对象,如果这个方法是个静态方法,这个参数可以传递null值,第二个参数就是方法需要的参数数据,是一个可变长度的参数.因为方法的参数个数可以有任意数量.

假设一个类中有下面的方法:

```java
public void doSth(String name) {
	System.out.println(name + " do sth");
}
```

通过反射调用的方法如下:

```java
Class<Person> clazz = Person.class;
Person p = clazz.newInstance();		
Method m = clazz.getDeclaredMethod("doSth", String.class) ;
m.invoke(p,"cj");
```

如果方法不能访问,仍然需要通过setAccessible来调整访问性.方法调用完毕之后再调回其访问性

```java
Method m = clazz.getDeclaredMethod("doSth", String.class) ;
m.setAccessible(true);
m.invoke(p,"cj");
m.setAccessible(false);	
```



# 字段

我们反射得到了某个Field对象之后,就可以得到此Field的名字,类型以及给字段赋值等操作.比如下面的代码就得到了字段的名字与类型

```java
Field field = ...;
Class<?> fieldClass = field.getType();
String name = field.getName();
int modifier = field.getModfiers();//得到修饰符
```

## 获取字段值

获取字段值主要是靠`get()`方法来完成,如果你确定字段的类型,可以调用对应类型的方法,比如调用getInt方法获取整数字段的值.这种以get开头获取字段值的方法主要针对基本类型,所以有8个这样的方法.

获取字段值分为获取静态与实例字段的值.如果是静态字段,调用上述方法获取字段时传递null,如果是实例字段的话,必须传递此字段所属类的对象给方法.

取得静态字段的值:

```java
Class<Person> clazz = Person.class;
Person p = clazz.newInstance();
//sa是类的一个public static字段
Field sa = clazz.getDeclaredField("sa");
System.out.println(sa.get(null));
System.out.println(sa.getInt(null));
```

取得实例字段的值

```java
Class<Person> clazz = Person.class;
Person p = clazz.newInstance();
Field a = clazz.getDeclaredField("a");
System.out.println(a.get(p));
System.out.println(a.getInt(p));
```

上面获取字段的值的方式,必须确保字段的修饰符是可以访问的,否则会抛出`IllegalAccessException`.

如果字段的修饰符限制了访问,可以通过修改其访问性来获取字段值或者调用其对应的getter方法来获取字段的值.比如下面的代码就是修改修饰符的方式来获取字段的值

```java
Field a = clazz.getDeclaredField("a");
a.setAccessible(true);
System.out.println(a.get(p));
a.setAccessible(false);
```

修改可访问性只是设定是否跳过java语言的访问控制检查,并不是修改了字段的修饰符.

如果一个不能访问的字段,比如私有字段有对应的getter方法,那么你可以通过调用getter方法来获得字段的值.不过遗憾的是Field类型并没有提供获取此字段对应的getter的API.

## 设置字段值

设置值与获取值是类似的,主要是通过set方法以及setDouble,setInt等方法来完成.比如下面的代码

```java
Field a = clazz.getDeclaredField("a");
a.setAccessible(true);
a.set(p,600);
System.out.println(a.get(p));
a.setAccessible(false);
```

# 数组与反射

数组是某个类型对象的一个合集,基于这个认识,数组的反射与普通的反射有一点点不同.比如下面的代码创建了一个长度为10的字符串数组.

```java
Class cls = Class.forName("java.lang.String");
Object arr = Array.newInstance(cls, 10);
Array.set(arr, 5, "this is a test");//设置第六个位置的字符串值
String s = (String)Array.get(arr, 5);
System.out.println(s);
String[] arr2 = (String[])arr;
System.out.println(arr2[5]);
```

其中Array类是在java.lang.reflection包下面的一个类型.

# 综合案例

反射通常用在bean之间的拷贝,从Map中拷贝数据到bean中,以及把数据库中读取的记录转换为一个bean等等场景中.

## 从Map生成bean

用过servlet的都知道,我们获取请求数据时需要把请求的数据封装到一个bean中,而Servlet中所有的请求的数据是放在一个map中的,基于这种情况,下面的案例是把一个map中的数据赋值给一个bean对象,map中键的名字作为bean的字段的名字,map中键对应的值作为对应字段的值.map的数据如下:

```java
Map<String,Object> map = new HashMap<>();
map.put("id",100);
map.put("name","cj");
map.put("age",18);
```

bean类的代码如下:

```java
public class UserInfoEntity {
	private Integer id;
	private String name;
  //省略getter,setter,toString()
}
```

实现把map中的数据拷贝到bean的代码如下,其中map中的age键在map中没有对应的字段,所以getDeclaredField时会抛出异常,但我们捕获了.所以并不影响最终bean实例的生成.

```java
public static void main(String[] args) throws Exception {
		Map<String,Object> map = new HashMap<>();
		map.put("id",100);
		map.put("name","cj");
		map.put("age",18);
		
		UserInfoEntity userinfo = copyData(map,UserInfoEntity.class);
		
		System.out.println(userinfo);
	}
	
	public  static <T> T copyData(Map<String,Object> source,Class<T> targetClass) throws Exception {
		T instance = targetClass.newInstance();
		for(Map.Entry<String,Object> entry: source.entrySet()) {
			try {
				String key = entry.getKey();
				Field f = targetClass.getDeclaredField(key);
				f.setAccessible(true);
				f.set(instance, entry.getValue());
				f.setAccessible(false);
			}catch(NoSuchFieldException e) {
				e.printStackTrace();
			}
		}
		return instance;
	}
```

最终输出的结果是:

```
UserInfoEntity [id=100, name=cj]
```

# 参考资料

https://www.oracle.com/technetwork/articles/java/javareflection-1536171.html

