# git命令

## 配置用户名和邮箱

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```



## 建立本地git仓库

1.cd到你的项目路径

```sh
cd /Users/cjk/Desktop/myShop
```

2.然后，输入git命令

```sh
git init
```

创建了一个空的本地仓库

3.将项目的所有文件添加到缓存中

```sh
git add .
```

4.将缓存中的文件Commit到git库

```sh
git commit -m "添加注释，一般是一些更改信息"
```

# 一、拉取Git项目到本地

## 1. 打开终端，cd到自己想要存放项目的文件夹

> $ cd /Users/ioskaifa/Desktop

## 2. 输入git clone url,url为你要拉取的项目地址

> $ git clone https://github.com/...

## 3. 项目拉取成功

# 二、 项目改动后上传到GitHub

## 1. git add 你改动后的文件,如果想要全部上传 git add .

> $ git add .

## 2. 执行git commit -m “添加注释”

> $ git commit -m "注释"

## 3. git push 上传，可能会让你输入github账号和密码按提示输入即可

> $ git push

# 三、git命令

查询当前远程的版本

> $ git remote -v

将新建的文件或修改过的文件添加到索引库

> $ git add .

本地分支代码保存到本地仓库

> $ git commit -m " 注释"

直接拉取并合并最新代码

> $ git pull origin master  // 拉取远端origin/master分支并合并到当前分支
>
> $ git pull origin dev // 拉取远端origin/dev分支并合并到当前分支

从本地提交代码到服务器

> $ git push origin master  // 将当前分支提交到远端origin/master分支
>  push到GitHub的文件要求小于100M



作者：旭日猎鹰
链接：https://www.jianshu.com/p/4a6ec946194c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。