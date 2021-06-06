# C++ 动态内存管理

## 1.动态内存和智能指针

### 1.1  直接管理内存

​		C++语言定义了两个运算符来分配和释放动态内存。运算符new分配内存,  delete释放new分配的内存。

​		C++中的动态内存管理是通过一对运算符来完成的：new，在动态内存中为对象分配空间并返回一个指向该对象的指针，可以选择对对象进行初始化；delete ,接受一个动态对象的指针，销毁该对象，并释放与之关联的内存。

### 1.2  智能指针

​		为什么要引入智能指针？

​		动态内存的使用很容易出现问题，确保正确的时间释放内存是极其困难的，问题在于：

- ​				其一，忘记释放的情况下，会产生**内存泄漏**；
- ​				其二，有时在尚有指针引用内存的情况下释放了它，就会产生引用非法内存的指针；

​		为了更容易并安全地使用动态内存，新的标准库提供了两种**智能指针（Smart Pointer）**类型来管理动态对象。智能指针行为类似常规指针，重要的区别是它负责自动释放所指向的对象。

新标准库提供的指针指针：

- shared_ptr允许多个指针指向同一个对象；
- unique_ptr则“独占”所指向的对象；
- weak_ptr是一种弱引用，指向shared_ptr所管理的对象。

以上三种类型定义都在memory头文件中。

### 1.2 .1  shared_ptr类

类似vector，智能指针也是模板。定义方式如下：

```C++
shared_ptr<string> p1;		// shared_ptr, 可以指向string
shared_ptr<list<int>> p2;   // shared_ptr, 可以指向int的list
```

智能指针的使用方式与普通指针类似。解引用一个智能指针返回它指向的对象。如果在一个条件判断中使用智能指针，效果就是检测它是否为空：

```c++
if (p1 && p1->empty())
	`*p1 = "hi";
```

#### shared_ptr 基本操作

```C++
shared_ptr<T> sp； //智能空指针，可以指向类型为T的对象。
unique_ptr<T> sp；
p		//将p用作一个条件判断，若p指向一个对象，则为true.
*p		//解引用p, 获得它指向的对象。
p->mem		//等价于（*p）.mem
p.get();		 //返回p中保存的指针。 小心使用！
swap(p, q); 	//交换p和q中的指针；
p.swap(q);

// 
make_shared<T> (args)	//返回一个shared_ptr,指向一个动态分配的类型为T的对象。使用args初始化此对象
shared_ptr<T> p(q); // p是shared_ptr q的拷贝； 此操作会递增q中的计数器；
p = q; // p和q都是shared_ptr,所保存的指针必须能相互转换。此操作会递减P的引用计数，递增q的引用技术；若P的引用技术变为0，则将其管理的原内存释放

p.unique();		//若p.use_count()为1， 返回true; 否则返回false;
p.use_count();  // 返回与p共享对象的智能指针数量；可能很慢，主要用于调试。
```

#### make_shared 函数

#### shared_ptr 的拷贝和赋值

#### shared_ptr 自动销毁所管理的对象

shared_ptr 使用示例：

```C++
// shared_ptr 完整示例
class Soultion{
    
    
};

int main(){
    
    return 0;
}
```



#### 实现自己的shared_ptr类

```C++
// 实现自己的shared_ptr类
class my_shared_ptr{
    
    
};
```

### 1.2.2  unique_ptr

​		一个unique_ptr "拥有"它所指向的对象。与shared_ptr不同，某个时刻只能有一个unique_ptr指向一个给定对象。当unique_ptr被销毁时，它所指向的对象也被销毁。

​		与shared_ptr 不同， 没用类似make_shared 的标准库函数返回一个unique_ptr。当我们定义一个unique_ptr时， 需要将其绑定到一个new返回的指针上。 类似shared_ptr，初始化 unique_ptr 必须采用直接初始化形式：

```c++
unique_ptr<double> p1;					// 可以指向一个double的unique_ptr
unique_ptr<int> p2(new int(42)); 		 //p2 指向一个值为42的int
```

unique_ptr 使用示例：

```c++
class Soultion{
    
    
};


int main(){
    return 0;
}
```

实现自己的unique_ptr类

```C++
// TODO:实现自己的unique_ptr
class my_unique_ptr{
    
};

```



### 1.2.3  weak_ptr

weak_ptr 使用示例：

```c++
//weak_ptr 完整使用示例
```

实现自己的weak_ptr类：

```c++
// TODO:实现自己的unique_ptr
```



## 2.动态数组

​		new/delete 运算符一次分配/释放一个对象， 但某些应用需要一次为很多对象分配内存的功能。例如，vector和string都是在连续内存中保存它们的元素，因此，当容器需要重新分配内存时，必须一次性为很多元素分配内存。

​		C++ 语言和标准库提供了两种分配一个对象数组的方法：

- ​		C++语言定义了另一种new表达式语言，可以分配并初始化一个数组对象。

- ​		标准库中包含一个名为allocator的类，允许我们将分配和初始化分离。使用allocator通常会提供更好的性能和更灵活的内存管理能力。

  

### 2.1 new和数组

为了让new分配一个数组对象，需要在类型名之后跟一对方括号，在其中芝麻要分配的对象的数目。如下，new分配要求数量的对象并返回指向第一个对象的指针：

```c++
int *pia = new int[get_size()]; // pia指向第一个int
// !!! 注意方括号中的大小必须是整形，但不必是常量
// 也可以用一个表述数组类型的类型别名来分配一个数组这样，new表达式中就不需要方括号了：
typedef int arrT[42];	// arrT表示42个int的整形数组
int *p = new arrT;		// 分配一个42个int的数组；p指向第一个int;
```



### 2.2 allocated类

​		new有一些灵活性上的局限，一方面表现在它将内存分配和对象构造组合在了一起。类似的，delete将对象析构和内存释放组合在了一起。

​		当分配一大块内存时，我们通常计划在这块内存上按需构造对象。在此情况下， 我们希望将内存分配和对象构造分离。这意味着我们分配大块内存， 但只在真正需要时才真正执行对象创建操作（同时付出一定开销）；

​		一般情况下，将内存分配和对象构造组合在一起可能会导致不必要的浪费。例如：

```c++
string *const p = new string[n]; // 构造n个空的string
string s;
string *q = p; 					// q指向第一个string
while(cin >> s && q != p +n)
	*q++  = s;

const size_t size = q - p;
// 使用数组
delete[] p;		// p指向一个数组； 记得用delete[] 来释放

```



allocated 类

​		标准库allocator 类定义在头文件memory中，他帮助我们将内存分配和对象构造分离开来。它提供一种类型感知的内存分配防范，它分配的内存是原始的、未构造的。

​		类似vector、allocator是一个模板。当一个allocator 对象分配内存时，它会根据给定的对象类型来确定恰当的内存大小和对齐位置：

```c++
allocator<string> alloc;			// 可以分配string的allocated对象
auto const  p = alloc.allocate(n);	 // 分配n个未初始化的string
```

​		完成示例：

```c++
// allocated使用完整示例

```



未完待续。。。

## 3 使用标准库： 文本查询程序

待完成。。。

















