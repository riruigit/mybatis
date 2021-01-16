# mybatis学习笔记

日期：01-16

## 相关资料

[官网文档](https://mybatis.org/mybatis-3/zh/getting-started.html)		[学习视频](http://www.bilibili.com/video/BV1NE411Q7Nx?p=1&share_medium=android&share_source=copy_link&bbid=XZ62ADC6CF1D4A2DC2ACC7D3209A2FB5B74FE&ts=1610768899453)		[狂神笔记](https://www.kuangstudy.com/bbs/1337710857777336322)



## 环境搭建

idea2020.3		mybatis3.4.6		maven3.6.3		mysql5.7	mysqljar5.1.47	junit4.11



## 项目创建

#### 文件夹规范

mapper : 里面放的是接口		命名规范  如 studentMapper.java

pojo : 里面是类 （getset方法以及tostring）



## mybatis配置文件



#### 核心配置文件*mybatis-config.xml*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--通过properties标签加载外部properties文件-->
    <properties resource="jdbc.properties"/>

    <!--自定义别名-->
    <typeAliases>
        <typeAlias type="com.mybatis.pojo.EMP" alias="EMP"/>
    </typeAliases>

    <!--数据源环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--加载映射文件-->
    <!--特别注意：加载映射文件有多种办法。但是这里不推荐使用 mapper	resource的办法来加载这个。-->
    <mappers>
        <mapper resource="EMPMapper.xml"/>
        <!--        <mapper class="com.mybatis.dao.UserDao"/>-->
    </mappers>

</configuration>
```

#### mysql连接信息配置文件jdbc.properties

```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://47.113.125.212:3306/qingqing
jdbc.username=qingqing
jdbc.password=qingqing
```

#### 新建项目

使用maven创建项目，在pom.xml导入mysql,junit,mybatis的jar包。依赖如下

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
```

#### 编写代码

##### 创建用户类：User

注意点1：下面的字段一定要和表的字段名字一样。

```java
package com.mybatis.pojo;

/**
 * @author 18364
 */
public class User {
    private String empNO;
    private String empName;
    private String position;
    private String MGR;
    private String hiredate;
    private int salary;
    private int bouns;
    private String deptNo;

    此处省略get和set方法。。。。。

    @Override
    public String toString() {
        return "EMP{\n" +
                "empNO='" + empNO + '\'' +
                ", empName='" + empName + '\'' +
                ", position='" + position + '\'' +
                ", MGR='" + MGR + '\'' +
                ", hiredate='" + hiredate + '\'' +
                ", salary=" + salary +
                ", bouns=" + bouns +
                ", deptNo='" + deptNo + '\'' +
                '}'+"\n";
    }
}
```

#### 创建*Dao*接口*UserDao.java*

```java
public interface UserDao {
    List<EMP> getEMPList();
}
```

#### 创建实现接口的配置文件*UserMapper.xml*

注意点1：namespace	和	id	以及	resultType	namespace

注意点2：路径（放在resources下）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.mybatis.dao.UserDao">
    <!--查询语句-->
    <select id="getEMPList" resultType="com.mybatis.pojo.EMP">
        select *  from EMP
           </select>
</mapper>
```

#### 使用junit来测试

在maven的test文件夹里面新建。

```java
@Test
public void test02() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = (new 			SqlSessionFactoryBuilder()).build(inputStream);
    SqlSession session = sqlSessionFactory.openSession();
    UserDao mapper = session.getMapper(UserDao.class);
    List<EMP> blog = mapper.getEMPList();
    System.out.println(blog.toString());
}
```

