# C++标准库体系结构与内核分析

> 所谓**泛型**编程(GP, Generic Programming), 就是使用**模板**为主要工具来编写程序.
>
> 而**STL**(Standard Template Library 标准模板库)是GP最成功的作品



- C++标准库的header files 不带.h, 例如 `#include <vector>`
- 新式C header files 不带.h, 例如`#include <cstdio>`
- 旧式C header files(带有.h)仍然可用, 例如`#include <stdio.h>`



## STL体系结构基础介绍

**STL六大部件:**

- **容器(Containers)**
- 分配器(Allocators)
- **算法(Algorithm)**
- 迭代器(Iterators)
- 适配器(Adapters)
- 仿函式(Functors)

![image-20230420200610997](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304202006100.png)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

using namespace std;

int main()
{
    int ia[6] = {1, 2, 3, 4, 5, 6};
    vector<int,allocator<int>> v1(ia, ia + 6);

    cout <<   (v1.begin(), v1.end(), not1(bind2nd(less<int>(), 3)));

    return 0;
}
```



STL的容器基本上都是采用的"前闭后开"区间, 即begin是头, end是尾的最后一个



## 容器之分类及各种测试

>  为了选用更高效的容器, 我们需要了解各种容器的特性

两大种类:

- Sequence Containers(循序式容器): Array, Vector, Deque, List, Forward-List(数列和链表)
- Associative Containers(关联式容器): Set/Multiset, Map/MultiMap(红黑树)
- Unordered Containers(不定序式容器): Unordered Set, Unordered map(哈希表)



- vector 的内存增长是两倍增长, 而增长这个过程是在另外一个地方开辟空间, 再拷贝过去, 是一个非常缓慢的过程

- algorithm中的算法都是全局函数, 可以在调用时显示地指出它是全局函数, 即在函数名前加双冒号: `::find(c.begin(), c.end(), target);`, 如果不加双冒号, 编译器也会在当前作用域寻找寻找不到的时候转向去寻找全局函数.   

- 有全局函数sort, 有的容器自带的也有sort, 如果有自带的sort请用自带的

- queue和stack的容器底层实现是用deque, 因为deque功能更强大, 所以很多人把它们(stack和queue)称为容器的适配器(Container Adapters)

- 由于stack和queue的特性, c++不提供他的迭代器, 防止你破坏这个容器的特性
- 关联式容器multiset/set, 红黑树, 插入慢, 查找快
- multimap/map, 红黑树, 和set的区别: 每个节点有两个数据, 一个是值一个是键

- 在不定序式容器中, 若某一个bucket中的元素数超过了bucket的数量, 则需要重排 

- multi代表其中的元素可以重复, 没有multi则不可以重复(key和value都重复才重复)



## 分配器

容器背后需要分配器支持其内存的分配, 你不用写容器已经有了默认的分配器

- 容器都是标准库规定的
- 容器都是有模板的且模板有第二参数, 且都有默认分配器
- `std::allocator`在`<memory>`下, 若要使用其他的allocator, 请自行`#include <ext/...>`
- 不建议直接使用分配器, 分配器最好都要搭配容器使用
  - 因为分配器需要free的时候需要指明当初分配多大的内存, 这显然是不人性化的, 不如使用`malloc/free`或`new/delete`



常规进行内存分配使用`malloc/free`时, 除了你定义的`size`还要有一部分的空间作为cookie, (你需要知道malloc的内存的大小), 最基础的分配器就是调用malloc和free, 但是我们可以想到, 对于一个容器进行malloc时, 并不需要每一个元素都有一个cookie(因为他们的大小总是确定好了的), 可以一下分配一大块, 再在内部切割, 这样可以省下很多内存