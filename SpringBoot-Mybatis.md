[toc]

# 创建工程

# 新建数据表

创建一个users表，并插入示例数据：

![image-20200318113534325](/home/long/.config/Typora/typora-user-images/image-20200318113534325.png)

建表语句：schema.sql

```mysql
DROP TABLE IF EXISTS users;
CREATE TABLE users (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '用户编号',
  `username` varchar(64) COLLATE utf8mb4_bin DEFAULT NULL COMMENT '账号',
  `password` varchar(32) COLLATE utf8mb4_bin DEFAULT NULL COMMENT '密码',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`),
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;
```

(utf8是utfmb3，只有3个字节，节省空间但不能表达所有的UTF-8；utf8mb4_bin区分大小写)

# pom文件中添加相关依赖

添加MySQL和Mybatis依赖，以及test用来做测试

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

# 配置application.yaml

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/users
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: *****
```

# 创建实体类

User.java，然后生成getter, setter和toString方法

```java
public class User {
    private Integer id;
    private String username;
    private String password;
    private Date createTime;
    ......
```

# Dao层和Service层

## 用注解方式

使用注解的方式时，SQL语句都写在注解里面。

@Mapper注解一般用在接口上，Mybatis的拦截器会自动把@Mapper注解的接口生成动态代理类。

@MapperScan的好处在于可以扫描一个或者多个包下（用逗号分开）的所有类，而不用逐个去添加@Mapper注解

DAO层：

```java
@Mapper
public interface UserDao {
    @Select("SELECT * FROM users WHERE id = #{id}")
    User findUserById(@Param("id") Integer id);

    @Select("SELECT * FROM users")
    List<User> findAllUser();

    @Update("UPDATE users SET password = #{password} WHERE id = #{id}")
    void updateUser(@Param("password") String password, @Param("id") Integer id);

    @Insert("INSERT INTO users(username, password, create_time) VALUES(#{username},#{password},#{createTime})")
    void insertUser(@Param("username") String username, @Param("password") String Password,
                    @Param("createTime") Date createTime);

    @Delete("DELETE from users WHERE id = #{id}")
    void deleteUserById(@Param("id") Integer id);
}
```

Service层，调用DAO的方法来实现具体的服务：

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao;

    public User selectUserById(Integer id){
        return userDao.findUserById(id);
    }

    public List<User> selectAllUser(){
        return userDao.findAllUser();
    }

    public void insertUser(){
        userDao.insertUser("ZhangSan","123",new Date());
        userDao.insertUser("WangWu","321",new Date());
    }

    public void deleteUser(Integer id){
        userDao.deleteUserById(id);
    }

    public void changePassword(){
        userDao.updateUser("333", 4);
    }
}
```

Controller层对应具体的路由：

```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping("/query")
    public User query(){
        return userService.selectUserById(1);
    }

    @RequestMapping("/insert")
    public List<User> insertUser(){
        String before ="Before: "+ userService.selectAllUser() + "]";
        System.out.println(before);
        userService.insertUser();
        return userService.selectAllUser();
    }

    @RequestMapping("/changePassword")
    public List<User> changerPassword(){
        userService.changePassword();
        return userService.selectAllUser();
    }

    @RequestMapping("delete")
    public List<User> testDelete(){
        userService.deleteUser(2);
        return userService.selectAllUser();
    }
}
```

启动应用如下：

```java
@SpringBootApplication
@MapperScan(basePackages = "com.example.demo.dao")
public class DemoApplication {

    public static void main(String[] args) {
        System.out.println("hi,long!");
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

## 用xml方式

使用xml方式时，SQL语句写在指定的.xml文件中。

在资源文件夹resources下新建一个mapper文件夹，然后在文件夹下创建UserMapper.xml文件.

DAO层如下：(在Application中有@MapperScan注解)

```java
public interface UserXMLDao {
    User findUserById(Integer id);
}
```

Service层中使用UserXMLDao接口：

```java
@Autowired
private UserXMLDao userXMLDao;

public User selectUserById(Integer id){
    return userXMLDao.findUserById(id);
}
```

UserMapper.xml文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.demo.dao.UserXMLDao">
    <select id="findUserById" parameterType="Integer" resultType="com.example.demo.bean.User">
        SELECT * FROM users WHERE id = #{id}
    </select>
</mapper>
```

在application.yaml中添加配置：

```yaml
mybatis:
    mapper-locations: classpath:mapper/*.xml
```

