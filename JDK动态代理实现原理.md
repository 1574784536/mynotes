# JDK动态代理实现原理

在JDK中提供了一个Proxy的工具类，专门用于生成代理对象。但是有一个前提是要求被代理的对象必须要有实现的接口。而核心创建代理对象的方法是newProxyInstance。

## newProxyInstance方法工作流程

这个方法中有一行核心代码：

Class<?> cl = getProxyClass0(loader, intfs);

这行代码作用是查找或生成指定的代理类，返回的是代理类的Class对象

![image-20190917101041419](https://tva1.sinaimg.cn/large/006y8mN6gy1g72ajfl089j30iy06dgly.jpg)

在这个方法中会创建一个ProxyClassFactory，这个工厂负责创建代理的Class

![image-20190917103441708](https://tva1.sinaimg.cn/large/006y8mN6gy1g72b8dcyxfj30mn06yq3d.jpg)

在ProxyClassFactory类中使用了ProxyGenerater这个类来生成具体的代理类的字节码，返回的是一个字节数组，然后通过defineClass0这个方法将直接数组加载到JVM中并创建一个Class对象返回，这个Class对象就是代理对象的Class。

![image-20190917103635659](https://tva1.sinaimg.cn/large/006y8mN6gy1g72bacg9ioj30m80cmq4g.jpg)

defineClass0方法是一个本地方法，由低层的jvm使用C++去实现，目的就是根据传入的字节码信息将其装载到jvm并创建成Class对象返回。

![image-20190917104106915](https://tva1.sinaimg.cn/large/006y8mN6gy1g72bf1ydo9j30my06dwet.jpg)