

# 初识Node.js

## 思考

1.为什么JavaScript可以在浏览器中被执行？

​	因为每个浏览器有相应的JavaScript引擎，可以解析JS代码

2.为什么JavaScript可以操作Dom和Bom？

​	每个浏览器都内置了Dom和Bom这样的API函数，所以JS代码可以调用它们

3.JavaScript运行环境

​	浏览器提供的引擎来解析和执行JS代码，并提供API作为接口供JS去调用

​	运行环境需要提供解析引擎，和API来给JS使用

​	浏览器和Node.js分别是两种运行环境

## Node.js简介

### 什么是Node.js

Node.js是一个基于Chrome V8引擎的JavaScript运行环境

官网：http://nodejs.cn/

1.浏览器是JavaScript的前端运行环境

2.Node.js是JavaScript的后端运行环境

3.Node.js中无法调用Dom和Bom等浏览器内置API

### 终端的简单使用

```shell
node -v //查看node的版本

node  xxx //xxx为要执行的文件
```

# fs文件系统模块

fs模块是Node.js官方提供的、用来操作文件的模块。它提供了一系列的方法和属性，用来满足用户对文件的操作需求

例如：

- fs.readFile()方法，用来读取指定文件中的内容
- fs.writeFile()方法，用来向指定的文件中写入内容

```js
const fs  = require('fs')  //导入fs模块
```

## 读取指定文件中的内容

```js
fs.readFile(path[,options],callback)
```

- path: 必选参数，字符串，表示文件的路径
- options：可选参数，表示以什么文件编码来读取文件
- callback：必选参数，文件读取完成后，通过回调函数拿到读取的结果

示例代码

```js
//导入fs模块，来操作文件
const fs = require('fs')

//调用fs.readFile()方法读取文件
//参数1:读取文件的存放路径
//参数2:读取文件时采用的编码格式，一般默认指定 utf-8
//参数3:回调函数，拿到读取失败和成功的结构 err  dataStr

fs.readFile('./files/1.txt','utf-8',function (err, dataStr) {
    //打印失败的结果
    console.log(err)
    console.log('----')
    //打印成功的结果
    console.log(dataStr)
})
```



可以通过判断err是否为null来判断文件是否读取成功

```js
const fs = require('fs')

fs.readFile('./files/1.txt','utf-8',function (err, dataStr) {
    if (err){
        return console.log('读取文件失败'+err.message)
    }
    console.log('读取文件成功',+dataStr)
})
```



## 向指定文件中写入内容

```js
fs.writeFile(file,data[,options],callback)
```

- file: 必选参数，字符串，表示文件的路径
- data:必选参数，表示要写入的内容。
- options：可选参数，表示以什么文件编码来读取文件，默认为utf-8
- callback：必选参数，文件写入完成后的回调函数

```js
const fs = require('fs')

fs.writeFile('./files/2.txt','abcd',function (err) {
    console.log(err)
})
```

同样也可以通过err是否为空来判断时候写入成功

## 路径问题

在使用fs模块操作文件时，如果提供的操作路径是以./或../开头的相对路径时，很容易出现路径动态拼贴错误的问题

原因：代码在运行的时候，会以执行node命令时所处的目录，动态拼接出被操作文件的完整路径（**而不是你js文件所在目录**）

解决方法：在操作文件时，不使用./或者../，而是直接使用绝对路径

```js
const fs = require('fs')

fs.readFile('C:\\Users\\YYM\\Desktop\\webstorm\\node_code\\files\\1.txt','utf-8',function (err, dataStr) {
    if (err){
        return console.log('读取文件失败'+err.message)
    }
    console.log('读取文件成功',+dataStr)
})
```

但这也带来另一个问题：移植性差，不利于维护

处理方法:使用 __dirname表示文件所处的目录

```js
// __dirname表示文件所处的目录
const fs = require('fs')

fs.readFile(__dirname+'/files/1.txt','utf-8',function (err, dataStr) {
    if (err){
        return console.log('读取文件失败'+err.message)
    }
    console.log('读取文件成功',+dataStr)
})
```

# path路径模块

path模块时Node.js官方提供的、用来处理路径的模块。它提供了一系列的方法和属性，用来满足用户对路径的处理

```js
path.join() //用来将多个路径片段拼接
path.basename() //用来从路径字符串中，解析出文件名
path.extname() //用来获取路径中的扩展名
```

使用前需要先导入它：

```js
const path = require('path')
```

## path.join()

```js
const path = require('path')
const pathStr = path.join('/a','/b/c','../','./d','e')
console.log(pathStr)
```

```js
const path = require('path')
const fs = require('fs')

fs.readFile(path.join(__dirname,'/files/1.txt'),'utf-8',function (err, dataStr) {
    if (err){
        return console.log('读取文件失败'+err.message)
    }
    console.log('读取文件成功',+dataStr)
})

```

## path.basename()

```js
path.basename(path[,ext])
```

- path <string> 必选参数，表示一个路径的字符串
- ext <string> 可选参数，表示文件扩展名（如果写了返回值就会删除扩展名）
- 返回: <string> 表示路径中的最后一部分

```js
const path = require('path')
//定义文件的存放路径
const fpath = '/a/b/c/index.html'

const fullName = path.basename(fpath)
console.log(fullName)
```

```js
const path = require('path')
//定义文件的存放路径
const fpath = '/a/b/c/index.html'

const nameWithoutExt = path.basename(fpath,'.html')
console.log(nameWithoutExt)
```

## path.extname()

```js
path.extname(path)
```

- path <string> 必选参数，表示一个路径的字符串
- 返回: <string> 返回得到的扩展名字符串

```js
const path = require('path')
//这是文件的存放路径
const fpath = '/a/b/c/index.html'
const fext = path.extname(fpath)
console.log(fext)
```

# http模块

## 什么是http模块

在网络节点中

负责消费资源的电脑，叫做客户端

负责对外提供网络资源的电脑，叫做服务器

http模块是Node.js官方提供的、用来创建web服务器的模块

通过http模块提供的http.createServer()方法，可以把一台普通的电脑，变成一台web服务器

## http模块的作用

服务器和普通电脑的区别在于，服务器上安装了web服务器软件，例如 IIS、Apache等。通过安装这些服务器软件，就能把一台普通的电脑变成一台web服务器

在Node.js中，我们不需要那种第三方的软件，我么能使用http模块，就能对外提供web服务

## 创建最基本的web服务器

### 使用

1.导入http模块

```js
//引入http模块
const http = require('http')
```

2.创建web服务器示例

```js
const server = http.createServer()
```

3.为服务器绑定request事件

```js
server.on('request',function (req, res) {
    console.log('someone visit our web server')
})
```

4.启动服务器-使用服务器示例的.listen()方法

```js
server.listen(8080,function () {
    console.log('server running at http://127.0.0.1:8080')
})
```

示例

```js
const http = require('http')

const server = http.createServer()

server.on('request',function (req, res) {
    console.log('someone visit our web server')
})
server.listen(8080,function () {
    console.log('server running at http://127.0.0.1:8080')
})
```

### req请求对象

- req是请求对象，它包含了与客户端相关的数据和属性
- req.url是客户端请求的url地址(不是完整的，是端口号后边的地址)
- req.method是客户端的method请求类型

```js
const http = require('http')

const server=http.createServer()

server.on('request',(req)=>{
    const url = req.url
    const method = req.method
    const str = `Your request url is ${url},and request method is ${method}`
    console.log(str)
})
server.listen(80,()=>{
    console.log('server running at http://127.0.0.1')
})
```

### res响应对象

在服务器的request事件处理函数中，如果向访问与服务器相关的数据或属性，使用res响应对象

```js
res.end()//向客户端发送指定的内容，并结束这次请求的处理过程
```



```js
server.on('request',(req,res)=>{
    const url = req.url
    const method = req.method
    const str = `Your request url is ${url},and request method is ${method}`
    console.log(str)
    res.end(str)
})
//这里将请求处理结束，并且返回了str
```

### 解决中文乱码问题

当使用res.end()方法，向客户端发送中文内容的时候，会出现乱码问题，此时，需要手动设置内容的编码格式

```js
const http =require('http')
const server = http.createServer()
server.on('request',(req, res)=>{
    const str = `您请求的url地址是${req.url},请求的method类型是${req.method}`
    //调用res.setHeader()方法，设置Content-Type 响应头,将编码设置为utf-8
    res.setHeader('Content-Type','text/html;charset=utf-8')
    res.end(str)
})
server.listen(80,()=>{
    console.log('server is running at http://127.0.0.1')
})
```

## 根据不同的url响应不同的html内容

### 核心实现步骤

①获取请求的 url 地址

②设置默认的响应内容为 404 Not found

③判断用户请求的是否为 / 或 /index.html 首页

④判断用户请求的是否为 /about.html 关于页面

⑤设置 Content-Type 响应头，防止中文乱码

⑥使用 res.end() 把内容响应给客户端

### 代码

```js
const http =require('http')
const server = http.createServer()
server.on('request',(req, res)=>{
    const url = req.url
    let content = '404 Not found'
    if (url==='/'||url==='/index.html')
    {
        content='<h1>首页</h1>'
    }
    else if (url==='/about.html')
    {
        content='<h1>关于</h1>'
    }
    res.setHeader('Content-Type','text/html;charset=utf-8')
    res.end(content)
})
server.listen(80,()=>{
    console.log('server is running at http://127.0.0.1')
})
```

# 模块化

## 模块化概念

**模块化**是指解决一个复杂问题时，自顶向下逐层把系统划分成若干模块的过程。对于整个系统来说，模块是可组合、分解和更换的单元。

编程领域中的模块化，就是**遵守固定的规则**，把一个大文件拆成独立并互相依赖的多个小模块。

把代码进行模块化拆分的好处：

①提高了代码的复用性

②提高了代码的可维护性

③可以实现按需加载

## Node.js模块化

### 分类

Node.js 中根据模块来源的不同，将模块分为了 3 大类，分别是：

- 内置模块（内置模块是由 Node.js 官方提供的，例如 fs、path、http 等）
- 自定义模块（用户创建的每个 .js 文件，都是自定义模块）
- 第三方模块（由第三方开发出来的模块，并非官方提供的内置模块，也不是用户创建的自定义模块，使用前需要先下载）

### 加载模块

使用强大的 require() 方法，可以加载需要的内置模块、用户自定义模块、第三方模块进行使用。

```js
//加载内置的fs模块
const fs = require('fs')

//加载用户的自定义模块（加载自定义模块需要写路径）
//而且自定义模块可以省略.js的后缀名const m1 = require('./06.m1.js')
const m1 = require('./06.m1')

//加载第三方模块
const moment = require('moment')
```

**注意:使用require()方法加载其他模块时，会执行被加载模块中的代码**

### 模块作用域

和函数作用域类似，在自定义模块中定义的变量、方法等成员，只能在当前模块内被访问，这种模块级别的访问限制，叫做**模块作用域**。

可以防止全局变量污染的问题

### 共享模块作用域成员

#### **1.** **module** **对象**

在每个 .js 自定义模块中都有一个 module 对象，它里面存储了和当前模块有关的信息

```js
console.log(module)//可以打印出来
```

```shell
Module {
  id: '.',
  path: 'C:\\Users\\YYM\\Desktop\\webstorm\\node_http',
  exports: {},
  filename: 'C:\\Users\\YYM\\Desktop\\webstorm\\node_http\\10.演示modele对象.js',
  loaded: false,
  children: [],
  paths: [
    'C:\\Users\\YYM\\Desktop\\webstorm\\node_http\\node_modules',
    'C:\\Users\\YYM\\Desktop\\webstorm\\node_modules',
    'C:\\Users\\YYM\\Desktop\\node_modules',
    'C:\\Users\\YYM\\node_modules',
    'C:\\Users\\node_modules',
    'C:\\node_modules'
  ]
}
```

#### 2.**module.exports** **对象**

在自定义模块中，可以使用 module.exports 对象，将模块内的成员共享出去，供外界使用。

外界用 require() 方法导入自定义模块时，得到的就是 module.exports 所指向的对象。

#### 3.exports对象

由于 module.exports 单词写起来比较复杂，为了简化向外共享成员的代码，Node 提供了 exports 对象。默认情况下，exports 和 module.exports 指向同一个对象。最终共享的结果，还是以 module.exports 指向的对象为准。

### 模块化规范

Node.js 遵循了 CommonJS 模块化规范，CommonJS 规定了模块的特性和各模块之间如何相互依赖。



CommonJS 规定：

①每个模块内部，module 变量代表当前模块。

②module 变量是一个对象，它的 exports 属性（即 module.exports）是对外的接口。

③加载某个模块，其实是加载该模块的 module.exports 属性。require() 方法用于加载模块。

## npm和包

### 包

#### 什么是包

Node.js 中的第三方模块又叫做包。

就像电脑和计算机指的是相同的东西，第三方模块和包指的是同一个概念，只不过叫法不同。

#### 包的来源

不同于 Node.js 中的内置模块与自定义模块，包是由第三方个人或团队开发出来的，免费供所有人使用。

**注意**：Node.js 中的包都是免费且开源的，不需要付费即可免费下载使用。

#### 为什么需要包

由于 Node.js 的内置模块仅提供了一些底层的 API，导致在基于内置模块进行项目开发的时，效率很低。

包是基于内置模块封装出来的，提供了更高级、更方便的 API，极大的提高了开发效率。

包和内置模块之间的关系，类似于 jQuery 和 浏览器内置 API 之间的关系。

#### 从哪里下载包

国外有一家 IT 公司，叫做 **npm, Inc.** 这家公司旗下有一个非常著名的网站： https://www.npmjs.com/ ，它是**全球最大的包共享平台**，你可以从这个网站上搜索到任何你需要的包，只要你有足够的耐心！

到目前位置，全球约 1100 多万的开发人员，通过这个包共享平台，开发并共享了超过 120 多万个包 供我们使用。

**npm, Inc.** **公司**提供了一个地址为 https://registry.npmjs.org/ 的服务器，来对外共享所有的包，我们可以从这个服务器上下载自己所需要的包。

**注意：**

- 从 https://www.npmjs.com/ 网站上搜索自己所需要的包
- 从 https://registry.npmjs.org/ 服务器上下载自己需要的包

### 切换npm的下包镜像源

#### 手动切换

```shell
npm config get registry #查看当前的下包镜像源
npm config set registry=https://registry.npm.taobao.org/  #切换为淘宝镜像源
```

#### nrm工具

为了更方便的切换下包的镜像源，我们可以安装 **nrm** 这个小工具，利用 nrm 提供的终端命令，可以快速查看和切换下包的镜像源。

```shell
npm i nrm -g #先全局安装nrm工具
nrm ls    #查看所有可用的镜像源
nrm use taobao #将下包的镜像源切换为taobao镜像
```



### npm 使用

![](https://img-blog.csdnimg.cn/20210912112123102.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6ICB5p2O5aS055qE5Luj56CB55Sf5rS7,size_20,color_FFFFFF,t_70,g_se,x_16)

```shell
npm i 包的完整名称    #安装包
```

这个会安装到项目文件夹，而不是全局包



```shell
#安装指定版本的包
#在后边用@版本号
npm i moment@2.22.2
```

#### 包的语义化版本规范

包的版本号是以“点分十进制”形式进行定义的，总共有三位数字，例如 **2.24.0**

其中每一位数字所代表的的含义如下：

第1位数字：大版本

第2位数字：功能版本

第3位数字：Bug修复版本

版本号提升的规则：只要前面的版本号增长了，则后面的版本号归零。

### npm文件

初次装包完成后，在项目文件夹下多一个叫做 node_modules 的文件夹和 package-lock.json 的配置文件。

其中：

node_modules 文件夹用来存放所有已安装到项目中的包。require() 导入第三方包时，就是从这个目录中查找并加载包。

package-lock.json 配置文件用来记录 node_modules 目录下的每一个包的下载信息，例如包的名字、版本号、下载地址等。



注意：程序员不要手动修改 node_modules 或 package-lock.json 文件中的任何代码，npm 包管理工具会自动维护它们。

### 包管理配置文件

**使用webstrom进行Vue开发时：配置文件和忽略信息都会自动创建**（以下是了解原理）

npm 规定，在项目根目录中，**必须**提供一个叫做 package.json 的包管理配置文件。用来记录与项目有关的一些配置信息。例如：

- 项目的名称、版本号、描述等
- 项目中都用到了哪些包
- 哪些包只在开发期间会用到
- 那些包在开发和部署时都需要用到

#### 多人协作问题

整个项目的体积很大程度上都是第三方包的体积，实际源代码的体积并不大

所以我们一般不上传第三方包，需要剔除node_modeles

#### **如何记录项目中安装了哪些包**

在项目根目录中，创建一个叫做 package.json 的配置文件，即可用来记录项目中安装了哪些包。从而方便剔除 node_modules 目录之后，在团队成员之间共享项目的源代码。

**注意**：今后在项目开发中，一定要把 node_modules 文件夹，添加到 .gitignore 忽略文件中。

#### 创建package.json

在项目创建之初就建立，之后这个文件会根据项目中的包自动修改

```shell
npm init -y
```

①上述命令只能在英文的目录下成功运行！所以，项目文件夹的名称一定要使用英文命名，不要使用中文，不能出现空格。

②运行 npm install 命令安装包的时候，npm 包管理工具会自动把包的名称和版本号，记录到 package.json 中。

#### **dependencies** **节点**

package.json 文件中，有一个 dependencies 节点，专门用来记录您使用 npm install 命令安装了哪些包。

#### **一次性安装所有的包**

当我们拿到一个剔除了 node_modules 的项目之后，需要先把所有的包下载到项目中，才能将项目运行起来。

```shell
npm i
```

#### **卸载包**

可以运行 npm uninstall 命令，来卸载指定的包：

```shell
npm uninstall 包名
```

注意：npm uninstall 命令执行成功后，会把卸载的包，自动从 package.json 的 dependencies 中移除掉。

#### **devDependencies** **节点**

如果某些包**只在项目开发阶段**会用到，在**项目上线之后不会用到**，则建议把这些包记录到 devDependencies 节点中。

与之对应的，如果某些包在开发和项目上线之后都需要用到，则建议把这些包记录到 dependencies 节点中。

您可以使用如下的命令，将包记录到 devDependencies 节点中：

```
npm i 包名 -D
```

如何判断需要安装的dev还是普通呢？

**通过npm官网查看，看对应的包的安装手册**

### 包的分类

#### 项目包

那些被安装到项目的 node_modules 目录中的包，都是项目包。

项目包又分为两类，分别是：

- 开发依赖包（被记录到 devDependencies 节点中的包，只在开发期间会用到）
- 核心依赖包（被记录到 dependencies 节点中的包，在开发期间和项目上线之后都会用到）

```shell
npm i 包名 -D  #开发依赖包（被记录到 devDependencies 节点中的包，只在开发期间会用到）
npm i 包名    #核心依赖包（被记录到 dependencies 节点中的包，在开发期间和项目上线之后都会用到）
```

#### 全局包

在执行 npm install 命令时，如果提供了 -g 参数，则会把包安装为全局包。

全局包会被安装到 C:\Users\用户目录\AppData\Roaming\npm\node_modules 目录下。

```shell
npm i 包名 -g   #全局安装包
npm uninstall 包名 -g   #全局卸载包
```

注意：

①只有工具性质的包，才有全局安装的必要性。因为它们提供了好用的终端命令。

②判断某个包是否需要全局安装后才能使用，可以参考官方提供的使用说明即可。

#### i5ting_toc

i5ting_toc 是一个可以把 md 文档转为 html 页面的小工具，使用步骤如下：

```shell
#将 i5ting_toc 安装为全局包
npm install -g  i5ting_toc
# 调用 i5ting_toc，将md转html
i5ting_toc -f  要转换的文件路径 -o
```

### 规范的包结构

在清楚了包的概念、以及如何下载和使用包之后，接下来，我们深入了解一下包的内部结构。



一个规范的包，它的组成结构，必须符合以下 3 点要求：

①包必须以单独的目录而存在

②包的顶级目录下要必须包含 package.json 这个包管理配置文件

③package.json 中必须包含 name，version，main 这三个属性，分别代表包的名字、版本号、包的入口。

注意：以上 3 点要求是一个规范的包结构必须遵守的格式，关于更多的约束，可以参考如下网址：

https://yarnpkg.com/zh-Hans/docs/package-json

### 开发属于自己的包

#### 需要实现的功能

（这里以这里三个功能作为示例）

① 格式化日期

② 转义 HTML 中的特殊字符

③ 还原 HTML 中的特殊字符

#### 初始化包的基本结构

①新建 haruka-tools 文件夹，作为包的根目录

②在 haruka-tools 文件夹中，新建如下三个文件：

- package.json （包管理配置文件）
- index.js     （包的入口文件）
- README.md （包的说明文档）

#### **初始化** **package.json**

```JSON
{
  "name": "haruka-tools",
  "version": "1.0.0",
  "main": "index.js",
  "description": "提供了格式化时间，HTMLEscape的功能", //包的简介
  "keywords": ["itheima","dataFormat","escape"], //关键词
  "license": "ISC"  //开源协议
}

```

关于更多 license 许可协议相关的内容，可参考 https://www.jianshu.com/p/86251523e898

#### 将不同的模块进行拆分

首先先写出功能对应的函数

①将格式化时间的功能，拆分到 src -> dateFormat.js 中

②将处理 HTML 字符串的功能，拆分到 src -> htmlEscape.js 中

③在 index.js 中，导入两个模块，得到需要向外共享的方法

④在 index.js 中，使用 module.exports 把对应的方法共享出去

```js
const dataFormat = require('./src/dataFormat')
const escape = require('./src/htmlEscape')


module.exports={
    ...dataFormat,
    ...escape   //使用...将其拆分，可以减少使用时的一层嵌套
}
```

### 发布包

#### 注册npm账号

①访问 https://www.npmjs.com/ 网站，点击 sign up 按钮，进入注册用户界面

②填写账号相关的信息：Full Name、Public Email、Username、Password

③点击 Create an Account 按钮，注册账号

④登录邮箱，点击验证链接，进行账号的验证

#### 登陆npm账号

npm 账号注册完成后，可以在终端中执行 npm login 命令，依次输入用户名、密码、邮箱后，即可登录成功。

**注意：在运行 npm login 命令之前，必须先把下包的服务器地址切换为 npm 的官方服务器。否则会导致发布包失败！**

#### 把包发布到npm上

将终端切换到包的根目录之后，运行 npm publish 命令，即可将包发布到 npm 上（注意：包名不能雷同）。

#### 删除已发布的包

运行 npm unpublish 包名 --force 命令，即可从 npm 删除已发布的包。

注意：

①npm unpublish 命令只能删除 72 小时以内发布的包

②npm unpublish 删除的包，在 24 小时内不允许重复发布

③发布包的时候要慎重，尽量不要往 npm 上发布没有意义的包！

## 模块的加载机制

### 模块优先从缓存中加载

**模块在第一次加载后会被缓存**。 这也意味着多次调用 require() 不会导致模块的代码被执行多次。

注意：不论是内置模块、用户自定义模块、还是第三方模块，它们都会优先从缓存中加载，从而提高模块的加载效率。

### **内置模块的加载机制**

内置模块是由 Node.js 官方提供的模块，内置模块的加载优先级最高。

例如，require('fs') 始终返回内置的 fs 模块，即使在 node_modules 目录下有名字相同的包也叫做 fs。

### **自定义模块的加载机制**

使用 require() 加载自定义模块时，必须指定以 ./ 或 ../ 开头的路径标识符。在加载自定义模块时，如果没有指定 ./ 或 ../ 这样的路径标识符，则 node 会把它当作内置模块或第三方模块进行加载。

同时，在使用 require() 导入自定义模块时，如果省略了文件的扩展名，则 Node.js 会按顺序分别尝试加载以下的文件：

①按照确切的文件名进行加载

②补全 .js 扩展名进行加载

③补全 .json 扩展名进行加载

④补全 .node 扩展名进行加载

加载失败，终端报错

### **第三方模块的加载机制**

如果传递给 require() 的模块标识符不是一个内置模块，也没有以 ‘./’ 或 ‘../’ 开头，则 Node.js 会从当前模块的父目录开始，尝试从 /node_modules 文件夹中加载第三方模块。

如果没有找到对应的第三方模块，则移动到再上一层父目录中，进行加载，直到文件系统的根目录。

例如，假设在 'C:\Users\itheima\project\foo.js' 文件里调用了 require('tools')，则 Node.js 会按以下顺序查找：

① C:\Users\itheima\project\node_modules\tools

② C:\Users\itheima\node_modules\tools

③ C:\Users\node_modules\tools

④ C:\node_modules\tools

### **目录作为模块**

当把目录作为模块标识符，传递给 require() 进行加载的时候，有三种加载方式：

①在被加载的目录下查找一个叫做 package.json 的文件，并寻找 main 属性，作为 require() 加载的入口

②如果目录里没有 package.json 文件，或者 main 入口不存在或无法解析，则 Node.js 将会试图加载目录下的 index.js 文件。

③如果以上两步都失败了，则 Node.js 会在终端打印错误消息，报告模块的缺失：Error: Cannot find module 'xxx'

# Express

## Express简介

### 什么是express

官方给出的概念：Express 是基于 Node.js 平台，快速、开放、极简的 Web 开发框架。

通俗的理解：Express 的作用和 Node.js 内置的 http 模块类似，是专门用来创建 Web 服务器的。

**Express** **的本质**：就是一个 npm 上的第三方包，提供了快速创建 Web 服务器的便捷方法。

Express 的中文官网：[ http://www.expressjs.com.cn/](http://www.expressjs.com.cn/)

### **进一步理解** **Express**

思考：不使用 Express 能否创建 Web 服务器？

答案：能，使用 Node.js 提供的原生 http 模块即可。



思考：既生瑜何生亮（有了 http 内置模块，为什么还有用 Express）？

答案：http 内置模块用起来很复杂，开发效率低；Express 是基于内置的 http 模块进一步封装出来的，能够极大的提高开发效率。



思考：http 内置模块与 Express 是什么关系？

答案：类似于浏览器中 Web API 和 jQuery 的关系。后者是基于前者进一步封装出来的。

### Express能做什么

对于前端程序员来说，最常见的两种服务器，分别是：

-  Web 网站服务器：专门对外提供 Web 网页资源的服务器。
-  API 接口服务器：专门对外提供 API 接口的服务器。

使用 Express，我们可以方便、快速的创建 Web 网站的服务器或 API 接口的服务器。

## Express的使用

### 安装

```shell
npm i express
```

### 创建基本的Web服务器

```js
//1.导入express
const express = require('express')

//创建web服务器
const app = express()

//启动web服务器
app.listen(80,()=>{
    console.log('express server running at http://127.0.0.1')
})
```

### 监听GET请求

```js
//参数1:客户端请求的URL地址
//参数2:请求对应的处理函数
//     req:请求对象（包含了与请求相应的属性和方法）
//     res:相应对象（包含了与响应对应的属性和方法）
app.get('请求URL',function (req, res) {

})
```

### 监听POST请求

```js
//参数1:客户端请求的URL地址
//参数2:请求对应的处理函数
//     req:请求对象（包含了与请求相应的属性和方法）
//     res:相应对象（包含了与响应对应的属性和方法）
app.post('请求URL',function (req, res) {

})
```

### 把内容发送给客户端

通过res.send方法，可以把处理好的内容发送给客户端

```js
app.get('/user',(req, res)=>{
    res.send({name:'zs',age:20,gender:'男'})
})
app.post('/user',function (req, res) {
    res.send('请求成功')
})
```

### **获取** **URL** **中携带的查询参数**

```js
app.get('/',(req, res)=>{
    //req.query默认是一个空对象
    //客户端使用?name=zx&age=20这种查询字符串形式，发送到服务器的参数可以通过req.query对象访问到
    //例如: req.query.name  req.query.age
    console.log(req.query)
})
```

### **获取** **URL**中的动态参数

通过req.params对象，可以访问到URL中，通过:匹配到的动态参数

```js
app.get('/user/:id',(req, res)=>{
    //req.query默认是一个空对象
    //客户端使用?name=zx&age=20这种查询字符串形式，发送到服务器的参数可以通过req.query对象访问到
    //例如: req.query.name  req.query.age
    console.log(req.params)
    res.send(req.params)
})
```

这里发送请求 http://127.0.0.1/user/1 可以得 id =1

## 托管静态资源

### express.static()

express 提供了一个非常好用的函数，叫做 express.static()，通过它，我们可以非常方便地创建一个静态资源服务器，例如，通过如下代码就可以将 public 目录下的图片、CSS 文件、JavaScript 文件对外开放访问了：

```js
app.use(express.static('public'))
```

现在，你就可以访问 public 目录中的所有文件了：

http://localhost:3000/images/bg.jpg

http://localhost:3000/css/style.css

http://localhost:3000/js/login.js

**注意：**Express 在指定的静态目录中查找文件，并对外提供资源的访问路径。

因此，存放静态文件的目录名不会出现在 URL 中。(也就是说，public这一层目录是不需要写的)

### 托管多个静态资源目录

多次调用express.static

```js
app.use(express.static('public'))
app.use(express.static('files'))
```

访问静态资源文件时，express.static() 函数会根据目录的添加顺序查找所需的文件。

两个文件夹有相同命名的文件，按添加顺序查找

### **挂载**路径前缀

如果希望在托管的静态资源访问路径之前，挂载路径前缀，则可以使用如下的方式：

```js
app.use('/public',express.static('public'))
```

## nodemon

### 为什么要使用nodemon

在编写调试 Node.js 项目的时候，如果修改了项目的代码，则需要频繁的手动 close 掉，然后再重新启动，非常繁琐。

现在，我们可以使用 nodemon（https://www.npmjs.com/package/nodemon） 这个工具，它能够监听项目文件的变动，当代码被修改后，nodemon 会自动帮我们重启项目，极大方便了开发和调试。

### 安装nodemon

在终端中，运行如下命令，即可将 nodemon 安装为全局可用的工具：

```shell
npm install -g nodemon
```

### 使用

基于 Node.js 编写了一个网站应用的时候，传统的方式，是运行 node app.js 命令，来启动项目。这样做的坏处是：代码被修改之后，需要手动重启项目。

现在，我们可以将 node 命令替换为 nodemon 命令，使用 nodemon app.js 来启动项目。这样做的好处是：代码被修改之后，会被 nodemon 监听到，从而实现自动重启项目的效果。

```js
node app.js
//将上方的命令更换为下方
nodemon app.js
```

## 路由

### **什么是路由**

广义上来讲，路由就是映射关系。

### **Express** **中的路由**

在 Express 中，路由指的是客户端的请求与服务器处理函数之间的映射关系。

Express 中的路由分 3 部分组成，分别是请求的类型、请求的 URL 地址、处理函数，格式如下：

```JS
app.METHOD(PATH,HANDLER)
```

### 示例

```js
//匹配GET请求，且请求URL为/
app.get('/',function(req,res)=>{
res.send('Hello World!')
})
//匹配POST请求，且请求URL为/
app.post('/',function(req,res)=>{
res.send('Hello World!')
})
```

### 路由的匹配过程

每当一个请求到达服务器之后，需要先经过路由的匹配，只有匹配成功之后，才会调用对应的处理函数。

在匹配时，会按照路由的顺序进行匹配，如果请求类型和请求的 URL 同时匹配成功，则 Express 会将这次请求，转交给对应的 function 函数进行处理。

路由匹配的注意点：

①按照定义的先后顺序进行匹配（所以优先级高的先写）

②请求类型和请求的URL同时匹配成功，才会调用对应的处理函数

### 路由的使用

#### 简单使用

```js
const express = require('express')
const app = express()
//挂载路由
app.get('/',(req, res)=>{
    res.send('hello,world')
})

app.post('/',(req, res)=>{
    res.send('Post request')
})

app.listen(80,()=>{
    console.log('http://127.0.0.1')
})
```

#### 模块化路由

为了方便对路由进行模块化的管理，Express **不建议**将路由直接挂载到 app 上，而是推荐将路由抽离为单独的模块。

将路由抽离为单独模块的步骤如下：

①创建路由模块对应的 .js 文件

②调用 express.Router() 函数创建路由对象

③向路由对象上挂载具体的路由

④使用 module.exports 向外共享路由对象

⑤使用 app.use() 函数注册路由模块

##### 创建路由模块

```js
//这是路由模块
//导入express
const express = require('express')
//创建路由对象
const router = express.Router()

//挂载具体的路由
router.get('/user/list',(req, res)=>{
    res.send('Get user list')
})
router.get('/user/add',(req, res)=>{
    res.send('Add new user')
})
//向外导出路由对象
module.exports = router
```

##### 注册路由模块

```js
const express = require('express')
const app = express()
//导入路由模块
const router = require('./04.router')
//注册路由模块
app.use(router)
app.listen(80,()=>{
    console.log('http://127.0.0.1')
})
```

##### 为路由模块添加前缀

类似于托管静态资源时，为静态资源统一挂载访问前缀一样，路由模块添加前缀的方式也非常简单：

```js
app.use('/api',router)  //在use的时候添加一个参数
```

app.use()函数的作用就是注册全局中间件

## 中间件

### 中间件概念

#### 什么是中间件

中间件（Middleware ），特指业务流程的中间处理环节。

#### **Express** 中间件的调用流程

当一个请求到达 Express 的服务器之后，可以连续调用多个中间件，从而对这次请求进行预处理。

![中间件](node\中间件.png)

#### 中间件格式

Express 的中间件，本质上就是一个 **function** **处理函数**，Express 中间件的格式如下：

![中间件2](node\中间件2.png)

注意：中间件函数的形参列表中，必须包含 next 参数。而路由处理函数中只包含 req 和 res。

#### next 函数的作用

**next** **函数**是实现多个中间件连续调用的关键，它表示把流转关系转交给下一个中间件或路由。

![中间件3](node\中间件3.png)

### 中间件使用

#### 定义中间件函数

```js
//定义一个最简单的中间件函数
const mw = function (req,res,next) {
    console.log('这是最简单的中间件函数')
    next()
}
```

#### 全局生效的中间件

客户端发起的任何请求，到达服务器之后，都会触发的中间件，叫做全局生效的中间件。

通过调用 app.use(中间件函数)，即可定义一个全局生效的中间件，示例代码如下：

```js
//定义一个最简单的中间件函数
const mw = function (req,res,next) {
    console.log('这是最简单的中间件函数')
    next()
}
//将mw注册为全局生效的中间件
app.use(mw)
```

#### **定义全局中间件的简化形式**

```js
app.use(function (req,res,next) {
    console.log('这是最简单的中间件函数')
    next()
})
```

#### 中间件的作用

多个中间件之间，**共享同一份** **req** **和** **res**。基于这样的特性，我们可以在上游的中间件中，**统一**为 req 或 res 对象添加自定义的属性或方法，供下游的中间件或路由进行使用。

#### 定义多个全局中间件

可以使用 app.use() 连续定义多个全局中间件。客户端请求到达服务器之后，会按照中间件定义的先后顺序依次进行调用

#### 局部生效的中间件

**不使用** app.use() 定义的中间件，叫做局部生效的中间件，示例代码如下：

```js

//定义中间件函数mw1
const mw1 = function (req, res, next) {
    console.log('这是中间件函数')
    next()
}
//mw1这个中间件只在“当前路由中生效”，这种用法属于“局部生效的中间件”
app.get('/',mw1,(req, res)=>{
    res.send('hello,world')
})
app.get('/user',(req, res)=>{
    res.send('user page')
})
```

#### 定义多个局部中间件

可以在路由中，通过如下两种等价的方式，使用多个局部中间件：

```js
app.get('/',mw1,mv2,(req, res)=>{
    res.send('hello,world')
})

app.get('/',[mw1,mw2],(req, res)=>{
    res.send('hello,world')
})
```

#### **了解中间件的5个使用注意事项**

①一定要在路由之前注册中间件

②客户端发送过来的请求，可以连续调用多个中间件进行处理

③执行完中间件的业务代码之后，不要忘记调用 next() 函数

④为了防止代码逻辑混乱，调用 next() 函数后不要再写额外的代码

⑤连续调用多个中间件时，多个中间件之间，共享 req 和 res 对象

### 中间件的分类

为了方便大家理解和记忆中间件的使用，Express 官方把常见的中间件用法，分成了 5 大类，分别是：

① 应用级别的中间件

② 路由级别的中间件

③ 错误级别的中间件

④ Express 内置的中间件

⑤ 第三方的中间件

#### 应用级别的中间件

通过 app.use() 或 app.get() 或 app.post() ，绑定到 app 实例上的中间件，叫做应用级别的中间件，代码示例如下：

```js
//应用级别的中间件（全局中间件）
app.use(function (req,res,next) {
    console.log('这是最简单的中间件函数')
    next()
})
//应用级别的中间件（局部中间件）--指mw1
app.get('/',mw1,(req, res)=>{
    res.send('hello,world')
})
```

#### **路由级别的中间件**

绑定到 express.Router() 实例上的中间件，叫做路由级别的中间件。它的用法和应用级别中间件没有任何区别。只不过，应用级别中间件是绑定到 app 实例上，路由级别中间件绑定到 router 实例上，代码示例如下：

```js
router.use(function (req,res,next) {
    console.log('这是最简单的中间件函数')
    next()
})
```

#### 错误级别的中间件

错误级别中间件的 function 处理函数中，必须有 4 个形参，形参顺序从前到后，分别是 (err, req, res, next)。

```js
app.get('/',(req, res)=>{
    throw new Error('服务器内部发生错误')
    res.send('home page')
})

app.use((err, req, res, next)=>{
    console.log('发生错误'+err.message)
    res.send('Error:'+err.message)
})
```

**注意：**错误级别的中间件，必须注册在所有路由之后！

#### **Express内置的中间件**

自 Express 4.16.0 版本开始，Express 内置了 3 个常用的中间件，极大的提高了 Express 项目的开发效率和体验：

① express.static 快速托管静态资源的内置中间件，例如： HTML 文件、图片、CSS 样式等（无兼容性）

② express.json 解析 JSON 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）

③ express.urlencoded 解析 URL-encoded 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）

```js
//配置解析 application/json 格式数据的内置中间件
app.use(express.json())
//配置解析 application/x-www-form-urlencoded 格式数据的内置中间件
app.use(express.urlencoded({extended:false}))
```

#### 第三方中间件

非 Express 官方内置的，而是由第三方开发出来的中间件，叫做第三方中间件。在项目中，大家可以按需下载并配置第三方中间件，从而提高项目的开发效率。

```
//默认情况下，如果不配置解析表单数据的中间件，则req.body默认等于undefined
```

例如：在 express@4.16.0 之前的版本中，经常使用 body-parser 这个第三方中间件，来解析请求体数据。使用步骤如下：

①运行 npm install body-parser 安装中间件

②使用 require 导入中间件

③调用 app.use() 注册并使用中间件

这个组件现在已经被内置express.urlencoded，用法一样

**注意：**Express 内置的 express.urlencoded 中间件，就是基于 body-parser 这个第三方中间件进一步封装出来的。

### 自定义中间件

#### **需求描述与实现步骤**

自己手动模拟一个类似于 express.urlencoded 这样的中间件，来解析 POST 提交到服务器的表单数据。

实现步骤：

①定义中间件

②监听 req 的 data 事件

③监听 req 的 end 事件

④使用 querystring 模块解析请求体数据

⑤将解析出来的数据对象挂载为 req.body

将自定义中间件封装为模块

#### 代码

```js
const qs = require('querystring')

const BodyParser = (req, res, next)=>{
    //定义中间件的业务逻辑
    let str = ''
    req.on('data',(chunk)=>{
        str+=chunk
    })
    req.on('end',()=>{
        /*console.log(str)*/
        const body=qs.parse(str)
        req.body = body
        next()
    })
}

module.exports = BodyParser
```

## Express写接口

### 创建基本的服务器

```js
//导入express模块
const express = require('express')
//创建express服务器实例
const app = express()
//调用app.listen方法，指定端口号并启动web服务器
app.listen(80,()=>{
    console.log('http://127.0.0.1')
})
```

### 创建路由模块

```js
//这是路由模块
//导入express
const express = require('express')
//创建路由对象
const router = express.Router()

//挂载具体的路由
router.get('/user/list',(req, res)=>{
    res.send('Get user list')
})
router.post('/user/add',(req, res)=>{
    res.send('Add new user')
})

//向外导出路由对象
module.exports = router

//----------------------------------

//app.js[导入并注册路由模块]

const apiRouter = require('./apiRouter.js')
app.use('/api',apiRouter)
```

### 编写get接口

```js
apiRouter.get('/get',(req, res)=>{
    //获取到客户端通过查询字符串，发送到服务器的数据
    const query = req.query
    res.send({
        status:0,    //状态，0表示成功，1表示失败
        msg:'Get请求成功',   //状态描述
        data:query            //需要响应给客户端的数据
    })
})
```

### 编写post接口

```js
apiRouter.post('/post',(req, res)=>{
    //获取到客户端通过请求体，发送到服务器的URL-encoded 数据
    const body = req.body
    res.send({
        status:0,
        msg:'POST请求成功',
        data:body
    })
})
```

**注意：如果要获取 URL-encoded 格式的请求体数据，必须配置中间件 app.use(express.urlencoded({ extended: false }))**

### **CORS** **跨域资源共享**

#### 接口的跨域问题

刚才编写的 GET 和 POST接口，存在一个很严重的问题：不支持跨域请求。

解决接口跨域问题的方案主要有两种：

① CORS（主流的解决方案，推荐使用）

② JSONP（有缺陷的解决方案：只支持 GET 请求）

**使用** **cors** **中间件解决跨域问题**

cors 是 Express 的一个第三方中间件。通过安装和配置 cors 中间件，可以很方便地解决跨域问题。

使用步骤分为如下 3 步：

①运行 npm install cors 安装中间件

②使用 const cors = require('cors') 导入中间件

③在路由之前调用 app.use(cors()) 配置中间件

#### **什么是** **CORS**

CORS （Cross-Origin Resource Sharing，跨域资源共享）由一系列 HTTP 响应头组成，**这些** **HTTP** **响应头决定浏览器是否阻止前端** **JS** **代码跨域获取资源**。

浏览器的同源安全策略默认会阻止网页“跨域”获取资源。但如果接口服务器配置了 CORS 相关的 HTTP 响应头，就可以解除浏览器端的跨域访问限制。

![跨域](node\跨域.png)

#### **CORS** **的注意事项**

①CORS 主要在服务器端进行配置。客户端浏览器**无须做任何额外的配置**，即可请求开启了 CORS 的接口。

②CORS 在浏览器中有兼容性。只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口（例如：IE10+、Chrome4+、FireFox3.5+）。

#### **CORS** **响应头部** Access-Control-Allow-Origin

响应头部中可以携带一个 **Access-Control-Allow-Origin** 字段，其语法如下:

```js
Access-Control-Allow-Origin: <origin> |*
```

其中，origin 参数的值指定了允许访问该资源的外域 URL。

例如，下面的字段值将**只允许**来自 http://itcast.cn 的请求：

```js
res.setHeader('Access-Control-Allow-Origin','http://itcast.cn')
```

如果指定了 Access-Control-Allow-Origin 字段的值为通配符 *****，表示允许来自任何域的请求，示例代码如下：

```js
res.setHeader('Access-Control-Allow-Origin','*')
```

#### **CORS** **响应头部** Access-Control-Allow-Headers

默认情况下，CORS **仅**支持客户端向服务器发送如下的 9 个请求头：

Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type （值仅限于 text/plain、multipart/form-data、application/x-www-form-urlencoded 三者之一）

如果客户端向服务器发送了额外的请求头信息，则需要在**服务器端**，通过 Access-Control-Allow-Headers 对额外的请求头进行声明，否则这次请求会失败！

```js
//允许客户端额外向服务器发送Content-Type请求头和X-Coustom-Header请求头
//注意：多个请求头之间使用英文的逗号进行分割
res.setHeader('Access-Control-Allow-Headers','Content-Type,X-Coustom-Header')
```

#### **CORS** **响应头部**  Access-Control-Allow-Methods

默认情况下，CORS 仅支持客户端发起 GET、POST、HEAD 请求。

如果客户端希望通过 PUT、DELETE 等方式请求服务器的资源，则需要在服务器端，通过 Access-Control-Alow-Methods来指明实际请求所允许使用的 HTTP 方法。

示例代码如下：

```JS
//只允许POST GET  DELETE  HEAD
res.setHeader('Access-Control-Allow-Methods'.'POST,GET,DELETE,HEAD')
//允许所有的Http请求
res.setHeader('Access-Control-Allow-Methods'.'*')
```

#### **CORS请求的分类**

客户端在请求 CORS 接口时，根据请求方式和请求头的不同，可以将 CORS 的请求分为两大类，分别是：

①简单请求

②预检请求

#### **简单请求**

同时满足以下两大条件的请求，就属于简单请求：

① 请求方式：GET、POST、HEAD 三者之一

② HTTP 头部信息不超过以下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type（只有三个值application/x-www-form-urlencoded、multipart/form-data、text/plain）

#### **预检请求**

只要符合以下任何一个条件的请求，都需要进行预检请求：

① 请求方式为 GET、POST、HEAD 之外的请求 Method 类型

② 请求头中包含自定义头部字段

③ 向服务器发送了 application/json 格式的数据

在浏览器与服务器正式通信之前，浏览器会先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求，所以这一次的 OPTION 请求称为“预检请求”。服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据。

#### **简单请求和预检请求的区别**

**简单请求的特点**：客户端与服务器之间只会发生一次请求。

**预检请求的特点**：客户端与服务器之间会发生两次请求，OPTION 预检请求成功之后，才会发起真正的请求。

### JSONP接口

#### 概念

概念：浏览器端通过 <script> 标签的 src 属性，请求服务器上的数据，同时，服务器返回一个函数的调用。这种请求数据的方式叫做 JSONP。

特点：

①JSONP 不属于真正的 Ajax 请求，因为它没有使用 XMLHttpRequest 这个对象。

②JSONP 仅支持 GET 请求，不支持 POST、PUT、DELETE 等请求。

#### **创建** **JSONP** 接口的注意事项

如果项目中已经配置了 CORS 跨域资源共享，为了**防止冲突**，必须在配置 CORS 中间件之前声明 JSONP 的接口。否则 JSONP 接口会被处理成开启了 CORS 的接口。示例代码如下：

```js
//优先创建JSONP接口【这个接口不会被处理成CORS接口】
app.get('/api/jsonp',(req,res)=>{})

//再配置CORS中间件[后续的所有接口，都会被处理成CORS接口]
app.use(cors())

//这是开启了CORS的接口
app.get('/api/get',(req,res)=>{})
```

#### **实现** **JSONP** **接口的步骤**

①获取客户端发送过来的回调函数的名字

②得到要通过 JSONP 形式发送给客户端的数据

③根据前两步得到的数据，拼接出一个函数调用的字符串

④把上一步拼接得到的字符串，响应给客户端的 <script> 标签进行解析执行

```js
app.get('/api/jsonp',(req,res)=>{
//获取客户端发送过来的回调函数的名字
const funcName = req.query.callback
//得到要通过 JSONP 形式发送给客户端的数据
const data  = {name:'zs',age:22}
//根据前两步得到的数据，拼接出一个函数调用的字符串
const scriptStr = `{funcName}(${JSON.stringify(data)})`
//把上一步拼接得到的字符串，响应给客户端的 <script> 标签进行解析执行
res.send(scriptStr)
})
```

