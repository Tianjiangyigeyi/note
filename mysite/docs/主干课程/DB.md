# 面向期末考试的DB复习

## Problem1: Relational Model and SQL

### 关系代数

#### 集合运算符

 并差交, 笛卡尔积

#### 关系运算符

- 选择: $\sigma_{条件}(表名)$
- 投影: $\Pi_{列名}(表名)$, 投影后要去除重复行
- 连接: 常用的有自然连接$\bowtie$
- 除: $\div$ 笛卡尔积逆运算

**关系代数解题方法**: 

- 常用格式 $\Pi_{??}(\sigma_{??}(表\bowtie表))$
- 如果题目中提到了"全部"的字眼, 则要考虑是否为除运算 
- 偶尔可以使用差运算简化表达式

### SQL语法

```sql
create table student(
	id int,
    name varchar(10)
    primary key(id),
    foreign key(name) references table2(name)
);

alter table student add score int;
alter table student add unique(name);
alter table student drop score;
alter table student alter column score double;

drop table student;

select id
from student
where 
group by () having
order by () asc/desc

insert into student(id, name)
values(1, 'LiHua');

update student set name = "Tom" where id = 2;

delete from student where id = 1;

create view 视图名 (列1, 列2)
as 子查询

drop view 视图名
```



## Problem2: E-R Model

![image-20230610141553712](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202306101416826.png)

## Problem3: Relational Formalization

### 函数依赖(function dependency)

**完全函数依赖**(Full functional dependency): x推出y且x的子集无法推出y, 则称y完全依赖x

**部分函数依赖**(Partial functional dependency): x推出y且x的子集可以推出y, 则称y部分依赖x

**传递函数依赖**(Pass functional dependency): x->y->z, 则称z传递依赖x

### 码(key)

**候选码**(candidate key): 最小的可以推出所有属性的属性组

> For relation schema R(A, B, C, D, E) with functional dependencies set F={A->B, BC->D, C->A}, **Find all candidate keys** of R

- 解题步骤:
  - 找出一定属于候选码的属性C, E(未出现在右边), 可能属于候选码的属性AB(两边都出现), 一定不属于候选码的属性D(只出现在右边)
  - 一个一个将可能属于候选码的属性加入到一定属于候选码的属性的属性组里, 进行**闭包运算**, 看是否可以推出全部属性, CE

**闭包运算**(Compute the Closure of attribute set)

> For relation schema R(A, B, C, D, E) with functional dependencies set F = {A->CD, C->B, B->D, B->E}, **Compute the Closure of attribute** set {B}

- 解题步骤:
  - 令X = {B}
  - X(0) = B
  - X(1) = BDE
  - X(2) = BDE
  - 由于X(2) = X(1), 所以{B}的闭包是{B,D,E}

**超码**(super key): 可以推出所有属性的属性组, 候选码是超码的子集

**主码**(primary key): 从候选码中挑出一个作为主码, 主码可以简称码

**主属性**(primary attribute): 包含在任何一个候选码中的属性

**非主属性**(non-primary attribute): 不包含在任何一个候选码中的属性

**外码**(foreign key): 关系模式(schema)R中, 若有一个属性或属性组不是R的码但是是另一个关系模式S的码, 则称x是R的外码

**全码**(all key): 整个属性组都是码, 则称为全码

### 范式(normal form)

**1NF**: 所有属性不可分割

**2NF**: 消除部分函数依赖, 即看非主属性是否可以由完整的候选码推出而不是候选码的子集推出

**3NF**: 消除非主属性对码的传递函数依赖, 即看非主属性是否由候选码直接推出

**BCNF**: 消除任何属性对码的传递函数依赖, 即每一个依赖关系左侧都必须包含码

### 最小函数依赖集(canonical cover)

**求最小函数依赖集的方法**:

- 拆分右侧, 如将A->BC拆为A->B, A->C
- 去除自身求闭包, 对每一个函数依赖关系的左侧求闭包, 如果求出来的闭包包括了这个依赖, 这删除这个依赖
- 左侧最小化

### 模式分解(decompose)

模式分解的准则: 无损连接, 保持函数依赖

无损连接: 分解后再次自然连接, 与分解前相同

**判断无损连接的方法**: 

- 画表格。列表示所有的属性，有多少属性就画多少个属性列。行表示分解后的关系，有几个关系就画几个关系行。
- 根据每一行关系进行判断。找到关系中的每个属性对应第几列，并在相应的位置上标为a ，下标j是表格里的列数。其余关系中不存在的属性则标为b ，ij是表格对应的行数和列数。
- 依次对函数依赖集里的各个依赖关系进行考察。例如有XY Z。在属性列中找到X和Y，观察X和Y的行列上是否有相同的标记（b的下标要相同）。若有，则查看它们对应在属性列Z上的各个标记。其中若有a ，则将属性列上的这些标记全部改为a 。若没有a ，则找到i值最小的b ，将这些标记全部改为b 。
- 反复执行以上操作，直到某一行全部变为a为止，则表明具有无损连接性。否则不具有无损连接性。

![image-20230608195217101](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202306081952201.png)

**模式分解的方法:** 

> For relation schema R(A, B, C, D, E) with functional dependencies set F={A->B, BC->D, C->A}, Decompose the relation R into a collection of BCDF relations

- 解题步骤:
  - 求出最小函数依赖集,Fmin =  {A->B, C->D, C->A}
  - 观察U, 找出未在Fmin中出现的属性, 将其分为一个集合{E}
  - 观察Fmin, 若有多个依赖关系的左侧相同, 则均划分到同一个集合中去, 若没有相同的, 则仅将该依赖关系划分到一个集合, {A, B}, {A, C, D}
  - 求出Fmin的候选码, 若候选码中未在上述分类中出现, 则单独将候选码分为一类{C, E}
  - 则可将R分为{A, B}, {A, C, D}, {C, E}, {E}

## Problem4: XML

## Problem5: B+ Tree

## Problem6: Query Processing

## Problem7: Concurrency Control

### 事务

事务的四个特性: ACID

- A: Atomicity 原子性, 一个事务要么全做要么全不做
- C: Consistency 一致性, 事务执行的结果必须一致, 如果一个事务被迫中断, 即事务中的一些操作做了, 剩下的还没做, 且做了的对数据库造成的修改, 已经存入物理数据库的话, 就会造成数据库处于不一致的状态, 因此事务要么全做要么全不做, 一致性和原子性是息息相关的
- I: Isolation 隔离性, 一个事务的执行不能被其他事务干扰
- D: Durability 持续性, 一个事务一旦提交, 它对数据库中数据的改变应该是永久性的

### 事务调度的准则

- 一组事务的调度必须保证：包含了所有事务的操作指令；一个事务内部的指令顺序必须保持不变
- 并行事务调度必须保证：可串性化，将所有可能的串行调度结果推演一遍，对于某个具体的并行调度再执行一遍，看是否能与某个串行调度的结果相同
- 判断可串性化的充分条件: 冲突可串性化（冲突可串性化一定是可串性化调度，但可串性化调度不一定是冲突可串性化）

### 封锁

X锁:写锁, S锁:读锁

**封锁协议**: 

- 一级封锁协议：写前加写锁，事务结束释放写锁；可防止丢失修改

- 二级封锁协议：写前加写锁，读前加读锁，读完释放读锁，事务结束释放写锁；可防止丢失修改和读脏数据

- 三级封锁协议（常用：支持一致性维护）：写前加写锁，读前加读锁，事务结束释放各锁；可防止丢失修改、读脏数据和不可重复读

  > 总结: 都是写前加写锁, 事务结束释放写锁, 区别在于一级无读锁, 二级读完释放读锁, 三级事务结束释放读锁

- 两段锁协议(2PL): 所有的事务必须分两个阶段对数据项加锁和解锁。 即 事务分两个阶段 ，第一个阶段是 获得封锁 。 事务可以获得任何数据项上的任何类型的锁，但是不能释放；第二阶段是 释放封锁 ，事务可以释放任何数据项上的任何类型的锁，但不能申请。

  > 三级封锁协议可以保证并发操作的正确性，但由于其太过严苛，对并发度有负面影响。三级封锁协议实际是两段锁协议的特例，是更严格的两段锁协议
  >
  > 事务遵循两段锁协议是可串性化的充分条件，遵循两段锁协议是可能发生死锁的

## Problem8: Aries Recovery Method

## Reference

- b站up主[没睡醒冬](https://space.bilibili.com/949043)的速成课

- ZJU 18, 19级DB期末考试试卷
