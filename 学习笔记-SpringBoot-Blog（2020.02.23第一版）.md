#  1.需求分析

信息时代的今天，网络已经成为人们工作，学习的一部分，不断充实和改变着人们的生活。在网络中，构建一个个性化的博客，可以充分地表达自己的思想，通过发布文章展示个人才能，舒服个人情感；网友则可以根据主题发表个人的意见，表达自己的想法，与博主进行思想交流，乐在其中。

# 2.概要设计

![](http://assets.processon.com/chart_image/5e52708de4b02bc3ad5cc427.png)

# 3.详细设计

1. 用户管理

   用户的相关信息如下：用户ID、用户名、用户密码、用户邮箱、用户手机号、注册时间、是否管理员 、是否删除。

2. 博文管理

   博文的相关信息如下：博文ID、发布日期、博文标题、分类ID、标签名称、博文内容、是否推荐、是否置顶  、是否删除。

3. 分类管理

   分类的相关信息如下：分类ID、分类名称、分类别名、分类描述 。

4. 评论管理（删除）

   评论的相关信息如下：评论ID、评论日期、点赞数、发表用户id、评论文章ID、评论内容、父评论ID、是否置顶  、是否删除。

5. 友链管理

   友链的相关信息如下：友链ID、添加日期、友链内容、友链描述 。

## 1.sql

```sql
CREATE DATABASE `blog`;
CREATE TABLE `tb_user` (
`user_id` INT(10) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '用户id',
`user_name` VARCHAR(10) NOT NULL COMMENT '用户姓名',
`user_password` VARCHAR(15) NOT NULL COMMENT '用户密码',
`user_email` VARCHAR(20) NOT NULL COMMENT '用户邮箱',
`user_phone` VARCHAR(15) NOT NULL COMMENT '用户手机',
`user_register` datetime NOT NULL COMMENT '用户注册时间',
`user_birthday` date NOT NULL COMMENT '用户生日',
`user_admin` INT(1) NOT NULL DEFAULT 0 COMMENT '是否管理员',
`user_delete` INT(1) NOT NULL DEFAULT 0 COMMENT '是否删除'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
CREATE TABLE `tb_article`(
`article_id` INT(10) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '博文id',
`article_date` datetime NOT NULL COMMENT '博文发布日期',
`article_title` VARCHAR(30) NOT NULL COMMENT '博文标题',
`classes_id` INT(10) NOT NULL COMMENT '分类id，外键：分类id',
FOREIGN KEY(classes_id) REFERENCES tb_classes(classes_id),
`article_label` VARCHAR(20) NOT NULL COMMENT '博文标签',
`article_content` TEXT NOT NULL COMMENT '博文内容',
`article_like` INT(10) NOT NULL DEFAULT 0 COMMENT '博文点赞数量',
`article_reply` INT(10) NOT NULL DEFAULT 0 COMMENT '博文回复数量',
`article_browse` INT(10) NOT NULL DEFAULT 0 COMMENT '博文浏览数量',
`article_recommend` INT(1) NOT NULL DEFAULT 0 COMMENT '是否推荐',
`article_stick` INT(1) NOT NULL DEFAULT 0 COMMENT '是否置顶',
`article_delete` INT(1) NOT NULL DEFAULT 0 COMMENT '是否删除'
) ENGINE = INNODB DEFAULT CHARSET = utf8;
CREATE TABLE `tb_classes`(
`classes_id` INT(10) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '分类id',
`classes_name` VARCHAR(20) NOT NULL COMMENT '分类名称',
`classes_sort` VARCHAR(20) NOT NULL COMMENT '分类别名',
`classes_describe` VARCHAR(50) NOT NULL COMMENT '分类描述',
`classes_delete` INT(1) NOT NULL DEFAULT 0 COMMENT '是否删除'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
CREATE TABLE `tb_comment`(
`comment_id` INT(10) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '评论ID',
`comment_time` datetime NOT NULL COMMENT '评论日期',
`comment_like` INT(10) DEFAULT 0 NOT  NULL COMMENT '评论点赞数',
`comment_userid` INT(10) NOT NULL COMMENT '外键：发表用户id',
FOREIGN KEY(comment_userid) REFERENCES tb_user(user_id),
`comment_articleid` INT(10) NOT NULL COMMENT '评论文章ID',
FOREIGN KEY(comment_articleid) REFERENCES tb_article(article_id),
`comment_content` text NOT NULL COMMENT '评论内容',
`comment_fatherid` INT(10) NOT NULL COMMENT '父评论ID',
FOREIGN KEY(comment_fatherid) REFERENCES tb_comment(comment_id),
`comment_stick` INT(1) NOT NULL DEFAULT 0 COMMENT '是否置顶',
`comment_delete` INT(1) NOT NULL DEFAULT 0 COMMENT '是否删除'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
CREATE TABLE `tb_link`(
`link_id` INT(10) AUTO_INCREMENT NOT NULL PRIMARY KEY COMMENT '友链id',
`link_time` datetime NOT NULL COMMENT '友链添加日期',
`link_content` VARCHAR(100) NOT NULL COMMENT '友链内容',
`link_describe` VARCHAR(50) NOT NULL COMMENT '友链描述',
`link_delete` INT(1) NOT NULL DEFAULT 0 COMMENT '是否删除'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
```



# 4.程序设计

技术选择：

- SpringBoot
- themeleaf
- vue+layui
- mysql
- mybatis

# 5.测试与调整

# 6.错误

1. 2020.02.25 15:56，未配置WebMvcConfigurerAdapter 导致静态资源被过滤（最后发现，在编译文件中没有static文件）

2. 时间：2020年2月27日14:35:49，

   问题：Consider the following:
   	If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
   	If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).

   猜测原因：添加Druid为配置数据源

   原因：添加mybatis-spring-boot-starter启动器，未配置数据源

3. 时间：2020年2月27日23:26:52

   问题：mapper注入失败

   原因：**路径错误：**这属于项目结构的问题，项目启动时，只有@SpringBootApplication 所在的包被扫描。此启动类应处于最上层的目录中，这样项目启动时，该目录以及下级目录的类都会被扫描。

   经验：不可以自己创建test类

4. 时间：2020年2月27日23:35:34

   问题：mapper-locations、config-location分不清，后者可以再application.properties中直接扫描mapper文件
   
5. 时间：2020年2月28日18:04:29

   问题：java.lang.ClassNotFoundException: Cannot find class: localDateTime

   原因：mybatis找不到LocalDateTime

6. 时间：2020年2月28日20:14:45

   问题：能从数据库查出数据，但是无法映射到实体类中

   原因：mybatis没有开启驼峰命名规则与匈牙利命名规则之间的转换，之前学mybatis的·时候好像开启了，导致现在没有在application.yaml中开启命名映射map-underscore-to-camel-case: true
   
7. 时间：2020年2月29日14:29:01

   问题：当templates下还有一个文件夹时，idea没法扫描到，然后thymeleaf也无法在里面加载后台传过来的变量，虽然能用，但是没有任何提示。

   原因：发现return "/user/index";是无法找到的，thymeleaf需要return "user/index";thymeleaf默认的前缀是template/

   【活跃】林 14:22:39
   然后thymeleaf也无法在里面加载后台传过来的变量

   【活跃】林 14:22:56
   虽然能用，但是没有任何提示

