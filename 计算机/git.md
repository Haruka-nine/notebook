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


# 失败处理

git提交时超时

原因：git在拉取或者提交项目时，中间会有git的http和https代理，但是我们本地环境本身就有[SSL](https://so.csdn.net/so/search?q=SSL&spm=1001.2101.3001.7020)协议了，所以取消git的https代理即可，不行再取消http的代理。  
解决办法：在项目文件夹的[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)窗口执行下面代码，取消git本身的https代理，使用自己本机的代理。

```shell
git config --global --unset http.proxy//取消http代理
git config --global --unset https.proxy//取消https代理 
```