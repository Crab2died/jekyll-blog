---
layout: post
title: Database
thumbnail: /images/db/mysql_logo.png
date: 2018-07-06 23:15:27 +0800
categories: Database
tags: Database
---

## 一. 四大特性(CIAD)
   1. **原子性(Atomicity)**: 要么全成功，要么全失败，失败会回滚。
   2. **一致性(Consistency)**: 一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执
      行之前和执行之后都必须处于一致性状态。
   3. **隔离性(Isolation)**: 隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，
      不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。
   4. **持久性(Durability)**: 一旦事物提交成功，那么数据库中的数据就是永久改变的，即使系统故障也不会丢失提交的事物。
   
## 二. 数据库隔离级别
### 1. 不考虑隔离级别造成的问题
   1. 更新丢失: 2个事物同时更新一条数据会有一个事物把另一个事物的更新覆盖了，这是因为系统没有任何操作隔离导致的.
   2. 脏读: 一个事物读取了另一个事物还未提交事物的数据
   3. 不可重复读: 一个事务对同一行数据重复读取两次，但是却得到了不同的结果
      * 虚读: 一个事物去改变数据改变后发现还有数据为按要求改变，是因为另一事物也做了改变的缘故
      * 幻读: 同一个事物内多次查询返回的结果不一致，是因为另一事物对数据进行了改变
     
### 2. 隔离级别(低 -> 高)
   1. **Read uncommitted(未授权读取、读未提交)**
      * 当一个事物在执行写操作时，则不允许另一事物执行写操作，可执行其他操作，可由排他锁来实现
      * 解决了更新丢失问题，但会出现脏读
   2. **Read committed(授权读取、读提交)**
      * 读取数据事物允许其他事物继续访问该数据，但未提交的数据禁止其他事物访问
      * 避免了脏读，但可能会出现幻读
   3. **Repeatable read(可重复读取)**
      * 读数据的事物将禁止写事物(可以读事物),写事物将禁止所有其他事物
      * 避免了脏读和不可重复读，但可能会出现幻读
   4. **Serializable(序列化)**
      * 事物严格按照顺序一个一个执行
      * 能避免所有情况，但会极大影响系统性能
    
### 3. 扩展
   1. 大多数数据库的默认级别就是Read committed，比如Sql Server , Oracle。  
        Mysql的默认隔离级别就是Repeatable read。 
   2. MySQL数据库隔离级别管理  
      * 查看隔离级别: `select @@tx_isolation`  
      * 修改隔离级别:    
      `set [glogal | session] transaction isolation level 隔离级别名称`如`set transaction isolation level read-committed`  
      或者`set tx_isolation='隔离级别名称'` 如 `set tx_isolation = 'read-committed'`
   3. 隔离级别的设置只对当前连接有效  

### 4. MySQL悲观锁、乐观锁、共享锁与排他锁
   1. **悲观锁**: 在操作数据时都认为会出现数据冲突，所以每次都会去获取锁，只有获取锁后才能对数据操作。
   2. **乐观锁**: 在操作数据都认为不会发生数据冲突，可以直接操作，一般由用户通过版本号自己实现。
   3. **共享锁**: 共享锁指的就是对于多个不同的事务，对同一个资源共享同一个锁。在SQL后面加上`lock in share mode`表示使用共享锁。
   4. **排他锁**: 排它锁与共享锁相对应，就是指对于多个不同的事务，对同一个资源只能有一把锁。与共享锁类型，在需要执行的
      语句后面加上`for update`就可以了。
    
## 三. 数据库的三大范式(Normal Form)
   1. **第一范式(1NF)**：强调的是列的原子性，即列不能够再分成其他几列。
   2. **第二范式(2NF)**：一个表必须有一个主键，二是其他列必须完全依赖于主键，而不能依赖主键一部分
   3. **第三范式(3NF)**：非主键列必须直接依赖于主键，不能存在传递依赖。即不能存在：非主键列A依赖于非主键列B，非主键列B依赖于主键的情况。
   
## 四. 数据库数据结构
   1. B+ Tree
      * 相较于B Tree多了一层，只有树的叶子节点存储实际数据，方便了range查询操作

## 五. mysql的MyISAM与InnoDB
   1. `show engines` 查看当前引擎与默认引擎
   2. 设置表引擎 CREATE后面加`CREATE TABLE "" () ENGINE=MyISAM` 或 `alter table 表名 ENGINE = InnoDB`
   3. 比较  
      * MyISAM不支持事物,InnoDB支持  
      * InnoDB支持行锁定,MyISAM不支持，只支持表锁定   
      * InnoDB支持外键,MyISAM不支持  
      * MyISAM支持全文检索,InnoDB不支持  
      * MyISAM内置一个数据计数器，能很容易得出`SELECT COUNT(*) FROM TABLE_NAME`结果  
      * MyISAM索引数据与表数据分离，而InnoDB索引与表数据紧密关联  
   
## 六. SQL功能
   1. **数据定义(DDL)**：用于定义SQL模式、基本表、视图和索引的创建和撤消操作
   2. **数据操纵(DML)**：数据操纵分成数据查询和数据更新两类。数据更新又分成插入、删除、和修改三种操作
   3. **数据控制**：包括对基本表和视图的授权，完整性规则的描述，事务控制等内容
   4. **嵌入式SQL使用规定**：涉及到SQL语句嵌入在宿主语言程序中使用的规则
  