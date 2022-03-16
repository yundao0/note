如何修改远程地址

https://blog.csdn.net/asdfsfsdgdfgh/article/details/54981823

基本操作+分制管理

https://www.runoob.com/git/git-branch.html

一般流程

https://www.cnblogs.com/manjin666/p/9634324.html

每次最初需要设置上下行的分枝对应

https://blog.csdn.net/nonfuxinyang/article/details/77206479

不同版本历史

https://blog.csdn.net/qq_39400546/article/details/100150320



git push --set-upstream origin master

# 初始配置步骤

```
//电脑最初配置
git config --global user.email "cloudshow11@gmail.com"
git config --global user.name "yundao0"

ssh-keygen -t rsa -C "cloudshow11@gmail.com"
```



```
//本地文件夹中
git init
cd.>README.md 或者 touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://**********
git push -u origin master
```

# 基本语句

```bash
# 新分支
$ git checkout -b iss53 #新建并切换
Switched to a new branch "iss53"
# 上面1个语句是下面2个语句的简写
$ git branch iss53 #建立分支
$ git checkout iss53 #切换分支
```

```bash
#版本回退
$ git reset --hard 版本号
```



# 代理

```
set HTTP_PROXY=http://127.0.0.1:12333
set HTTPS_PROXY=http://127.0.0.1:12333

export http_proxy="http://127.0.0.1:12333"
export https_proxy="http://127.0.0.1:12333"
```

