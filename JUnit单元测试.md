# 单元测试

## 1.简介

在日常开发中，我们编写的任何代码都需要经过严谨的测试才可以发布。以往的测试方式都是通过编写一个main函数进行简单的测试，并使用大量的print语句输出结果，这种方式其实是不可取的，它将导致大量的冗余代码在程序中，并且是不利于维护的。那么有没有统一集中测试的办法呢？这就是单元测试的作用了。所谓的单元测试是指对软件中的最小可测试单元进行检查和验证。（最小单元可以是一个方法，也可以是一个类，根据具体的场景进行定义）。在Java中通常使用Junit工具来完成。

## 2. 使用

将下载的JUnit.jar和hamcrest-core.jar导入到项目中即可，接下来就可以编写单元测试类。测试类也有相应的命名规范，通常以XxxTest方式来命名，Xxx就是你要测试的类名。测试类中编写的任何测试方法是不需要返回值的。测试方法名规范为testXxx(), Xxx表示目标类中的方法名。 

### 2.1 Junit的注解

| 注解         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| @Test        | 标注在方法上，表示当前方法是一个测试方法                     |
| @Before      | 标注在方法上，用于在执行任何单元测试之前先执行的方法         |
| @After       | 标注在方法上，用于在执行任何单元测试之后再执行的方法         |
| @BeforeClass | 标注在静态方法上，在测试方法之前先执行，且只执行一次。通常可用于初始化操作 |
| @AfterClass  | 标注在静态方法上，在测试方法之后执行，且只执行一次。通常用于释放资源等操作 |
| @Ignore      | 标注在测试方法上，表示忽略当前的此方法                       |

例子：

~~~java
public class UserDaoTest {

    @BeforeClass
    public static void before1(){
        System.out.println("before1");
    }

    @AfterClass
    public static void after1(){
        System.out.println("after1");
    }

    @Before
    public void before2(){
        System.out.println("before2");
    }

    @After
    public void after2(){
        System.out.println("after2");
    }

    @Test
    public void testSaveUser(){
        UserDao dao = new UserDao();
        dao.saveUser();
    }

    @Test
    //@Ignore 忽略当前的测试方法
    public void testFindUserById(){
        System.out.println("执行单元测试");
        UserDao dao = new UserDao();
        dao.findUserById();
    }
}
~~~

### 2.2 使用断言（Assert）

所谓断言，就是将预期的条件或者表达式参与测试中，判定测试的结果是否和预期的一致。在单元测试中通常会结合Assert来进行测试。

例子：

~~~java
import edu.nf.service.UserService;
import org.junit.Test;
//使用静态导入(导入的是某个静态方法)
import static org.junit.Assert.assertEquals;

public class UserServiceTest {

    @Test
    public void testFindUserName(){
        UserService service = new UserService();
       
        /*使用断言进行测试,第一个参数是期望返回的结果，第二个是目标方法的返回值，然后将这两个值进行比较，如果两个值相等达到预期的效果，则测试通过，否则将引发异常*/
    assertEquals("wangl",service.findUserName(1002));
    }
}
~~~

Assert类中提供了众多的assert方法来进行比较，而这些方法的返回值都是void，如果判定结果不同通过则引发相应的异常信息。常用断言方法如下：

| 方法              | 说明                         |
| ----------------- | ---------------------------- |
| assertEquals      | 判定两个值是否相等           |
| assertNotNull     | 判定结果不允许为空           |
| assertNull        | 判定结果为空                 |
| assertSame        | 判定两个对象是否是同一个引用 |
| assertArrayEquals | 判定两个数组的内容是否相等   |
| ...               | ...                          |

### 2.3 测试套件

所谓的测试套件，就是将一系列的单元测试类集中在一起进行批量的单元测试。

测试套件需要的注解：

| 注解          | 说明                                         |
| ------------- | -------------------------------------------- |
| @RunWith      | 标注在类上，表示当前类是一个测试套件的运行器 |
| @SuiteClasses | 标注在类上，用于集中所有的单元测类的class    |

例子：

~~~java
//定义一个运行期，Suite.class为测试套件
@RunWith(Suite.class)
//在@Suite.SuiteClasses注解的参数中定义所有单元测试类的Class
@Suite.SuiteClasses({UserDaoTest.class, UserServiceTest.class})
public class SuiteTest {
}

~~~

