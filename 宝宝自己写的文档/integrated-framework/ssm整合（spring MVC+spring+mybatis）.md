## ssm整合（spring MVC+spring+mybatis）

1. 导包

   1. 把所有相关jar包导入到WEB-INF/lib文件夹里
   2. 如果使用了maven，直接加入依赖。

2. 配置web.xml

   1. springmvc
   2. spring
   3. 字符过滤器

3. 创建springmvc,spring的配置文件

   1. datesource（数据源）

      ​	c3p0,Druid

   2. 配置sqlSessionFactory

   3. 配置mybatis的扫描

   4. 配置事务管理器

   5. 事务管理器的通知

4. 编写mybatis的映射文件，写sql语句



