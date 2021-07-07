# 索引
```
索引是一种加快查询数据的数据结构，目的是加快查询的效率。
a. 索引减少了需要扫描的数据行的数量，将原来的全表扫描，使用特定的数据结构，能够快速的定位数据行。
b. 使用有序的索引，避免了排序，将原来的随机的IO操作，变成了顺序的IO操作，执行有序。
```
* 索引种类
```
主键索引（PRIMARY KEY）：
主键索引一般是在创建表的时候指定的主键，一个表只有一个主键索引，特点是 唯一、非空。
若未指定主键，Mysql默认会生成一个6字节长的ROWID作为隐含的自动增长主键作为主键索引。

唯一索引（UNIQUE）：
与主键索引的区别是唯一索引允许为空，但以NULL为条件查询不走索引，一般使用 空字符 代替 NULL。
其特点就是唯一性，在实际中更多的是用来保证数据的唯一性，否则可用普通索引代替。
组合索引也可以是唯一索引，只要保证创建的列值是唯一的。

普通索引（INDEX）：
不多说。

组合索引（INDEX）：
组合索引即用多个字段创建一个索引，相对于多字段的单列索引，组合索引的查询效率更高，其查询遵循 最左前缀原则。

全文索引（FULLTEXT）：
针对一些大的文本字段创建的索引。

```
* 回表查询
```
回表查询简单来说就是「通过二级索引查询数据，得不到完整的数据行，需要再次查询主键索引来获得数据行」。

聚簇索引：
主键索引是聚簇索引，其叶子节点保存着完整的数据行。

非聚簇索引：
其叶子节点保存主键索引ID，通过这类索引查询数据需要回表查询。
```
* 索引覆盖
```
查询数据列只用从索引中就能够取得，不必从数据表中读取，换句话说查询列要被所使用的索引覆盖。
这类查询可以避免再次回表查询，速度较快。
所以「组合索引」在一定程度上能够避免「回表查询」，因为组合索引的叶子节点包含多个字段。
```
* 最左前缀原则
```
组合索引组织B+Tree的数据方式：
在创建索引时，排序优先级按照组合中从最左边的顺序优先排序，遵循 最左前缀原则。

组合索引的查询过程：
对于 (name,age,sex) 的组合索引，name,age,sex/name,age/name 都会走索引，但是 age,sex 不走索引。

Like模糊查询：
模糊查询字符串也是遵循最左前缀原则的，'x%'会走索引，而'%x%'不走索引。
```
* 索引原则和优化
```
a. 索引列不要在表达式中出现，这样会导致索引失效，如：SELECT * FROM table WHERE id+1=5 。
b. 尽量不要在条件NOT IN、<>、!= 中使用索引。
c. 在索引列的字段中不要出现NULL值，NULL值会使索引失效，可以用特殊的字符比如空字符串或者0来代替NULL值。
d. 联合索引的查询应该遵循最左前缀原则。
e. 一般对于区别性比较大的字段建立索引，在联合索引中区别性比较大(识别度比较高)放在最前面，提高索引的命中率。
f. 索引的大小要适度，不宜过大，避免索引的冗余。
```
* 常见问题
```
为什么 MySQL 官方建议使用自增主键作为表的主键？
a. 可以避免页分裂。
b. 性能好。
c. 数据存储空间占用小。

页分裂：
MySQL在底层又是以数据页为单位来存储数据的，一个数据页大小默认为 16k，也就是说如果一个数据页存满了，mysql 就会去申请一个新的数据页来存储数据。
为了保证顺序行，如果时自增ID，本身就是顺序写，而自定义ID需要放在指定的位置，会导致一条数据分散在两个页空间。


```
# 锁
* MySQL 中提供了几类锁？
```
MySQL提供了全局锁、表级锁和行级锁，其中InnoDB支持表级锁和行级锁，MyISAM只支持表级锁。

```
* 什么是共享锁？
```
共享锁又称读锁（Read Lock），是读取操作时创建的锁。
如果事务T对数据A加上共享锁后，则其他事务只能对A再加共享锁，不能加排他锁。获准共享锁的事务只能读数据，不能修改数据。


```
* 什么是排它锁？
```
排他锁又称写锁（Write Lock）：
如果事务T对数据A加上排他锁后，则其他事务不能再对A加任任何类型的锁。获准排他锁的事务既能读数据，又能修改数据。

for update

lock table read/write

```
* 悲观锁和乐观锁有什么区别？
```

```


* InnoDB 如何实现行锁？
```

```
* 优化锁方面你有什么建议？
```

```



# 事务
* 并发问题
```
脏读：
一个事务读取到另一个事务还未提交的数据。

不可重复读：
不可重复读是指在同一个事务内，两次相同的查询返回了不同的结果。

幻读：
一个事务得插入或者删除，影响到另一个事务得查询范围结果。


```
* 隔离级别
```

```

# 存储引擎
* InnoDB与MyISAM
```
MyISAM可以不存在主键索引，但InnoDB必须主键索引。

```

