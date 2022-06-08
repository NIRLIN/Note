[TOC]

# 1.数据库设计

1. 用户表

```sql
CREATE TABLE `shop_user` (
`uid` INT(10) NOT NULL PRIMARY KEY COMMENT '用户id',
`username` VARCHAR(10) NOT NULL COMMENT '用户名',
`password` VARCHAR(10) NOT NULL COMMENT '密码',
`telephone` VARCHAR(10) NOT NULL COMMENT '手机'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
```

2. 商品表

```sql
CREATE TABLE `shop_product` (
`pid` INT(10) NOT NULL PRIMARY KEY COMMENT '商品id',
`pname` VARCHAR(10) NOT NULL COMMENT '商品名称',
`pprice` decimal NOT NULL COMMENT '商品价格',
`stock` INT(10) NOT NULL COMMENT '库存'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
```

3. 订单

```sql
CREATE TABLE `shop_order` (
`oid` INT(10) NOT NULL PRIMARY KEY COMMENT 'oid',
`uid` INT(10) NOT NULL COMMENT '用户id',
`username` VARCHAR(10) NOT NULL COMMENT '用户名',
`pid` INT(10) NOT NULL  COMMENT '商品id',    
`pname` VARCHAR(10) NOT NULL COMMENT '商品名称',
`pprice` decimal NOT NULL COMMENT '商品价格',
`number` INT(10) NOT NULL COMMENT '购买数量'
)ENGINE = INNODB DEFAULT CHARSET = utf8;
```

