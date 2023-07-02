# 面向期末考试的DB复习

> 由于课改，该复习框架已经过时，但建立之初并没考虑课改之后的情况，有部分内容与大标题无关或者关联较小。

## Problem1: Relational Model and SQL

### 关系代数

#### 集合运算符

 并差交, 笛卡尔积

#### 关系运算符

- 选择: $\sigma_{条件}(表名)$
- 投影: $\Pi_{列名}(表名)$, 投影后要去除重复行
- 连接: 常用的有自然连接$\bowtie$
- 除: $\div$ 笛卡尔积逆运算

### 关系代数解题方法

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

### 最小函数依赖集(canonical cover)与正则覆盖

**求最小函数依赖集的方法**:

- 拆分右侧, 如将A->BC拆为A->B, A->C
- 去除自身求闭包, 对每一个函数依赖关系的左侧求闭包, 如果求出来的闭包包括了这个依赖, 这删除这个依赖
- 左侧最小化

**求正则覆盖则是在最小函数依赖的基础上合并函数依赖即可**

### 模式分解(decompose)

模式分解的准则: 无损连接, 保持函数依赖

无损连接: 分解后再次自然连接, 与分解前相同

### 判断无损连接的方法

- 画表格。列表示所有的属性，有多少属性就画多少个属性列。行表示分解后的关系，有几个关系就画几个关系行。
- 根据每一行关系进行判断。找到关系中的每个属性对应第几列，并在相应的位置上标为a ，下标j是表格里的列数。其余关系中不存在的属性则标为b ，ij是表格对应的行数和列数。
- 依次对函数依赖集里的各个依赖关系进行考察。例如有XY Z。在属性列中找到X和Y，观察X和Y的行列上是否有相同的标记（b的下标要相同）。若有，则查看它们对应在属性列Z上的各个标记。其中若有a ，则将属性列上的这些标记全部改为a 。若没有a ，则找到i值最小的b ，将这些标记全部改为b 。
- 反复执行以上操作，直到某一行全部变为a为止，则表明具有无损连接性。否则不具有无损连接性。

![image-20230608195217101](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202306081952201.png)

### 模式分解的方法

> For relation schema R(A, B, C, D, E) with functional dependencies set F={A->B, BC->D, C->A}, Decompose the relation R into a collection of BCDF relations

- 解题步骤:
  - 求出最小函数依赖集,Fmin =  {A->B, C->D, C->A}
  - 观察U, 找出未在Fmin中出现的属性, 将其分为一个集合{E}
  - 观察Fmin, 若有多个依赖关系的左侧相同, 则均划分到同一个集合中去, 若没有相同的, 则仅将该依赖关系划分到一个集合, {A, B}, {A, C, D}
  - 求出Fmin的候选码, 若候选码中未在上述分类中出现, 则单独将候选码分为一类{C, E}
  - 则可将R分为{A, B}, {A, C, D}, {C, E}, {E}

## Problem4: B+ Tree

> 大概了解，以后有空再写

插入：达到degree就分裂，左小右大于等，叶子由链表连接，存实际数据(父子可重复)，上面都是索引(父子不重复)

删除：左边优先不能小于该degree要求的最小数据个数

block transfer：io；seek：在磁盘中定位到首地址

## Problem5: Query Processing

### 列式存储

- 查：磁盘io节省十倍，数据压缩节省十倍=百倍效率
- 增：多列族存储
- 删：列族标记删除
- 改：列族标记更新

缺点：写入次数多，增删改效率存在问题

### 索引

索引（index）是数据库中用于提高数据检索性能的数据结构。它可以加快查询速度，减少数据库系统需要扫描的数据量。

在数据库中，主要有两种索引类型：主索引（primary index）和二级索引（secondary index）。

主索引是基于数据表的主键构建的索引。主键是用于唯一标识每条记录的列或列组合。主索引将数据按照主键的值进行排序，并提供快速的按照主键查找记录的能力。主索引是数据库表的关键索引，每个表只能有一个主索引。

而二级索引是基于数据表的非主键列构建的索引。它可以加速根据非主键列进行查询的操作。二级索引的构建方式与主索引类似，它会按照指定的非主键列的值进行排序，并生成一个索引结构，用于快速定位符合查询条件的记录。一个表可以有多个二级索引，每个二级索引都是独立的。

使用二级索引可以在数据库查询时减少数据的扫描量，提高查询效率。当使用非主键列作为查询条件时，数据库系统可以先根据二级索引定位到符合条件的记录，然后再通过主索引或者其他方式获取完整的数据。

然而，相比于主索引，二级索引也存在一些限制和性能方面的考虑。首先，构建和维护二级索引需要占用额外的存储空间和计算资源。其次，当进行插入、更新或删除操作时，数据库需要同时更新主索引和相关的二级索引，这可能会带来一定的开销。因此，在设计数据库时，需要权衡使用二级索引的利弊，并根据实际需求进行选择。

#### 求size的题

n是数量，I是length长度，v是varies不同项个数

#### 求b+树height

- 找分支因子 BL = (块大小 - 指针大小) / (键值大小 + 指针大小) + 1
- 树高 = log𝐵𝐿(𝑁 + 1), N是表中的记录数

## Problem6: Concurrency Control

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

### **封锁协议**

- 一级封锁协议：写前加写锁，事务结束释放写锁；可防止丢失修改

- 二级封锁协议：写前加写锁，读前加读锁，读完释放读锁，事务结束释放写锁；可防止丢失修改和读脏数据

- 三级封锁协议（常用：支持一致性维护）：写前加写锁，读前加读锁，事务结束释放各锁；可防止丢失修改、读脏数据和不可重复读

  > 总结: 都是写前加写锁, 事务结束释放写锁, 区别在于一级无读锁, 二级读完释放读锁, 三级事务结束释放读锁

- 两段锁协议(2PL): 所有的事务必须分两个阶段对数据项加锁和解锁。 即 事务分两个阶段 ，第一个阶段是 获得封锁 。 事务可以获得任何数据项上的任何类型的锁，但是不能释放；第二阶段是 释放封锁 ，事务可以释放任何数据项上的任何类型的锁，但不能申请。

  > 三级封锁协议可以保证并发操作的正确性，但由于其太过严苛，对并发度有负面影响。三级封锁协议实际是两段锁协议的特例，是更严格的两段锁协议
  >
  > 事务遵循两段锁协议是可串性化的充分条件，遵循两段锁协议是可能发生死锁的

### 证明2PL不是冲突可串行化的必要条件

考虑以下两个事务T1和T2，以及两个数据项A和B：

T1：lock(A)，lock(B)，读取A的值，读取B的值，unlock(B)，unlock(A) T2：lock(B)，lock(A)，读取B的值，读取A的值，unlock(A)，unlock(B)

假设在某个并发调度中，T1和T2按照以下顺序执行：

T1(lock(A)) T2(lock(B)) T1(lock(B)) T2(lock(A)) T1(读取A的值) T2(读取B的值) T1(读取B的值) T2(读取A的值) T2(unlock(A)) T1(unlock(B)) T2(unlock(B)) T1(unlock(A))

观察上述调度，我们可以看到T1和T2之间没有冲突操作，因为它们按照不同的顺序锁定和解锁数据项。然而，该调度的结果与某个串行执行的结果是不等价的。

考虑以下串行执行的顺序：

T1(lock(A)) T1(读取A的值) T1(lock(B)) T1(读取B的值) T1(unlock(B)) T1(unlock(A))

在上述串行执行中，T1和T2是按照顺序执行的。然而，与之前的并发调度相比，读取操作的顺序发生了改变。因此，2PL协议并不能保证所有冲突可串行化调度的结果都与某个串行执行的结果等价。

## Problem7: Aries Recovery Method

#### ARIES

利用语义的恢复和隔离算法(Algorithm for Recovery and Isolation Exploiting Semantics)是一种基于**WAL**的故障恢复机制。

> WAL(Wrie-Ahead Logging) 协议要求更新任意 Page 都要记录 Log，并且
>
> - 必须保证所有 Page 刷盘之前与它有关的所有 Log 已经刷盘。
> - 必须保证事务提交前该事务的所有 Log 已经刷盘。

**ARIES 的核心思想可以总结为 4 点：**

- 在数据落盘之前，所有写操作都必须记录在日志中并落盘

- 必须使用 **Steal + No-Force** 缓存管理策略

  > - Steal: 允许将未提交事务的改动刷盘。
  >
  > - No-Force: 不必在事务提交时把该事务的所有改动刷盘。
  >
  > 显然，WAL 协议完美兼容 STEAL/NO-FORCE 的策略。我们可以为一个更新操作记录一条 Redo Log 用来指示我们在 Recovery 期间如何 Redo 这个更新操作；记录一条 Undo Log 用来指示我们在（Rumtime 或者 Recovery 期间）事务回滚的时候如何 Undo 这个更新操作。（一般在 Log 数据量小的情况下，这两条 Log 会合并成一条。）

- 当 DBMS 重启时，按照日志记录的内容重做数据，恢复到故障发生前的状态
- 在 undo 过程中记录 undo 操作到日志中，确保在恢复期间再次出现故障时不会执行多次相同的 undo 操作

WAL中的每条日志记录都需要包含一个全局唯一且一般是单调递增的log sequence number (**LSN**)

#### 八股解题步骤：

![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202306151347517.webp)

1. 找DPT（diety page table）中最小的RecLSN（该page最早被修改，而pageLSN是page最后一次被修改）作为redo的起始点
2. 将checkpoint的uncommitted txns表（ATT，Active Transactions Table）更新，最早的一条LSN为undo的终点
3. 求Analysis之后的DPT，只需要在checkpoint之后找所有修改记录的log而不用管是否commit
4. 求Analysis之后的undo list，只需从当前crash的点走到undo end找没有commit或者abort的txns即可
5. 求recovery之后的value，只需要从redo开始往下走，只用管commit的txn即可
6. 求recovery之后的additional log record只需记录是如何从当前crash点的值过渡到第5点求出的value，再将该事务abort

## Reference

选择题：

1. SQL转关系代数表达式
2. 级联更新与删除
3. 正则覆盖
4. 关系代数表达式变形
5. B+树索引查询求block transfer和seek的次数
6. 列式存储的优缺点
7. 判断分解是否保持函数依赖
8. 主键索引与二级索引
9. 估计查询的size
10. hash join
11. 外部排序

大题：

1. 写SQL语句
2. 画ER图
3. 证明2PL不是冲突可串行化的必要条件
4. ARIES算法
5. LSM模型

- b站up主[没睡醒冬](https://space.bilibili.com/949043)的速成课

- ZJU 18, 19级DB期末考试试卷

