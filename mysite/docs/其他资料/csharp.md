# C#

---

入门自

[01 基础类型，变量，函数_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1sy4y1u7cw/?p=1)

1. `class`:都是引用类型(被函数调用时直接访问地址对应的值),分配在**堆**

   ```csharp
   class myclass
   {
   	public int x;
       protected double y;
       ...
   }
   ```

2. `struct`:值类型(被函数调用时拷贝一个struct,不会影响原来那个struct的值),分配在**栈**,也可以有变量和函数, `ref`可以传递引用

3. `interface`:函数的集合,告诉别人有哪些函数可以供使用

   ```csharp
   interface A
   {
   	void Func();
   }
   ```

4. `继承`: 同c++的继承思想,

   ```csharp
   class B : A
   {
   	...
   }
   ```

5. `abstract`:抽象类,不可被实例化,因为由部分未实现的函数,等待子类去实现

6. `函数的覆盖`: virtual override 子类重写

7. 子类可以调用protected

8. 所有类型都有基类`System.Object`, `ToString()`函数可以重写

9. 泛型`<T>`可以用于类或函数，使用`where` 进行约束
10. 容器:
    - 动态数组: `List `可动态增长
    - 字典: `Dictionary` Key-Value对，用于查询
    - Hash集合: `Hashset`保证数据的唯一
    - 栈:`Stack` Push Pop 后进先出
    - 队列: `Queue` Enqueue Dequeue 先进先出
11. 函数指针: `delegate `可以存储函数，使用+=-= 增删，本质上是监听者模式
    - `Action<T1T2>` 无返回值的delegate
    - `Func<T1,T2,TResult>` 有返回值的delegate，返回值类型为TResult
12. 属性 `Attribute`,可以给类，变量，函数 添加额外的代码信息，配合反射使用
13. 反射:获取代码信息，可以获取一个程序集，类，函数，变量等信息，使用 `obiect.GetType()`或` typeof(T)`来获取类型信息
14. 宏:条件编译，通过定义宏来切换代码开关#if #else #endif #define #undef