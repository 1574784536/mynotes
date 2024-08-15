# ClassLoader教程

## 1. 简介

Classloader的核心作用就是将编译之后的class文件加载到jvm运行的内存当中。在jvm的规范当中，类加载器主要分为三种：引导类加载器（BootClassLoader）、扩展类加载器（ExtClassLoader）、系统类加载器（AppClassLoader）

## 2. 加载器分类

### 2. 1引导类加载器

引导类加载器主要加载的class是jdk本身的类库（JAVA_HOME\lib目录下的jar文件），这些类库都是jdk核心的类库（rt.jar），也是最重要的，因此由这个加载器去完成加载和初始化。并且这个加载器是使用C语言实现的。

### 2.2 扩展类加载器

扩展类加载器的主要作用就是加载jdk的扩展包的jar文件（JAVA_HOME\lib\ext目下的jar文件），这些文件是JDK的扩展类库。

### 2.3 系统类加载器

系统了加载器的核心作用就是加载classpath路径下的class文件以及jar文件，这些类库通常由开发人员自己编写的，因此这些类都是通过系统类加载完成加载。

## 3. 委托模式

在执行类加载的时候，最先由系统类加载器开始，但它会把加载的执行权先交给扩展类加载器，同样，扩展类加载器也会将加载的执行权交给引导类加载器，最终由引导类加载器开始加载，如果需要加载的类库不是引导类加载器加载的范畴，那么就会将加载权交回给扩展类加载器。同样，如果类库不是扩展类加载器加载的范畴，最后就交由给系统类加载器来完成，这个过程就是委托。这样做的目的室为了保证系统类库加载时的安全性，如：Object、String类等等，这些都应该由引导类加载器来完成，不应该交由其他类加载器，因为，如果这些类库加载的后可以由用户来进行操作，那么就可会导致类的不安全。例如：用户可以重载或修改类中的方法，这是绝对禁止的。

![image-20190822094245289](http://ww2.sinaimg.cn/large/006y8mN6gy1g687mba77mj30fk0c2dfz.jpg)

## 4. ClassLoader的常见API

除了引导到类加载器是有C语言来实现以外，其他的类加载器都是继承自ClassLoader这个类，而这个类中提供了相应的加载API来完成类加载以及初解析和始化的过程。

| API                                                  | 说明                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| getParent()                                          | 获取上一级的类加载器                                         |
| loadClass(String name)                               | 加载名称为name的类，返回的是一个Class实例，在此方法中会间接调用findClass方法 |
| findClass(String name)                               | 加载名称为name的类，返回的是一个Class实例。通常自定义类加载器时，会重写此方法。 |
| findLoadedClass（String name）                       | 检查名称为name的Class是否已经加载过，返回的是一个Class实例，如果Class为null，就表示未加载，否则就是已经加载 |
| defineClass(String name, byte[] b, int off, int len) | 将字节数组b的二进制数据转换成Java中的Class对象               |
| resolveClass(Class c)                                | 解析并连接到指定的Class                                      |

## 2. 自定义类加载器

我们也可以自定义类加载器，只需要继承ClassLoader，通常需要重写父类的findClass方法。需要注意的是，自定义类加载器它的上一级的类加载器是AppClassLoader，也会遵循委托的模式。

![image-20190822094508431](http://ww3.sinaimg.cn/large/006y8mN6gy1g687osvx5pj30ll0emaab.jpg)

示例：

~~~java
public class DemoClassLoader extends ClassLoader {
    /**
     *  类加载的根路径
     */
    private String loadRootPath;

    public DemoClassLoader(String loadRootPath){
        this.loadRootPath = loadRootPath;
    }

    /**
     * 重写父类的findClass方法
     * @param name 需要加载的完整类名
     * @return
     * @throws ClassNotFoundException
     */
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        //构建类文件的绝对路径，用于I/O流读写操作
        String loadPath = loadRootPath + convertNameToPath(name);
        //通过输入流将class文件读入内存当中
        File file = new File(loadPath);
        byte[] bytes = readClassFile(file);
        //将直接数组转换为Class对象
        Class<?> clazz = defineClass(name, bytes, 0, bytes.length);
        return clazz;
    }

    /**
     * 根据File对象将class文件读入内存，返回一个字节数组
     * @param file
     * @return
     */
    private byte[] readClassFile(File file){
        //构建文件输入流
        try(FileInputStream fis = new FileInputStream(file);
            ByteArrayOutputStream baos = new ByteArrayOutputStream()){
            int len = 0;
            byte[] bytes = new byte[1024];
            while((len = fis.read(bytes, 0, bytes.length)) != -1){
                    //将读取的直接保存在一个缓存中
                baos.write(bytes, 0, len);
            }
            //将流中的直接数组直接返回
            return baos.toByteArray();
        }catch (IOException e){
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }

    /**
     * 将完整类名转换为一个相对路径
     * @return
     */
    private String convertNameToPath(String name){
        String path = name.replace(".", File.separator) + ".class";
        return path;
    }
}
~~~

使用：

~~~java
public class Main {
    public static void main(String[] args) throws Exception{
        String rootPath=System.getProperty("user.home")+File.separator;
        DemoClassLoader cl = new DemoClassLoader(rootPath);
        Class clazz = cl.loadClass("Hello");
      	//创建类实例
        Object hello = clazz.newInstance();
        Object hello2 = clazz.newInstance();
        System.out.println(hello);
        System.out.println(hello2);
      	//获取当前Class对象的类加载器
        System.out.println(clazz.getClassLoader());
    }
}
~~~

注意：如果是不同的类加载器加载同一个class文件，那么产生的Class对象是不一样的。