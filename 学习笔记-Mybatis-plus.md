# Mybatis-plus学习笔记

##  1.简介

**MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。**

**特性**

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 2.项目配置

### 1.MybatisPlusConfig

```java
//分页插件
@Bean
public PaginationInterceptor paginationInterceptor() {
    return new PaginationInterceptor();
}
```

```java
//逻辑删除
@Bean
public ISqlInjector sqlInjector() {
    return new LogicSqlInjector();
}
```
### 2.MyMetaObjectHandler

```java
@Component//一定不要忘记把MyMetaObjectHandler加入ioc容器当中
public class MyMetaObjectHandler implements MetaObjectHandler {
    //插入时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("gmtCreate", LocalDateTime.now(), metaObject);
        this.setFieldValByName("gmtModified", LocalDateTime.now(), metaObject);
    }

    //更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("gmtModified", LocalDateTime.now(), metaObject);
    }
}

```

### 3.UserMapper

```
//在对应的mapper上继承基本类的 BaseMapper
@Mapper
@Repository
public interface UserMapper extends BaseMapper<User> {

}
```

### 4.User

```java
public class User {
    //默认使用ID_WORKER作为全局唯一id
    //AUTO，使用AUTO时，数据库也需要进行设置自增
    //NONE，不使用id自增
    //@TableId(type = IdType.ID_WORKER)
    private Long id;
    private String name;
    private Integer age;
    private String email;
    //逻辑删除
    @TableLogic
    private Integer deleted;
    //字段添加填充
    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime gmtCreate;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime gmtModified;
}
```

### 5.application.yml

```xml
spring:
  datasource:
    username: root 
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis-plus?serverTimezone=UTC
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl#日志
  global-config:
    db-config:
      logic-delete-value: 1#逻辑删除
      logic-not-delete-value: 0

```



### 6.常用CRUD

```java
List<User> users = userMapper.selectList(null);
userMapper.insert(user);
userMapper.updateById(user);
User user = userMapper.selectById(1);
List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2));//查询多个
List<User> users = userMapper.selectByMap(objectObjectHashMap);//利用map查询
Page<User> userPage = new Page<>(1,5);//分页查询
userMapper.selectPage(userPage,null);
userMapper.deleteById(1242806634540007425L);
userMapper.deleteBatchIds(Arrays.asList(1242755960355708930L));//删除多个
```



### 7.WrapperTest

```java
QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
userQueryWrapper.isNotNull("name").ge("age",22);
List<User> users = userMapper.selectList(userQueryWrapper);
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.eq("name","Billie");
User user = userMapper.selectOne(wrapper);
QueryWrapper<User> age = new QueryWrapper<User>().between("age", 20, 30);
userMapper.selectCount(age);
QueryWrapper<User> like = new QueryWrapper<User>().like("name", "J");
List<User> users = userMapper.selectList(like);
```







## 3.注意事项

- mybatis与mybatis-plus不要一起导包，以免发生冲突