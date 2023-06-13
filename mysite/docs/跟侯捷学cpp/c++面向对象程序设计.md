# C++面向对象高级编程(上)

---

## 如何优雅地写一个类?

---

> 侯捷面向对象编程上篇第六章内容

```cpp
//该头文件并不完整,有些函数尚未给出,直接编译会出错,只把需要理解的地方标出,供参考

#ifndef _COMPLEX_//防卫式声明
#define _COMPLEX_

class complex
{
public:
    complex (double r = 0, double i = 0)//默认参数
        :re(r), im(i)//initializing list 初始化列表
    { }
    complex& operator += (const complex&);//成员函数,操作符重载
    double real() const { return re; }
    double imag() const { return im; }
    //只要不太长,编译器就会认为是inline函数,速度更快
    //tips:如果在class外部使用inline关键字,编译器将会尽量让他成为内联函数
    //只有成员函数可以使用类型限定符'const',只要该成员函数内不会改变re,im则建议加上'const'
private:
    double re, im;//私有成员不能被外部访问,体现了面向对象编程"封装"的思想
    
    friend complex& _doapl (complex*, const complex&);
    //友元函数,可以访问private成员
};

inline complex&
_doapl(complex* ths, const complex& r)//将会使用ths来保存结果,ths的值会改变,故不用const
{
	ths->re += r.re;
    ths->im += r.im;
    return *ths;
    //question:为什么返回值是*ths,而函数声明的返回值是 complex&
    //answer:*ths是一个complex类型,*ths并不是一个local变量,可以引用
}

inline complex&//考虑到返回值并不是local变量,可以使用引用传值
    //pass by reference 比 pass by value更快,能使用引用传值尽量使用
complex::operator += (const complex& r)//this指针是所有成员函数的隐含参数,指向调用该函数的对象
{
    return _doapl(this, r);
}

inline complex
operator + (const complex& x, const complex& y){
	return complex ( x.real () + y.real() ,
                     x.imag () + y.imag() );//临时声明一个complex对象,在这一行执行完毕后将会灰飞烟灭
}

//重载
inline complex
operator + (const complex& x, double y){
	return complex ( x.real () + y, x.imag () );
}

//传统的cout定义时不会考虑到程序员自己定义的各种类
//为了使用 cout << c1这种用法(c1是一个complex对象),我们考虑到操作符重载一定是作用于操作符右边
//假如在成员函数内操作符重载 '<<' 那将会写成 c1 << cout, 这并不符合程序员的习惯
//于是我们只能将该操作符重载定义为全局函数
#include <iostream>
ostream&//为了可以连续:cout << c1 << c2
operator << (ostream& os, const complex& x)
{
	os << '(' << real(x) << ',' << imag(x) << ')';
}

#endif // _COMPLEX_
```



## Class with pointer members

---

- 拷贝构造&拷贝赋值:一个一个bit拷贝过去,所以指针不能直接用编译器的拷贝(浅拷贝),只要类包含指针就要写出以下三个函数(**Big three**)
  - 构造函数接受的参数是自己的东西`String (const String& str);`则为**拷贝构造函数**
  - `String& operator = (const String& str);`操作符重载,**拷贝赋值**
  - 析构函数`~String();`:需要delete,防止内存泄露 
- 拷贝构造&拷贝赋值的区别:拷贝构造直接分配空间并赋值,拷贝赋值要先清除原来的内存空间(*这里要先**检测自我赋值***)



 ## 堆、栈与内存管理

---

- **stack**:大括号是一个作用域（scope），所谓stack就是作用域中的一块内存空间，**在函数内声明的任何变量，其所使用的内存都取自上述stack**

- **heap**:或成system heap（和数据结构中的heap不同），是指由**操作系统**提供的一块**global**内存空间，程序可以动态分配（allocated）从中获得若干区块（blocks），**任何时候new的对象分配的内存空间都取自heap**,因此,`new`的变量必须要`delete`不然将会内存泄露(指针死亡,指针指向的内存空间却没有)

- **静态变量**:若在作用域中用`static`关键字声明的变量,不会再作用域结束后消失

- **全局变量**:在`main`函数之前声明,其生命在整个程序结束后才会结束,可以把它视为一种`static obj`,其作用域是整个程序

- **new**:`new`先分配内存,再调用`ctor`

  - ```cpp
    Complex* pc = new Complex(1, 2);
    
    //编译器转化为:
    /*1*/void* mem = operator new( sizeof(Complex) );
    //分配内存,其中的operator new是一个比较特殊的函数名,其内部调用malloc(mem)
    /*2*/pc = static_cast<Complex*>(mem);
    //转型
    /*3*/pc->Complex::Complex(1, 2);
    //构造函数
    ```

- **delete**:`delete`先调用`dtor`,再释放内存

  - ```cpp
    String* ps = new String("Hello");
    ...
    delete ps;
    
    //编译器转化为:
    /*1*/String::~String(ps);
    //析构函数
    /*2*/operator delete(ps);
    //释放内存,其中的operator delete是一个比较特殊的函数名,其内部调用free(ps)
    ```

  - ![image-20230407112621752](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304071126913.png)

- **array**:`array new`要搭配`array delete`

  - ```cpp
    Complex* p = new Complex[3];
    ...
    delete[] p;	//唤起三次dtor
    delete p;	//唤起一次dtor
    ```

  - ![image-20230407113056384](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304071130510.png)

  - 如果`class`不带指针,两种delete的结果一样(因为不需要调用析构函数回收空间),但为养成好习惯,任何时候`array new`都要搭配`array delete`



## 复习String类的实现过程

---

```cpp
#include <string.h>
class String
{
public:
    String(const char* cstr = 0);
    String(const String& str);
    String& operator=(const String& str);
    ~String();
    char* get_c_str() const { return m_data; }
private:
	char* m_data;
};

inline String::String(const char* cstr = 0)
{
    if (cstr){
        m_data = new char[strlen(cstr) + 1];
        strcpy(m_data, cstr);
    }else{
        m_data = new char[1];
        *m_data = '\0';
    }
}

inline String::~String()
{
    delete[] m_data;
}

inline String::String(const String& str)
{
    m_data = new char[strlen(str.m_data) + 1];
    strcpy(m_data, str.m_data);
}

inline String& String::operator=(const String& str)
{
    if(this == &str)
        return *this;

    delete[] m_data;
    m_data = new char[strlen(str.m_data) + 1];
    strcpy(m_data, str.m_data);
    return *this;
}
```



## 类模板，函数模板，及其他

---

- **`static`进一步补充**:

  考虑这样一个class:

  ```cpp
  class Account {
  public:
      static double m_rate;
      static void set_rate(const double& x) { m_rate = x; }
  };
  double Account::m_rate = 8.0;
  
  int main()
  {
  	Account::set_rate(5.0);
      
      Account a;
      a.set_rate(7.0);
  }
  ```

  - 编译器会自动在成员函数中补充一个参数`this`指向调用者,但是程序员在成员函数的参数列表**不可以**写它,而在函数里**可写可不写**.

  - 静态函数没有`this`,且静态函数只能存取静态数据, **`a.set_rate(7.0);`这种形式仍是合法的**,但不会像普通的成员函数一样将`a`作为`this`传给成员函数

  - 静态数据必须要在**类外**定义`double Account::m_rate = 8.0;`

  - 当我们想在某一处调用`静态函数`时,我们可能还没有`对象`,这时可以通过`Account::set_rate(5.0);`直接调用

  - 实现**`单例(Singleton)`**:把`ctors`放在`private`里

    ```cpp
    class A {
    public:
        static A& getInstance();
        setup(){ ... }
    private:
    	A();
        A(const A& rhs);
        ...
    };
    
    A& A::getInstance()
    {
    	static A a;
        return a;
    }
    //一个非常好的写法,只有在用到getInstance时才会建立A,且只建立一个
    ```

- **进一步补充`cout`**:是一个`ostream`对象,`ostream`类中有相当多的`operator<<`重载 

- **进一步补充class template, 类模板**:`template<typename T>`在后面使用时`Complex<double> c1();`编译器会生成几乎一模一样的代码,只是将`T`替换成了`double`,因此有人说这样在成**代码量的膨胀**,但这并不一定是不好的,因为确确实实需要那么多份,膨胀的只是最开始带有`T`的版本.

- **进一步补充function template**:`template <class T>`对于某些通用的函数(如比大小),可以使用函数模板,并且在使用时不必像类模板一样要显式指出`<typename>`,编译器会进行实参推导,自动选择合适的实参类型

  ```cpp
  template <class T>//如果不是自己定义的类则为typename
  inline const T& min(const T& a, const T& b)
  {
  	return b < a ? b : a;//这里需要补充许多操作符重载
  }  
  ```

- **进一步补充`namespace`**:

  ![image-20230407144057984](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304081710779.png)



## 组合与继承

---

- 面向对象的思想:类和类之间的关系

- **Composition(复合)**:类里面有别的类,

- **Delegation(委托)**:又称***Composition by reference***类里面有指针指向别的类,一个有名的手法

  ![image-20230407151033167](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304081710966.png)

  - **pImpl :** pointer to implement(Handle/Body),将接口与内部实现分离,可以做到客户端的共享

- **Inheritance(继承)**:

  ```cpp
  struct _List_node_base
  {
  	_List_node_base* _M_next;
      _List_node_base* _M_prev;
  };
  
  template<typename _Tp>
  struct _List_node
      : public _List_node_base//public继承
  {
  	_Tp _M_data;
  }
  ```

  - 对于构造和析构,同样遵循**构造由内而外,析构由外而内 **原则(*derived在外, base在内*)

    > 需要特别注意的是,*base* *class(*父类)的*dtor*必须是*virtual*否则会出现*undefined behavior*.

  - 继承最主要是搭配虚函数



## 虚函数与多态

---

- 在任何成员函数之前加上`virtual`

- 数据被继承下来,占用了内存的一部分,但是函数的继承,继承的是函数的调用权

- **虚函数**:

  - **非虚函数(*non-virtual*)**:你**不希望子类重新定义**(*overwrite,覆写*)它
  - **虚函数(*virtual*)**:你**希望子类重新定义**(*overwrite,覆写*)它,且它**已经有了默认的定义**
  - **纯虚函数(*pure virtual*)**:子类**一定要重新定义**(*overwrite,覆写*)它,它**没有默认的定义**

  - ![image-20230408132537111](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304081710713.png)