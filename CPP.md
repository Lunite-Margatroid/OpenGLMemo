# C++

## 静态函数

静态函数无法被其他文件链接。

## 头文件保护

```c++
// 方法一
#program once
// 方法二
#ifndef _HEAD_H
#define _HEAD_H


#endif
```

方法二在比较老的代码里可能会有

## 枚举

```c
enum {a, b, c};
enum example : unsigned char{i,j,k};
```

枚举可以不命名

枚举可以指定类型，这是之前不知道的。只能指定整数类型。

### enum & enum class

enum class 相当于又套了一层命名空间

```c++
enum Weekdays
{
    Sun, Mon, Tue, Wed, Thu, Fir, Sat
};
Sun == 0;

enum class Month
{
    Jan=1, Feb, Mar,  Apr, May, Jun, Jul, Aug, Sept, Oct, Nov, Dec 
};

Month::Jan == 1;
```



## 删除默认构造函数

```c++
class the_object
{
public:
    the_object() = delete;
    static void func1(){
        // to do something
    }
};
```

## override关键字

c++11添加override关键字，显式声明覆写函数。提高可读性、防止拼写错误。

```c++
typename classname::function() override {};
```



## mutable关键字

修饰成员变量，使被const修饰的methord可以改变该变量。

这种情况可能只在debug过程中会用到。

```c++
class Entity
{
private:
    mutable int count;
public:
    void func() const
    {
        count++;
    }
};
```



另一种很少用的用法是用在lambda函数中。

```c++
int main()
{
    int x = 0;
    // []里写传参方式
    auto f = [=]() mutable
    {
        x++;
        std::cout << x << std::endl;
        // 用copy的方式传参，不会改变x的值。
        // copy方式传参，不加mutable的话，x++无法进行。
    };
    
    f();
    return 0;
}
```



## 初始化成员列表

初始化成员列表的顺序要和声明的顺序一样。

```c++
class Coord
{
private:
    int x,y,z;
public:
    Coord(int xx, int yy, int zz)
        : x(xx),y(yy),z(zz)
    {
    	Init();
    }
}
```

## explicit关键字

禁止构造函数隐式类型转换

```c++
class Coord
{
private:
    int x,y,z;
public:
    explicit Coord(int x)
        :x(x),y(0),z(0)
    {
	}
}

void main()
{
    Coord c = 2;		// error
    Coord b(2);			// right
    return 0;
}
```

## 智能指针

```c++
#include <memory>

class Coord
{
private:
    int x,y,z;
public:
    Coord(int xx, int yy, int zz)
        :x(xx),y(yy), z(zz)
    {}
}

int main()
{
    // -------------------------unique_ptr----------------------------
    // 作用域指针   它的析构函数会delete指针指向的对象
    {
        // 以下两种方式效果相同
        // unique_ptr禁止copy
    	std::unique_ptr<Coord> coord1(new Coord(0,0,0));
    	std::unique_ptr<Coord> coord2 = std::make_unique<Coord>(0,0,0);
    }
    
    // -------------------------shared_ptr---------------------------
    // 记录引用次数 当引用次数为零 delete
    {
        std::shared_ptr<Coord> coord1;
        {
            std::shared_ptr<Coord> coord2 = std::make_shared<Coord>(0,0,0);
            coord1 = coord2;		// copy
        }
    }// 到这里Coord对象才被delete
    
    // ------------------------weak_ptr-----------------------------
    // 不会增加引用次数
    {
        std::weak_ptr<Coord> coord1;
        {
            std::shared_ptr<Coord> coord2 = std::make_shared<Coord>(0,0,0);
            coord1 = coord2;		// copy 但不记录引用次数
        }// 到这里Coord对象就被delete
    }
    
    return 0;
}

 
```

## 重载"->"操作符

一般用不到

```C++
//---------一个unique_ptr智能指针的定义可能是这样的------------
template<typename T>
class ScopedPtr
{
private:
    T* m_ptr;
public:
    ScopedPtr(T* ptr)
        :m_ptr(ptr)
    {
    }
    ~ScopedPtr()
    {
        delete m_ptr;
    }
    
    T* operator -> ()
    {
        return m_ptr;
    }
    
    const T* operator -> () const
    {
        return m_ptr;
    }
}
```

## 利用"->"获取成员偏移量

```c++
struct Vector3
{
    float x, y, z;
};

int main()
{
    int offset = int( & ((Vector3*)0)->y );
        
    return 0;
}
```

## offsetof宏

获得结构成员的偏移量。

```c++
#define offset(struct_name, member_name) size_t(&((struct_name*)0)->member_name)

struct Vertex
{
    float Position[3];
    float Color[4];
    float TexCoord[2];
};

offsetof(Vertex, Position) == 0;
offsetof(Vertex, Color) == 12;
offsetof(TexCoord) == 28;
```



## 容器的遍历

```c++
#include <vector>

void func(int n)
{
    // to do something
}

int main()
{
    std::vector<int> array;
    array.push_back(1);
    array.push_back(432);
    array.push_back(532);
    array.push_back(545);
    array.push_back(643);
    array.push_back(4);
    
    for (int i : array)
    {
        func(i);
        // etc.
    }// 这样实际上是对array中每个元素复制了一份
     // 当元素是对象的时候，可能浪费资源
    
    for(int& i : array)
    {
        // to do something
    }
    /* or */
    for(const int& i : array)
    {
        // to do something
    }
    
    // 当然用传统的迭代索引或者迭代器 is better
    return 0;
}
```

## 手动设置vector容量大小

```c++
#include <vector>
int main()
{
    std::vector<int> array;
    array.reserve(10);
    return 0;
}
```

## 优化vector的构造

```c++
#include <vector>
class Coord
{
private:
    float x,y,z;
public:
    Coord(float xx, float yy, float zz)
        :x(xx),y(yy), z(zz)
    {}
}

int main()
{
    std::vector<Coord> vertice;
    vertice.emplace_back(-1.0f,-1.0f,-1.0f);
    // 这里传递的是对象构造函数的参数列表
    // 防止在栈区构造和copy
    return 0;
}
```

## 处理多返回值

一种方法是用引用或者指针作为参数传入函数，然后。。。就像win32 API常用的那样。

第二种，声明一个struct，包含要返回的变量。很好理解。

第三种。返回一个vector或者array。

第四种，使用std::tuple和std::pair。不想用，也不想学，遇到的时候查一下得了。

## template

template不止可以模版类型。

比如，std::array可能是这样实现的。

> 不同于std::vector, std::array是在栈上创建的数组。

模板本质上是编译器在编译的时候根据调用的情况，替换模版类型或对象，重新构造的函数和类。

```c++
template<typename T, int N>
class array
{
private:
    T m_array[N];
public:
    array(){}
    T& operator[](const unsigned int index)
    {
        return m_array[index];
    }
    int GetSize(){ return N; }
};
```

比如，当这样调用的时候：

```c++
int main()
{
    array<float, 5> a;
    return 0;
}
```

编译器构造了这样一个类：

```c++
class array
{
private:
    float m_array[5];
public:
    array(){}
    float& operator[](const unsigned int index)
    {
        return m_array[index];
    }
    int GetSize(){ return 5; }
};
```

## auto关键字

自动类型，有利有弊。在用std的时候，可能要输入很长的typename，可以用一下。

> auto也可以加&用引用。

```c++
int main()
{
    std::vector<std::string> strings = {"hello","world","!"};
    for(auto i = strings.begin(); i != strings.end(); i++)
    {
        
    }
    return 0;
}
```



## marco  宏

简化一些代码，这方面不用说了。

带参define和字符串连接，用到的时候再查。

通过宏判断Configuration。debug模式下要log日志，在release模式下不需要。

```c++
#ifdef _DEBUG
#define LOG(x) ...// do something to log
#else
#define LOG(x)	// nothing
#endif
```

## 函数指针

```c++
void func()
{}

void ProcessInt(int n)
{
    // for example
    Print(n);	// or do something else
}

void ForEach(std::vector<int> array, void(*func)(int))
{
    for(int i:array)
	    func(i);
}

int main()
{
    //--------------------1---------------------
	void(*f_ptr)(void) = func;
    f_ptr();		// 像函数一样调用
    // 看起来有点复杂
    
    // 那么
    auto f_ptr_auto = func;
    f_ptr_auto();
    
    // 可以用typedef
    typedef void(*FuncPtr)(void);
    FuncPtr funcPtr = func;
    funcPtr();
    
    // ------------------2------------------
    // 实现ForEach
    std::vector<int> array = {13,453,32,5,64,23};
    ForEach(array, ProecssInt);
    
    return 0;
}
```

## lambda（待补充）

语法形式

```c++
[ capture ] ( params ) opt -> ret { body; };
```

[] :表示不捕获任何变量
[=]：表示按值捕获变量
[&]：表示按引用捕获变量
[this]：值传递捕获当前的this



## std::function（待补充）



## std::find_if（待补充）



## 位移符号优先级

 <<   >> 的优先级比+ -低

## namespace

命名空间的作用是防止命名冲突

```c++
{
    namespace s = std;
    // 在当前作用域中可以用s代替std
    
}
```

命名空间可以嵌套

```c++
{
    namespace LM { namespace math{
     	
        
    }}
    
    // 偶尔会用到这个
    std::fstream file("input.txt", std::ios::in);
    // std::ios::in估计也是一个命名空间的嵌套吧
    // 也可以这么用
    using namespace std::ios;
    // or
    using namespace std;
    using namespace ios;
    std::fstream outfile("output.txt",out);
    
    namespace a = std::ios;
    std::fstream file("input.txt", a::in);
}
```

## 多线程

### 初始化线程

```c++
void ToDo()
{
    // to do something
    
}
int main()
{
	std::thread thd(ToDo);
    // 线程thd随即开始执行
    
    system("pause");
}
```

### join

```c++
void ToDo()
{
    // to do something
    
}
int main()
{
	std::thread thd(ToDo);
    // 接收一个函数指针
    // 线程thd随即开始执行
    
    thd.join();
    // 当前线程阻塞 等待线程thd执行结束后 当前线程继续执行
    system("pause");
}
```

### 线程sleep

```c++
{
    using namespace std::literals::chrono_literals;
    std::this_thread::sleep_for(1s);
    
}


```

### std::this_thread对象

每个线程有一个唯一对象std::this_thread可以通过它对线程下达指令

## 计时器

### chrono

```c++
#include <chrono>
{
    auto start = std::chrono::high_resolution_clock::now();
    // to do something
    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<float> duration = end - start;
    std::cout << duration.count() << 's' << std::endl;
}
```

### win32 API

QueryPerformanceCounter()

需要的时候查手册

### 简单的class Timer

构造时开始计时，析构时结束计时。

```c++
struct Timer
{
    std::chrono::time_point<std::chrono::steady_clock> start, end;
    std::chrono::duration<float> duration;
    Timer()
    {
        start = std::chrono::high_resolution_clock::now();
    }
    ~Timer()
    {
        end = std::chrono::high_resolution_clock::now();
        duration = end - start;
        
        float ms = duration.count() * 1000.0f;
        std::cout << "Timer:" << ms << "ms" << std::endl;
    }  
};
```

## std::sort()

```c++
#incldue <algorithm>
#include <vector>
#include <functional>

int main()
{
    std::vector<int> array;
    //......
    // 一般情况 使用基础类型  默认使用operator< 升序排列
    std::sort(array.begin(), array.end());
    
    //-------------1-----------------
    // 传入lambda  取代operator<
    std::sort(array.begin(), array.end(),[](const int& a, const int & b)
              {
                  return a < b;
              });
    // -----------2-------------
    // 传入lambda  如果用>的话就会升序
    // 用这种方式就可以自定义类型
    std::sort(array.begin(), array.end(),[](const int& a, const int & b)
              {
                  return a > b;
              });
    // ---------------3----------------
    // 重载operator < 运算符应该也是可以的
    return 0;
}
```

## type punning

这个特性我基本已经理解，这也是我喜欢c++的原因之一。

```c++
{
    // 之前写过这样的代码，用的就是这个特性
    float f;
    *(unsigned int*)f = 0xff000000 >> 1;
    
}
```

利用这个特性，实现遗传算法的二进制编码也非常方便。

当初发明遗传算法的人想出二进制编码的法子至少有两种原因。一、限于计算机性能。二、当时还没有出现java或者java还不流行，c和basic是主流，用二进制编码是解决存储压力最好的方法，在一定程度上，精度也可以提高。如果用汇编写编码和解码，效率也是非常高的，也并不麻烦。

智能优化算法在这一方面还可以优化，可以仿照二进制编码设计一种改进的实数编码方式，以提高效率。这样的话，四则运算是不是也要重新定义呢。

## union

可以通过union用多种类型解释同一段内存。

union也可以像class和struct一样声明成员函数。

```c++
{
    // 通过union实现上面的功能
    union Num
    {
        unsigned int n;
        float f;
    }p;
    p.n = = 0xff000000 >> 1;
    _ASSERT(p.f == inf);
}



struct Vector2
{
    float a, b;
}

struct Vector4
{
    union
    {
        struct{float x, y, z, w;};
        struct{Vector2 p,q;};
    };
};
// 也可以像这样用。Vector4可以用两种方式来解释这16byte的内存。
```

第一次在C Primer Plus上读到的时候，完全无法理解这个特性的妙处。

## 虚析构函数

正常情况下只用析构函数就够了。

```c++
class RGB
{
    float *r,*g,*b;
public:
    RGB()
    {// 我知道这样很蠢  只是一个栗子
        r = new float[3];
        g = r+1;
        b = g+1;
    };
	~RGB()
    {
        delete[] r;
    };   
};
class RGBA:public RGBA
{
	float *a;
public:
    RGBA()
    {
        a = new float;
    };
    ~RGBA()
    {
        delete a;
    };
};
int main()
{
    // 这种情况下就会出问题
    // ta会调用RGBA的构造函数而不会调用RGBA的析构函数
    // 猜测：用不着的栈区内存应该会随着栈帧的pop释放掉 比如float* a
    // 于是就有一段堆区内存 new了却没有delete
    // 给基类的析构函数加上virtual就能解决这个问题
    RGB* color = new RGBA();
    delete color;
    return 0;
}
```

## 类型转换

使用static_cast, dynamic_cast, const_cast, reinterpret_cast等关键字的好处之一是便于程序员find(ctrl + F).

尝试自己实现static_cast, dynamic_cast, const_cast, reinterpret_cast.

```c++
#include <iostream>
class Object
{
public:
	char c;
	Object() :c('o')
	{}
	virtual ~Object() {}

	void Function()
	{
		std::cout << c << std::endl;
	}
};

class Derive: public Object
{
public:
	char d;
	Derive() :d('d')
	{}
	Derive(Object& b):d('d')
	{

	}
	~Derive()
	{}
	void Function()
	{
		std::cout << d << std::endl;
	}
};

class DeriveA : public Object
{
public:
	DeriveA()
	{}
	~DeriveA()
	{}
	void Function()
	{

	}
};

namespace cast_test
{
	// static_cast和显式强转是一样的
	// 自定义的class必须要有对应的构造函数
	// 强转和static_cast都会调用对应类型转换函数（构造函数）
	// 指针可以直接使用 不会调用构造函数


	// dynamic_cast是基于RTTI实现的
	// 主要用于从基类到派生类的转换
	// 成功返回true 失败返回false
    // 成功返回派生类的指针 失败返回NULL
	// RTTI大概是Run Time Type Identity 运行时类型识别
	// 我不会RTTI就不试着实现了
	// 基类的析构函数必须virtual 也是为了确保安全
	// 不会调用构造函数
	// 只有原本基类指针指向的就是派生类才会转换成功


	// reinterpret_cast
	// 最暴力 最底层 最单纯的类型转换
	// 不改变内存  改变解释内存的方式
	// 也就是指针类型转换
	template <class T>
	T* ReinterpretCast(void* p)
	{
		return (T*)p;
	}

	// const_cast
	// 若一块内存初始化的同时被声明为const，其他非const的指针也无法指向它
	// 但是 通过const_cast就可以
	// 也可以通过其他手段实现 比如吧const int*强转为int*
	// 呃、、、这玩意儿不就是个强转吗？
    
    // 小结
    // 所有的转换都是能够使用C风格的方法实现的
    // 这东西只是为了安全和便于调试
};

int main()
{
	using namespace cast_test;
	//------------static------------
	float n = 100.0f;
	int p;
	p = static_cast<int>(n);
	std::cout << p << std::endl;

	Object b[2];
	Derive d = static_cast<Derive>(b[1]);
	Derive q = (Derive)b[1];
	d.Function();
	q.Function();


	//------------dynamic_cast------------
	Object* u = new Derive();
	Derive* k = dynamic_cast<Derive*>(u);
	// 转换成功返回指针 否则返回NULL
	if (k)
		std::cout << "转换成功" << std::endl;
	else
		std::cout << "转换失败" << std::endl;
	delete u;

	k = dynamic_cast<Derive*>(b);
	if (k)
		std::cout << "转换成功" << std::endl;
	else
		std::cout << "转换失败" << std::endl;

	//-----------reinterpret_cast--------------
	int integer = 0xff000000 >> 1;
	float *f = reinterpret_cast<float*>(&integer);
	std::cout << *f << std::endl;

	float deci = static_cast<float>(integer);
	std::cout << deci << std::endl;

	// ---------------const_cast------------
	int aa = 2;
	// const int bb = const_cast<const int>(aa);
	const int* bb = const_cast<const int*>(&aa);
	// *bb = 3;
	std::cout << *bb << std::endl;

	int* cc = const_cast<int*>(bb);
	*cc = 3;
	std::cout << *cc << std::endl;
	//int* dd = bb;

	// 通过强转实现
	int* ee = (int*)bb;
	*ee = 1990;
	std::cout << aa << std::endl;
	return 0;
};
```

## 结构化绑定-多个返回值

```c++
#include <tuple>
#include <string>

// 把返回类型写成tuple
std::tuple<std::string, int> CreatePerson()
{
    return {"Lunite", 21};
}
/*
还有pair可以使用
std::pair<std::string, int> CreatePerson()
{
    return {"Lunite", 21};
}
*/
int main()
{
    {
    	// methord1
   		std::tuple<std::string, int> person = CreatePerson();
    	std::string& name = std::get<0>(person);	# 获得索引为0的类型的变量
    	int age = std::get<1>(person);				# 获得索引为1的类型的变量
    }
    {
    	// another methord 
    	std::string name;
        int age;
    	std::tie(name, age) = CreatePerson();
    }
    {
        // 结构化绑定
        // structured binding
        // c++17 and newer
        auto[name, age] = CreatePerson();
        
    }
    return 0;
    
}
```

## std::optional

C++ 17 and newer

```c++
#include <optional>
#include <fstream>
#include <string>

std::optional<std::string> ReadFileAsString(const std::string& filePath)
{
    std::ifstream infile(filePath);
    if(infile)
    {
        std::string data = infile.read();
        return data;
        infile.close();
    }
    else
    {
        return {};
    }
}

int main()
{
 
    std::optional<std::string> data = ReadFileAsString("data.txt");
    if(data.has_value()) // 或者直接if(data)
    {
        // 读取文件成功
        std::string strData = data.value();
    }
    else
    {
        // 读取文件失败
    }
    
    // 还有更方便的用法
    std::string strData = data.value_or("Process Fault");
    // data没有数据的话 将"Process Fault"赋给strData
    return 0;
}
```

## std::variant

c++17 and newer

variant像是一个type-safe union

```c++
#include <variant>
#include <string>
int main()
{
    {
        std::variant<std::string, int> var;
        var = 2;
        // 返回对应类型的变量
        int n = std::get<int>(var);
        // n == 2
        std::string str = std::get<std::string>(var);
        // error
        
        std::string* pStr = std::get_if<std::string>(var);
        // 如果类型正确返回指针
        // 否则返回NULL
        
        var.index();
        // 以上面代码为例
        // 如果var是std::string类型 返回0
        // 如果var是int类型 返回1
    }
    return 0;
}
```

## std::any

```c++
#include <any>
#include <string>
int main()
{
    {
        std::any data = std::make_any<std::string>("Lunite"); 
    }
    {// 或者简单地
        std::any data;
        data = 1234;
        data = std::string("Lunite");
        
        std::string str = std::any_cast<std::string>(data);
        int n = std::any_cast<int>(data);	// error
    }
    return 0;
}
```

## 并行for循环

这不是一个合适的例子。

直接运行的话，异步执行会慢一些。大概是由于次要任务花的资源相较于主要任务太多了。

```c++
#include <future>
#include <vector>
#include <cmath>
#include <chrono>
#include <string>
struct Timer
{
    std::chrono::time_point<std::chrono::steady_clock> start, end;
    std::chrono::duration<double> duration;
    std::string strTip;
    bool autoTimer;
    Timer()
    {
        strTip = "Unnamed Timer";
        autoTimer = true;
        start = std::chrono::high_resolution_clock::now();
    }
    Timer(const std::string& strTimer)
    {
        strTip = strTimer;
        autoTimer = false;
        start = std::chrono::high_resolution_clock::now();
    }
    void TimerOn(const std::string& strTimer)
    {
        strTip = strTimer;
        autoTimer = false;
        start = std::chrono::high_resolution_clock::now();
    }
    void TimerOff()
    {
        end = std::chrono::high_resolution_clock::now();
        duration = end - start;
        float ms = duration.count() * 1000.0f;
        std::cout << strTip<<": " << ms << "ms" << std::endl;
    }
    ~Timer()
    {
        end = std::chrono::high_resolution_clock::now();
        if(autoTimer)
        {
        	duration = end - start;
        	float ms = duration.count() * 1000.0f;
        	std::cout << "Timer:" << ms << "ms" << std::endl;
        }
    }  
};


static std::mutex sMutex;

int IsPrime(int n)
{
   int m = sqrt(n);
   for(int i=2;i <= m; i++)
   {
       if(n%i == 0)
           return 0;
   }
   return 1;
}
static void Func(std::vector<int>* vec,int n)
{
    int m = IsPrime(n);
    std::lock_guard<std::mutex> lock(sMutex);	// 这大概是一个作用域互斥锁
    vec->push_back(m);
}

int main()
{
    std::vector<int> arr = {53243123,543534523,43251,3523454,544356,26457,8643653};
    std::vector<int> vec;
    std::vector<int> vec0;
    std::vector<std::future<void>> future;	// 储存std::async的返回值 不太理解
    Timer timer;
    timer.TimerOn("并行");
    for(const int n: arr)
    {
        future.push_back(std::async(std::launch::async, Func, vec, n));
    }
    timer.TimerOff();
    timer.TimerOn("串行");
    for(const int n: arr)
    {
        vec0.push_back(IsPrime(n));
    }
    timer.TimerOff();
    for(int n:vec)
    {
        std::cout << n << ' ' ;
    }
    std::cout <<'\n';
    for(int n:vec0)
    {
        std::cout << n << ' ' ;
    }
    std::cout <<'\n';
    system("pause");
    return 0;
}
```

## 更快的string

```c++
#include <string>
#include <iostream>

void PrintName(std::string_view strName)
{
    std::cout << strName << std::endl;
}

int main()
{
    {// 这样写会调用3次构造函数 分配3次堆内存
    	std::string strName = "Lunite Margatroid";
    	std::string firstName = strName.substr(0,6);	// [0,6)
    	std::string secondName = strName.substr(7,17);	// [7,17)
    }
    {// 这样写会调用1次构造函数 分配一次内存
        std::string strName = "Lunite Margatroid";
        std::string_view firstName(strName.c_str(),6);
        std::string_veiw secondName(strName.cstr()+7, 10);
        
        // string_view 拷贝构造函数不会分配堆内存
        PrintName(firstName);
        PrintName(secondName);
    }
    {// 与上面相同 相对原始的写法
        std::string strName = "Lunite Margatroid";
        const char* firstName = strName.c_str();
        int lenFirstName = 6;
        const char* secondName = strName.c_str()+7;
        int lenSencondName = 10;
        // 可以封装自己的string类
    }
    {// 完全放在栈区
        const char* strName = "Lunite Margatroid";
        const char* firstName = strName;
        int lenFirstName = 6;
        const char* secondName = strName+7;
        int lenSencondName = 10;
    }
    return 0;
}
```

## 单例模式

```c++
#include <iostream>
#include <stdlib.h>
class random
{
public:
	random(const random&) = delete;	// 删除拷贝构造函数
	static random& Get() 
	{
		return s_randomInstance;
	};

	static float randomFloat()
	{
		unsigned char temp = 126;
		random& r = Get();
		r.fRandom = 1.0f * rand() / RAND_MAX;
		return r.fRandom;
	}
private:
	float fRandom = 0;
	static random s_randomInstance;
	random() { srand(time(0)); };	// 默认构造函数设为private 防止非法实例化
};
random random::s_randomInstance;

int main()
{
	for (int i = 0; i < 10; i++)
	{
		std::cout << random::randomFloat() << std::endl;
	}
	return 0;
}
```

## 小字符串优化

std::string 当初始化的字符串小于一定大小，不会在堆区分配内存，而是在栈区。

## 跟踪内存分配的简单方法

```c++
#incldue <memory>
#include <iostream>
#include <string>

struct MemoryTrace
{
    uint32_t AllocatedMemory = 0;
    uint32_t FreedMemory = 0;
    uint32_t CurrentUsage(){return AllcocatedMEmory - FreeMemory}
};
static MemoryTrace s_memoryTrace;

void* new(size_t size)
{
    s_memoryTrace.AllocatedMemory += size;
    std::cout << "Allocating: " << size << " bytes\n";
    return malloc(size);
}

void delete(void* ptr, size_t size) //size is optional
{
    s_memoryTrace.FreedMemory += size;
    std::cout << "Freeing: " << size << " bytes\n";
    free(ptr);
}

void MemoryTracePrint()
{
    std::cout << "Current Memory Usage: " << s_memoryTrace.CurrentUsate() << " bytes\n";
}
void main()
{
    std::string string("hello world!");
    int *arr = new int[20];
    delete[] arr;
    return 0;
}
```

## 左值与右值 l value and r value

简单说，l value是有地址的变量，r value是立即数或者临时变量（比如函数的返回值）。

如果函数的返回值是一个地址或者引用，那就是另外，可以理解为是lvalue左值。

左值引用。

```c++
void Func(int& n)
{}

void Func2(const int& n)
{}

int main()
{
    int &n = 10;	// error
    const int& a = 10;	// It's OK.
    // 这里相当于在栈上定义了一个int，把它的引用赋予a
    
    Func(10);	// error
    Func2(10);	// It's OK.
    return 0;
}
```

```c++
#include <iostream>
#include <string>
void Print1(const std::string& str)	// 常量引用 可以接受左值和右值
{
    std::cout << str << std::endl;
    
}
void Print2(std::string& str)	// 左值引用 只能接受左值
{
    std::cout << str << std::endl;
}

void Print3(std::string&& str)	// 右值rvalue引用 只能接受右值
{
    std::cout << str << std::endl;
}

// 如果这玩意儿有个重载
void Print(std::string&& str)
{
    std::cout << str << std::endl;
}
void Print(const std::string& str)
{
    std::cout << str << std::endl;
}

int main()
{
    std::string firstName = "Lunite";
    std::string secondName = "Margatroid";
    std::string name = firstName + secondName;
    
    Print1(firstName + secondName);	// It's OK.
    Print1(name);					// It's OK.
    Print2(firstName + secondName);	// error
    print2(name);					// It's OK.
    Print3(firstName + secondName);	// It's OK.
    Print3(name);					// error
    
    // name 是一个左值lvalue
    // firstName + secondName 是一个右值 rvalue
    // 这就是为什么大家总是写常量引用 const std::string&
    
    Print(firstName + secondName);
    // firstName + secondName 是一个右值 rvalue
    // 会优先调用std::string&& 右值引用的重载
    return 0;
}
```

## 运算符优先级

| 优先级 | 符号             | 结合性 |
| ------ | ---------------- | ------ |
| 1      | ::               |        |
| 2      | ()               | L-R    |
| 2      | ->               | L-R    |
| 2      | .                | L-R    |
| 2      | const_cast       | L-R    |
| 2      | dynamic_cast     | L-R    |
| 2      | reinterpret_cast | L-R    |
| 2      | static_cast      | L-R    |
| 2      | typeid           | L-R    |
| 2      | 后缀++           | L-R    |
| 2      | 后缀--           | L-R    |
| 3      | &                | R-L    |
| 3      | *                | R-L    |
| 3      | （type）         | R-L    |
| 3      | sizeof           | R-L    |
| 3      | new              | R-L    |
| 3      | new[]            | R-L    |
| 3      | delete           | R-L    |
| 3      | delete[]         | R-L    |
| 4      | . *              | L-R    |
| 4      | -> *             | L-R    |
| 5      | * 乘号           | L-R    |
| 5      | /                | L-R    |
| 5      | %                | L-R    |
| 6      | +                | L-R    |
| 6      | -                | L-R    |
| 7      | <<               | L-R    |
| 7      | >>               | L-R    |
| 8      | <                | L-R    |
| 8      | <=               | L-R    |
| 8      | >=               | L-R    |
| 8      | >                | L-R    |
| 9      | ==               | L-R    |
| 9      | !=               | L-R    |
| 10     | &与              | L-R    |
| 11     | ^异或            | L-R    |
| 12     | \|或             | L-R    |
| 13     | &&               | L-R    |
| 14     | \| \|            | L-R    |
| 15     | :?               | R-L    |
| 16     | =                | R-L    |
| 16     | * =              | R-L    |
| 16     | / =              | R-L    |
| 16     | %=               | R-L    |
| 16     | +=               | R-L    |
| 16     | -=               | R-L    |
| 16     | &=               | R-L    |
| 16     | ^=               | R-L    |
| 16     | \|=              | R-L    |
| 16     | <<=              | R-L    |
| 16     | >>=              | R-L    |
| 17     | throw            | L-R    |
| 18     | ,                | L-R    |

## 未定义行为

```c++
#include <iostream>

void PrintSum(int a, int b)
{
    std::cout << a << " + " << b << " = " << a + b << std::endl;
}

int main()
{
    int n = 0;
    PrintSum(n++, n++);	// 未定义行为
    return 0;
}
```

## 移动语义 std::move

这个是一般的写法  会进行一次深拷贝 总共进行两次堆内存分配

```c++
#include <iostream>
#include <stdio.h>
class String
{
private:
    uint32_t m_nSize;
    char* m_str;
public:
    String() = default;
    String(const char* str)
    {
        printf("Create\n");
        m_nSize = strlen(str);
        m_str = new char[m_nSize];
        memcpy(m_str, str, m_nSize);
    }
    String(const String& other)
    {
        printf("Copy\n");
        m_nSize = other.m_nSize;
        m_str = new char[m_nSize];
        memcpy(m_str, other.m_str, m_nSize);
    }
    ~String()
    {
        printf("Destroy\n");
        delete[] m_str;
    }
    void Print()
    {
        for(int i =0 ;i<m_nSize;i++)
            printf("%c", m_str[i]);
        printf("\n");
    }
};

class Entity
{
private:
    String m_name;
public:
    Entity(const String& str):m_name(str) // 这里进行了一次Create和一次Copy
    {
        
    }
    void PrintName()
    {
        m_name.Print();
    }
};

int main()
{
    Entity player("Lunite");  
    return 0;
}
```

移动语义写法  不会进行拷贝构造 只进行一次堆内存分配

```c++
#include <iostream>
#include <stdio.h>
class String
{
private:
    uint32_t m_nSize;
    char* m_str;
public:
    String() = default;
    String(const char* str)
    {
        printf("Create\n");
        m_nSize = strlen(str);
        m_str = new char[m_nSize];
        memcpy(m_str, str, m_nSize);
    }
    String(const String& other)
    {
        printf("Copy\n");
        m_nSize = other.m_nSize;
        m_str = new char[m_nSize];
        memcpy(m_str, other.m_str, m_nSize);
    }
    // 写一个右值引用的版本
    // 或许ta可以叫做移动构造函数
    String(String&& other) noexcept	// 禁止报错
    {
        printf("Move\n");
        m_nSize = other.m_nSize;
        m_str = other.m_str;
        
        // 防止内存被释放
        other.m_nSize = 0;
        other.m_str = nullptr;
    }
    ~String()
    {
        printf("Destroy\n");
        delete[] m_str;
    }
    void Print()
    {
        for(int i =0 ;i<m_nSize;i++)
            printf("%c", m_str[i]);
        printf("\n");
    }
};

class Entity
{
private:
    String m_name;
public:
    Entity(const String& str):m_name(std::move(str))	// 把它转换成右值引用
        // 也可以写成 m_name((String&&)str)
    {
        
    }
    void PrintName()
    {
        m_name.Print();
    }
};

int main()
{
    Entity player("Lunite");  
    return 0;
}
```

```c++
#include <iostream>
#include <stdio.h>
class String
{
private:
    uint32_t m_nSize;
    char* m_str;
public:
    String() = default;
    String(const char* str)
    {
        printf("Create\n");
        m_nSize = strlen(str);
        m_str = new char[m_nSize];
        memcpy(m_str, str, m_nSize);
    }
    String(const String& other)
    {
        printf("Copy\n");
        m_nSize = other.m_nSize;
        m_str = new char[m_nSize];
        memcpy(m_str, other.m_str, m_nSize);
    }
    // 写一个右值引用的版本
    // 或许ta可以叫做移动构造函数
    String(String&& other) noexcept	// 禁止报错
    {
        printf("Move\n");
        m_nSize = other.m_nSize;
        m_str = other.m_str;

        // 防止内存被释放
        other.m_nSize = 0;
        other.m_str = nullptr;
    }

    String& operator=(const String& other)
    {
        m_nSize = other.m_nSize;
        m_str = new char[m_nSize];
        memcpy(m_str, other.m_str, m_nSize);
        return *this;
    }
    // move版本的赋值重载
    String& operator=(String&& other) noexcept
    {
        if (this != &other)	// 防止自己move自己
        {
            delete[] m_str;	// 释放原有的内存

            m_nSize = other.m_nSize;
            m_str = other.m_str;

            // 防止内存被释放
            other.m_nSize = 0;
            other.m_str = nullptr;
        }
        return *this;
    }
    ~String()
    {
        printf("Destroy\n");
        delete[] m_str;
    }
    void Print() const
    {
        for (int i = 0; i < m_nSize; i++)
            printf("%c", m_str[i]);
        printf("\n");
    }
};

int main()
{
    {
        String name = "Lunite";
        String dest;
        dest = std::move(name);		//String& operator=(String&& other) noexcept
        name.Print();
    }
    {
        String name = "Apple";
        String orange(std::move(name));	// String(String&& other) noexcept
        name.Print();
    }
    // 这2种情况下 name都会成为空对象

    return 0;
}
```





## 持续集成 Continuous Integation

Jenkins是一个CI托管工具。一般部署在服务器，负责将提交的代码编译并测试。

## Array的实现

```c++
template<class T, int n>
class Array
{
private:
    T m_data[n];
public:    
    // constexpr修饰函数，使函数在编译阶段就可以确定。
    // const确保使const变量也能调用
    constexpr int Size() const
    { return n};
    
    // 重载[]运算法
    T& operator[](int index)
    {
        if(!(index < n))	// 防止越界
        { __debugbreak();}
        return m_data[index];
    }
    
    // const版本 重载[]运算符
    // 确保const变量也可以调用 同时避免修改数据
    const T& operator[](int index) const
    {
        if(!(index < n))
        { __debugbreak();}
        return m_data[index];
    }
    
    const T* Data() const
    {
        return m_data;
    }
    T* Data()
    {
        return m_data;
    }
}

int main()
{
    
}
```

constexpr修饰函数，使函数在编译阶段就可以确定。

## 作用域之外的变量

下面程序会先后打印"construct", "Destroy", "456789", "123456". 

在栈上的数据，即使出了作用域，只要函数没有返回，它们就一直存在。只是对作用域外不可见。但是还是可以通过地址访问。

```c++
#include <iostream>

class Object
{
public:
	int n;
	Object()
	{
		std::cout << "construct" << std::endl;
	}
	~Object()
	{
		std::cout << "Destroy" << std::endl;
	}
};

int main()
{
	int* p;
	{
		Object obj;
		obj.n = 123456;
		p = &obj.n;
	}
	int* ptr;
	{
		int a = 456789;
		ptr = &a;
	}
	std::cout << *ptr << std::endl;
	std::cout << *p << std::endl;
	system("pause");
	return 0;
}
```

## Vector实现

```c++
template<class T>
class Vector
{
private:
	T* m_Data = nullptr;
	size_t m_Size = 0;
	size_t m_Capacity = 0;

	void ReAllocate(size_t newCapacity)
	{
		// 重新设定大小

		// 如果新设定的大小小于原来的size
		size_t temp = m_Size;
		if (m_Size > newCapacity)
			m_Size = newCapacity;


		// 开辟新的内存
		T* newData = (T*)::operator new(newCapacity * sizeof(T));
        
		memset(newData, 0, newCapacity * sizeof(T));
        // 这里把所有的内存初始化为0
        // 这样有原因
        // 用operator new开辟内存 ，没有调用对象的构造函数，内存数据是随机的。
        // 如果该对象含有堆内存，那么在赋值的时候
        // 要先调用析构函数，free一个随机地址，然后抛异常。
        // 应该还有更好的写法把。
        
		for (int i = 0; i < m_Size; i++)
		{
			newData[i] = std::move(m_Data[i]);
		}

		// 析构原来的对象
		for (int i = 0; i < temp; i++)
			m_Data[i].~T();
		// 释放原先的内存
		if(m_Data)
			::operator delete(m_Data, m_Capacity * sizeof(T));
		// 指向新内存
		m_Data = newData;
		// 更新cap
		m_Capacity = newCapacity;
	}

public:
	Vector()
	{
		// 申请两个元素的内存
		ReAllocate(2);
	}
	~Vector()
	{
		Clear();
		::operator delete(m_Data, m_Capacity * sizeof(T));
	}

	void Clear()
	{
		for (int i = 0; i < m_Size; i++)
			m_Data[i].~T();
		m_Size = 0;
	}

	void PushBack(const T& t)
	{
		if (m_Size >= m_Capacity)
			ReAllocate(m_Capacity + m_Capacity / 2);
		m_Data[m_Size] = t;
		m_Size++;
	}

	void PushBack(T&& t)
	{
		if (m_Size >= m_Capacity)
			ReAllocate(m_Capacity + m_Capacity / 2);
		m_Data[m_Size] = std::move(t);
		m_Size++;
	}

	template<typename... Args>
	T& EmplaceBack(Args&&... args)
	{
		if (m_Size >= m_Capacity)
			ReAllocate(m_Capacity + m_Capacity / 2);
		new(&m_Data[m_Size]) T(std::forward<Args>(args)...);
		return m_Data[m_Size++];
	}

	T& operator[](unsigned int index)
	{
		if (index >= m_Size)
		{
			//__debugbreak();
		}
		return m_Data[index];
	}

	const T& operator[](unsigned int index) const
	{
		if (index >= m_Size)
		{
			//__debugbreak();
		}
		return m_Data[index];
	}

	void PopBack()
	{
		if (m_Size > 0)
		{
			m_Size--;
			m_Data[m_Size].~T();
		}
	}

	size_t Size()const 
	{
		return m_Size; 
	}
};
```

## unordered_map 的一些小技巧

```c++
#include <unoredered_map>
#include <string>
#include <iostream>
int main()
{
    // 使用using代替长长的命名
    using ScoreMap = std::unordered_map<std::string, int>;
    using MapIter = std::unordered_map<std::string, int>::const_iterator;
    
    ScoreMap map;
    map["Lunite"] = 1;
    map["Margatroid"] = 0;
    
    // 同时获取key和value
    // c++17 and newer
    for(auto [key, value] : map)
    {
        std::cout << key << " = " << value <<std::endl;
    }
    
    return 0;
}
```

## 二分查找

因为边界问题总是搞不清楚，直接保存一个写好的得了。

```c++
bool Find(vector<int>& arr, int target, int& ind )
    {
        int n = arr.size();
        int low= ind, high = n-1;
        int mid = (low + high + 1) >> 1;
        while(low < high)
        {
            if(arr[mid] <=target)
            {
                low = mid;
            }
            else if(arr[mid] > target)
            {
                high = mid - 1;
            }
            mid = (low + high + 1) >> 1;
        }
        ind = mid;
        return arr[ind] == target;
    }
```
