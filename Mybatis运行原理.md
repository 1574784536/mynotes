Mybatis运行原理

    Resources类， Mybatis中IO流工具类。作用：加载mybatis.xml配置文件。
    SqlSessonFactoryBuilder() 构建器。作用：创建SqlSessionFactory接口的实现类。
    XMLconfigBuilder类， Mybatis全局配置文件内容构建器类。作用：负责读取流内容并转换为JAVA代码。
    Configuration类， 封装了全局配置文件所有配置信息。
    DefaultSqlSessionFactory类，是SqlSessionFactory接口的实现类。
    Transaction事务类。每个SqlSession会带有一个Transaction对象。
    TransactionFactory事务工厂。负责生产Transaction。
    Executor类，Mybatis执行器。作用：负责执行SQL命令，相当于JDBC中statement对象（或者PrepareStatement或CallableStatement），默认执行器为SimpleExcutor、批量操作为BatchExcutor，通过openSession(参数控制)
    DefaultSqlSession类是SqlSession接口的实现类
    10.ExceptionFactory类，Mybatis中异常工厂。
    运行流程：
    

![](E:\下载\20190417093946933.png)

首先，在Mybatis运行开始时需要先通过Resources加载全局配置文件，下面需要实例化SqlSessionFactoryBuilder构建器，帮助SqlSessionFactory接口实现DefaultSqlSessionFactory。
其次，在实例化DefaultSqlSessionFactory之前，需要先创建XmlConfigBuilder解析全局配置文件流，并把解析结果存放在Configuration之中，之后把Configuration传递给DefaultSqlSessionFactory。到此SqlSessionFactory工厂创建成功。
然后，由SqlSessionFactory工厂创建SqlSession，每次创建SqlSession时，都需要TransactionFactory创建Transactio对象，同时还需要创建SqlSession的执行器Excutor，最后实例化DefaultSqlSession，传递给SqlSession接口。
最后， 根据项目需求使用SqlSession接口中的API完成具体的事务操作。
如果事务执行成功，提交给数据库，关闭SqlSession。
如果事务执行失败，需要进行rollback回滚事务。