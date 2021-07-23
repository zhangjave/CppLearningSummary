



# Linux Shell  实践

## 获取当前目录

```shell
# 相对路径 返回 .
SHELL_FOLDER=$(dirname "$0")
# 绝对路径
SHELL_FOLDER=$(cd "$(dirname "$0")";pwd)

SHELL_FOLDER=$(dirname $(readlink -f "$0"))

```



## echo

### 显式普通字符串

```shell
echo "It is a test"
```

这里的双引号完全可以省略，以下命令与上面实例效果一致：

```shell
echo It is a test
```



### 显示转义字符

选项：

- -n 不换行输出
- -e 启用反斜线转义解释
- -E 禁用反斜线转义解释（默认）

### 显示变量

```shell
#!/bin/bash
read name
echo "$name It is a test"
```

### 显示换行

```shell
echo -e "OK! \n" # -e 开启转义
echo "It is a test"
```

### 显示结果定向至文件

```shell
echo "It is a test" > myfile
# 或
echo "It is a test" >> myfile
```



## Shell变量命名规则

变量名必须是以字母或下划线字符“_”开头，后面跟字母、数字或下划线字符。不要使用?、*或其他特殊字符命名你的变量。

注意：

变量名和等号之间不能有空格。
首个字符必须为字母（a-z  A-Z）。
中间不能有空格，可以是下划线。
不能使用标点符号。
不能使用bash里的关键字。



## 流输出 > 和 >>

```shell
# 在shell中， '>'  为创建： 
echo “hello shell”  > out.txt

# '>>' 为追加：
echo “hello shell”  >> out.txt
# 当out.txt 文本不存在时,'>'与‘>>’都会默认创建out.txt文本，并将hello shell 字符串保存到out.txt中
# 当out.txt文本存在时，‘>’会将out.txt文本中的内容清空，并将hello shell 字符串存入
# 而‘>>’会将 hello shell追加保存到out.txt的末尾
```



## 获取当前日期时间

### 原格式输出

```shell
#!/bin/bash
time1=$(date)
echo $time1
```

### 时间串输出

```shell
#!/bin/bash
time2=$(date "+%Y-%m-%d %H:%M:%S")
time3=$(date "+%Y%m%d%H%M%S")
echo $time2
echo $time3
```

## 判断文件或文件夹是否存在

### 文件夹不存在则创建

```shell
if [ ! -d "/data/" ];then
	mkdir /data
else
	echo "文件夹已经存在"
fi
```

### 文件存在则删除

```shell
if [ ! -f "/data/filename"];then
	echo "文件不存在"
else
	rm -f /data/filename
fi
```

### 判断文件夹是否存在

```shell
if [ -d "/data/" ];then
	echo "文件夹存在"
else
	echo "文件夹不存在"
fi
```

### 判断文件是否存在

```shell
if [ -f "/data/filename"];then 
	echo "文件存在"
else
	echo "文件不存在"
fi
```

### 文件比较符

```shell
-e 判断对象是否存在
-d 判断对象是否存在，并且为目录``
-f 判断对象是否存在，并且为常规文件``
-L 判断对象是否存在，并且为符号链接``
-h 判断对象是否存在，并且为软链接``
-s 判断对象是否存在，并且长度不为0``
-r 判断对象是否存在，并且可读``
-w 判断对象是否存在，并且可写``
-x 判断对象是否存在，并且可执行``
-O 判断对象是否存在，并且属于当前用户``
-G 判断对象是否存在，并且属于当前用户组``
-nt 判断file1是否比file2新 [ ``"/data/file1"` `-nt ``"/data/file2"` `]``
-ot 判断file1是否比file2旧 [ ``"/data/file1"` `-ot ``"/data/file2"` `]
```