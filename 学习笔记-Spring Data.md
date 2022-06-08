[TOC]

# 1.数据库设计

用户（id、姓名、年龄）

user（id、name、age）

# 2.SQL

```sql
CREATE DATABASE `user`;
CREATE TABLE `tb_user` (
`id` VARCHAR(10) NOT NULL PRIMARY KEY COMMENT 'id',
`name` VARCHAR(10) NOT NULL COMMENT '姓名',
`age` VARCHAR(10) NOT NULL COMMENT '年龄'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
```

# 3.创键Spring Boot项目

1. 创建项目
2. 选择spring-boot-starter-data-jpa与spring-boot-starter-web

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```
3. application.yaml

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/user?serverTimezone=UTC
```

4. Application
 ```java
   @SpringBootApplication
   public class Application {
   
   	public static void main(String[] args) {
   		SpringApplication.run(Application.class, args);
   	}
   
   }
 ```

5. dao

```java
@Repository
public interface UserDao extends JpaRepository<TbUser,Long> {

}
```

6. pojo

```java
@Entity
@Table(name = "tb_user")
public class TbUser {

  @Id
  private String id;
  @Column(length = 10)
  private String name;
  @Column(length = 10)
  private String age;
}
```

7. controller

```java
@RestController
public class HelloController {
    @Autowired
    private UserDao userDao;
    @RequestMapping("/hello")
    public String hello(){
        TbUser tbUser=new TbUser();
        tbUser.setId("1");
        tbUser.setName("nie");
        tbUser.setAge("10");
        userDao.save(tbUser);
        return "a";
    }
    @RequestMapping("/all")
    public String all(){
        List<TbUser> all = userDao.findAll();
        return all.toString();
    }
    @RequestMapping("/delete")
    public String delete(Long id){
        userDao.deleteById(id);
        return "a";
    }

}
```