# Linux下编译Boost.python库



## 一、前言

​		有待添加。



## 二、安装Python 3.6

### 2.1   安装包下载

下载离线安装包，并解压。

```sh

sudo apt-get http://xxxx.Python-3.6.8.tgz
tar -zxvf Python-3.6.1.tgz
cd Python-3.6.1/

```

### 2.2  编译安装

安装

```shell
./configure --prefix=/usr/local/
make
make altinstall
```

### 2.3  配置为默认python环境

```shell
cd /usr/bin
my python python.backup
ln -s /usr/local/bin/python3.6 /usr/bin/python
ln -s /usr/local/bin/python3.6 /usr/bin/python3

rm -rf /usr/bin/python2
ln -s /usr/bin/python2.7 /usr/bin/python2

```



## 三、安装boost.python库

### 3.1 下载boost库离线安装包

### 3.2 编译安装

### 3.3 测试

#### 测试代码

```c++
#include <string>
#include <boost/python.hpp>

using namespace std;
using namespace boost::python;
	
struct World{
    void set(string msg) { this->msg = msg; }
    string greet() { return msg; }
    string msg;
};

//特别注意下面的模块名hello同将来引入Python的模块名、编译完成的文件名，三者必须相同 
BOOST_PYTHON_MODULE(hello){
   class_<World>("World")
        .def("greet", &World::greet)
        .def("set", &World::set)
   ;
}
```

#### 编译

```sh
#生成.o临时编译文件
g++ -fpic -c hello.cpp $(pkg-config --cflags python3)
#生成.so工作文件
g++ -shared -o hello.so hello.o -lboost_python36 $(pkg-config --cflags --libs python3)
```

上面的两行编译命令中，有两个编译参数可能是需要根据具体版本做调整的，一个是pkg-config库管理工具中的python3，这个名称和版本号可以检查如下路径的配置文件，根据自己需要选择对应的库版本，比如python3对应需要有python3.pc文件：

```shell
ls /usr/local/lib/pkgconfig/python*pc
```

　另外一个是第二行命令中的-lboost_python37，这个检查已经安装的库版本来决定，比如-lboost_python37对应需要有libboost_python37.dylib文件，特别注意这个版本同将来运行的python环境版本必须精确一致，小版本也必须相同：

```sh
ls /usr/local/lib/libboost_python*
```

#### 验证

```python
import hello

test = hello.World()
test.set("HelloWorld")
test.greet()
```

#### bjam编译

boost官方推荐使用Boost.Build系统bjam来编译，比Makefile之类的确会略微的方便一点，这里介绍出来供参考。
安装bjam：`brew install bjam`。　

在当前目录建立一个文本文件Jamroot,内容为：

```bash
import python ;

using python : 3 ;

lib boost_python37 ;

project demo
  : requirements
    <location>.
    <library>boost_python37
  ;	
#注意下面的hello,同cpp文件中最后导出的模块名必须相同
python-extension hello
	: hello.cpp
	: <cxxflags>"`pkg-config --cflags python3`"
	: <linkflags>"`pkg-config --libs python3`"
	;
```

在命令行执行bjam命令，会自动编译生成hello.o及hello.dylib文件，.o文件为临时文件可以删除，.dylib文件改名为.so文件就是我们需要的Python扩展库，使用起来是完全相同的。

