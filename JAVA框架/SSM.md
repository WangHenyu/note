#  1.MyBatis

## 1.1.MyBatis入门案例

<h4>开发环境</h4>

> 构建工具：maven 3.5.4 
>
> MySQL版本：MySQL 8 
>
> MyBatis版本：MyBatis 3.5.7

<h4>准备工作</h4>

初始化数据库并创建user表

![image-20220720225807392](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220720225807392.png)

创建实体类

```java
public class User {
    private Integer id;
    private String username;
    private String password;
    private String telephone;

    /*无参和带参构造*/
    /*getter和setter方法*/
    /*toString方法*/
}
```

<h4>1.创建Maven工程</h4>

![image-20220720224433247](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220720224433247.png)

<h4>2.引入依赖</h4>

> 在pom.xml文件中引入mybatis、mysql、junit依赖

```xml
<dependencies>
    <!-- Mybatis核心 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

<h4>3.加入maven插件</h4>

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory><!--所在的目录-->
            <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
    
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

<h4>4.创建MyBatis核心配置文件</h4>

> 1.习惯上命名为mybatis-config.xm (非强制)
>
> 2.核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息
>
> 3.核心配置文件存放的位置是src/main/resources目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--设置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    
    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```

<h4>4.创建Mapper接口</h4>

```java
package com.why.mapper;

public interface UserMapper {
    
    /**
     * 添加用户信息
     */
    int insertUser();
}

```

<h4>6.创建MyBatis映射文件</h4>

ORM (Object Relationship Mapping）对象关系映射

| JAVA概念 | 数据库概念 |
| -------- | ---------- |
| 类       | 表         |
| 属性     | 字段       |
| 对象     | 记录       |

>1.映射文件的命名规则：表所对应的实体类的类名+Mapper.xml
>
>2.MyBatis映射文件用于编写SQL, 访问以及操作表中的数据
>
>3.MyBatis中可以面向接口操作数据, 要保证两个一致：
>
>​	3.1.mapper接口的全类名和映射文件的命名空间（namespace）保持一致
>
>​	3.2.mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.why.mapper.UserMapper">
    
    <!--int insertUser()-->
    <insert id="insertUser">
        insert into t_user values(null,'Jack','123456','15149390721')
    </insert>
    
</mapper>

```

<h4>测试</h4>

```java
public class MyBatisTest {
    @Test
    public void test01() throws IOException {
        //读取MyBatis的核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        //通过核心配置文件所对应的字节输入流创建工厂类SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
        //创建SqlSession对象，此时通过SqlSession对象所操作的sql都会自动提交
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        //通过代理模式创建UserMapper接口的代理实现类对象
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        /*
        调用UserMapper接口中的方法，就可以根据UserMapper的全类名匹配映射文件，
        通过调用的方法名匹配映射文件中的SQL标签，并执行标签中的SQL语句
        */
        int result = userMapper.insertUser();
        
        //通过唯一标识执行sql 唯一标识：namespace.sqlId
        //int result =  sqlSession.insert("com.why.mapper.UserMapper.insertUser");
        
        sqlSession.close();
    }
}

```

搭建MyBatis环境步骤总结：

​	1、创建Maven工程

​	2、引入依赖

​	3、加入Maven插件

​	4、创建MyBatis核心配置文件

​	5、创建Mapper接口

​	6、创建MyBatis映射文件



MySQL不同版本的注意事项 

​	1、驱动类

​		MySQL5版本使用jdbc5驱动, 驱动类使用：```com.mysql.jdbc.Driver ```

​		MySQL8版本使用jdbc8驱动, 驱动类使用：```com.mysql.cj.jdbc.Driver ```

​	2、连接地址url 

​		MySQL5版本的url： ```jdbc:mysql://localhost:3306/ssm ```

​		MySQL8版本的url： ```jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC```

## 1.2.加入日志功能

1、引入log4j的依赖

```xml
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2、在resources目录下创建log4j.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">

    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}%m (%F:%L) \n" />
        </layout>
    </appender>

    <logger name="java.sql">
        <level value="debug" />
    </logger>

    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>

    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>

</log4j:configuration>

```

> 日志级别
>
> FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试)

## 1.3.核心配置文件详解

> 核心配置文件中的标签必须按照固定的顺序： 
>
> properties?, settings?, typeAliases?, plugins?, environments?, mappers? (部分)
>
> 详情见mybatis-3-config.dtd文件

<h4>properties标签</h4>

 向创建jdbc.properties文件

> 该文件保存数据库的配置信息

```properties
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.username = root
jdbc.password = 123456
```

在核心配置文件中使用properties标签

```xml
<!--引入properties文件-->
<properties resource="jdbc.properties" />

<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <!-- 使用${key}读取配置文件中的value -->
            <property name="driver" value="${jdbc.driver}"/>
            <property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>
```

<h4>settings标签</h4>

> 设置例如可用于配置日志功能

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

<h4>typeAliases标签</h4>

> typeAliases标签用来设置类型别名

```xml
<!--设置类型别名-->
	<typeAliases>
	<!--
		第一种方式：使用typeAlias标签设置别名
		type：设置需要设置别名的类型
		alias：设置某个类型的别名，若不设置该属性默认使用类名作为别名且不区分大小写
	-->
	<typeAlias type="com.atguigu.mybatis.pojo.User" alias="User"></typeAlias>
        
	<!--
		第二种方式：使用package标签设置别名
		以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写
	-->
	<package name="com.atguigu.mybatis.pojo"/>
</typeAliases>

```

<h4>environments标签</h4>

> environments标签用于配置多个连接数据库的环境

```xml
<environments default="development">
    <environment id="development">
        <!--
            transactionManager：设置事务管理方式
            type属性有两个值
                JDBC使用的是JDBC中原生的事务管理方式
                MANAGED例如被Spring管理
        -->
        <transactionManager type="JDBC"/>
        <!--
            dataSource：配置数据源
            type属性有三个值
                POOLED 表示使用数据库连接池缓存数据库连接
                UNPOOLED 表示不使用数据库连接池
                JNDI 表示使用上下文中的数据源
        -->
        <dataSource type="POOLED">
            <!--设置连接数据库的驱动-->
            <property name="driver" value="${jdbc.driver}"/>
            <!--设置连接数据库的连接地址-->
            <property name="url" value="${jdbc.url}"/>
            <!--设置连接数据库的用户名-->
            <property name="username" value="${jdbc.username}"/>
            <!--设置连接数据库的密码-->
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>
```

<h4>mappers标签</h4>

> mappers标签用来引入映射文件

```xml
<mappers>
	<!-- 第一种方式：使用mapper标签引入映射文件 -->
    <mapper resource="mappers/UserMapper.xml"/>
    
    <!--
		第二种方式：使用package标签引入映射文件
		这种方式需要满足两个条件：
    		1、mapper接口所在的包要和映射文件所在的包一致
    		2、mapper接口要和映射文件的名字一致
    -->
    <package name="com.atguigu.mybatis.mapper"/>
</mappers>
```

<h4>完整的核心配置文件</h4>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <properties resource="jdbc.properties" />

    <typeAliases>
        <!--<typeAlias type="com.why.pojo.User"></typeAlias>-->
        <package name="com.why.pojo"/>
    </typeAliases>

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

    <mappers>
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
        <package name="com.why.mapper"/>
    </mappers>
</configuration>
```

<h4>设置核心配置文件模板</h4>

> Settings &rarr; Editor &rarr; File and Code Templates &rarr; Files &rarr; +

## 1.4.MyBatis的CRUD

<h4>新增数据</h4>

```xml
<!--int insertUser();-->
<insert id="insertUser">
	insert into user values(null,'Eric','123456','15167250837')
</insert>
```

<h4>删除数据</h4>

```xml
<!--int deleteUser();-->
<delete id="deleteUser">
	delete from user where id = 7
</delete>
```

<h4>修改数据</h4>

```xml
<!--int updateUser();-->
<update id="updateUser">
	update user set username='Mary',password='654321' where id = 1
</update>
```

<h4>查询（结果为单个对象）</h4>

```xml
<!--User getUserById();-->
<select id="getUserById" resultType="com.why.pojo.User">
	select * from user where id = 2
</select>
```

<h4>查询（结果为多个对象）</h4>

~~~html
<!--List<User> getUserList();-->
<select id="getUserList" resultType="com.why.pojo.User">
	select * from user
</select>
~~~

> 注意事项： 
>
> 1.查询的标签select必须设置属性resultType或resultMap, 用于设置实体类和数据库表的映射关系 
>
> resultType：自动映射, 用于属性名和表中字段名一致的情况 
>
> resultMap：自定义映射, 用于一对多或多对一或字段名和属性名不一致的情况

<h4>基于注解的CRUD</h4>

~~~java
@Repository
public interface UserMapper{

    @Select("select * from user where id = #{id}")
    User selectByUserId(@Param("id")Long id);
    
    // @Insert(sql语句) 添加
    // @Delete(sql语句) 删除
    // @Update(sql语句) 修改
}
~~~



## 1.5.MyBatis获取参数

MyBatis获取参数有两种方式： #{} 和 ${}

${}的本质就是字符串拼接, 需要手动添加单引号

```sql
select * from user where username = ${name}
-- 编译后
select * from user where username = Jack
```

#{}的本质就是占位符赋值

```sql
select * from user where username = #{name}
-- 编译后
select * from user where username = 'Jack'
```



<h4>情况1: 单个简单参数(掌握)</h4>

> Mapper接口中的方法只有一个简单参数（Java基本类型和String） 时
>
> 使用#{任意字符} 或 ${任意字符} 获取参数
>
> #{}中的任意字符推荐使用形参名 见名知意

```xml
<!-- User queryUserByName(String username); -->

<select id="queryUserByName" resultType="User">
    select * from user where username = #{name}
    <!-- 使用${}时要加单引号 -->
    <!--
    select * from user where username = '${name}'
	-->
</select>
```

<h4>情况2：多个参数(掌握)</h4>

> 当 Mapper接口方法多个参数时, MyBatis会自动将这些参数放在一个map集合中
>
> ​	1、使用@Param注解 （重要）
>
> ​	2、按参数位置 （不常用）
>
> ​			以arg0、arg1...为键, 以参数为值；
>
> ​			以 param1、param2...为键, 以参数为值；

~~~xml
<!--1、使用@Param注解-->
<!--
     User queryUserByNameAndTel(@Param("name") String username,
                                @Param("phone") String telephone);
-->
<select id="queryUserByNameAndTel" resultType="User">
    select * from user where username = #{name} and telephone = #{phone}
</select>
~~~

```xml
<!--2、按参数位置-->
<!--User queryUserByNameAndPwd(String username,String password);-->

<select id="queryUserByNameAndPwd" resultType="User">
    <!--
        select * from user where username = #{arg0} and password = #{arg1}
    -->

    select * from user where username = #{param1} and password = #{param2}
</select>
```

<h4>情况3：多个参数-使用对象(掌握)</h4>

> 使用对象传递参数每个属性都是一个参数
>
> 获取参数方式#{对象属性}
>

~~~xml
<!--int addUser(User user);-->

<insert id="addUser">
    insert into user values (null,#{username},#{password},#{telephone})
</insert>
~~~

测试

```java
    @Test
    public void test06(){
        SqlSession sqlSession = SqlSessionUitl.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user  = new User(null,"Mary","123456","17869765432");
        mapper.addUser(user);
    }
```

<h4>情况4：多个参数-Map集合(了解)</h4>

> 若mapper接口中的方法需要的参数为多个时, 此时可以手动创建map集合将这些数据放在 map中
>
> 可以使用 #{key} 或 ${key} 获取map中的value参数

```xml
<!--User selectUserByNameAndPwd(Map<String,Object>);-->

<select id="selectUserByNameAndPwd" resultType="User">
    select * from user where username = #{name} and password = #{pwd}
</select>
```

测试

```java
    @Test
    public void test05(){
        SqlSession sqlSession = SqlSessionUitl.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String,String> map = new HashMap<>();
        map.put("name","Jack");
        map.put("pwd","123456");
        User user = mapper.selectUserByNameAndPwd(map);
        System.out.println(user);
    }
```

## 1.6.MyBatis结果自动映射

<h4>情况1：查询结果简单数据类型</h4>

>在MyBatis中 对于Java中常用的类型都设置了类型别名
>
例如：(部分) 详情见MyBatis官方文档

| Java类型 | 别名             |
| -------- | ---------------- |
| Integer  | int\|integer     |
| int      | \_int\|\_integer |
| Map      | map              |
| String   | string           |
```xml
<!--Integer getUserCount();-->

<select id="getUserCount" resultType="integer">
    select count(*) from user
</select>
```

<h4>情况2：查询结果是实体对象</h4>

> 前提条件：
>
> ​	数据库中的字段名和对象的属性保持一致
>
> resultType封装结果集原理：
>
> ​	首先根据resultType获取封装的实体类
>
> ​	创建实体类对象
>
> ​	通过setXXX给属性赋值
>
> ​	setXXX怎么得来：set+字段名首字母大写

**结果为单个实体类**

```xml
<!--User getUserById(@Param("id") int id);-->

<select id="getUserById" resultType="User">
	select * from user where id = #{id}
</select>
```

**结果为多个实体类**

```xml
<!--List<User> getUserList();-->

<select id="getUserList" resultType="User">
	select * from user
</select>
```

注意：Mapper接口方法返回是集合类型, <font color = 'red'>需要指定集合中的类型</font>而不是集合本 身

<h4>情况3：查询结果是没有相对应的实体类</h4>

> 若字段的值为null这不会存到map中

**结果为单条数据**

```xml
<!--Map<String,Object> getNameAndPhoneByID(@Param("id")Integer id);-->

<select id="getNameAndPhoneByID" resultType="map">
    select username,telephone as phone from user where id = #{id}
</select>
```

**结果为多条数据**

1、方法1

```xml
<!--List<Map<String,Object>> getNameAndPhone();-->

<select id="getNameAndPhone" resultType="map">
    select username,telephone as phone from user
</select>

<!--结果：
	[
	 {phone=15149390721, username=Jack}, 
	 {phone=17869765432, username=Eric}, 
	 {phone=13777678273, username=Bob}, 
	 {phone=17628392736, username=Mary}
	]
-->
```

2、方法2

```xml
<!--
	@MapKey("id")
	List<Map<String,Object>> getNameAndPhone();
-->

<select id="getNameAndPhone" resultType="map">
    select id,username,telephone as phone from user
</select>

<!--结果：
	{
	 9={phone=15149390721, id=9, username=Jack}, 
	 10={phone=17869765432, id=10, username=Eric}, 
	 11={phone=13777678273, id=11, username=Bob}, 
	 12={phone=17628392736, id=12, username=Mary}
	}
-->
```

## 1.7.MaBatis自定义映射

> resultMap 可以自定义 sql 的结果和 java 对象属性的映射关系
>
> 常用在字段名和 java 对象属性名不一样的情况
>
> 使用方式： 
>
> ​	1.先定义 resultMap,指定列名和属性的对应关系
>
> ​	2.在中把 resultType 替换为 resultMap

准备工作：新建员工表和部门表

![image-20220722213720570](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220722213720570.png)

![image-20220722214555962](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220722214555962.png)

新建Emp类

```java
public class Emp {
    private Integer empId;
    private String empName;
    private Integer empAge;
    private String empSex;
 
 	/* 无参和全参构造 */
 	/* getter 和 setter */
 	/* toString方法 */
}
```

定义和使用resultMap

```xml
<resultMap id="empMap" type="Emp">
    <!-- 主键字段使用 id -->
    <id column="emp_id" property="empId"/>
	<!-- 非主键字段使用 result-->
    <result column="emp_name" property="empName"/>
    <result column="emp_age" property="empAge"/>
    <result column="emp_sex" property="empSex"/>
</resultMap>

<!--Emp queryById(Integer empId);-->

<select id="queryById" resultMap="Emp">
    select * from t_emp where emp_id = #{id}
</select>
```

数据库字段名和对象属性名不一致的<font color='red'>另外</font>两种解决方案：

**方案1：查询数据时给字段起别名**

```xml
<select id="getById" resultType="userMap">
    select username name,password pwd,telephone tel from user where id = #{id}
</select
```

**方案2：在核心配置文件中使用** 

```xml
<settings>
	<!-- 
		自动将下划线名称映射为驼峰命名 
		emp_id     >>>    emoId
		emp_name   >>>    empName
		emp_sex    >>>    empSex
	-->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

<h4>多对一映射处理</h4>

> 场景模拟： 查询员工信息以及员工所对应的部门信息

~~~java
//部门类
public class Dept {
    private Integer deptId;
    private String deptName;
    
    /* 无参和全参构造、getter 和 setter、toString方法 */
}

//员工类
public class Emp {
    private Integer empId;
    private String empName;
    private Integer empAge;
    private String empSex;
    private Dept dept;
    
    /* 无参和全参构造、getter 和 setter、toString方法 */
}
~~~

**方式1：级联方式处理映射关系**

```xml
<resultMap id="empDeptMap" type="Emp">
    <!--开启mapUnderscoreToCamelCase后这些标签可以不写-->
    <!--
    	<id column="emp_id" property="empId"></id>
    	<result column="emp_name" property="empName"/>
    	<result column="emp_age" property="empAge"/>
    	<result column="emp_sex" property="empSex"/>
    -->
    <result column="dept_id" property="dept.deptId"></result>
    <result column="dept_name" property="dept.deptName"></result>
</resultMap>

<!--Emp getEmpAndDeptByEid(@Param("eid") int eid);-->
<select id="getEmpAndDeptByEid" resultMap="empDeptMap">
    select emp.*,dept.*
    from t_emp emp
    left join t_dept dept
    on emp.dept_id =dept.dept_id
    where emp.emp_id = #{eid}
</select>
```

**方式2：使用association处理映射关系**

~~~xml
<resultMap id="empDeptMap" type="Emp">
    <!--开启mapUnderscoreToCamelCase后这些标签可以不写-->
    <!--
    	<id column="emp_id" property="empId"></id>
    	...
    -->
    <!-- 实体类属性使用 association-->
    <association property="dept" javaType="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
    </association>
</resultMap>

<!--Emp getEmpAndDeptByEid(@Param("eid") int eid);-->
<select id="getEmpAndDeptByEid" resultMap="empDeptMap">
    select emp.*,dept.*
    from t_emp emp
    left join t_dept dept
    on emp.dept_id =dept.dept_id
    where emp.emp_id = #{eid}
</select>
~~~

**方法3：分步查询**

首先查询员工信息

```xml
    <resultMap id="empAndDeptMap" type="Emp">
	    <!--开启mapUnderscoreToCamelCase后这些标签可以不写-->
        <!--
			<id column="emp_id" property="empId"></id>
			...
		-->
        <association property="dept"
                     select="com.why.mapper.DeptMapper.queryDeptById"
                     column="dept_id"/>
    </resultMap>
    <!--Emp getEmpById(@Param("empId")Integer empId);-->
    <select id="getEmpById" resultMap="empAndDeptMap">
        select * from t_emp where emp_id = #{empId}
    </select>
```

然后根据员工的部门ID查询部门

~~~xml
<!--Dept queryDeptById(@Param("deptId")Integer deptId);-->
<select id="queryDeptById" resultType="Dept">
    select dept_id ,dept_name  from t_dept where dept_id = #{deptId}
</select>
~~~

> 首先执行getEmpById的sql语句,然后通过association标签中的select属性的值找到下一步要执行的sql
>
> 而column属性表示查询结果dept_id字段的值作为参数传递给下一步的sql,然后执行queryDeptById的sql语句 

<h4>一对多映射处理</h4>

> 模拟场景：根据部门id查新部门以及部门中的员工信息

**方式1：使用collection处理映射**

```xml
<resultMap id="deptEmpMap" type="Dept">
    <id property="deptId" column="dept_id"/>
    <result property="deptName" column="dept_name"/>
    <!-- ofType：设置collection标签所处理的集合属性中存储数据的类型 -->
    <collection property="emps" ofType="Emp">
        <id property="empId" column="emp_id"/>
        <result property="empName" column="emp_name"/>
        <result property="empAge" column="emp_age"/>
        <result property="empSex" column="emp_sex"/>
    </collection>
</resultMap>

<!--Dept getDeptEmpByDid(@Param("did") int did);-->
<select id="getDeptEmpByDid" resultMap="deptEmpMap">
    select dept.*,emp.*
    from t_dept dept
    left join t_emp emp
    on dept.dept_id = emp.dept_id
    where dept.dept_id = #{did}
</select>
```

**方式2：分步查询**

首先查询部门信息

```xml
<resultMap id="deptEmpMap1" type="Dept">
    <id property="deptId" column="dept_id"/>
    <result property="deptName" column="dept_name"/>
    <!-- ofType：设置collection标签所处理的集合属性中存储数据的类型 -->
    <collection property="emps"
                fetchType="eager"
                select="com.why.mapper.EmpMapper.getEmpByDeptId"
                column="dept_id"/>
</resultMap>

<!--Dept getDeptByDid(@Param("did") int did);-->
<select id="getDeptByDid" resultMap="deptEmpMap1">
    select * from t_dept where dept_id = #{did}
</select>
```

然后根据部门ID查询员工

~~~xml
<!--Emp getEmpByDeptId(@Param("did") Integer did);-->
<select id="getEmpByDeptId" resultType="Emp">
    select * from t_emp where dept_id = #{did}
</select>
~~~



<h4>延迟加载</h4>

分步查询的优点：可以实现延迟加载

全局配置延时加载

~~~xml
<settings>
    <!--
        延时加载
            true  开启延时加载
            false 关闭延时加载
    -->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!--
        按需加载：
            true 任何方法的调用都会加载该对象的所有属性
            false 按需加载
    -->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
~~~

局部延时加载

~~~xml
<resultMap id="empAndDeptMap" type="Emp">
	<!--
		fetchType:
			eager 立即加载
			lazy  延迟加载
	-->
    <association property="dept"
                 fetchType="eager"
                 select="com.why.mapper.DeptMapper.queryDeptById"
                 column="dept_id"/>
</resultMap>
~~~



## 1.8.MyBatis特殊SQL

<h4>模糊查询</h4>

> 模糊查询的实现有两种方式
>
> ​	一是 Java 代码中给查询数据参数加上“%” 
>
> ​	二是 在 mapper 文件 sql 语句的条件位置加上“%”

```xml
<!--List<User> queryLike(@Param("name")String name);-->

<select id="queryLike" resultType="User">
    <!--
    select * from user where username like '%${name}%'
	select * from t_user where username like concat('%',#{mohu},'%')
    -->
    select * from user where username like "%"#{name}"%"
</select>
```

<h4>获取自增主键的值</h4>

> useGeneratedKeys：设置使用自增的主键 
>
> keyProperty：将获取的自增的主键放在传输的参 数user对象的某个属性中

```xml
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into user values (null,#{username},#{password},#{telephone})
</insert>
```

```java
@Test
public void test12(){
    SqlSession sqlSession = SqlSessionUitl.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = new User(null,"Tom","123456","17856754367");
    //User{id=null, username='Tom', password='123456', telephone='17856754367'}
    System.err.println(user);
    //在执行insert操作后将自增ID的值放入到参数user对象的id属性上
    mapper.insertUser(user);
    //User{id=19, username='Tom', password='123456', telephone='17856754367'}
    System.err.println(user);
}
```

## 1.9.MyBatis动态SQL

<h4>if标签</h4>

> 当 test 的值为 true 时,会将其包含的 SQL 片断拼接 到其所在的 SQL 语句中。

~~~xml
<!--List<Emp> queryEmp(Emp emp);-->
<select id="queryEmp" resultType="Emp">
    select * from t_emp where
    <if test="empName != null and empName != ''">
        emp_name = #{empName}
    </if>
    <if test="empAge != null and empAge != ''">
        and emp_age = #{empAge}
    </if>
</select>
~~~

使用if标签存在的问题

​	1.当所有的if标签中的条件全都不成立时 执行的SQL语句在末尾会多出where `select * from t_emp where`

​	2.当首条if标签中的条件不成立时 执行的SQL语句在where后面会多出and `select * from t_emp where and emp_age = ?`

解决方法：

​	1.在where后面添加恒成立的条件

​	2.使用where标签

~~~xml
<select id="queryEmp" resultType="Emp">
    select * from t_emp where 1=1
    <if test="empName != null and empName != ''">
        and emp_name = #{empName}
    </if>
    <if test="empAge != null and empAge != ''">
        and emp_age = #{empAge}
    </if>
</select>
~~~

<h4>where标签</h4>

> where标签配合if标签使用
>
> where标签的功能：
>
> ​	1.若where标签中有条件成立,会自动生成where
>
> ​	2.会删除if标签中内容前面多余的and
>
> ​	3.若where标签中的条件全都不成立,则where标签不起任何作用

```xml
<!--List<Emp> queryEmp(Emp emp);-->
<select id="queryEmp" resultType="Emp">
    select * from t_emp
    <where>
        <if test="empName != null and empName != ''"> 
            and emp_name = #{empName}
        </if>
        <if test="empAge != null and empAge != ''">
            and emp_age = #{empAge} 
        </if>
    </where>
</select>
```

<h4>trim标签</h4>

> trim用于去掉或添加标签中的内容 
>
> 常用属性： 
>
> ​	prefix: 在trim标签中的内容的前面添加某些内容 
>
> ​	prefixOverrides: 在trim标签中的内容的前面去掉某些内容 
>
> ​	suffix: 在trim标签中的内容的后面添加某些内容 
>
> ​	suffixOverrides: 在trim标签中的内容的后面去掉某些内容

~~~xml
<!--List<Emp> queryEmp(Emp emp);-->
<select id="queryEmp" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="empName != null and empName != ''">
            emp_name = #{empName} and
        </if>
        <if test="empAge != null and empAge != ''">
            emp_age = #{empAge} and
        </if>
    </trim>
</select>
~~~

<h4>choose、when、otherwise标签</h4>

> choose、when、otherwise标签配合使用
>
> when相当于else if,otherwise相当于else、
>
> 使用choose标签最终只会选择一个条件

~~~xml
<!--List<Emp> queryEmp(Emp emp);-->
<select id="queryEmp" resultType="Emp">
    select * from t_emp where
    <choose>
        <when test="empName != null and empName != ''">
            emp_name = #{empName}
        </when>
        <when test="empAge != null and empAge != ''">
            and emp_age = #{empAge}
        </when>
        <otherwise>
            emp_sex = '男'
        </otherwise>
    </choose>
</select>
~~~

如果empName和empAge条件都成立 `select * from t_emp where emp_name = ?`

如果empName和empAge条件都不成立 `select * from t_emp where emp_sex = '男'`

<h4>foreach标签</h4>

> foreach标签中的属性：
>
> ​	collectionn 表示要遍历的集合类型 如list,array
>
> ​	item 表示集合中的成员
>
> ​	separator 集合成员之间的分隔符
>
> ​	open 开始的字符
>
> ​	close 结束的字符

~~~xml
<!--Integer insertEmps(@Param("emps") List<Emp> emps);-->
<insert id="insertEmps">
    insert into t_emp values
    <!--对于集合类型参数,如果没有使用@Param注解 collection应该使用list-->
    <foreach collection="emps" item="emp" separator=",">
        (null,#{emp.empName},#{emp.empAge},#{emp.empSex},null)
    </foreach>
</insert>
~~~

~~~xml
<!--Integer deleteByIds(@Param("ids") Integer[] ids);-->
<delete id="deleteByIds">
    delete from t_emp where emp_id in
    <!--对于数组类型参数,如果没有使用@Param注解 collection应该使用array-->
    <foreach collection="ids" item="arr" open="(" separator="," close=")">
        #{arr}
    </foreach>
</delete>
~~~

<h4>sql片段</h4>

> sql标签用于定于sql片段
>
> include标签用于引用sql片段

~~~xml
<!--创建sql片段-->
<sql id="queryColumn">
    select emp_id,emp_name,emp_age,emp_sex from t_emp
</sql>

<select id="queryJack" resultType="Emp">
    <!--引用sql片段-->
    <include refid="queryColumn"/>
    where emp_name = 'Jack'
</select>
~~~

## 1.10.MyBatis缓存

<h4>一级缓存</h4>

> 一级缓存是sqlSession级别的,即通过同一个sqlSession对象查询的数据会被缓存
>
> 一级缓存是MyBatis默认开启的
>
> 一级缓存失效的四种情况：
>
> ​	1.不同的sqlSession对应不同一级缓存
>
> ​	2.同一个sqlSession但是查询条件不同
>
> ​	3.同一个sqlSession两次查询操作之间执行了任何增删改操作
>
> ​	4.同一个sqlSession两次查询操作之间手动执行清空缓存 `sqlSession.clearCache();`

```java
SqlSession sqlSession = SqlSessionUitl.getSqlSession();
EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
List<Emp> emps1 = mapper.queryAll();
//第二次查询相同的数据从缓存中查
List<Emp> emps2 = mapper.queryAll();
```

~~~java
SqlSession sqlSession = SqlSessionUitl.getSqlSession();
EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
List<Emp> emps1 = mapper.queryAll();
//手动清空一级缓存
sqlSession.clearCache();
List<Emp> emps2 = mapper.queryAll();
~~~

<h4>二级缓存</h4>

> 二级缓存是SqlSessionFactory级别,通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存
>
> 二级缓存的开启
>
> ​	1.在核心配置文件中,设置全局配置属性cacheEnabled="true"，默认为true
>
> ​	2.在映射文件中设置标签\<cache/>
>
> ​	3.二级缓存必须在SqlSession关闭或提交之后有效
>
> ​	4查询的数据所转换的实体类类型必须实现序列化的接口
>
> 二级缓存的失效
>
> ​	两次查询操作之间执行了任何增删改操作

~~~java
 SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
         .build(Resources.getResourceAsStream("mybatis-config.xml"));
 SqlSession sqlSession1 = sqlSessionFactory.openSession(true);
 SqlSession sqlSession2 = sqlSessionFactory.openSession(true);
 EmpMapper mapper1 = sqlSession1.getMapper(EmpMapper.class);
 EmpMapper mapper2 = sqlSession2.getMapper(EmpMapper.class);
 mapper1.queryAll();
 //二级缓存必须在SqlSession关闭或提交之后有效
 sqlSession1.close();
 mapper2.queryAll();
 sqlSession1.close();
~~~

~~~
Cache Hit Ratio [com.why.mapper.EmpMapper]: 0.5 
~~~

<h4>二级缓存的相关设置</h4>

>eviction属性的值：
>
>​	LRU 最久未使用,移除最长时间不被使用的对象
>
>​	FIFO 先进先出,按对象进入缓存的顺序来移除
>
>​	SOFT 软引用,移除基于垃圾回收器状态和软引用规则的对象
>
>​	WEAK 弱引用,更积极地移除基于垃圾收集器状态和弱引用规则的对象
>
>flushInterval: 缓存刷新间隔,单位毫秒
>
>​	默认不设置,即调用语句时刷新
>
>size:代表缓存最多可以存储多少个对象
>
>readOnly:只读
>
>​	true 只读缓存,会给所有调用者返回缓存对象的相同实例,性能好
>
>​	false（默认值） 读写缓存,会返回缓存对象的拷贝，安全性高

~~~xml
<cache eviction="LRU"
       flushInterval="60000"
       size="1000"
       readOnly="false"/>
~~~

MyBatis缓存查询的顺序

​	先查询二级缓存

​	如果二级缓存未命中,就查询一级缓存

​	如果一级缓存未命中,就从数据库中查

<h4>整合第三方缓存EHCache</h4>

1.添加依赖

~~~xml
<!-- Mybatis EHCache整合包 -->
<dependency>
	<groupId>org.mybatis.caches</groupId>
	<artifactId>mybatis-ehcache</artifactId>
	<version>1.2.1</version>
</dependency>
<!-- slf4j日志门面的一个具体实现 -->
<dependency>
	<groupId>ch.qos.logback</groupId>
	<artifactId>logback-classic</artifactId>
	<version>1.2.3</version>
</dependency>
~~~

2.创建EHCache的配置文件ehcache.xml

~~~xml
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\ehcache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
~~~

| 属性名                          | 是否必须 | 作用                                                         |
| :------------------------------ | -------- | ------------------------------------------------------------ |
| maxElementsInMemory             | 是       | 在内存中缓存的element的最大数目                              |
| maxElementsOnDisk               | 是       | 在磁盘上缓存的element的最大数目，若是0表示无穷大             |
| eternal                         | 是       | 设定缓存的elements是否永远不过期。true表示缓存的数据始终有效 |
| overflowToDisk                  | 是       | 设定当内存缓存溢出的时候是否将过期的element 缓存到磁盘上     |
| timeToIdleSeconds               | 否       | 当两次访问的时间超过timeToIdleSeconds的属性取值时,这些数据便会删除 |
| timeToLiveSeconds               | 否       | 缓存element的有效生命期                                      |
| diskSpoolBufferSizeMB           | 否       | 磁盘缓存的缓存区大小,默认30MB                                |
| diskExpiryThreadIntervalSeconds | 否       | 磁盘缓存的清理线程运行间隔,默认120秒                         |
| memoryStoreEvictionPolicy       | 否       | 移除缓存中element的策略,默认是LRU (可选LFU、FIFO)            |

3.设置二级缓存的类型

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

4.创建logback的配置文件logback.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是： 时间、日志级别、线程名称、打印日志的类、日志主体内容、换行
            -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger]
                [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是： DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.why.mapper" level="DEBUG"/>
</configuration>
~~~

## 1.11.MyBatis逆向工程

1.添加依赖和逆向工程插件

~~~xml
<dependencies>
    <!-- 依赖MyBatis核心包 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- log4j日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
</dependencies>
<!-- 控制Maven在构建过程中相关配置 -->
<build>
<!-- 构建过程中用到的插件 -->
    <plugins>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.0</version>
            <!-- 插件的依赖 -->
            <dependencies>
                <!-- 逆向工程的核心依赖 -->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <!-- MySQL驱动 -->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.16</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
~~~

2.创建逆向工程的配置文件

> 逆向工程的名字必须是generatorConfig.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
    targetRuntime: 执行生成的逆向工程的版本
        MyBatis3Simple: 生成基本的CRUD（清新简洁版）
        MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3Simple">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                        userId="root"
                        password="123456">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.why.mybatis.pojo"
                            targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.why.mybatis.mapper"
                         targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.why.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
~~~

3.双击执行插件

> maven &rarr; Plugins &rarr; mybatis-generator &rarr; mybatis-generator:generate

4.创建mybatis核心配置文件

## 1.12MyBatis分页插件

1.导入依赖

~~~xml
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>5.2.0</version>
</dependency>
~~~

2.配置分页插件

> 在MyBatis的核心配置文件中配置插件

~~~xml
<plugins>
	<!--设置分页插件-->
	<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
~~~

3.使用分页插件

Page对象

~~~java
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
SqlSession sqlSession = new SqlSessionFactoryBuilder().build(is).openSession(true);
EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
/*开启分页插件功能
    pageNum：当前页的页码
    pageSize：每页显示的条数
*/
Page<Object> page = PageHelper.startPage(1, 5);
mapper.selectAll();
System.err.println(page.getResult());

/*	page对象中常见的属性
        pageNum:当前页的页码
        pageSize:每页显示的条数
        startRow:当前页首条记录的下标
        endRow:当前页末条记录的下标
        total:总记录条数
        pages:总页数
*/
~~~

PageInfo对象

~~~java
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
SqlSession sqlSession = new SqlSessionFactoryBuilder().build(is).openSession(true);
EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
//开启分页插件功能
PageHelper.startPage(8, 10);
List<Emp> emps = mapper.selectAll();
/*
	list:分页之后的数据
	navigatePages:导航分页的页码数
*/
PageInfo<Emp> empPageInfo = new PageInfo<Emp>(emps, 3);

/*
首页 上一页 ... 7,8,9 ... 下一页 尾页 (navigatePages = 3)
PageInfo对象常用属性
    pageNum、pageSize、size、startRow、endRow、total、pages
    prePage：上一页的页码
    nextPage:下一页的页码
    isFirstPage:是否是第一页
    isLastPage:是否是最后一页
    hasPreviousPage:是否有前一页
    hasNextPage:是否有后一页
    navigatePages:导航分页的页码数
    navigateFirstPage:导航分页开始的页码(上面例子中的7)
    navigateLastPage:导航分页结束的页码(上面例子中的9)
    navigatepageNums:导航分页的页码[7,8,9]
*/
~~~




<hr>

# 2.Spring

## 2.1.Spring入门

<h4>1.创建maven工程</h4>

<h4>2.导入Spring依赖</h4>

~~~xml
<dependencies>
    <!--Spring核心依赖-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
~~~

<h4>3.定义接口和实体类</h4>

~~~java
public interface SomeService {
    void doSome();
}

public class SomeServiceImpl implements SomeService {
    @Override
    public void doSome() {
        System.out.println("doSome方法执行");
    }
}

~~~

<h4>4.创建Spring配置文件</h4>

> 配置文件建议使用applicationContext.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
    注册bean对象
        id：自定义对象的名称
        class：类的全限定名称,不能是接口
    -->
    <bean id="someService" class="com.why.service.SomeServiceImpl"/>
</beans>
~~~

<h4>5.测试</h4>

~~~java
ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
SomeService someService = (SomeService)ioc.getBean("someService");
someService.doSome();
~~~
## 2.2.IOC

### 2.2.1.获取bean的方式

<h4>方式1:根据Id获取</h4>

~~~java
ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
//根据Id获取bean
SomeService someService = (SomeService)ioc.getBean("someService");
someService.doSome();
~~~

<h4>方式2：根据类型获取（常用）</h4>

> 根据类型获取bean时,要求IOC容器中指定类型的bean有且只能有1个
>
> ​	1.当配置了两个同类型bean标签时 `org.springframework.beans.factory.NoUniqueBeanDefinitionException`
>
> ​	2.当没有配置该类型的bean标签时 `org.springframework.beans.factory.NoSuchBeanDefinitionException`

~~~java
ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
//根据类型获取bean
SomeService someService = ioc.getBean(SomeService.class);
someService.doSome();
~~~

<h4>方式3：根据Id和类型获取</h4>

~~~java
ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
//根据Id和类型获取bean
SomeService someService = ioc.getBean("someService", SomeService.class);
someService.doSome();
~~~

### 2.2.2.基于XML的依赖注入

<h4>1.set注入</h4>

> property标签：通过组件类的setXxx()方法给组件对象设置属性
>
> 简单类型使用value
>
> 引用类型使用ref

~~~java
public class School {
    private String schoolName;
    private String address;
    private List<Student> students;
    /*无参和有参构造*/
	/*setter和getter*/
	/*toString方法*/
}
public class Student {
    private String name;
    private Integer age;
    private String[] hobby;
    private School school;
    /*无参和有参构造*/
	/*setter和getter*/
	/*toString方法*/
}
~~~

<h5>1.1.简单类型</h5>

~~~xml
<bean id="mySchool" class="com.why.School">
    <!--简单类型-->
    <property name="schoolName" value="浙江大学"></property>
    <property name="address" value="浙江杭州"></property>
</bean>

~~~

<h5>1.2.引用类型</h5>

~~~xml
<bean id="student" class="com.why.Student">
    <property name="name" value="Jack"></property>
    <property name="age" value="20"></property>
    <!--引用类型:ref方式-->
    <property name="school" ref="mySchool"></property>
</bean>

<bean id="student" class="com.why.pojo.Student">
    <property name="name" value="Jack"></property>
    <property name="age" value="20"></property>
    <!--引用类型:内部bean-->
    <property name="school">
        <bean id="mySchool" class="com.why.pojo.School">
            <property name="schoolName" value="浙江大学"/>
            <property name="address" value="浙江杭州"/>
        </bean>
    </property>
</bean>
~~~

<h5>1.3.数组类型</h5>

~~~xml
<bean id="student" class="com.why.pojo.Student">
    <property name="name" value="Jack"></property>
    <property name="age" value="20"></property>
    <!--数组类型-->
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
</bean>
~~~

<h5>1.4.集合类型</h5>

~~~xml
<bean id="mySchool" class="com.why.pojo.School">
    <property name="schoolName" value="浙江大学"></property>
    <property name="address" value="浙江杭州"></property>
    <!--集合类型-->
    <property name="students">
        <list>
            <ref bean="studentOne"/>
            <ref bean="studentTwo"/>
            <ref bean="studentThree"/>
        </list>
    </property>
</bean>
~~~

<h5>1.5.Map类型</h5>

~~~xml
 <bean id="mySchool" class="com.why.pojo.School">
     <property name="schoolName" value="浙江大学"></property>
     <property name="address" value="浙江杭州"></property>
     <!--Map类型-->
     <property name="studentMap">
         <map>
             <entry>
                 <key><value>studentOne</value></key>
                 <ref bean="studentOne"></ref>
             </entry>
             <entry>
                 <key><value>studentTwo</value></key>
                 <ref bean="studentTwo"></ref>
             </entry>
         </map>
     </property>
 </bean>
~~~

<h4>2.构造注入</h4>

~~~xml
<bean id="studentOne" class="com.why.Student">
    <constructor-arg name="stuName" value="Andy"></constructor-arg>
    <constructor-arg name="stuAge" value="20"></constructor-arg>
    <constructor-arg name="stuSchool" ref="mySchool"></constructor-arg>
</bean>
<!--index:指明该参数对应着构造器的第几个参数,从0开始(少用)-->
<bean id="studentTwo" class="com.why.Student">
    <constructor-arg index="0" value="Mary"></constructor-arg>
    <constructor-arg index="1" value="20"></constructor-arg>
    <constructor-arg index="2" ref="mySchool"></constructor-arg>
</bean>
<!--index属性不要也行,若参数类型相同或之间有包含关系,则需要保证赋值顺序要与构造器中的参数顺序一致-->
<bean id="studentThree" class="com.why.Student">
    <constructor-arg value="Bob"></constructor-arg>
    <constructor-arg value="20"></constructor-arg>
    <constructor-arg ref="mySchool"></constructor-arg>
</bean>
~~~

<h4>3.注入特殊的值</h4>

> 注入null 需要使用null标签
>
> 注入实体 
>
> ​	1.用实体符号
>
> ​	2.CDATA节

注入null

~~~xml
<bean id="student" class="com.why.pojo.Student">
    <property name="name">
        <null/>
    </property>
</bean>
~~~

注入实体 

~~~xml
<bean id="student" class="com.why.pojo.Student">
    <property name="name">
        <!--快捷键：大写CD-->
        <value><![CDATA[<Jack>]]></value>
    </property>
</bean>
~~~

<h4>4.注册集合类型的bean</h4>

~~~xml
<!--list集合类型的bean-->
<util:list id="students">
	<ref bean="studentOne"></ref>
	<ref bean="studentTwo"></ref>
	<ref bean="studentThree"></ref>
</util:list>
<!--map集合类型的bean-->
<util:map id="teacherMap">
	<entry>
		<key><value>10010</value></key>
		<ref bean="teacherOne"></ref>
	</entry>
	<entry>
		<key><value>10086</value></key>
		<ref bean="teacherTwo"></ref>
	</entry>
</util:map>
~~~

### 2.2.3.引入外部属性文件

1.加入依赖

~~~xml
<!-- MySQL驱动 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.16</version>
</dependency>
<!-- 数据源 -->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.0.31</version>
</dependency>
~~~

2.创建properties属性文件

~~~properties
jdbc.user=root
jdbc.password=atguigu
jdbc.url=jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.driver=com.mysql.cj.jdbc.Driver
~~~

3.引入外部属性文件

~~~xml
<!-- 引入外部属性文件 -->
<context:property-placeholder location="classpath:jdbc.properties"/>
<!-- 配置bean -->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
	<property name="url" value="${jdbc.url}"/>
	<property name="driverClassName" value="${jdbc.driver}"/>
	<property name="username" value="${jdbc.user}"/>
	<property name="password" value="${jdbc.password}"/>
</bean>

~~~

### 2.2.4.bean的作用域

> 通过配置bean标签的scope属性来指定bean的作用域范围

| 取值              | 含义                                   |
| ----------------- | -------------------------------------- |
| singleton（默认） | 在IOC容器中,这个bean的对象始终为单实例 |
| prototype         | 在IOC容器中,这个bean的对象有多个实例   |

~~~xml
<bean id="school" class="com.why.pojo.School" scope="singleton"></bean>
<bean id="student" class="com.why.pojo.Student" scope="prototype"></bean>
~~~

### 2.2.5.bean的生命周期

> 1.bean对象创建(调用无参构造器) 
>
> 2.给bean对象设置属性
>
> 3.bean对象初始化之前操作(由bean的后置处理器负责)
>
> 4.bean对象初始化(需在配置bean时指定初始化方法)
>
> 5.bean对象初始化之后操作(由bean的后置处理器负责) 
>
> 6.bean对象就绪可以使用 
>
> 7.bean对象销毁(需在配置bean时指定销毁方法)

~~~java
public class User{
    private String username;
    private String password;
    
    public User(){System.out.println("生命周期:bean对象创建");}
    
    public void initMethod(){
        System.out.println("生命周期:bean对象初始化");
    }
    public void destroyMethod(){
        System.out.println("生命周期:bean对象销毁");
    }
    public void setUsername(String username){
        System.out.println("生命周期:依赖注入");
        this.username = username;
    }
    /*其他 getter 和 setter 方法*/    
}
~~~

~~~xml
<!-- 使用init-method属性指定初始化方法 -->
<!-- 使用destroy-method属性指定销毁方法 -->
<bean class="com.atguigu.bean.User" 
      scope="prototype" 
      init-method="initMethod"
      destroy-method="destroyMethod">
    
	<property name="id" value="1001"></property>
	<property name="username" value="admin"></property>
	<property name="password" value="123456"></property>
	<property name="age" value="23"></property>
</bean>
~~~

bean的后置处理器

> bean的后置处理器会在生命周期的初始化前后添加额外的操作
>
> 需要实现BeanPostProcessor接口

~~~java
public class MyBeanProcessor implements BeanPostProcessor {
	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("☆☆☆" + beanName + " = " + bean);
		return bean;
	}
	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("★★★" + beanName + " = " + bean);
	return bean;
	}
}
~~~

~~~xml
<!-- bean的后置处理器要放入IOC容器才能生效 -->
<bean id="myBeanProcessor" class="com.atguigu.spring.process.MyBeanProcessor"/>
~~~

### 2.2.6.基于XML的自动装配

<h4>byName</h4>

> byName方式自动注入时Spring会根据用于类型的变量名去调用相应的setter方法

~~~java
public class Student {
    private String name;
    private Integer age;
    private School schoolXXX;
	/*setter*/
}
~~~

~~~xml
<bean id="student" class="com.why.pojo.Student" autowire="byName">
    <property name="name" value="Eric"></property>
    <property name="age" value="20"></property>
</bean>

<bean id="schoolXXX" class="com.why.pojo.School">
    <property name="schoolName" value="温州大学"></property>
    <property name="addr" value="浙江温州"></property>
</bean>
~~~

<h4>byType</h4>

> 配置文件中被调用者bean的class属性指定的类,要与代码中调用者bean类的某引用类型属性类型同源
>
> byType方式自动注入要求同源的被调用bean只能有一个

~~~xml
<bean id="student" class="com.why.pojo.Student" autowire="byType">
    <property name="name" value="Andy"></property>
    <property name="age" value="20"></property>
</bean>
~~~

### 2.2.7.指定多个配置文件

> 在实际应用里,随着应用规模的增加,系统中Bean数量也大量增加,导致配置文件变得非常庞大、臃肿。
>
> 为了避免这种情况的产生,提高配置文件的可读性与可维护性,可以将Spring配置文件分解成多个配置文件。

spring-school.xml

```xml
<bean id="middleSchool" class="com.why.pojo.School">
    <property name="schoolName" value="温州中学"></property>
    <property name="addr" value="浙江温州"></property>
</bean>
```

spring-student.xml

```xml
<bean id="lh" class="com.why.pojo.Student">
    <property name="name" value="林浩"></property>
    <property name="age" value="20"></property>
    <!--引用类型-->
    <property name="school" ref="middleSchool"></property>
</bean>
<!--引入spring-school.xml-->
<import resource="classpath:spring-school.xml"/>
```
applicationContext.xml(主配置文件)

```xml
<import resource="classpath:spring-school.xml"/>
<import resource="classpath:spring-student.xml"/>
<!--也可使用通配符*-->
<!-- <import resource="classpath*:spring-*.xml"/> -->
```

### 2.2.8基于注解的依赖注入

> 对于注解的DI,需要在Spring配置文件中配置组件扫描器,用于在指定的基本包中扫描注解。

~~~xml
	<!--声明组件扫描器：指定注解所在的包-->
    <context:component-scan base-package="com.why"/>
    <!-- 指定多个包
    <context:component-scan base-package="com.why,com.ehy"/> 第1种方式：逗号分隔
    <context:component-scan base-package="com.why;com.ehy"/> 第2种方式：分号分隔
    <context:component-scan base-package="com.why com.ehy"/> 第3种方式：空格分隔
    <context:component-scan base-package="com"/> 第4种方式：指定到父包,也会扫描到子包下级的子包-->

c   <context:component-scan base-package="com.why">
    	<!--指定不扫描的类
        	annotation 根据注解排除,expression中设置要排除的注解的全类名
        	assignable 根据类型排除,expression中设置要排除的类型的全类名-->
    	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
~~~

定义Bean的注解

> - @Component 对普通类进行标识
> - @Repository 对DAO实现类进行标识
> - @Service 对Service实现类进行标识
> - @Controller 对Controller实现类进行标识

~~~java
// 不指定value属性,bean的id是类名的首字母小写school
@Component("collage")
public class School {
    private String schoolName;
    private String address;
}
~~~

简单类型属性注入

> 对于简单类型的属性使用@Value注解
>
> 使用注解进行依赖注入无需提供setter方法
>
> @Value注解使用的位置
>
> ​	1.属性上
>
> ​	2.setter方法上
>
> ​	3.构造方法上

~~~java
@Component
public class School {
    @Value("浙江大学")
    private String schoolName;
    @Value("浙江杭州")
    private String addr;
}
~~~

### 2.2.9基于注解的自动装配

<h4>@Autowired</h4>

> @Autowried注解的原理
>
> ​	默认通过byType方式自动装配
>
> ​	若有多个匹配的类型就转换成byName方式自动装配
>
> ​	若存在多个匹配的类型且无法通过Id匹配则抛出异常
>
> @Autowired使用较多,因为同类型的bean通常只配置单个

~~~java
@Component
public class Student {
    @Value("Jack")
    private String name;
    @Autowired
    private School school;
}

~~~

@Autowired可以配合@Qualifier注解实现byName的自动装配

~~~java
@Component
public class Student {
    @Value("Jack")
    private String name;
    @Autowired
    @Qualifier("middleSchool")
    private School school;
}
~~~

<h4>@Resource</h4>

> @Resource是JDK提供的注解
>
> @Resource默认按byName的方式自动装配
>
> 若按byName的方式注入按名称不能注入bean时,则会按照byType的方式自动装配

~~~java
@Component
public class Student {
    @Value("Jack")
    private String name;
    @Resource
    private School school;
}

~~~

## 2.3.AOP

### 2.3.1AOP相关概念

> - 横切关注点: 从方法中抽取出来的非核心业务
> - 通知: 每个横切关注点上要做的事都需要方法实现,这样的方法就叫通知方法
> - 切面: 封装通知方法的类
> - 目标: 要被增强的对象
> - 连接点: 可以被切面织入的具体方法
> - 切入点: 匹配连接点的式子

### 2.3.2.切入点表达式

> execution(访问权限类型 <font color='red'>返回值类型</font> 包名类名 <font color='red'>方法名(参数类型和参数个数)</font> 抛出异常类型)
> 红色字体表示必选,黑色字体表示可选

| 符号 | 意义                                                         |
| ---- | ------------------------------------------------------------ |
| *    | 0至多个任意字符                                              |
| ..   | 用在方法参数中表示任意多个参数 用在包名后面表示当前包以及其子包路径 |
| +    | 用在类名后面表示当前类及子类 用在接口后表示当前接口及其实现类 |

~~~java
举例：
execution(public * *(..))
指定切入点为：任意公共方法
execution(* set*(..))
指定切入点为：任何一个以“set”开始的方法
execution(* *..service.*.*(..))
指定所有包下的 serivce 子包下所有类（接口）中所有方法为切入点
~~~

### 2.3.3.基于注解的AOP

1.导入AspectJ依赖

~~~xml
<!--AspectJ依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
~~~

2.定义目标

~~~java
public interface Target {
    Integer div(int a,int b);
}

@Component
public class TargetImpl implements Target {
    public Integer div(int a, int b) {
        System.out.println("我是被增强的方法");
        return a/b;
    }
}
~~~

3.定义切面

~~~java
/*
  @Aspect：是AspectJ框架的注解表示当前类是切面类
  @Order: 定义切面的优先级,数值越小优先级越高,默认是2147483647
 */
@Order(1)
@Aspect
@Component
public class MyAspect {

    @Before("execution(public int com.why.spring.service.TargetImpl.div(int,int))")
    public void before(){
        System.err.println("前置通知：在目标方法执行前执行");
    }
}
~~~

4.开启AspectJ的自动代理

> 在spring的配置文件applicationContext.xml文件中配置

~~~xml
<!--开启注解扫描器-->
<context:component-scan base-package="com.why.spring"/>
<!--开启AspectJ的自动代理,为目标对象自动生成代理-->
<aop:aspectj-autoproxy/>
~~~

测试

~~~java
ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
Target target = ioc.getBean(Target.class);
target.div(1,2);
~~~

### 2.3.4.通知的类型

> AspectJ 中常用的通知有五种类型:
>
> ​	前置通知 @Before
>
> ​	后置通知 @AfterReturning
>
> ​	异常通知 @AfterThrowing
>
> ​	最终通知 @After
>
> ​	环绕通知 @Around

前置通知

~~~java
    @Before("execution(* com.why.spring.service.*.div(..))")
    public void beforeOther(JoinPoint joinPoint){
   	 	/*
   	 	  不光前置通知的方法,可以包含一个 JoinPoint 类型参数,所有的通知方法均可包含该参数
   	 	*/
        System.out.println("连接点的方法定义："+joinPoint.getSignature());
        System.out.println("连接点方法的参数个数："+joinPoint.getArgs().length);
    }

~~~

后置通知

> 由于是目标方法之后执行,所以可以获取到目标方法的返回值
>
> returning属性就是用于指定接收方法返回值的变量名的

~~~java
    @AfterReturning(value = "execution(* com.why.spring.service.*.div(..))",returning = "result")
    public void afterReturning(Object result){
        if (result != null) {
            Integer i = (Integer) result;
            result = i * 100;
        }
        System.out.println("后置通知：在目标方法执行后的功能增强,如事务的处理");
        System.out.println("结果的100倍："+result);
    }

~~~

异常通知

> throwing属性用于指定所发生的异常类对象
>
> 被注解为异常通知的方法可以包含一个参数Throwable,参数名称为throwing指定的名称,表示发生的异常对象

~~~java
	@AfterThrowing(value = "execution(* com.why.spring.service.*.div(..))",throwing = "ex")
    public void afterThrowing(Throwable ex){
        /*
        异常通知可以做什么？
        把异常发生的时间、地点、原因记录到数据库,日志文件等等
        可以在异常发生时,把异常信息通过邮件、短信发送给开发人员
        */
        System.out.println("异常通知：在目标方法抛出异常执行,异常原因："+ex.getMessage());
    }
~~~

最终通知

> 无论目标方法是否抛出异常该增强均会被执行

~~~java
	@After("execution(* com.why.spring.service.*.div(..))")
    public void after(){
        System.out.println("最终通知,总是会执行");
    }
~~~

环绕通知

> 被注解为环绕增强的方法要有返回值Object类型
>
> 方法可以包含一个ProceedingJoinPoint类型的参数,接口ProceedingJoinPoint其有一个proceed()方法,用于执行目标方法
>
> 若目标方法有返回值,则该方法的返回值就是目标方法的返回值 最后,环绕增强方法将其返回值返回

~~~java
    @Around(value = "execution(* com.why.spring.service.*.div(..))")
    public Object around(ProceedingJoinPoint pjp){
        Object object = null;
        try{
            System.out.println("环绕通知>>前置通知");
            //执行目标方法
            object = pjp.proceed();
            System.out.println("环绕通知>>后置通知");
        }catch(Throwable e){
            System.out.println("环绕通知>>异常通知");
        }finally{
            System.out.println("环绕通知>>最终通知");
        }
        return object;
    }
~~~

### 2.3.5.定义切入点

> 当较多的通知增强方法使用相同的 execution 切入点表达式时,编写、维护均较为麻烦。AspectJ 提供了@Pointcut 注解,用于定义 execution 切入点
>
> 表达式。其用法是,将@Pointcut 注解在一个方法之上,以后所有的 execution 的value 属性值均可使用该方法名作为切入点。代表的就是@Pointcut 定
>
> 义的切入点。这个使用@Pointcut 注解的方法一般使用 private 的标识方法,即没有实际作用的方法。

~~~java
    @After("myPoint()")
    public void after(){
        System.out.println("最终通知,总是会执行");
    }
	/*
      @Pointcut:用来定义和管理切面点,简化切入点的定义
    */
    @Pointcut(value = "execution(* com.why.spring.service.*.div(..))")
    public void myPoint(){
        //无需写代码
    }
~~~

### 2.3.6.基于XML的AOP

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.why.spring"/>

    <aop:config>
        <!--配置切面-->
        <aop:aspect ref="myAspect" order="1">
            <!--定义切入点-->
            <aop:pointcut id="pointCutOne" expression="execution(* com.why.spring.service.*.div(..))"/>
            <!--前置通知-->
            <aop:before method="before" pointcut-ref="pointCutOne"/>
            <!--后置通知-->
            <aop:after-returning method="afterReturning" pointcut-ref="pointCutOne" returning="result"/>
            <!--异常通知-->
            <aop:after-throwing method="afterThrowing" pointcut-ref="pointCutOne" throwing="ex"/>
            <!--最终通知-->
            <aop:after method="after" pointcut-ref="pointCutOne"/>
        </aop:aspect>
        
        <aop:aspect ref="myAspect" order="2">
            <!--定义切入点-->
            <aop:pointcut id="pointCutTwo" expression="execution(* com.why.spring.service.*.add(..))"/>
            <!--环绕通知-->
            <aop:around method="around" pointcut-ref="pointCutTwo"/>
        </aop:aspect>
    </aop:config>
</beans>
~~~

## 2.4.声明式事务

### 2.4.1.JDBCTemplate

> JDBCTemplate是Spring对JDBC进行的封装

1.导入依赖

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.1</version>
</dependency>
<!-- Spring 持久化层支持jar包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>5.3.1</version>
</dependency>
<!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
<!-- 数据源 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.31</version>
</dependency>
 <!-- Spring 测试相关 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.1</version>
</dependency>
<!-- junit测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
~~~

2.创建jdbc.properties文件

~~~properties
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.username = root
jdbc.password = 123456
~~~

3.创建Spring配置文件

~~~xml
<!--导入外部属性文件-->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!--配置druid数据源-->
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>

<!--配置JDBCTemplate-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="druidDataSource"/>
</bean>
~~~

4.测试JdbcTemPlate功能

~~~java
/*
指定当前类在Spring的测试环境中执行 
此时可以通过注入的方式直接获取IOC容器中的bean
*/
@RunWith(SpringJUnit4ClassRunner.class)
/*设置Spring测试环境的配置文件*/
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateTest {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void insert() {
        String sql = "insert into user values(null,?,?,?)";
        int result = jdbcTemplate.update(sql, "Alice", "123456", "18374637896");
        System.out.println(result);
    }
    @Test
    public void delete() {
        String sql = "delete from user where id = ?";
        int result = jdbcTemplate.update(sql, 20);
        System.out.println(result);
    }
    @Test
    public void update() {
        String sql = "update user set password = ? where username = ?";
        int result = jdbcTemplate.update(sql, "654321", "Bob");
        System.out.println(result);
    }
    @Test
    public void selectOne() {
        String sql = "select * from user where id = ?";
        User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), 21);
        System.out.println(user);
    }
    @Test
    public void selectList() {
        String sql = "select * from user";
        List<User> userList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<User>(User.class));
        userList.forEach(user -> System.out.println(user));
    }
    @Test
    public void count(){
        String sql = "select count(*) from user";
        Integer result = jdbcTemplate.queryForObject(sql, Integer.class);
        System.out.println(result);
    }
}
~~~

问题解决

`Information:java: javacTask: 源发行版 8 需要目标发行版 1.8`

解决方法: File &rarr; Settings &rarr; Build &rarr; Java Compiler

### 2.4.2.事务使用AOP通知

~~~java
//spring给业务方法在执行时，增加上事务的切面功能
@Around("execution(* 所有的业务类中的方法)")
public Object myAround(ProceedingJoinPoint pjp) {
	try{
		PlatformTransactionManager.beginTransaction();//使用spring的事务管理器,开启事务
		pjp.proceed(); //执行目标方法
		PlatformTransactionManager.commit();//业务方法正常执行,提交事务
	}catch(Exception e){
		PlatformTransactionManager.rollback();//业务方法正常执行,回滚事务
	}
}
~~~



### 2.4.3.使用声明式事务

> 事务管理器是PlatformTransactionManager接口对象,其主要用于完成事务的提交、回滚，及获取事务的状态信息
>
> PlatformTransactionManager接口有两个常用的实现类：
>
> ​	DataSourceTransactionManager: 使用JDBC或 MyBatis进行数据库操作时使用
>
> ​	HibernateTransactionManager: 使用Hibernate进行持久化数据时使用
> 

1.添加事务配置

~~~xml
<!--配置事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="druidDataSource"/>
</bean>

<!--
  开启事务的注解驱动
  通过注解@Transactional所标识的方法或标识的类中所有的方法，都会被事务管理器管理事务
  若事务管理器bean的id是transactionManager 则transaction-manager可以省略
  因为 transaction-manager属性的默认值是transactionManager
  导入的名称空间选http://www.springframework.org/schema/tx那个
-->
<tx:annotation-driven transaction-manager="transactionManager"/>
~~~

2.添加@Transactional注解

> @Transactional一般都是添加在业务层的代码上,这里只是演示效果
>
> @Transactional标识在方法上,只会影响该方法 
>
> @Transactional标识的类上,则会影响类中所有的方法

~~~java
@Test
@Transactional
public void testTransaction() {
    String insertSql = "insert into user values(null,?,?,?)";
    String deleteSql = "delete from t_emp where emp_id = ?";
    jdbcTemplate.update(deleteSql, 9);
    jdbcTemplate.update(insertSql, "Alice", "123456", "18374637896123");
}
~~~

### 2.4.4@Transactional注解常见属性

> readOnly: 只读
>
> timeout: 超时时间
>
> isolation: 事务的隔离级别
>
> propagation: 事务的传播级别

~~~java
@Transactional(readOnly = true,
               timeout = 10,
               isolation =  Isolation.DEFAULT,
               propagation = Propagation.REQUIRED)
~~~

回滚策略

> 声明式事务默认只针对运行时异常回滚,编译时异常不回滚
>
> rollbackFor属性:需要设置一个Class类型的对象 
>
> rollbackForClassName属性:需要设置一个字符串类型的全类名 
>
> noRollbackFor属性:需要设置一个Class类型的对象 
>
> noRollbackForClassName属性:需要设置一个字符串类型的全类名

~~~java
@Transactional(rollbackFor = ArithmeticException.class)//遇到算数异常时回滚
@Transactional(noRollbackFor = ArithmeticException.class)//遇到算数异常时不回滚
@Transactional(rollbackForClassName = "java.lang.ArithmeticException")//遇到算数异常时回滚
@Transactional( noRollbackForClassName = "java.lang.ArithmeticException")//遇到算数异常时不回滚
~~~

事务隔离级别

> 读未提交：READ UNCOMMITTED
>
> 读已提交：READ COMMITTED
>
> 可重复读：REPEATABLE READ 
>
> 串行化：SERIALIZABLE

~~~java
@Transactional(isolation = Isolation.DEFAULT)//使用数据库默认的隔离级别
@Transactional(isolation = Isolation.READ_UNCOMMITTED)//读未提交
@Transactional(isolation = Isolation.READ_COMMITTED)//读已提交
@Transactional(isolation = Isolation.REPEATABLE_READ)//可重复读
@Transactional(isolation = Isolation.SERIALIZABLE)//串行化
~~~

事务传播行为

> Spring定义了七个事务传播行为常量
>
> ​	PROPAGATION_REQUIRED 指定的方法必须在事务内执行,若当前存在事务就加入到当前事务中,若当前没有事务则创建一个新事务 （默认）
>
> ​	PROPAGATION_REQUIRES_NEW 总是新建一个事务,若当前存在事务就将当前事务挂起,直到新事务执行完毕
>
> ​	PROPAGATION_SUPPORTS 指定的方法支持当前事务,但若当前没有事务,也可以以非事务方式执行
>
> 
>
> ​	PROPAGATION_MANDATORY (不常用)
>
> ​	PROPAGATION_NESTED (不常用)
>
> ​	PROPAGATION_NEVER (不常用)
>
> ​	PROPAGATION_NOT_SUPPORTED (不常用)

~~~java
@Transactional(propagation = Propagation.REQUIRED)
@Transactional(propagation = Propagation.REQUIRES_NEW)
@Transactional(propagation = Propagation.SUPPORTS)
~~~



<hr>

# 3.SpringMVC

## 3.1.SpringMVC入门

<h4>开发环境</h4>

> 构建工具：maven3.5.4 
>
> 服务器：tomcat8.5 
>
> Spring版本：5.3.1

<h4>1.创建Manven web工程</h4>

![image-20220802193612181](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220802193612181.png)

<h4>2.创建java和resources文件夹</h4>

> java → 右键 → Mark Directory as → Sources Root
> resources → 右键 → Mark Directory as → Resources Root

<h4>3.导入依赖</h4>

~~~xml
<dependencies>
  <!-- SpringMVC -->
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.1</version>
  </dependency>
  <!-- 日志 -->
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
  </dependency>
  <!-- ServletAPI -->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
  </dependency>
</dependencies>

~~~

<h4>4.配置web.xml文件</h4>

~~~xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <!-- 配置SpringMVC的前端控制器(中央调度器),对浏览器发送的请求统一进行处理 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
        <init-param>
            <!-- contextConfigLocation为固定值 -->
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <!--作为框架的核心组件，在启动过程中有大量的初始化操作要做而这些操作放在第一次请求时才执行会严重影响访问速度,
		   因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--
          设置springMVC的核心控制器所能处理的请求的请求路径
          /所匹配的请求可以是/login或.html或.js或.css方式的请求路径,但是/不能匹配.jsp请求路径的请求
        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
~~~

<h4>5.创建请求控制器</h4>

~~~java
/**
 * 表示当前类为处理器
 */
@Controller
public class MyController { 
    /**
     * 请求映射：把指定的请求交给方法处理
     */
    @RequestMapping("/hello")
    public String hello(){
        System.out.println("hello springMVC");
        return "hello"
}
~~~

<h4>6.创建SpringMVC配置文件</h4>

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 声明组件扫描器 -->
    <context:component-scan base-package="com.why"/>

    <!-- 配置视图解析器:帮助我们处理视图的路径和扩展名 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 前缀：表示视图所在的路径 -->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!-- 后缀：表示视图文件的扩展名 -->
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
~~~

<h4>7.在/WEB-INF/jsp 目录下创建hello.jsp文件</h4>

<h4>8.项目部署并在浏览器中访问</h4>

![image-20220802200920642](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220802200920642.png)

## 3.2.RequestMapping

> @RequestMapping注解使用位置
>
> ​	放在类上: 设置映射请求的请求路径的初始信息
>
> ​	放在方法上: 设置映射请求请求路径的具体信息
>
> @RequestMapping注解常用属性
>
> ​	value: 通过请求的请求地址匹配请求映射
>
> ​	method: 通过请求的请求方式匹配请求映射
>
> ​	params: 通过请求的请求参数匹配请求映射（了解）
>
> ​			"param"：要求请求映射所匹配的请求必须携带param请求参数 
>
> ​			"!param"：要求请求映射所匹配的请求必须不能携带param请求参数 
>
> ​			"param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value 
>
> ​			"param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value
>
> ​	headers: 通过请求的请求头信息匹配请求映射（了解）
>
> ​			"header"：要求请求映射所匹配的请求必须携带header请求头信息 
>
> ​			"!header"：要求请求映射所匹配的请求必须不能携带header请求头信息 
>
> ​			"header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value 
>
> ​			"header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

~~~java
/**
 * 当value是数组时表示该请求映射能够匹配多个请求地址所对应的请求
 * 当method是数组时表示该请求映射能够匹配多种请求方式的请求
 */
@RequestMapping(value = {"/hello","/nihao"},
                method = {Reques,tMethod.GET, RequestMethod.POST},
                params = {"username","password!=123456"},
                headers = "data")
~~~

springMVC支持ant风格的路径

> ？:表示任意的单个字符 
>
> \* :表示任意的0个或多个字符 
>
> \**:表示任意层数的任意目录 

~~~java
/**
 * springMVC支持ant风格的路径
 */
@RequestMapping("/a?c")
@RequestMapping("/a*c")
@RequestMapping("/**/abc")
~~~

## 3.3.处理器方法获取参数	

> 处理器方法可以包含以下四类参数,这些参数会在系统调用时由系统自动赋值。
>
> - HttpServletRequest
> - HttpServletResponse
> - HttpSession
> - 请求中所携带的请求参数

### 3.3.1.通过ServetAPI获取

~~~java
@RequestMapping("/testParam")
public String testParam(HttpServletRequest request){
	String username = request.getParameter("username");
	String password = request.getParameter("password");
	System.out.println("username:"+username+",password:"+password);
	return "success";
}
~~~

### 3.3.2.逐个接收参数

> 只要保证请求参数名与控制器方法的参数名相同即可(顺序无所谓)

~~~java
/**
 * 请求路径： .../testParam?username=zs&password=123456 
 */
@RequestMapping("/testParam")
public String testParam(String username, String password){
	System.out.println("username:"+username+",password:"+password);
	return "success";
}

~~~

### 3.3.3.通过@RequestParam注解

> @RequestParam注解用于解决请求中的参数名和方法参数不同的情况
>
> @RequestParam注解的使用位置：方法参数前面
>
> @RequestParam注解的属性
>
> ​	value: 指定为形参赋值的请求参数的参数名
>
> ​	required: 设置是否必须传输此请求参数,默认为true
>
> ​	defaultValue: 设置当请求参数中没有值或值为""时的默认值
>
> 
>
> 与@RequestParam注解功能相似的注解：
>
> ​	@RequestHeader：将请求头信息和控制器方法的形参创建映射关系
>
> ​	@CookieValue：是将cookie数据和控制器方法的形参创建映射关系

~~~java
/**
 * 请求路径： .../testParam?username=zs&password=123456 
 */
@RequestMapping("/testParam")
public String testParam(@RequestParam("username")String name, 
                        @RequestParam("password")String pwd){
	System.out.println("name:"+name+",pwd:"+pwd);
	return "success";
}
~~~

### 3.3.4.通过对象获取

> 使用对象接收参数时需要保证对象的属性和请求参数相同

~~~java
public class User {
    // 属性名必须和请求参数名相同
    private String username; 
    private Integer password;
    // setter and getter 这些方法必须写
}
~~~

~~~java
/**
 * 请求路径： .../testpojo?username=zs&password=123456 
 */
@RequestMapping("/testpojo")
public String testPOJO(User user){
	System.out.println(user);
	return "success";
}

~~~

## 3.4.中文参数乱码问题

> 对于前面所接收的请求参数,若含有中文,则会出现中文乱码问题
>
> ​	在tomcat7中get和post请求都会出现乱码问题
>
> ​	在tomcat8中只有post请求会出现乱码问题
>
> 解决乱码问题
>
> ​	1.对于get请求的乱码问题可以在tomcat的web.xml文件的Connector标签中添加`URIEncoding = "UTF-8"`属性
>
> ​	2.对于post请求的乱码问题Spring给出了专门的字符集过滤器CharacterEncodingFilter类

~~~xml
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!--给过滤器属性赋值-->
    <init-param>
        <!--项目使用的字符编码-->
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <init-param>
        <!--强制请求（request）对象使用encoding的编码方式-->
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <!--强制应答（response）对象使用encoding的编码方式-->
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <!--强制所有请求,先经过过滤器处理-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
~~~


## 3.5.处理器方法的返回值

> 使用@Controller 注解的处理器的处理器方法,其返回值常用的有四种类型：
>
> - ModelAndView
> - String
> - 无返回值 void
> - 返回自定义类型对象 Object

<h3>ModelAndView</h3>

若处理器方法处理完后,需要跳转到其它资源,且又要在跳转的资源间传递数据,此时处理器方法返回 ModelAndView 比较好

~~~java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
    
	ModelAndView mv = new ModelAndView();
	//向请求域共享数据
	mv.addObject("testScope", "hello,ModelAndView");
	//设置视图,实现页面跳转
	mv.setViewName("success");
	return mv;
}
~~~

<h3>String</h3>

处理器方法返回的字符串可以指定逻辑视图名,通过视图解析器解析可以将其转换为物理视图地址

~~~java
@RequestMapping(value ="/testString")
public String testString()){
    //使用完整视图路径就不能使用视图解析器
    //return "/WEB-INF/view/show.jsp";
    //逻辑名称,需要配置视图解析器
	return "show";
}
~~~

<h3>Void</h3>

> 没有数据和视图,可以使用HttpServletResponse对象输出数据。
>
> 若处理器对请求处理后,无需跳转到其它任何资源,此时可以让处理器方法返回void,如AJAX请求的响应

<h3>Object</h3>

> 处理器方法也可以返回Object对象用来响应ajax请求
>
> 这个Object可以是Integer,String,自定义对象,Map,List等
>
> 但返回的对象不是作为逻辑视图出现的,而是作为直接在页面显示的数据出现的
>
> 返回对象,需要使用@ResponseBody注解,将转换后的JSON数据放入到响应体中

~~~java
@RequestMapping("/userJson.do")
//这个注解的作用就是把user转换后的json通过HttpServletResponse对象输出给浏览器
@ResponseBody
public User doAjaxJson(String username,Integer userage){
    User user = new User();
    user.setUsername(username);
    user.setUserage(userage+10);
    return user;
}
~~~

## 3.6.域对象共享数据

<h3>request域</h3>

1.使用servletAPI共享数据

~~~java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
	request.setAttribute("testScope", "hello,servletAPI");
	return "success";
}
~~~

2.使用ModelAndView共享数据

~~~java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
    
	ModelAndView mv = new ModelAndView();
	//向请求域共享数据
	mv.addObject("testScope", "hello,ModelAndView");
	//设置视图,实现页面跳转
	mv.setViewName("success");
	return mv;
}
~~~

3.使用Model共享数据

> 与Model相似的还有map、ModelMap
>
> Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap 类型的

~~~java
@RequestMapping("/testModel")
public String testModel(Model model){
	model.addAttribute("testScope", "hello,Model");
	return "success";
}

@RequestMapping("/testMap")
public String testMap(Map<String, Object> map){
	map.put("testScope", "hello,Map");
	return "success";
}

@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
	modelMap.addAttribute("testScope", "hello,ModelMap");
	return "success";
}
~~~

<h3>session域</h3>

~~~java
@RequestMapping("/testSession")
public String testSession(HttpSession session){
	session.setAttribute("testSessionScope", "hello,session");
	return "success";
}
~~~

<h3>application域</h3>

~~~java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
	ServletContext application = session.getServletContext();
	application.setAttribute("testApplicationScope", "hello,application");
	return "success";
}
~~~

## 3.7.静态资源处理

<h3>方式1</h3>

> 在springmvc的配置文件加入 mvc:default-servlet-handler标签
>
> ​	springmvc框架会在项目运行时,加入DefaultServletHttpRequestHandler对象,让这个对象处理静态资源的访问。它会像一个检查员,
>
> ​	对进入DispatcherServlet 的 URL 进行筛查,如果发现是静态资源的请求,就将该请求转由 Web 应用服务器默认的 Servlet 处理。

~~~xml
<!--声明注解驱动
    default-servlet-handler和@RequestMapping使用有冲突-->
<mvc:annotation-driven />

<!--
    创建DefaultServletHttpRequestHandler处理静态资源。
    DefaultServletHttpRequestHandler把接收的静态资源的地址,转发给了tomcat的default
    优点：
      解决方式简单
    缺点：
      依赖tomcat服务器提供的能力
-->
<mvc:default-servlet-handler />
~~~

<h3>方式2</h3>

> 在 Spring3.0 版本后,Spring定义了专门用于处理静态资源访问请求的处理器ResourceHttpRequestHandler,并且添加了\<mvc:resources/>标签,
>
> 框架会创建ResourceHttpRequestHandler控制器对象, 使用这个对象处理静态资源的访问。 
>
> 这种方式不依赖tomcat服务器(推荐使用)

~~~xml
    <!--
        mapping: 访问静态资源的uri地址,可以使用通配符**,表示任意的目录和目录和资源名称
        location: 静态资源在项目中的位置,不要使用/WEB-INF目录 
	-->
    <mvc:resources mapping="/image/**" location="/image/" />
    <mvc:resources mapping="/js/**" location="/js/" />

    <!--一句话设置静态资源-->
    <!--<mvc:resources mapping="/static/**" location="/static/" />-->
~~~

## 3.8.转发与重定向

> SpringMVC 框架把原来 Servlet 中的请求转发和重定向操作进行了封装。现在可以使用简单的方式实现转发和重定向。
>
> - forward:表示转发,实现request.getRequestDispatcher(“xx.jsp”).forward()
>
> - redirect:表示重定向,实现 response.sendRedirect(“xxx.jsp”)

~~~java
mv.setViewName("forward:/hello.jsp");
mv.setViewName("redirect:/other.jsp");
~~~

<h3>springMVC视图

</h3>

> SpringMVC中的视图是View接口,视图的作用渲染数据,将模型Model中的数据展示给用户
>
> 当工程引入jstl的依赖,转发视图会自动转换为JstlView 若使用的视图技术为Thymeleaf,在SpringMVC的配置文件中配置了Thymeleaf的视图解析器,由此
>
> 视图解析器解析之后所得到的是ThymeleafView

Jsp视图解析器

~~~xml
<!-- 配置视图解析器:帮助我们处理视图的路径和扩展名 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!-- 前缀：表示视图所在的路径 -->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!-- 后缀：表示视图文件的扩展名 -->
    <property name="suffix" value=".jsp"/>
</bean>
~~~

Thymeleaf视图解析器

~~~xml
<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
	<property name="order" value="1"/>
	<property name="characterEncoding" value="UTF-8"/>
	<property name="templateEngine">
		<bean class="org.thymeleaf.spring5.SpringTemplateEngine">
			<property name="templateResolver">
				<bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
					<!-- 视图前缀 -->
					<property name="prefix" value="/WEB-INF/templates/"/>
					<!-- 视图后缀 -->
					<property name="suffix" value=".html"/>
					<property name="templateMode" value="HTML5"/>
					<property name="characterEncoding" value="UTF-8" />
				</bean>
			</property>
		</bean>
	</property>
</bean>
~~~

<h3>视图控制器view-controller</h3>
> 当控制器方法中,仅仅用来实现页面跳转,即只需要设置视图名称时,可以将处理器方法使用view-controller标签进行表示
>
> 注意：
>
> ​	当SpringMVC中设置任何一个view-controller时,其他控制器中的请求映射将全部失效
>
> ​    此时需要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签`<mvc:annotation-driven/>`


~~~xml
<!--
  path：设置处理的请求地址
  view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/hello" view-name="hello"></mvc:view-controller>

~~~

## 3.9.处理AJAX请求

### 3.9.1.@RequestBody

> @RequestBody可以获取请求体信息
>
> @RequestBody获取json格式的请求参数的条件：
>
> ​	1.导入jackson的依赖
>
> ​	2.SpringMVC的配置文件中设置开启mvc的注解驱动
>
> ​	3.使用@RequestBody注解

jackson依赖

~~~xml
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.12.1</version>
</dependency>
~~~



~~~js
axios.post("/SpringMVC/test/RequestBody/json",{username:"admin",password:"123456"})
     .then(response=>{
		console.log(response.data);
	 });
~~~

~~~java
//将json格式的数据转换为map集合
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody Map<String, Object> map){
	System.out.println(map);//{username=admin, password=123456}
}

//将json格式的数据转换为实体类对象
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody User user){
	System.out.println(user);//User{id=null, username='admin', password='123456', age=null,gender='null'}
}
~~~

### 3.9.2.@ResponseBody

> @ResponseBody可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

~~~java
@RequestMapping("/testResponseBody")
public String testResponseBody(){
	//此时会跳转到逻辑视图success所对应的页面
	return "success";
}

@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
	//此时响应浏览器数据success
	return "success";
}
~~~

> @ResponseBody响应浏览器json数据的条件
>
> ​	1.导入jackson的依赖
>
> ​	2.SpringMVC的配置文件中设置开启mvc的注解驱动
>
> ​	3.使用@ResponseBody

~~~java
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public List<User> testResponseBody(){
	User user1 = new User(1001,"admin1","123456",23,"男");
	User user2 = new User(1002,"admin2","123456",23,"男");
	User user3 = new User(1003,"admin3","123456",23,"男");
	List<User> list = Arrays.asList(user1, user2, user3);
	return list;
}
~~~

### 3.9.3.@RestController

> @RestController注解是springMVC提供的一个复合注解
>
> 标识在控制器的类上
>
> 相当于为类添加了@Controller注解并且为其中的每个方法添加了@ResponseBody注解

## 3.10.文件上传与下载

<h4>文件下载</h4>

~~~java
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
	//获取ServletContext对象
	ServletContext servletContext = session.getServletContext();
	//获取服务器中文件的真实路径
	String realPath = servletContext.getRealPath("/static/img/1.jpg");
	//创建输入流
	InputStream is = new FileInputStream(realPath);
	//创建字节数组
	byte[] bytes = new byte[is.available()];
	//将流读到字节数组中
	is.read(bytes);
	//创建HttpHeaders对象设置响应头信息
	MultiValueMap<String, String> headers = new HttpHeaders();
	//设置要下载方式以及下载文件的名字
	headers.add("Content-Disposition", "attachment;filename=1.jpg");
	//设置响应状态码
	HttpStatus statusCode = HttpStatus.OK;
	//创建ResponseEntity对象
	ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers,statusCode);
	//关闭输入流
	is.close();
	return responseEntity;
}

~~~

<h4>文件上传</h4>

> 文件上传要求form表单的请求方式必须为post并且添加属性enctype="multipart/form-data"
>
> SpringMVC中将上传的文件封装到MultipartFile对象中

1.先导入文件上传相关依赖

~~~xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.1</version>
</dependency>
~~~

2.在springMVC配置文件中配置文件解析器

~~~xml
<!--
  必须通过文件解析器的解析才能将文件转换为MultipartFile对象
  id必须为multipartResolver
-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
~~~

3.控制器方法

~~~java
@RequestMapping("/testUp")
public String testUp(MultipartFile photo, HttpSession session) throws IOException {
	//获取上传的文件的文件名
	String fileName = photo.getOriginalFilename();
	//处理文件重名问题
	String hzName = fileName.substring(fileName.lastIndexOf("."));
	fileName = UUID.randomUUID().toString() + hzName;
	//获取服务器中photo目录的路径
	ServletContext servletContext = session.getServletContext();
	String photoPath = servletContext.getRealPath("photo");
	File file = new File(photoPath);
	if(!file.exists()){
		file.mkdir();
	}
	String finalPath = photoPath + File.separator + fileName;
	//实现上传功能
	photo.transferTo(new File(finalPath));
	return "success";
}
~~~

## 3.11.异常处理器

> SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口HandlerExceptionResolver
>
> HandlerExceptionResolver接口的实现类有：
>
> ​	DefaultHandlerExceptionResolver
>
> ​	SimpleMappingExceptionResolver

<h3>基于XML的异常处理</h3>

~~~xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
	<property name="exceptionMappings">
		<props>
			<!--
		  	  key:处理器方法执行过程中出现的异常的全类名
		  	  value:表示若出现指定异常时,跳转到指定页面
			-->
			<prop key="java.lang.ArithmeticException">error</prop>
		</props>
	</property>
	<!--
	  exceptionAttribute属性设置一个属性名,将出现的异常信息在请求域中进行共享
	-->
	<property name="exceptionAttribute" value="ex"></property>
</bean>
~~~

<h3>基于注解的异常处理</h3>

> 异常相关的注解：
>
> - @ExceptionHandler:放在方法的上面,表示此方法可以处理某个类型的异常
> - @ControllerAdvice: 放在类的上面, 表示这个类中有异常的处理方法

~~~java
@ControllerAdvice
public class GlobalExceptionResolver{
    //定义异常处理方法
    @ExceptionHandler(value = ArithmeticException.class)
    public ModelAndView doException(Exception ex){
        ModelAndView mv = new ModelAndView();
        mv.addObject("tips","处理异常");
        mv.addObject("ex",ex);
        mv.setViewName("error");
        return mv;
    }
    
    @ExceptionHandler(value = NullPointerException.class)
    public ModelAndView doNullxception(Exception ex){...}
    
    //处理上面两个异常以外的其他异常
 	@ExceptionHandler 
    public ModelAndView doOtherException(Exception ex){...}
}
~~~

## 3.12.拦截器

> SpringMVC中的Interceptor拦截器主要作用是拦截指定的用户请求,并进行相应的预处理与后处理。
>
> SpringMVC中的拦截器有三个抽象方法:
>
> ​	preHandle: 控制器方法执行之前执行preHandle(),其boolean类型的返回值表示是否拦截或放行
>
> ​	postHandle: 控制器方法执行之后执行postHandle() 
>
> ​	afterCompletion: 处理完视图和模型数据,渲染视图完毕之后执行afterCompletion()

1.定义拦截器

~~~java
public class MyInterceptor implements HandlerInterceptor {
    /**
     * 预先处理请求的方法
     * 特点：
     *  1. 预处理方法他的执行时间：在控制器方法之前先执行的
     *  2. 可以对请求做处理,可以做登录的检查,权限的判断,统计数据等等
     *  3. 决定请求是否执行,true表示放行,false表示拦截该请求
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("执行MyInterceptor拦截器预先处理请求的方法");
        return true;
    }

    /**
     * 后处理方法
     * 特点：
     *  1. 在控制器方法之后执行的
     *  2. 能获取到控制器方法的执行结果,可以修改原来的执行结果,可以修改数据,也可以修改视图
     *  3. 可以做对请求的二次处理
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("执行MyInterceptor拦截器后处理方法");
    }

    /**
     * 最后执行的方法
     * 特点：
     *  1.在请求处理完成后执行的,请求处理完成的标志是 视图处理完成,对视图执行forward操作后
     *  2.可以做程序最后要做的工作, 释放内存, 清理临时变量
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("执行MyInterceptor拦截器最后执行的方法");
    }
}
~~~

2.声明拦截器

~~~xml
<!--声明拦截器-->
<mvc:interceptors>
    <!--声明第一个拦截器-->
    <mvc:interceptor>
        <!--指定拦截器的拦截地址-->
        <mvc:mapping path="/**"/>
        <!--指定使用的拦截器-->
        <bean class="com.why.interceptor.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
~~~

<h3>多个拦截器</h3>

> 当有多个拦截器时形成拦截器链,拦截器链的执行顺序与其注册顺序一致
>
> 当某一个拦截器的preHandle()方法返回true并被执行到时,会向一个专门的方法栈中放入该拦截器的afterCompletion()方法
>
> 也就是说当某个拦截器的preHandle()返回true时它的afterCompletion()一定会被执行

1）两个拦截器,第一个preHandle=true, 第二个拦截器preHandle=true

~~~java
=====MyInterceptor111111拦截器的preHandle====
=====MyInterceptor222222拦截器的preHandle====
执行了MyController的doSome方法
=====MyInterceptor222222拦截器的postHandle====
=====MyInterceptor111111拦截器的postHandle====
=====MyInterceptor222222拦截器的afterCompletion====
=====MyInterceptor111111拦截器的afterCompletion====
~~~

2）两个拦截器,第一个preHandle=true,第二个拦截器preHandle=false

```
=====MyInterceptor111111拦截器的preHandle====
=====MyInterceptor222222拦截器的preHandle====
=====MyInterceptor111111拦截器的afterCompletion====
```

3）两个拦截器,第一个preHandle=false,第二个拦截器preHandle=true|false

```
=====MyInterceptor111111拦截器的preHandle====
```

总结

1.若每个拦截器的preHandle()都返回true 

​	preHandle()会按照配置的顺序执行,而postHandle()和afterCompletion()会按照配置的反序执行 

2.若某个拦截器的preHandle()返回了false 

​	preHandle()返回false和它之前的拦截器的preHandle()都会执行,postHandle()都不执行

​	返回false 的拦截器之前的拦截器的afterCompletion()会执行

<h3>拦截器和过滤器的区别</h3>

> 1.拦截器是springmvc框架中的对象,过滤器是servlet中的对象
>
> 2.拦截器对象是框架容器创建的, 过滤器对象是tomcat创建的对象
>
> 3.拦截器是侧重对请求做判断的、处理的、可以截断请求,过滤器是侧重对request,response对象的属性参数设置值的
>
> 4.拦截器的他执行时间有三个:控制器方法之前、之后、请求完成后,过滤器是在请求之前和响应之前
>
> 5.拦截器是拦截对controller动态资源请求的,过滤器可以过滤所有请求动态的和静态的
>
> 6.拦截器和过滤器执行时间不同的, 先执行的过滤器,后面是中央调度器,后面才是拦截器,再后面是控制器方法
>
> 7.拦截器是基于java的反射机制的,而过滤器是基于函数回调

![image-20220803221913479](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220803221913479.png)

## 3.13.配置类

### WebInit配置类代替web.xml

> 在Servlet3.0环境中,容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类,如果找到的话就用它来配置Servlet容器
>
> Spring提供了这个接口的实现名为SpringServletContainerInitializer
>
> 这个类反过来又会查找实现WebApplicationInitializer的类并将配置的任务交给它们来完成
>
> Spring3.2引入了一个便利的WebApplicationInitializer基础实现名为`AbstractAnnotationConfigDispatcherServletInitializer`

~~~java
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
	/*指定spring的配置类*/
	@Override
	protected Class<?>[] getRootConfigClasses() {
		return new Class[]{SpringConfig.class};
	}
    
	/*指定SpringMVC的配置类*/
	@Override
	protected Class<?>[] getServletConfigClasses() {
		return new Class[]{WebConfig.class};
	}
    
	/*指定DispatcherServlet的映射规则，即url-pattern*/
	@Override
	protected String[] getServletMappings() {
		return new String[]{"/"};
	}
    
	/*添加过滤器*/
	@Override
	protected Filter[] getServletFilters() {
		CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
		encodingFilter.setEncoding("UTF-8");
		encodingFilter.setForceRequestEncoding(true);
		HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
		return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
	}
}

~~~

### SpringConfig配置类代替spring的配置文件

~~~java
@Configuration
public class SpringConfig {
//ssm整合之后spring的配置信息写在此类中
}
~~~

### WebConfig配置类替SpringMVC.xml

~~~java
@Configuration
//扫描组件 相当于<context:component-scan base-package="com.why"/>
@ComponentScan("con.why")
//开启MVC注解驱动 相当于<mvc:annotation-driven/>
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
	//使用默认的servlet处理静态资源
    //<mvc:default-servlet-handler />
	@Override
	public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
		configurer.enable();
	}
	//配置文件上传解析器
	@Bean
	public CommonsMultipartResolver multipartResolver(){
		return new CommonsMultipartResolver();
	}
	//配置拦截器
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		FirstInterceptor firstInterceptor = new FirstInterceptor();
		registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
	}
	//配置视图控制
    //<mvc:view-controller path="/" view-name="index"/>
	@Override
	public void addViewControllers(ViewControllerRegistry registry) {
		registry.addViewController("/").setViewName("index");
	}
	//配置异常映射
	@Override
	public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
		SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
		Properties prop = new Properties();
		prop.setProperty("java.lang.ArithmeticException", "error");
		//设置异常映射
		exceptionResolver.setExceptionMappings(prop);
		//设置共享异常信息的键
		exceptionResolver.setExceptionAttribute("ex");
		resolvers.add(exceptionResolver);
	}
	//配置生成模板解析器
	@Bean
	public ITemplateResolver templateResolver() {
		WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
		// ServletContextTemplateResolver需要一个ServletContext作为构造参数,可通过WebApplicationContext 的方法获得
		ServletContextTemplateResolver templateResolver = new 
            		ServletContextTemplateResolver(webApplicationContext.getServletContext());
		templateResolver.setPrefix("/WEB-INF/templates/");
		templateResolver.setSuffix(".html");
		templateResolver.setCharacterEncoding("UTF-8");
		templateResolver.setTemplateMode(TemplateMode.HTML);
		return templateResolver;
	}
	//生成模板引擎并为模板引擎注入模板解析器
	@Bean
	public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
		SpringTemplateEngine templateEngine = new SpringTemplateEngine();
		templateEngine.setTemplateResolver(templateResolver);
		return templateEngine;
	}
	//生成视图解析器并未解析器注入模板引擎
	@Bean
	public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
		ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
		viewResolver.setCharacterEncoding("UTF-8");
		viewResolver.setTemplateEngine(templateEngine);
		return viewResolver;
	}
}
~~~

## 3.14.springMVC执行流程

> 1.浏览器提交请求到中央调度器
>
> 2.中央调度器直接将请求转给处理器映射器
>
> 3.处理器映射器会根据请求,找到处理该请求的处理器,并将其封装为处理器执行链后返回给中央调度器
>
> 4.中央调度器根据处理器执行链中的处理器,找到能够执行该处理器的处理器适配器
>
> 5.处理器适配器调用执行处理器
>
> 6.处理器将处理结果及要跳转的视图封装到一个对象 ModelAndView 中,并将其返回给处理器适配器
>
> 7.处理器适配器直接将结果返回给中央调度器
>
> 8.中央调度器调用视图解析器,将ModelAndView中的视图名称封装为视图对象
>
> 9.视图解析器将封装了的视图对象返回给中央调度器
>
> 10.中央调度器调用视图对象,让其自己进行渲染,即进行数据填充,形成响应对象
>
> 11.中央调度器响应浏览器



![image-20220803224152274](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220803224152274.png)



<hr>

# 4.SSM整合

## 4.1.ContextLoaderListener

> springMVC的ioc容器是在dispatchServlet进行初始化的过程中创建的,当配置了\<load-on-startup>1\</load-on-startup>表示dispatchServlet在服
>
> 务器启动的时候初始化。而springMVC的控制层组件依赖于spring业务层组件,spring的自动装配是在获取ioc容器的时候执行的.所以spring的ioc容器创建
>
> 时间应该配置在springMVC的ioc容器的创建时间之前,也就是在服务器启动的时候
>
> ​	Spring提供了监听器ContextLoaderListener,实现ServletContextListener接口
>
> ​	可监听ServletContext的状态,在web服务器的启时读取Spring的配置文件并创建Spring的IOC容器
>
> 服务器三大组件的执行顺序
>
> ​	监听器 &rarr; 过滤器 &rarr; servlet

~~~xml
<listener>
	<!--
  	  配置Spring的监听器，在服务器启动时加载Spring的配置文件
  	  Spring配置文件默认位置和名称：/WEB-INF/applicationContext.xml可通过上下文参数自定义Spring配置文件的位置和名称
	-->
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--自定义Spring配置文件的位置和名称-->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:spring.xml</param-value>
</context-param>
~~~



## 4.2.准备工作

1.准备表

~~~sql
CREATE TABLE `t_emp` (
`emp_id` int(11) NOT NULL AUTO_INCREMENT,
`emp_name` varchar(20) DEFAULT NULL,
`emp_age` int(11) DEFAULT NULL,
`emp_sex` char(1) DEFAULT NULL,
PRIMARY KEY (`emp_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
~~~

1.创建maven web工程

2.创建资源文件夹（java和resources）

3.导入依赖

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.why.ssm</groupId>
    <artifactId>09-ssm</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <spring.version>5.3.1</spring.version>
    </properties>

    <dependencies>
        <!--spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--springMVC-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- Mybatis核心 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <!--mybatis和spring的整合包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
        <!-- 连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.9</version>
        </dependency>
        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
        <!--分页-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.2.0</version>
        </dependency>
        <!-- log4j日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        <!-- ServletAPI -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!--jackson-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.12.1</version>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <!-- Spring5和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.12.RELEASE</version>
        </dependency>
    </dependencies>
</project>
~~~



## 4.3.配置web.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 配置Spring的编码过滤器 -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <!-- 配置SpringMVC的前端控制器 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 设置SpringMVC的配置文件的位置和名称 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!-- 设置Spring的配置文件的位置和名称 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!-- 配置Spring的监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
~~~



## 4.4.配置springMVC.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.why.ssm.controller"/>
    
    <!--配置视图解析器-->
    <bean id="viewResolver"
          class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
    <!-- 配置访问首页的视图控制 -->
    <mvc:view-controller path="/" view-name="index"/>
    <!-- 配置默认的servlet处理静态资源 -->
    <mvc:default-servlet-handler />
    <!-- 开启MVC的注解驱动 -->
    <mvc:annotation-driven />
</beans>
~~~



## 4.5.搭建MyBatis环境

<h3>创建jdbc.properties</h3>

~~~properties
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.username = root
jdbc.password = why0417
~~~

<h3>创建Mybatis核心配置文件</h3>

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!-- 引入properties文件 -->
    <!--<properties resource="jdbc.properties"/>-->

    <settings>
        <!--将下划线映射为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

    <!-- 别名 -->
    <typeAliases>
        <package name="com.why.ssm.pojo"/>
    </typeAliases>

    <plugins>
        <!--配置分页插件-->
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>

    <!--    
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
    -->

    <!-- 引入映射文件 -->
    <mappers>
        <package name="com.why.mapper"/>
    </mappers>
</configuration>
~~~

<h3>创建Mapper接口和映射文件</h3>
~~~java
/*
 Mapper接口
*/
public interface EmpMapper {
    List<Emp> getAllEmp();
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.why.ssm.mapper.EmpMapper">

    <!--List<Emp> getAllEmp();-->
    <select id="getAllEmp" resultType="Emp">
        select * from t_emp
    </select>
</mapper>
~~~

<h3>创建日志文件log4j.xml</h3>

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}%m (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>

~~~



## 4.6.创建spring配置文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描组件-->
    <context:component-scan base-package="com.why.ssm">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!-- 引入jdbc.properties -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!-- 配置Druid数据源 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!-- 配置用于创建SqlSessionFactory的工厂bean -->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 设置MyBatis配置文件的路径（可以不设置） -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!-- 设置数据源（如果在这设置了数据源那么在mybatis核心配置文件中就不需要设置数据源了） -->
        <property name="dataSource" ref="dataSource"></property>
        <!-- 设置类型别名所对应的包 -->
        <!--<property name="typeAliasesPackage" value="com.why.ssm.pojo"/>-->
        <!--设置映射文件的路径-->
        <!--<property name="mapperLocations" value="classpath:mapper/*.xml"/>-->
    </bean>
    <!--
      配置mapper接口的扫描配置
      由mybatis-spring提供，可以将指定包下所有的mapper接口创建动态代理并将这些动态代理作为IOC容器的bean管理
    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.why.ssm.mapper"></property>
    </bean>
</beans>
~~~



## 4.7.测试

<h3>创建实体类</h3>

~~~java
/*
 实体类
*/
public class Emp {
    private Integer empId;
    private String empName;
    private Integer empAge;
    private String empSex;
    //无参构造、有参构造、getter、setter、toString...
}
~~~

<h3>编写业务层代码</h3>

~~~java
public interface EmpService {
    PageInfo getAll();
}

@Service
public class EmpServiceImpl implements EmpService {
    @Autowired
    private EmpMapper empMapper;

    public PageInfo getAll() {
        PageHelper.startPage(1,3);
        List<Emp> empList = empMapper.getAllEmp();
        PageInfo pageInfo = new PageInfo(empList);
        return pageInfo;
    }
}
~~~

<h3>编写控制层代码</h3>

~~~java
@Controller
public class EmpController {

    @Autowired
    private EmpService empService;

    @RequestMapping("/all")
    @ResponseBody
    public PageInfo queryAll(){
        PageInfo pageInfo = empService.getAll();
        return pageInfo;
    }
}
~~~



<hr>

# 5.SpringBoot


SpringBoot官方文档：https://docs.spring.io/spring-boot/docs/current/reference/html/

## 5.1.入门案例

<h4>开发环境</h4>

> Java版本：java8以上
>
> 构建工具：maven3.3以上
>
> SpringBoot版本：2.3.4

<h4>maven设置</h4>

~~~xml
<!--阿里云镜像-->
<mirrors>
    <mirror>
      <id>nexus-aliyun</id>
      <mirrorOf>central</mirrorOf>
      <name>Nexus aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>

<!--JDK编译版本-->
<profiles>
    <profile>
         <id>jdk-1.8</id>
         <activation>
           <activeByDefault>true</activeByDefault>
           <jdk>1.8</jdk>
         </activation>
         <properties>
           <maven.compiler.source>1.8</maven.compiler.source>
           <maven.compiler.target>1.8</maven.compiler.target>
           <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
         </properties>
    </profile>
</profiles>
~~~

<h4>1.创建maven工程</h4>

<h4>2.引入起步依赖</h4>

~~~xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
~~~

<h4>3.创建主程序</h4>

~~~java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}
~~~

<h4>4.编写controller</h4>

> 注意要写在主程序同包或子包下
>
> 因为SpringBoot默认扫描主程序所在的包
>
> 如果不在主程序同包或子包下需要使用@SpringBootApplication注解的scanBasePackages属性

~~~java
@RestController
public class MyController {

    @RequestMapping("/hello")
    public String hello(){
        return "Hello SpringBoot 2";
    }
}
~~~

<h4>5.启动主程序并在浏览器中访问</h4>



## 5.2.SpringBoot特点

<h3>依赖管理</h3>

> SpringBoot提供的启动器starter都以spring-boot-starter-*格式命名
>
> 而第三方提供的启动器都以*-spring-boot-starter格式命名

版本号

> SpringBoot的父工程中几乎声明了所有开发中常用的依赖的版本号,自动版本仲裁机制
>
> ​	引入依赖默认都可以不写版本
>
> ​	引入非版本仲裁的依赖要写版本号
>
> 修改默认的版本号
>
> ​	1.查看spring-boot-dependencies里面规定当前依赖的版本用的key
>
> ​	2.在当前项目里面重写配置

~~~xml
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
~~~

<h3>自动配置</h3>

> SpringBoot为我们自动配置了许多功能
>
> ​	自动配置TomCat
>
> ​	自动配置SpringMVC: dispatchServlet、字符编码过滤器等
>
> ​	默认的包结构：主程序所在包及其下面的所有子包里面的组件都会被默认扫描进,无需配置包扫描
>
> ​	各种配置拥有默认值：默认配置最终都是映射到某个类上,如MultipartProperties,这些类由容器创建
>
> ​	按需加载所有自动配置项



## 5.3.配置类

> Spring在3.0以后加入了JavaConfig使用java类做配置文件使用,避免繁琐的 xml 配置

### @Configuration

~~~java
/**
 * 1.声明这个类时配置类
 * 2.配置类本身也是组件（Bean）
 * 3.proxyBeanMethods属性
 *     Full模式( proxyBeanMethods = true ): 每个@Bean方法被调用多少次返回的组件都是单实例的
 *     Lite模式( proxyBeanMethods = false ): 每个@Bean方法被调用多少次返回的组件都是新创建的
 */
@Configuration
public class IocConfig {

    /**
     * 给容器中添加组件
     * 默认以方法名作为Bean的id,value或name属性可自定义Bean的id
     */
    @Bean
    public User user(){
        User user = new User();
        user.setUsername("Jack");
        user.setPassword("1234");
        return user;
    }
}

~~~

### @Import

> 功能：给容器导入组件
>
> 使用位置：容器中组件的类上

~~~java
@Import(User.class)
@Configuration
public class IocConfig {
    
}
~~~

### @Conditional

> 功能：满足Conditional指定的条件,则进行组件注入
>
> 位置：类上或方法上
>
> @Conditional有很多派生注解
>
> ​	@ConditionalOnBean、@ConditionalOnMissingBean、@ConditionalOnClass、@ConditionalOnMissingClass ...等

~~~java
@Configuration
public class IocConfig {

    @Bean
    public Pet tom(){
        return new Pet("tom","猫");
    }

    /**
     * 如果容器中存在对应的Bean就将user注入到容器
     *    value: 存在Pet类型的Bean
     *    name：存在id为tom的Bean
     *    type：存在Pet类型的Bean
     */
    @ConditionalOnBean(
            //name = "tom",
            //type = "com.why.boot.pojo.Pet",
            value = Pet.class)
    @Bean
    public User user(){
        User user = new User();
        user.setUsername("Jack");
        return user;
    }
}

~~~

### @ImportResource

> 作用：引入原生xml配置文件
>
> 位置：配置类上

~~~java
@Configuration
@ImportResource("classpath:beans.xml")
public class IocConfig {

}
~~~

### @PropertyResource

> 作用：引入properties文件
>
> 位置：配置类上

~~~java
@Data
@Component("tiger")
public class Tiger {

    @Value("${tiger.name}")
    private String name;
    @Value("${tiger.sex}")
    private String sex;
}
~~~

~~~java
@Configuration
@PropertySource("classpath:tiger.properties")
public class IocConfig {

}
~~~

### 配置绑定

@ConfigurationProperties

> 作用：读取到properties文件中的内容,并且把它封装到JavaBean中
>
> 位置：组件类上或标注了@Bean的方法上
>
> 用法：
>
> ​	@ConfigurationProperties+@Component配合使用
>
> ​	@ConfigurationProperties+@EnableConfigurationProperties配合使用
>
> ​	@ConfigurationProperties+@Bean配合使用（给第三方Bean绑定属性）

application.yml

~~~yaml
student:
  name: Alice
  sex: female
~~~

@ConfigurationProperties+@Component配合使用

```java
@Data
@Component
@ConfigurationProperties(prefix = "student")
public class Student {

    private String name;
    private String sex;
}
```

@ConfigurationProperties+@EnableConfigurationProperties配合使用

~~~java
@Data
@ConfigurationProperties(prefix = "student")
public class Student {

    private String name;
    private String sex;
}

@Configuration
@EnableConfigurationProperties(Student.class)
public class IocConfig {

}
~~~

@ConfigurationProperties+@Bean配合使用

~~~yaml
mydefined:
  datasource:
    url: jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: why0417
~~~

~~~java
@Configuration
public class DataSourceConfig {

    @Bean
    @ConfigurationProperties(prefix = "mydefined.datasource")
    public DruidDataSource druidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        // 相当于下面代码
        // druidDataSource.setUrl("");
        // druidDataSource.setDriver(new Driver());
        // druidDataSource.setUsername("");
        // druidDataSource.setPassword("");
        return druidDataSource;
    }
}
~~~

### 宽松绑定（了解）

> @Value注解不支持松散绑定
>
> @ConfigurationProperties支持宽松绑定
>
> ​	在配置文件中属性key可任意起
>
> ​	但是@ConfigurationProperties的prefix属性必须纯小写或使用中划线-分开

~~~yaml
# 在配置文件中key的起名比较随意
# MYDEFINED:
# MY_DEFINED:
# MY-DEFINED:
# myDefined:
# my-defined:
# my_defined:
mydefined:
  datasource:
    url: xxx
~~~

~~~java
@Bean
// 绑定前缀命名规范: 纯小写 或 使用中划线-分开
@ConfigurationProperties(prefix = "mydefined.datasource")
@ConfigurationProperties(prefix = "my-defined.datasource")
// 下面这些写法是不被允许的
// @ConfigurationProperties(prefix = "my_defined.datasource")
// @ConfigurationProperties(prefix = "myDefined.datasource")
// @ConfigurationProperties(prefix = "MYDEFINED.datasource")
public DruidDataSource druidDataSource() {
    DruidDataSource druidDataSource = new DruidDataSource();
    return druidDataSource;
}
~~~

### 数据校验（了解）

添加数据校验相关依赖

~~~xml
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
</dependency>
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
</dependency>
~~~

开启Bean的数据校验

~~~java
@Data
@Component
@ConfigurationProperties(prefix = "student")
@Validated // 开启Bean的数据校验
public class Student {

    private String name;
    @Max(value = 100,message = "年龄不能超过100")
    private Integer age;
    private String sex;
}
~~~



## 5.4.自动配置原理

### 主程序引导加载自动配置类

> 主程序上有@SpringBootApplication注解,此注解由3个注解复合而成,他们分别是
>
> ​	@SpringBootConfiguration：声明这个类是配置类,作用等同于@Configuration
>
> <font color = 'red' >	@EnableAutoConfiguration</font>：开启自动配置
>
> ​	@ComponentScan：组件扫描器,默认扫描@ComponentScan注解所在的类、类所在的包和子包

~~~java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
							   @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
~~~

> @EnableAutoConfiguration也是一个复合注解
>
> ​	@AutoConfigurationPackage:这个注解指定了默认的包规则
>
> ​	@Import(AutoConfigurationImportSelector.class)：引入<font color = 'red'>AutoConfigurationImportSelector</font>组件

~~~java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {

}
~~~

在AutoConfigurationImportSelector中

> 1.利用getAutoConfigurationEntry函数;给容器中批量导入组件
>
> 2.在getAutoConfigurationEntry中调用getCandidateConfigurations函数,获取到所有需要导入到容器中的配置类
>
> 3.在getCandidateConfigurations函数利用工厂加载SpringFactoriesLoader.loadSpringFactories得到所有的组件
>
> 4.从META-INF/spring.factories位置来加载文件
>
> ​	默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
>
> ​	spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories
>
> ​	上面这个文件里面写死了spring-boot启动时就要给容器中加载的所有配置类

### 按需开启自动配置项

> 虽然我们127个场景的所有自动配置启动的时候默认全部加载(xxxxAutoConfiguration)
>
> 但是在每个配置类中都有@Conditional注解
>
> SpringBoot按照条件装配规则最终会按需配置

~~~java
@Configuration(proxyBeanMethods = false)
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {
	/* 源码省略... */
}
~~~

### 修改默认配置

> 很多配置类上使用了@EnableConfigurationProperties注解绑定配置
>
> 最终这些配置信息都与springBoot的配置文件application.yml绑定在一起
>
> 如果我们想修改默认的配置有两种方法：
>
> ​	1.修改application.yml配置文件
>
> ​	2.使用自己@Bean替换底层的组件,因为SpringBoot默认会在底层配好所有的组件但是如果用户自己配置了以用户的优先

~~~java
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {
	/* 源码省略... */
}
~~~

~~~java
@ConfigurationProperties(prefix = "spring.datasource")
public class DataSourceProperties implements BeanClassLoaderAware, InitializingBean {
	/* 源码省略... */
}
~~~



<h6>自动配置原理总结：</h6>

- SpringBoot先加载所有的自动配置类xxxxxAutoConfiguration
- 每个自动配置类按照条件进行生效,默认都会绑定配置文件指定的值,xxxxProperties里面拿
- 而xxxProperties和配置文件进行了绑定
- 生效的配置类就会给容器中装配很多组件

<font color = 'red'>xxxxxAutoConfiguration ---> 组件  --->xxxxProperties里面拿值  ----> application.properties</font>

定制化配置

1.直接使用自己@Bean替换底层的组件

2.去看这个组件是获取的配置文件什么值就去修改



## 5.5.开发技巧

### Lombok

1.下载插件

2.引入依赖

~~~xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
~~~

3.注解使用

> LomBok常用注解
>
> ​	@Data: 自动生成Getter、Sertter、toString、equals、hashCode方法
>
> ​	@Setter: 自动生成Setter方法
>
> ​	@Getter: 自动生成Getter方法
>
> ​	@ToString: 自动生成toString方法
>
> ​	@AllArgsConstructor: 自动生成全参构造
>
> ​	@NoArgsConstructor: 自动生成无参构造
>
> ​	@EqualsAndHashCode: 自动生成equals和hashCode方法
>
> ​	@Slf4j: 日志：log.info("xxxx");

### 热部署

1.引入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
~~~

2.快速重启

~~~
Ctrl+F9
~~~

自动启动热部署

> 第1步：Settings &rarr; Bulid &rarr; Compiler &rarr; Build project automatically
>
> 第2步：ctrl+alt+shift+/ &rarr; Registry &rarr; 勾选compiler.automake.allow.when.app.running
>
> 激活方式：Idea失去焦点5秒后启动热部署

自定义不参与重启的排除项

~~~Yaml
devtools:
  restart:
    # 这些内容发生改变不会自动热部署
    exclude: public/**,static/**
~~~



### Spring Initailizr

> 快速构建SpringBoot项目
>
> Custom
>
> ​	https://start.springboot.io/ （国内地址）
>
> ​	https://start.aliyun.com/ （阿里云代理）

![image-20220808155553183](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220808155553183.png)

### 隐藏文件夹

> Settings &rarr; Editor &rarr; File Types &rarr; Ignored Files and Folders
>
> 输入要隐藏的文件名,支持*号通配符

### Maven问题解决

> 测试时一直加载
>
> Loading org.junit.platform:junit-platform-launcher...

~~~xml
<dependency>
    <!-- this is needed or IntelliJ gives junit.jar or junit-platform-launcher:11.5.2 not found errors -->
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-launcher</artifactId>
    <scope>test</scope>
</dependency>
~~~





## 5.6.配置文件详解

> SpringBoot配置文件必须以application开头
>
> SpringBoot支持两种格式的配置文件
>
> ​	properties （默认）
>
> ​	yaml格式：后缀为.yaml或.yml
>
> properties的配置文件的优先级高于yaml格式的配置文件
>
> ​	properties > yml > yaml
>
> ​	如果两个格式的配置文件中有相同的key,优先选择properties中的
>
> [官网配置文件详解](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.core)

### yaml基本语法

1.key: value 冒号后面必须有空格

2.大小写敏感

3.使用缩进表示层级关系,缩进不允许用tab只允许使用空格(idea会自动将tab转化成空格)

4.字符串无需加引号,但也可以使用单引号或双引号表示字符串.单引号和双引号的区别

​	单引号表示字符串内容不会被转义

​	双引号表示字符串内容会被转义

### yaml数据类型

字面量:date、boolean、string、number、null

~~~yml
null: ~
int: 123       # 也支持二进制、八进制(0开头)、十六进制(0x开头)
folat: 3.14    # 支持科学计数法: 6.8523015e+5  
boolean: true  # 无大小写区别
String: xxx    # 字符串可以不添加引号
String: 'xxx'  # 单引号表示字符串内容不会被转义
String: "xxx"  # 双引号表示字符串内容会被转义
date: 2022-8-8 # 日期必须使用yyyy-MM-dd格式

# 支持使用 ${} 引用数据
baseDir: C:\cache
tempDir: ${baseDir}\temp
~~~

对象类型: map、hash、set、object 

~~~yaml
# 行内写法
k: {k1: v1,k2: v2,k3: v3}
# 非行内写法
k: 
  k1: v1
  k2: v2
  k3: v3
~~~

数组类型: array、list、queue

~~~yaml
# 行内写法
k: [v1,v2,v3]
# 非行内写法
k: 
  - v1
  - v2
  - v3
~~~

如果遇到很复杂的类型可以先写成行内写法,再转换成非行内写法

~~~yaml
# @Data 
# @Component
# @ConfigurationProperties(prefix = "person")
# public class Person{
# 	private Map<String,List<Pet>> pets
# }
person:
  pets: {k1: [{k2: v2,k3: v3},{k4: v4,k5: v5}],k6: {k7: v7,k8: v8}}
# 逐层简化
person:
  pets: 
    k1: [{k2: v2,k3: v3},{k4: v4,k5: v5}]
    k6: [{k7: v7,k8: v8}]
# 最终结果
person:
  pets:
    k1: 
      - {k2: v2,k3: v3}
      - {k4: v4,k5: v5}
    k6:
      - {k7: v7,k8: v8}
~~~

### 配置提示

> 配置文件提示消失解决方法
>
> ​	Setting → Project Structure → Facets &rarr; 选中对应工程 &rarr; Customize Spring Boot &rarr;添加配置文件
>
> 自定义的类和配置文件绑定一般没有提示
>
> 我们可以配置注释处理器就能实现提示功能,但这个功能只是对开发有用,所以注释处理器不参与打包

~~~xml
<dependency>
    <!--注释处理器-->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

 <build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <!--不参与打包-->
                    <exclude>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-configuration-processor</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

### 多文件配置

> 命名必须以 application-环境标识.properties|yml,如
>
> ​	application-dev.yml 表示开发环境
>
> ​	application-pro.yml 表示生产环境
>
> ​	application-test.yml 表示测试环境
>
> 最后在application.yml中指定使用的环境文件

~~~yaml
spring: 
  profiles:
    active: dev
~~~

> 使用include属性在激活指定环境的情况下,同时对多个环境进行加载使其生效
>
> 从Spring2.4版开始使用group属性替代include属性

~~~yml
spring: 
  profiles:
    active: dev
    include: devDB,devMVC
    
#    group:
#      "dev": devDB,devMVC
#      "pro": proDB,proMVC
#      "test": testDB,testMVC
~~~



### 高级配置

临时属性

~~~shell
java –jar springboot.jar –-server.port=80
~~~

idea中使用临时属性

> 方式1：Edit Configurations &rarr; Configuration &rarr; Program arguments

方式2

~~~java
@SpringBootApplication
public class StaticApplication {

    public static void main(String[] args) {
        //指定临时属性(几乎不用)
        String[] arg = new String[]{"--server.port=80"};
        SpringApplication.run(StaticApplication.class, arg);
        //不携带参数启动应用
        //SpringApplication.run(StaticApplication.class);
    }
}
~~~

SpringBoot中4级配置文件 

> 1级：工程路径(jar包路径)：config/application.yml (最高)
>
> 2级：工程路径(jar包路径)：application.yml 
>
> 3级：classpath：config/application.yml 
>
> 4级：classpath：application.yml (最低)



## 5.7.Web开发

### 5.7.1.静态资源访问

<h4>静态资源目录</h4>

> springBoot默认规定静态资源在类路径下的 `/static` 或 `/public` 或 `/resources` 或 `/META-INF/resources` 文件夹中
>
> 访问：项目根路径/+静态资源名
>
> 静态资源默认映射/**,但请求进来时,先确定Controller是否能处理,Controller不能处理的所有请求都交给静态资源处理器

设置静态资源访问前缀 （默认无前缀）

~~~yaml
spring:
  mvc:
    # 静态资源访问前缀
    static-path-pattern: /static/**
~~~



<h4>欢迎页面和Favicon</h4>

> springBoot默认将静态资源目录下的index.html作为欢迎页
>
> springBoot默认将静态资源目录下的favicon.ico作为页签图标
>
> 特别注意：如果想使用欢迎页和favicon则不能配置静态资源访问前缀

### 5.7.2.请求处理

<h4>RestFul风格</h4>

> @PathVariable:获取 url 中的数据
>
> @GetMapping: 处理get方式的请求
>
> @PostMapping: 处理post方式的请求
>
> @DeleteMapping: 处理delete方式的请求
>
> @PutMapping: 处理put方式的请求

表单如何提交delete和put请求

~~~html
<body>
    <form action="/user" method="post">
        <!--name必须是_method (可以通过配置类修改)-->
        <input hidden name="_method" value="delete">
        <input type="submit" value="delete请求">
    </form>

    <form action="/user" method="post">
        <input hidden name="_method" value="put">
        <input type="submit" value="put请求">
    </form>
</body>
~~~

开启页面表单的Rest功能

~~~yaml
spring:
  mvc:
    hiddenmethod:
      filter:
        # 开启页面表单的Rest功能
        enabled: true
~~~

~~~java
@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public String queryUser(@PathVariable("id")Integer id){
        System.out.println(id);
        return "get";
    }

    @PostMapping("/user")
    public String insertUser(){return "post";}

    @DeleteMapping("/user")
    public String deleteUser(){return "delete";}

    @PutMapping("/user")
    public String updateUser(){return "put";}
}
~~~

自定义filter

~~~java
/**
 * 自定义filter
 */
@Bean
public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
    HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
    methodFilter.setMethodParam("_m");
    return methodFilter;
}
~~~

<h4>请求处理原理</h4>

> 1.请求进来经过DispatcherServlet调用doGet方法,DispatcherServlet继承其父类FrameworkServlet的doGet方法
>
> 2.FrameworkServlet的doGet方法中调用doService,而DispatcherServlet重写了doService
>
> 3.DispatcherServlet的doService方法调用了doDispatch方法
>
> 4.SpringMVC功能分析都从org.springframework.web.servlet.DispatcherServlet的doDispatch()方法开始

~~~java
public class DispatcherServlet extends FrameworkServlet {
	protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
		/* 部分源码省略... */
		try {
			processedRequest = checkMultipart(request);
			multipartRequestParsed = (processedRequest != request);
	
			// Determine handler for the current request.
        	 // 找到当前请求使用哪个Handler(Controller的方法)处理.
			mappedHandler = getHandler(processedRequest);
        	
			/* 部分源码省略... */
    	}
	}
	
    // doDispatch调用getHandler
    protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
         // handlerMappings:处理器映射
		if (this.handlerMappings != null) {
			for (HandlerMapping mapping : this.handlerMappings) {
				HandlerExecutionChain handler = mapping.getHandler(request);
				if (handler != null) {
					return handler;
				}
			}
		}
		return null;
	}
}
~~~

> 所有的请求映射都在HandlerMapping中
>
> - SpringBoot自动配置欢迎页的WelcomePageHandlerMapping,访问 / 能访问到index.html
> - SpringBoot自动配置了默认的RequestMappingHandlerMapping
> - 请求进来,挨个尝试所有的HandlerMapping看是否有请求信息
>
> - 	如果有就找到这个请求对应的handler
>
> - 	如果没有就是下一个HandlerMapping

~~~shell
# 所有的请求映射都在HandlerMapping中
handlerMappings：
0 = {RequestMappingHandlerMapping@6017} 
    mappingRegistry = {AbstractHandlerMethodMapping$MappingRegistry@6219} 
		registry = {HashMap@6316}  size = 6
			# 保存了所有@RequestMapping和handler的映射规则
			0 = {HashMap$Node@6324} "{POST [/user]}"
				key = {RequestMappingInfo@6330} "{POST [/user]}"
				value = {AbstractHandlerMethodMapping$MappingRegistration@6331} 
					mapping = {RequestMappingInfo@6330} "{POST [/user]}"
					handlerMethod = {HandlerMethod@6364} "com.why.boot.controller.UserController#insertUser()"
					...
			1 = {HashMap$Node@6326} "{PUT [/user]}"
			2 = {HashMap$Node@6329} "{GET [/user/{id}]}"
			3 = {HashMap$Node@6328} "{DELETE [/user]}"
			4 = {HashMap$Node@6327} "{ [/error]}"
			5 = {HashMap$Node@6325} "{ [/error], produces [text/html]}"
		...	
1 = {WelcomePageHandlerMapping@6018} 
2 = {BeanNameUrlHandlerMapping@6019} 
3 = {RouterFunctionMapping@6020} 
4 = {SimpleUrlHandlerMapping@6021} 
~~~

### 5.7.3.获取请求参数

1.注解

> @RequestParam、@PathVariable、@RequestHeader、@CookieValue、@RequestBody （MVC笔记中写过）
>
> @RequestAttribute：获取request域中的数据
>
> @MatrixVariable：获取矩阵变量

~~~java
/**
 * 请求路径：/cars/sell;low=34;brand=byd,audi,yd
 * SpringBoot默认是禁用了矩阵变量的功能
 * 矩阵变量必须有url路径变量才能被解析
 */
@GetMapping("/cars/{path}")
public String carsSell(@MatrixVariable("low") Integer low,
                       @MatrixVariable("brand") List<String> brand,
                       @PathVariable("path") String path){
    
    System.out.println(low);//34
    System.out.println(brand);//[byd, audi, yd]
    System.out.println(path);//sell
    return null;
}

/**
 * 若矩阵变量中有相同的矩阵变量名
 * /boss/1;age=20/2;age=10
 */
@GetMapping("/boss/{bossId}/{empId}")
public String boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
                   @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
    
    System.out.println(bossAge); //20
    System.out.println(empAge);  //10
    return null;

}
~~~

开启矩阵变量的功能

~~~java
@Configuration
public class MyConfig implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        // 支持矩阵变量
        urlPathHelper.setRemoveSemicolonContent(true);
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
~~~

2.servlet API

3.复杂参数

### 5.7.4.拦截器

1.定义拦截器类实现HandlerInterceptor接口

~~~java
public class UserInterceptor implements HandlerInterceptor {
    /**
     * 返回值true表示放行false表示拦截
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //从session中获取user的信息
        User user =(User)request.getSession().getAttribute("user");
        //判断用户是否登录
        if (null==user){
            response.sendRedirect(request.getContextPath()+"/user/error");
            return false;
        }
        return true;
    }
}
~~~

2.注册拦截器对象

~~~java
@Configuration
public class MVCConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //addPathPatterns拦截的路径
        String[] path = {
                "/user/**"
        };
        //excludePathPatterns排除的路径
        String[] excludePath = {
                "/user/login","/user/noLg","/user/error"
        };
        //创建用户拦截器对象并指定其拦截的路径和排除的路径
        registry.addInterceptor(new UserInterceptor())
                .addPathPatterns(path)
                .excludePathPatterns(excludePath);
    }
}
~~~

### 5.7.5.文件上传

> 文件上传必须是POST提交且enctype属性必须为multipart/form-data

~~~html
<form action="/upload" method="post" enctype="multipart/form-data">
    用户名: <input type="text" name="username"> <br><br>
    手机号: <input type="text" name="telephone"> <br><br>
    <!-- 单文件上传 -->
    头  像: <input type="file" name="avatar"> <br><br>
    <!-- 多文件上传 -->
    生活照: <input type="file" name="photos" multiple> <br><br>
    <input type="submit" value="提交">
</form>
~~~

~~~java
@PostMapping("/upload")
public String upload(@RequestParam("username") String username,
                     @RequestParam("telephone") String telephone,
                     // 单文件上传
                     @RequestPart("avatar") MultipartFile avatar,
                     // 多文件上传
                     @RequestPart("photos") MultipartFile[] photos) throws IOException {

    if ( !avatar.isEmpty()) {
        // 获取文件名
        String originalFilename = avatar.getOriginalFilename();
        // 将文件存到指定位置
        avatar.transferTo(new File("D:\\cache\\"+originalFilename));
    }

    if ( photos.length > 0){
        for (MultipartFile photo : photos) {
            if ( !photo.isEmpty()) {
                String originalFilename = photo.getOriginalFilename();
                photo.transferTo(new File("D:\\cache\\"+originalFilename));
            }
        }
    }
    return "success";
}
~~~

> SpringBoot默认允许传输单个文件最大值为1MB
>
> 若报`org.apache.tomcat.util.http.fileupload.impl.FileSizeLimitExceededException`异常可以编写下面的配置

~~~yaml
spring:
  servlet:
    multipart:
      # 单个文件最大值
      max-file-size: 10MB
      # 单个请求最大值
      max-request-size: 100MB
~~~

### 5.7.6.错误处理

1.自定义错误页面

> 在template/error文件夹中放入404.html、500.html或4xx.html、5xx.html等
>
> springBoot会根据错误状态码匹配error中的错误页面,优先精准匹配,如无法精准匹配则找4xx.html、5xx.html

2.@ControllerAdvice+@ExceptionHandler （最常用）

~~~java
/**
 * 处理整个web controller的异常
 */
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler({NullPointerException.class,ArithmeticException.class})
    public String handleException(Exception e){
        // 可以些记录异常信息...
        // 跳到指定错误页面
        return "errorTip";
    }
}
~~~

3.@ResponseStatus自定义异常

~~~java
@ResponseStatus(value = HttpStatus.FORBIDDEN,
                reason = "自定义异常")
public class MyException extends RuntimeException{

    public MyException() {

    }
    public MyException(String message) {
        super(message);
    }
}
~~~

4.自定义HandlerExceptionResolver（了解）

~~~java
@Component
@Order(value = Ordered.HIGHEST_PRECEDENCE) // 数值越小优先级越高
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        try {
            // 设置错误信息
            response.sendError(999,"我喜欢的异常");
        } catch (IOException e) {
            e.printStackTrace();
        }
        return new ModelAndView();
    }
}
~~~



### 5.7.7.Servlet

<h4>注解方式</h4>

1.创建Servlet类

2.使用@WebServlet注解

~~~java
@WebServlet(urlPatterns = "/myServlet")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
                   throws ServletException, IOException {
        response.getWriter().write("SpringBoot Use Servlet");
        response.getWriter().flush();
        response.getWriter().close();
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
                   throws ServletException, IOException {
        doGet(request,response);
    }
}
~~~

3.在引导类上使用@ServletComponentScan注解

~~~java
@SpringBootApplication
@ServletComponentScan("com.why.servlet")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

~~~

<h4>配置类方式</h4>

1.创建Servlet

```java
public class MyServletOne extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.getWriter().write("SpringBoot Use Servlet！！！");
        response.getWriter().flush();
        response.getWriter().close();
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request,response);
    }
}
```

2.注册Servlet

~~~java
@Bean
public ServletRegistrationBean servletRegistrationBean(){
    return new ServletRegistrationBean(new MyServletOne(),"/beanServlet");
}

~~~

### 5.7.8.过滤器

<h4>注解方式</h4>

1.创建过滤器

~~~java
/**
 * 过滤所有请求
 */
@WebFilter(urlPatterns = "/*")
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
        	    throws IOException, ServletException {
        
        System.out.println("进入自定义过滤器");
        chain.doFilter(request,response);
    }
}
~~~

2.在引导类上使用@ServletComponentScan注解

~~~java
@SpringBootApplication
@ServletComponentScan({"com.why.servlet","com.why.filter"})
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
~~~

<h4>配置类方式</h4>

1.创建过滤器

~~~java
public class MyFilterOne implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
                throws IOException, ServletException {

        System.out.println("进入自定义过滤器!!!");
        chain.doFilter(request,response);
    }
}
~~~

2.注册过滤器

~~~java
@Bean
public FilterRegistrationBean filterRegistrationBean(){
    FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
    //设置自定义的过滤器
    filterRegistrationBean.setFilter(new MyFilterOne());
    // 添加拦截路径
    filterRegistrationBean.addUrlPatterns("/*");
    return filterRegistrationBean;
}
~~~

### 5.7.9.监听器

> Servlet 规范中定义了八个监听器接口:
>
> 监听声明周期
>
> ​	ServletRequestListener
>
> ​	HttpSessionListener
>
> ​	ServletContextListener
>
> 监听值的变化
>
> ​	ServletRequestAttributeListener
>
> ​	HttpSessionAttributeListener
>
> ​	ServletContextAttributeListener
>
> 针对session中的对象
>
> ​	HttpSessionBindingListener
>
> ​	HttpSessionActivationListener

<h4>注解方式</h4>

1.创建监听器

~~~java
@WebListener
public class MyListener implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        System.out.println("session对象被创建了");
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println("session对象被销毁了");
    }
}
~~~

2.在引导类上使用@ServletComponentScan注解

<h4>配置类方式</h4>

1.创建监听器

~~~java
public class MyListener implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        System.out.println("session对象被创建了");
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println("session对象被销毁了");
    }
}
~~~

2.注册监听器

~~~java
@Bean
public ServletListenerRegistrationBean servletListenerRegistrationBean(){
    return new ServletListenerRegistrationBean(new MyListener());
}
~~~



在springBoot中使用Servlet原生组件总结

> - 使用注解方式
>
>    第1步: 创建组件并添加注解
>
>    第2步: 在引导类上使用@ServletComponentScan注解
>
> - 使用配置类方式
>
>    第1步: 创建组件
>
>    第2步: 向容器中注册组件对象

### 5.7.10.字符编码问题

<h4>方式1</h4>

1.在配置文件中设置编码格式

~~~yaml
server:
  servlet:
    encoding:
      enabled: true
      force: true
      charset: UTF-8
~~~

2.设置响应的编码

~~~java
@WebServlet(urlPatterns = "/myServlet")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
                   throws ServletException, IOException {
        // 设置响应的编码格式
        response.setContentType("text/html;character=utf-8");
        response.getWriter().write("你好世界");
        response.getWriter().flush();
        response.getWriter().close();
    }
}
~~~

<h4>方式2</h4>

1.添加字符编码过滤器

~~~java
@Bean
public FilterRegistrationBean characterFilterRegistrationBean(){
    // 创建字符编码过滤器
    CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
    // 强制使用指定字符编码
    characterEncodingFilter.setForceEncoding(true);
    // 设置指定字符
    characterEncodingFilter.setEncoding("utf-8");

    FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
    // 添加字符编码过滤器
    filterRegistrationBean.setFilter(characterEncodingFilter);
    filterRegistrationBean.addUrlPatterns("/*");
    return filterRegistrationBean;
}
~~~

2.设置响应的编码格式

~~~java
@WebServlet(urlPatterns = "/myServlet")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
                   throws ServletException, IOException {
        // 设置响应的编码格式
        response.setContentType("text/html;character=utf-8");
        response.getWriter().write("你好世界");
        response.getWriter().flush();
        response.getWriter().close();
    }
}
~~~

3.关闭springboot的http字符编码支持

~~~yaml
server:
  servlet:
    encoding:
      # 关闭springboot的http字符编码支持
      # 只有关闭了这个后字符编码过滤器才能生效
      enabled: false
~~~

### 5.7.11.内置web服务器

> springBoot中内置了3个服务器
>
> ​	tomcat
>
> ​	jetty
>
> ​	underow

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <!-- 排除tomcat依赖 -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- 使用jetty作为web服务器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>

~~~

### 5.7.12.跨域问题

> 浏览器出于安全的考虑,使用 XMLHttpRequest对象发起 HTTP请求时必须遵守同源策略
>
> 同源策略要求源相同才能正常进行通信,即协议、域名、端口号都完全一致

1.使用@CrossOrigin

> 常用属性
>
> - `origins`：允许可跨域的域
> - `maxAge`：请求有效期
> - `methods`：允许的请求方式
> - `allowCredentials`：浏览器是否发送凭证信息如Cookie
> - `allowedHeaders`：被允许的请求头

~~~java
/**
 * 可以在支持跨域的方法上或者是Controller上加上@CrossOrigin注解
 */
@RestController
@CrossOrigin(origins = "http://localhost:8888")
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/user/findAll")
    public ResponseResult findAll(){
        List<User> users = userServcie.findAll();
        return new ResponseResult(200,users);
    }
}
~~~

2.配置CorsInterceptor

~~~java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许时间
                .maxAge(3600);
    }
}
~~~

3.使用CorsFilter

~~~java
@Configuration
public class MvcConfig {
    
    @Bean
    public FilterRegistrationBean<CorsFilter> corsFilter(){
        FilterRegistrationBean<CorsFilter> corsFilterFilterRegistrationBean = new FilterRegistrationBean<>();
        
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.setAllowedMethods(Arrays.asList("*"));
        corsConfiguration.setAllowedHeaders(Arrays.asList("*"));
        corsConfiguration.setAllowedOrigins(Arrays.asList("http://localhsot:8888"));
        corsConfiguration.setMaxAge(3600L);
        
        UrlBasedCorsConfigurationSource urlBasedCorsConfigurationSource = new UrlBasedCorsConfigurationSource();
        urlBasedCorsConfigurationSource.registerCorsConfiguration("/**",corsConfiguration);
        
        corsFilterFilterRegistrationBean.setFilter(new CorsFilter(urlBasedCorsConfigurationSource));
        corsFilterFilterRegistrationBean.setOrder(-1);
        return corsFilterFilterRegistrationBean;
    }
}
~~~



### 5.7.13.使用AOP

> 在SpringBoot中默认是开启AOP功能的

~~~yaml
spring: 
  aop: 
    auto: true
~~~

1.添加依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
~~~

2.定义切面

~~~java
@Aspect
@Component
public class MyAspect {

    @Pointcut("execution(* com.why.boot.service.UserServiceImpl.queryUser(..))")
    public void point(){

    }

    @Around("point()")
    public Object printLog(ProceedingJoinPoint joinPoint){
        Object proceed = null;
        System.out.println("queryUser()即将被调用");
        try {
            proceed = joinPoint.proceed();
            System.out.println("queryUser()被调用完了");
        } catch (Throwable e) {
            e.printStackTrace();
            System.out.println("queryUser()出现了异常");
        }
        return proceed;
    }
}
~~~

<h4>切换动态代理</h4>

1.在引导类上添加@EnableAspectJAutoProxy注解

> proxyTargetClass属性
>
> ​	true: cglib动态代理(默认值)
>
> ​	false: jdk动态代理	

~~~java
@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass = false)// 修改代理方式
public class WebApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(WebApplication.class, args);
    }
}
~~~

2.如果想生效还需要在配置文件中做如下配置

~~~yaml
spring:
  aop:
    proxy-target-class: false # 切换动态代理的方式
~~~



## 5.8.数据访问

### 5.8.1.整合Druid

> [Druid官方地址](https://github.com/alibaba/druid)
>
> springBoot默认配置的数据源为HikariDataSource

1.引入起步依赖

~~~xml
<!--Druid提供的起步依赖-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
~~~

2.基础配置

> 更多配置查看[这里](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)

~~~yaml
spring:
  datasource:
    druid:
      url: jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: why0417
~~~

### 5.8.2.整合MyBatis

1.起步依赖

~~~xml
<!--mybatis起步依赖-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
<!--数据库连接-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
~~~

2.常用配置

~~~yaml
mybatis:
  # myBatis核心配置文件位置 (与configuration不能同时使用)
  # config-location: classpath:mybatis/mybatis-config.xml
  # mapper文件位置
  mapper-locations: classpath:mybatis/mapper/*.xml
  # 别名
  type-aliases-package: com.why.boot
  configuration:
    # 日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    # 驼峰命名
    map-underscore-to-camel-case: true
    
# 可以不写mybatis核心配置文件,所有全局配置文件的配置都放在configuration配置项中即可
~~~

### 5.8.3.整合MybatisPlus

1.起步依赖

~~~xml
<!--mybatisPlus起步依赖-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
<!--数据库连接-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
~~~

2.常用配置

> [更多配置信息](https://baomidou.com/pages/56bac0/#%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE)

~~~yaml
mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  type-aliases-package: com.why.boot
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl

~~~

### 5.8.4.整合Redis





## 5.9.单元测试

### 5.9.1.常用注解

> @DisplayName: 为测试类或者测试方法设置展示名称
>
> @Disabled: 表示测试类或测试方法不执行
>
> @RepeatedTest: 表示方法可重复执行
>
> @BeforeEach: 表示在每个单元测试之前执行
>
> @AfterEach: 表示在每个单元测试之后执行
>
> @BeforeAll: 表示在所有单元测试之前执行
>
> @AfterAll: 表示在所有单元测试之后执行
>
> @Timeout: 表示测试方法运行如果超过了指定时间将会返回错误

~~~java
@Test
@DisplayName("xxx测试类")
void test00(){/*...*/}

@Test
@Disabled
void test01(){/*...*/}

@Test
@RepeatedTest(5)
void test2(){/*...*/}

@BeforeEach
@AfterEach
void test03(){/*...*/}

@BeforeAll
@AfterAll
static void test04(){/*...*/}

@Test
@Timeout(value = 100,unit = TimeUnit.MILLISECONDS)
void test05(){/*...*/}
~~~

### 5.9.2.断言

> 断言（assertions）是测试方法中的核心部分,用来对测试需要满足的条件进行验证
>
> 这些断言方法都是 `org.junit.jupiter.api.Assertions` 的静态方法

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

~~~java
@Test
@DisplayName("断言测试")
void test05(){
    int result = sum(2,3);
    // assertEquals(预期值,实际结果)
    // 若预期值等于实际值则测试通过
    Assertions.assertEquals(5,result);

    // 若预期值不等于实际值则测试通过
    Assertions.assertNotEquals(6,result);
}
~~~

数组断言

> 通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等

~~~java
@Test
@DisplayName("数组断言")
void test06(){
    int[] arr1 = new int[]{1,2,3};
    int[] arr2 = new int[]{1,2,3};
    // 若两个数组内容相同则测试通过
    Assertions.assertArrayEquals(arr1,arr2);
}
~~~

组合断言

> assertAll方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言
>
> 可以通过 lambda 表达式很容易的提供这些断言

~~~java
@Test
@DisplayName("组合断言")
void test07(){
    Assertions.assertAll("xxx",
            () -> assertEquals(2,1+1),
            () -> assertTrue(1 > 0));
}
~~~

异常断言

~~~java
@Test
@DisplayName("异常断言")
void test08(){
    NullPointerException exception = Assertions.assertThrows(
            NullPointerException.class,() -> {
                // 出现异常测试才能通过
                String str = null;
                str.length();
            }
    );
}
~~~

超时断言

~~~java
@Test
@DisplayName("超时断言")
public void test09() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
~~~

快速失败

> 通过 fail 方法直接使得测试失败

~~~java
@Test
@DisplayName("fail")
public void test10() {
	fail("This should fail");
}
~~~

### 5.9.3.前置条件

> 前置条件（assumptions假设）类似于断言
>
> 不同之处在于不满足的断言会使得测试方法失败而不满足的前置条件只会使得测试方法的执行终止
>
> 前置条件可以看成是测试方法执行的前提,当该前提不满足时,就没有继续执行的必要

~~~java
@Test
@DisplayName("前置条件")
void test11(){
    // assumption为true则测试通过,false则测试终止
    Assumptions.assumeTrue( 1 > 0);
    // assumption为false则测试通过,true则测试终止
    Assumptions.assumeFalse(1 > 2);

    Assumptions.assumingThat(
            2>1,
            () -> System.out.println("assumption为true则执行")
    );
}
~~~



### 5.9.4.单元测试专属属性

> 使用SpringBootTest注解的args或properties指定测试专用属性
>
> args属性要加--前缀

~~~java
// @SpringBootTest(args = {"--test.message = testValue"})
@SpringBootTest(properties = {"test.message = testValue"})
public class WebApplicationTest {

    @Value("${test.message}")
    private String message;

    @Test
    public void test01(){
        System.out.println(message);
    }
}
~~~

### 5.9.6.Web环境模拟测试

~~~java
// 开启虚拟MVC调用
@AutoConfigureMockMvc
// 开启web环境
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
public class WebApplicationTest {

    // 虚拟MVC调用对象
    @Test
    void webTest(@Autowired MockMvc mvc) throws Exception {
        // 创建虚拟请求
        MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/stu");
        // 执行请求
        ResultActions action = mvc.perform(builder);
        // 定义预期虚拟请求的状态
        StatusResultMatchers status = MockMvcResultMatchers.status();
        ResultMatcher ok = status.isOk();

        // 定义预期虚拟请求的响应体
        ContentResultMatchers content = MockMvcResultMatchers.content();
        ResultMatcher result = content.string("Hello SpringBoot2");
        ResultMatcher jsonResult = content.json("{\"name\":\"jack\",\"age\":20,\"sex\":\"male\"}");

        // 定义预期虚拟请求的响应头
        HeaderResultMatchers header = MockMvcResultMatchers.header();
        ResultMatcher headerResult = header.string("Content-Type", "application/json");
        // 使用本次真实执行结果与预期结果进行比对
        action.andExpect(ok);
        action.andExpect(jsonResult);
        action.andExpect(headerResult);
    }
}
~~~

### 5.9.7.数据回滚

> 为测试用例添加事务,SpringBoot会对测试用例对应的事务提交操作进行回滚

~~~java
// 如果想在测试用例中提交事务
// @Rollback(false)
@SpringBootTest
@Transactional
public class DaoTest {
    @Autowired
    private BookService bookService;

    // 测试类...
}
~~~



## 5.10.指标监控

### 5.10.1.简单使用

1.添加依赖

~~~~xml
<dependency>
 	<groupId>org.springframework.boot</groupId>
 	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
~~~~

2.配置启用监控端点

~~~~yml
management:
  endpoints:
    # 配置启用所有端点
    enabled-by-default: true
    web:
      exposure:
      	# 暴露所有端点
        include: "*"
  endpoint:
    health:
      show-details: always
~~~~

3.访问监控接口

> http://localhost:8080/actuator

### 5.10.2.常用端点

| 端点名称       | 描述                                    |
| :------------- | :-------------------------------------- |
| beans          | 显示应用程序中所有Spring Bean的完整列表 |
| health         | 显示应用程序运行状况信息                |
| inf            | 显示应用程序信息                        |
| loggers        | 显示和修改应用程序中日志的配置          |
| metrics        | 显示当前应用程序的“指标”信息            |
| mappings       | 显示所有@RequestMapping路径列表         |
| scheduledtasks | 显示应用程序中的计划任务                |

### 5.10.3.图形化界面

<h6>服务端</h6>

1.创建SpringBoot工程

2.引入依赖

~~~xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
</dependency>
~~~

3.配置服务端口

~~~yaml
server:
  port: 8888
~~~

4.在启动类上加@EnableAdminServer注解

5.启动项目

<h6>客户端</h6>

1.添加依赖

~~~xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.7.4</version>
</dependency>
~~~

2.配置地址

~~~yaml
spring:
  boot:
    admin:
      client:
        url: http://localhost:8888
~~~

3.启动项目



## 5.11.打包与日志

### 打包与运行

> 1.确认打包时是否具有SpringBoot对应的maven插件
>
> 2.打包：mvn package
>
> 3.运行：java -jar xxx.jar

~~~xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
~~~

### 日志

日志级别

> TRACE：运行堆栈信息
>
> DEBUG：程序员调试代码使用
>
> INFO：记录运维过程数据
>
> WARN：记录运维过程报警数据
>
> ERROR：记录错误堆栈信息
>
> FATAL：致命错误
>
> SpringBoot默认的日志级别是`INFO`

常用日志配置

~~~yaml
logging:
  # 设置日志组
  group:
    controller: com.why.boot.controller
    service: com.why.boot.service
  # 分组设置日志级别
  level:
    controller: debug
    service: error
  # 设置日志输出格式
  pattern:
    console: "%d - %m%n"
  # 设置日志文件(位置：项目所在目录)
  file:
    name: server.log
  # 超过1MB新建日志文件
  logback:
    rollingpolicy:
      max-file-size: 10KB
      file-name-pattern: server.%d{yyyy-MM-dd}.%i.log
~~~



<hr>

# 6.MyBatisplus

官网地址: https://baomidou.com/

## 6.1.入门案例

开发环境

> JDK: JDK8+ 
>
> 构建工具: maven3.5.4 
>
> MySQL版本: MySQL5.7
>
> SpringBoot: 2.6.3 
>
> MyBatis-Plus: 3.5.1

准备工作：创建数据库表

~~~sql
CREATE DATABASE `mybatis_plus`;
use `mybatis_plus`;
CREATE TABLE `user` (
`id` bigint(20) NOT NULL COMMENT '主键ID',
`name` varchar(30) DEFAULT NULL COMMENT '姓名',
`age` int(11) DEFAULT NULL COMMENT '年龄',
`email` varchar(50) DEFAULT NULL COMMENT '邮箱',
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

<h4>1.创建SpringBoot工程</h4>

![image-20220805205957064](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220805205957064.png)

<h4>2.导入依赖</h4>

~~~xml
<dependencies>
    <!--springBoot-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <!--MybatisPlus-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.1</version>
    </dependency>
    <!--Lombook-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <!--Mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
~~~

<h4>3.创建application.yml配置文件</h4>

> [更多MybatisPlus配置信息](https://baomidou.com/pages/56bac0/#%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE)

~~~yaml
spring:
  # 配置数据源信息
  datasource:
    # 配置数据源类型
    type: com.zaxxer.hikari.HikariDataSource
    # 配置连接数据库信息
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf8&useSSL=false
    username: root
    password: why0417

# 配置MybatisPlus日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
~~~

<h4>4.添加实体类、mapper和启动类</h4>

~~~java
/*
 实体类
*/
@Data
//设置实体类映射的数据库表
//默认表名为类名首字母小写
@TableName("t_user")
public class User {
    
    @TableId
    private Long id;
    private String name;
    private Integer age;
    private String email;
}

/*
 Mapper接口
*/
@Repository
public interface UserMapper extends BaseMapper<User> {
    
}
/*
 启动类
*/
@SpringBootApplication
@MapperScan("com.why.mp.mapper")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

~~~

<h4>5.测试</h4>

~~~java
@SpringBootTest
class ApplicationTest{

    @Autowired
    private UserMapper userMapper;

    @Test
    public void selectList(){
        List<User> users = userMapper.selectList(null);
        users.forEach(System.err::println);
    }
}
~~~



## 6.2.基本CRUD

> MyBatis-Plus中的基本CRUD在内置的`BaseMapper`中都已得到了实现
>
> 我们只需要将mapper接口继承BaseMapper就可以直接使用它提供的方法

<h3>插入</h3>

~~~java
@Test
public void testInsert(){
    /*
     若id为空MybatisPlus会根据雪花算法自动生成id
    */
    User user = new User(null,"Alice",22,"123@qq.com");
    int result = userMapper.insert(user);
    System.err.println(result);
}
~~~

<h3>删除</h3>

~~~java
@Test
public void testDelete(){
    //通过id删除记录
    int result = userMapper.deleteById(1555540556094910466L);
    System.err.println(result);
}
@Test
public void testDelete(){
    //通过多个id批量删除
    List<Long> ids = Arrays.asList(1L,2L,3L);
    int result = userMapper.deleteBatchIds(ids);
    System.err.println(result);
}
@Test
public void testDelete(){
    //通过map条件删除
    Map<String,Object> map = new HashMap<>();
    map.put("name","Alice5");
    map.put("age",22);
    int result = userMapper.deleteByMap(map);
    System.err.println(result);
}
~~~

<h3>修改</h3>

~~~java
@Test
public void testUpdate(){
    User user = new User(1L,"admin",18,"123@126.com");
    int result = userMapper.updateById(user);
    System.err.println(result);
}
~~~

<h3>查询</h3>

~~~java
@Test
public void testSelect(){
    //根据id查找
    User user = userMapper.selectById(1L);
    System.err.println(user);
}
@Test
public void testSelect(){
    //根据多个id查找多条记录
    List<Long> ids = Arrays.asList(1L, 2L);
    List<User> users = userMapper.selectBatchIds(ids);
    System.err.println(users);
}
@Test
public void testSelect(){
    //通过map条件查询用户信息
    Map<String,Object> map = new HashMap<>();
    map.put("name","admin");
    map.put("age",18);
    List<User> users = userMapper.selectByMap(map);
    System.err.println(users);
}
@Test
public void testSelect(){
    //查询所有
    List<User> users = userMapper.selectList(null);
    System.err.println(users);
}
~~~

<h3>通用Service</h3>

> MyBatis-Plus中有一个接口IService和其实现类ServiceImpl,封装了常见的业务层逻辑 详情查看源码IService和ServiceImp

~~~java
/**
* UserService继承IService模板提供的基础功能
*/
public interface UserService extends IService<User> {
    
}
/**
* ServiceImpl实现了IService,提供了IService中基础功能的实现
*/
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
    
}
~~~

~~~java
@SpringBootTest
public class TestService {

    @Autowired
    private UserService userService;
    @Test
    public void testInsert(){
        ArrayList<User> users = new ArrayList<>();
        users.add(new User(null,"Tom",20,"123@qq.com"));
        users.add(new User(null,"Bob",20,"123@qq.com"));
        users.add(new User(null,"Jon",20,"123@qq.com"));
        //批量添加
        boolean saved = userService.saveBatch(users);
        System.err.println(saved);
    }
}

~~~



## 6.3.常用注解

### @TableName

> MybatisPlus在确定操作的表时,由BaseMapper的泛型决定,且默认操作的表名为实体类型的类名首字母小写
>
> @TableName用于解决实体类型与数据库表名不一致问题
>
> 除了@TableName方式,还可以通过全局配置解决实体类与表名不一致的问题

~~~java
@TableName("t_user")
public class User {
    /*...*/
}
~~~

通过全局配置解决问题

~~~yaml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: t_  #设置表的默认前缀
~~~

### @TableId

> MybatisPlus在实现CRUD时,会默认将id作为主键列
>
> 当表中的主键不是id时MybatisPlus会抛出异常
>
> 解决方案:
>
> ​	在实体类中userId属性上通过@TableId将其标识为主键
>
> @TableId的属性
>
> ​	value:指定表中的主键字段
>
> ​	type:指定主键策略
>
> ​		IdType.ASSIGN_ID(默认):基于雪花算法的策略生成数据id
>
> ​		IdType.AUTO:使用数据库的自增策略,前提是数据库设置了主键自增
>
> ​		IdType.NONE:不使用主键策略
>
> ​		IdType.INPUT:该类型可以通过自己注册自动填充插件进行填充
>
> ​		IdType.ID_WORKER:只有当插入对象ID为空才自动填充 针对数值类型的id
>
> ​		IdType.ID_WORKER_STR:只有当插入对象ID为空才自动填充 针对字符串类型的id
>
> ​		IdType.UUID 使用UUID

~~~java
@Data
@TableName("t_user")
public class User {
    /*
      数据库中的主键字段为user_id
    */
    @TableId(value="user_id",type=IdType.AUTO)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
~~~

配置全局主键策略

~~~yaml
mybatis-plus:
  global-config:
    db-config:
      id-type: auto  #配置主键策略
~~~

### @TableField

> @TableField用于解决实体类中的`属性名和字段名不一致`的问题
>
> 情况1: 若实体类中的属性使用的是驼峰命名风格而表中的字段使用的是下划线命名风格
>
> ​	MyBatis-Plus会自动将下划线命名风格转化为驼峰命名风格,我们无需处理
>
> 情况2: 若实体类中的属性和表中的字段不满足情况1
>
> ​	属性上使用@TableField("字段名")设置属性所对应的字段名
>
> @TableField的exist属性
>
> ​	true: 默认值
>
> ​	false: 该属性没有映射数据库表字段
>
> @TableField的fill属性
>
> ​	fill属性用来指定`自动填充`
>
> ​	  FieldFill.INSERT 添加数据时自动填充
>
> ​	  FieldFill.UPDATE 修改数据时自动填充
>
> ​	  FieldFill.INSERT_UPDATE 添加和修改数据时自动填充
>
> 如果想要实现自动填充还需要我们创建类去实现MetaObjectHandler接口定义填充的数据

~~~java
@TableName("t_user")
public class User {
    /**
     * 表中的字段名为user_id、user_name、user_age、user_email
     */
    @TableId(value = "user_id")
    private Long userId;
    @TableField("user_name")
    private String name;
    private Integer userAge;
    private String userEmail;
    
    @TableField(fill = FieldFill.INSERT)
	private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
	private Date updateTime;
}
~~~

MetaObjectHandler接口实现类

~~~java
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
	private static final Logger LOGGER =
						LoggerFactory.getLogger(MyMetaObjectHandler.class);
    // 使用mp实现添加功能时这个方法执行
	@Override
	public void insertFill(MetaObject metaObject) {
		LOGGER.info("start insert fill ....");
		this.setFieldValByName("createTime", new Date(), metaObject);
		this.setFieldValByName("updateTime", new Date(), metaObject);
 	}
    // 使用mp实现修改功能时这个方法执行
	@Override
	public void updateFill(MetaObject metaObject) {
		LOGGER.info("start update fill ....");
		this.setFieldValByName("updateTime", new Date(), metaObject);
	 }
}
~~~



### @TableLogic

> @TableLogic用于标识逻辑删除的字段

~~~java
@TableName("t_user")
public class User {
    
    @TableId(value = "user_id")
    private Long userId;
    private String userName;
    private Integer userAge;
    private String userEmail;
    @TableLogic
    private Integer isDelete;
}
~~~

配置逻辑删除插件(3.4.0版本之前要配置)

~~~java
@Bean
public ISqlInjector SqlInjector(){
    return new LogicSqlInjector();
}
~~~



## 6.4.条件构造器

![image-20220805235936864](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220805235936864.png)

> Wrapper:条件构造抽象类,最顶端父类 
>
> ​	AbstractWrapper:用于查询条件封装，生成 sql 的 where 条件 
>
> ​		QueryWrapper:查询条件封装 
>
> ​		UpdateWrapper:Update 条件封装 
>
> ​		AbstractLambdaWrapper:使用Lambda 语法 
>
> ​			LambdaQueryWrapper:用于Lambda语法使用的查询Wrapper 
>
> ​			LambdaUpdateWrapper:Lambda更新封装Wrapper


### QueryWrapper

> 查询和删除记录的条件构造器都使用QueryWrapper
>
> 根据记录可以使用实体类和QueryWrapper配合使用

组装查询条件

~~~java
@Test
public void testSelectWrapper(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    /**
     * 组装查询条件
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user 
     * WHERE (user_name LIKE ? AND user_age BETWEEN ? AND ? AND user_email IS NOT NULL)
     */
    wrapper.like("user_name","T")
            .between("user_age",18,25)
            .isNotNull("user_email");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
~~~

组装排序条件

~~~java
@Test
public void test01(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    /**
     * 组装排序条件
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user ORDER BY user_age DESC,user_id ASC
     */
    wrapper.orderByDesc("user_age")
           .orderByAsc("user_id");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
~~~

条件的优先级

~~~java
    @Test
public void test02(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    /**
     * 默认连接就是and
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user 
     * WHERE (user_name LIKE ? AND user_age = ? OR user_email IS NOT NULL)
     */
    wrapper.like("user_name","T")
           .eq("user_age",20)
           .or()
           .isNotNull("user_email");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}

@Test
public void test03(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    /**
     * lambda表达式内的逻辑优先运算
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user 
     * WHERE (user_name LIKE ? AND (user_age = ? AND user_email IS NOT NULL))
     */
    wrapper.like("user_name","T")
           .and(w -> w.eq("user_age",20).isNotNull("user_email"));
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
~~~

组装select子句

~~~java
@Test
public void test04(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    /**
     * SELECT user_name,user_age FROM t_user WHERE (user_age > ?)
     */
    wrapper.select("user_name","user_age")
           .gt("user_age",18);
    List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);
    System.out.println(maps);
}
~~~

### UpdateWrapper

> 修改记录有两种方式:
>
> 1.实体类和QueryWrapper配合使用
> 
> 2.UpdateWrapper

实体类和QueryWrapper配合使用

~~~java
@Test
public void test05(){
    /**
     * 为null的属性对应的字段不参与修改
     * UPDATE t_user SET user_name=?, user_email=? WHERE (user_name = ?)
     */
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.eq("user_name","mary");
    User user = new User(null,"Jack",null,"123@126.com");
    int result = userMapper.update(user, wrapper);
    System.out.println(result);
}
~~~

UpdateWrapper

~~~java
@Test
public void test06(){
    /**
     * 若User不为null,set中设置的优先级大于对象中设置的值
     * 如set中修改user_age的值为18,user对象中也修改user_age的值为16,但最终是修改18
     * UPDATE t_user SET user_age=?,user_email=? WHERE (user_name = ?)
     */
    UpdateWrapper<User> wrapper = new UpdateWrapper<>();
    wrapper.set("user_age",18)
            .set("user_email","123@Jack.com")
            .eq("user_name","Jack");
    //User user = new User(null,"Andy",16,"123@Andy.com");
	//int result = userMapper.update(user, wrapper);
    int result = userMapper.update(null, wrapper);
    System.out.println(result);
    
}
~~~

### Condition

> 在实际开发的过程中,这些条件数据来源于用户输入是可选的
>
> 因此我们在组装这些条件时,必须先判断用户是否选择了这些条件 若选择了就需要组装该条件否则一定不能组装
>
> 使用condition避免的大量的if,可以简化代码的编写

~~~java
@Test
public void test07(){
    /**
     * wrapper.eq(boolean condition,Object column,Object val)
     * 若condition的值为true,则将条件拼接到sql中
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user WHERE (user_name = ? AND user_age > ?)
     */
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    String username = "Tom";
    Integer ageBegin = 18;
    Integer ageEnd = null;
    wrapper.eq(StringUtils.isNotBlank(username),"user_name",username)
            .gt(ageBegin!=null,"user_age",ageBegin)
            .gt(ageEnd!=null,"user_age",ageEnd);
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
~~~

### LambdaQueryWrapper

> 使用函数式接口获取字段名防止因字段名写错而引发的错误
>
> 其余用法与QueryWrapper相同

~~~java
@Test
public void test09(){
    /**
     *  用法：类名::getter
     * 避免使用字符串表示字段
     * 防止因字段名写错而引发的错误
     */
    LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
    wrapper.like(User::getName,"T")
            .between(User::getUserAge,18,25)
            .isNotNull(User::getUserEmail);
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
~~~

### LambdaUpdateWrapper

> LambdaUpdateWrapper与LambdaQueryWrapper用法相似

~~~java
@Test
public void test08(){
    /**
     * 避免使用字符串表示字段
     * 防止因字段名写错而引发的错误
     */
    LambdaUpdateWrapper<User> wrapper = new LambdaUpdateWrapper<>();
    wrapper.set(User::getUserAge,18)
            .set(User::getUserEmail,"123@Jack.com")
            .eq(User::getName,"Jack");
    int result = userMapper.update(null, wrapper);
    System.out.println(result);
}
~~~



## 6.5.插件

### 分页插件

> MyBatisPlus自带分页插件,只要简单的配置即可实现分页功能

1.添加配置类

~~~java
@Configuration
@MapperScan("com.why.mp.mapper")
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
~~~

2.使用分页插件

~~~java
@Test
public void testPage(){
    /**
     * SELECT user_id,user_name AS name,user_age,user_email FROM t_user LIMIT ?
     */
    Page<User> page = new Page<>(1,3);
    userMapper.selectPage(page, null);
    System.out.println("获取分页数据："+page.getRecords());
    System.out.println("当前页页码号："+page.getCurrent());
    System.out.println("每页记录条数："+page.getSize());
    System.out.println("总记录条数："+page.getTotal());
    System.out.println("是否有上页："+page.hasPrevious());
    System.out.println("是否有下页："+page.hasNext());
}
~~~

自定义分页方法

~~~java
@Repository
public interface UserMapper extends BaseMapper<User> {
    /**
     * 自定义分页方法要求：
     * 1.返回值必须是Page类型
     * 2.传递Page对象作为参数且必须放在首位
     */
    Page<User> selectPageByAge(@Param("page") Page<User> page,
                               @Param("age") Integer age);
}
~~~

~~~xml
<!--
	使用分页插件sql语句无需使用limit
	MybatisPlus会自动进行分页
-->
<select id="selectPageByAge" resultType="com.why.mp.pojo.User">
    select * from t_user where user_age = #{age}
</select>
~~~

### 乐观锁插件

> 乐观锁最常用的实现方式: 使用数据版本Version记录机制实现,当读取数据时,将version字段的值一同读出,数据每更新一次就对此version值加1

1.给数据库表添加version字段

2.给实体类添加version属性并加上@Version注解

3.添加乐观锁插件配置

~~~java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    //分页插件
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
    //乐观锁插件
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}
~~~



### 性能分析插件

~~~java
/**
 * 旧版本的写法
 * SQL 执行性能分析插件
 * 开发环境使用,线上不推荐
 * maxTime 指的是 sql 最大执行时长
 */
@Bean
@Profile({"dev","test"})// 设置 dev test 环境开启
public PerformanceInterceptor performanceInterceptor() {
	PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
    // 超过此处设置的ms则sql不执行
	performanceInterceptor.setMaxTime(100);
    // SQL是否格式化,默认false
	performanceInterceptor.setFormat(true);
	return performanceInterceptor;
}
~~~



### 旧版插件

3.4.0版本之前并没有引入MybatisPlusInterceptor

旧版本直接new插件注入到容器即可 如分页插件

~~~java


    @Bean
    public PaginationInnerInterceptor paginationInnerInterceptor() {
        return new PaginationInnerInterceptor();
    }
~~~

其他的就不演示了



## 6.6.代码生成器

> AutoGenerator是MyBatis-Plus的代码生成器
>
> 通过AutoGenerator可以快速生成Entity、Mapper、Mapper XML、Service、Controller等各个模块的代码,极大的提升了开发效率

1.引入依赖

~~~xml
<!--代码生成器依赖-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.1</version>
</dependency>
<!--传递依赖包-->
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.31</version>
</dependency>
~~~

2.快速生成

> [点击这里](https://baomidou.com/pages/981406/#%E6%95%B0%E6%8D%AE%E5%BA%93%E9%85%8D%E7%BD%AE-datasourceconfig)查看详细的配置信息

~~~java
public class FastAutoGeneratorTest {
    public static void main(String[] args) {
        FastAutoGenerator.create(
                "jdbc:mysql://localhost:3306/ssm?characterEncoding=utf-8&userSSL=false",
                "root",
                "why0417")
                .globalConfig(builder -> {
                    //设置作者
                    builder.author("wang") 
                            //开启swagger模式
                            //.enableSwagger() 
                            //覆盖已生成文件
                            .fileOverride()
                            //指定输出目录
                            .outputDir("D://mybatisPlus"); 
                })
                .packageConfig(builder -> {
                    //设置父包名
                    builder.parent("com.why")
                            //设置父包模块名
                            .moduleName("mybatisPlus")
                            //设置mapperXml生成路径
                            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D://mybatisPlus")); 
                })
                .strategyConfig(builder -> {
                    //设置需要生成的表名
                    builder.addInclude("t_user")
                            //设置过滤表前缀
                            .addTablePrefix("t_", "c_"); 
                })
                //使用Freemarker引擎模板 默认的是Velocity引擎模板
                .templateEngine(new FreemarkerTemplateEngine()) 
                .execute();
    }
}

~~~



## 6.7.多数据源

1.引入依赖

~~~xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
    <version>3.5.0</version>
</dependency>
~~~

2.配置多数据源

~~~yml
spring:
  datasource:
    dynamic:
      # 设置默认的数据源或者数据源组,默认值为master
      primary: master
      # 严格匹配数据源,默认false, 值为true时未匹配到指定数据源时抛异常
      strict: false
      datasource:
        master:
          driver-class-name: com.mysql.cj.jdbc.Driver
          url: jdbc:mysql://localhost:3306/ssm?characterEncoding=utf8&useSSL=false
          username: root
          password: 123456
        slave_1:
          driver-class-name: com.mysql.cj.jdbc.Driver
          url: jdbc:mysql://localhost:3306/whyorder?characterEncoding=utf8&useSSL=false
          username: root
          password: 123456
~~~

3.在Service组件总使用@DS注解指定访问的数据库

~~~java
@DS("master")
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {

}

@DS("slave_1")
@Service
public class OrderServiceImpl extends ServiceImpl<OrderMapper, Order> implements OrderService {

}
~~~

4.测试

~~~java
@SpringBootTest
public class TestDatasource {

    @Autowired
    private UserService userService;
    @Autowired
    private OrderService orderService;

    @Test
    public void test(){
        System.out.println(userService.getById(2L));
        System.out.println(orderService.getById(101));
    }
}

~~~

## 6.8.MyBatisX插件

1.安装插件

> File -> Settings -> Plugins

2.插件用法

> 参考官网地址 [点击这里](https://baomidou.com/pages/ba5b24/#%E5%8A%9F%E8%83%BD)

