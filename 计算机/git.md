# git相关的命令



## 提交到远程仓库



### 1.初始化本地仓库

``` shell
git init
```

这个命令是将这个文件夹设置为本地仓库,本质是一个隐藏的.git文件夹

### 2.添加文件到本地仓库

```shell
git add . //添加所有文件到缓存区

git commit -m "这里写提交注释" // 提交到本地仓库
```

### 3.将本地仓库与远程仓库关联（两种方式:1.http方式；2.SSL方式）

这一步仅第一次需要进行

```shell
git remote add origin https://github.com/JianhaoChung/DGL_GCNER.git
```

后边的是github的http连接或者SSL连接

### 4.将项目推送到远程仓库

```shell
git push -u origin master
```



### 一些查看命令

```shell
git status  //查看当前项目中文件的状态。
```

