

#  let和const

var的限制很少，很少报错，这在开发中

```js
//es5语法中定义变量使用var
var a;
console.log(a); //这里会输出 undefine，因为a还没被定义,但并不会报错
a = 2
```

var 存在变量提升,可以重复定义

```js
console.log(a)
var i = 0  //表面是这样

var i     //实际上，最上边会提升出来一个var的声明
console.log(a) 
var i = 0
```



## let

1.let声明变量，没有变量提升（不能在定义前使用）

```js
console.log(a)  //这里就会直接报错
let a = 10
```

2.是一个块级作用域

```js
if (1===1){
   let b = 10
}
console.log(b) //报错 b is not defined，b只能在id的大括号那个块级作用域中使用
```

3.不能重复声明

```js
let a= 1
let a= 3
console.log(a) //报错

let a= 1
a= 3
console.log(a) //可以修改但不可以重复声明
```

## const

const 声明常量时 **一旦被声明 无法修改**

```js
const a= 1
a= 3
console.log(a)  //这里就会报错
```

在声明变量时，const拥有let声明变量的3个性质

- 声明变量，没有变量提升（不能在定义前使用）
- let声明变量，没有变量提升
- 不能重复声明

但也有独特的性质：

const 可以声明对象类型的常量

这时它只能修改对象中的数据，而不能直接修改对象指向

```js
const person = {
	name:'小马哥'
}
person.name = 'alex' //这样可以

person = {
	age:20  
}                    //这样报错
```

### 经典例子

#### 作用1：for循环

```js
const arr = []
 for (let i = 0;i<10;i++){
    arr[i] = function () {
        return i
    }
 }
console.log(arr[5]())  //输出5
```

```js
var arr = []
 for (var i = 0;i<10;i++){
    arr[i] = function () {
        return i
    }
 }
console.log(arr[5]())  //输出10
//原因：var被变量提升，所以其实相当于一个全局变量，在最后循环完成后为10
//调用函数，return i ，就是10
```

#### 作用2： 不会污染全局变量

```js
let RegExp = 10
console.log(RegExp)
console.log(window.RegExp)
```

这两个打印不一样，说明let不会改变window上的全局变量

## 建议

**在默认情况下用const，而只有在你知道变量值需要被修改的情况下使用let**

# 模板字符串

原来我们要在字符串中添加变量需要进行字符串拼接

```js
let id =1,
    name = '小马哥'
oBox.innerHTML = " <ul><li><p id = "+id+ ">"+name+"</p></li></ul>"  //过于麻烦
```

```js
//es6写法
let htmlStr =  `<ul>
          			<li>
              			<p id = ${id}>${name}</p>
          			</li>
      			</ul>`
```

使用反引号 ``,插入变量时使用${变量名}

# 函数

## 函数默认值

### 1.带参数默认值的函数

```js
//es5设置默认值方法（比较麻烦）
      function add(a, b) {
          a = a||10
          b = b||20
          return a+b
      }
      console.log(add())
```

```js
//es6设置默认值
      function add(a=10, b=20) {
          return a+b
      }
      console.log(add())

```

### 2.默认的表达式也可以是一个函数

```js
      function add(a, b = getVal(5)) {
          return a+b
      }
      function getVal(val) {
          return val+5
      }
	  console.log(add(10))//输出20
```

## 剩余参数

在es5中获取函数的参数需要使用函数局部变量arguments进行遍历来获取

在es6中提供了剩余参数：由三个点...和一个紧跟着的具名参数指定  ...keys

**注意：具名参数必须放在参数最后**

和arguments的区别就是arguments是获取全部参数，我们到我们需要的参数需要进行遍历一下

而使用剩余参数，我们可以把不确定的参数放后面，来直接获得

## 扩展运算符...

剩余运算符：把多个独立的合并到一个数组中

扩展运算符：将一个数组分割，并将各个项作为分离的参数传给函数

## 箭头函数

使用=>来定义 function(){} 等于 ()=>{}

几种写法：

```js
//正常函数写法
let add = function(a,b){
	return a+b
}
//几种箭头函数写法
let add = (a,b)=>{
	return a+b
}
// 只有一个参数时，更简洁
let add = val =>{
    return a+b
}
//直接返回时
let add = val => (val+5)  //括号可加可不加
//直接返回一个参数
let add = val => val
//正常
let add = (a,b)=>(a+b)
```

**返回值如果是对象，必须用()给括起来，建议日常就使用括号，更直观**

## 箭头函数this指向

箭头函数是没有this绑定的

es5中的this指向:取决于调用该函数的上下文对象

```js
let PageHandle = {
          id:123,
          init : function () {
              document.addEventListener('click',function (event) {
                  this.doSomeThings(event.type)  //这里this已经变成了document，所以会报错
              })
          },
          doSomeThings: function (type) {
              console.log(`事件类型:${type},当前id:${this.id}`)
          }
      }
      PageHandle.init()

//但是在es5中这个问题是可以解决的
          init : function () {
              document.addEventListener('click',function (event) {
                  this.doSomeThings(event.type)
              }.bind(this),false)
          },
//使用bind将this传入就可以
//但很显然，这并不利于理解
```

```js
//使用箭头函数
          init : function () {
              document.addEventListener('click',(event)=>{
                  this.doSomeThings(event.type)
              },false)
          },
```

箭头函数没有this指向，箭头函数内部this值只能通过查找作用域链，向上找

一旦使用箭头函数，当前就不存在作用域

注意事项:

- 一旦使用箭头函数，内部就不会存在arguments对象
- 箭头函数不能使用new关键字来实例化对象(function函数是一个对象，但箭头函数不是，他其实就是一个表达式)

# 解构赋值

解构赋值是对赋值运算符的一种扩展

它针对数组和对象来操作

优点：代码书写上简洁易读

## 对对象解构

原先我们想要对象中的数据进行外部赋值的话需要依次书写

```js
      let node = {
          type:'iden',
          name:'foo'
      }
      let type = node.type
      let name = node.name
```

我们可以直接用下面的写法，完全结构（但名字要和内部一样）

```js
let {type,name} = node
```

也可以不全部结构(只解构let)

```js
let {name} = node
```

也可以使用剩余运算符

```js
let {name,...res} = node
```

## 对数组解构

```js
let arr = [1,2,3]
let [a,b]=arr
//可嵌套
let [a,[b],c] = [1,[2],3]

```

# 扩展的对象功能

## 定义时的简写

```js
        const name = '小马哥',age = 20
        const person = {
            name:name,
            age:age,
            ageName:function () {

            }
        }
        //上方定义可以进行简写
         const person = {
            name,  //等价于name:name，当名字一样时可以简写
            age,
            ageName() {
                
            }
        }
```

## 对象的方法

### is

比较两个值是否**严格**相等（但我们不经常用，一般使用 === ）

```js
NaN === NaN     //这个为false
Object.is(NaN,NaN)  //这个为true
```

es5中有 == 和 === 两种比较运算符，但都有各自的问题

== 使用时会自动转换数据类型    10 和 ‘10’ 会判定相等

=== 虽然不会转换数据类型但 自带的变量 NaN === NaN 为false

所以出现了is，它和 === 基本相同，对基本数据类型比较值，对对象比较地址，也就是所谓的严格比较

但解决了 === 的问题

### assign

对象的合并

Object.assign(target,obj1,obj2....)

```js
        let oldObj = {c:1}
        let newObj = Object.assign(oldObj,{a:1},{b:2})
        console.log(newObj)
        console.log(oldObj) //后两个输出一样，含有三个属性的对象
```

会将后面的合并到第一个

# Symbol

原始数据类型Symbol,它表示独一无二的值

```js
        const name = Symbol('name')
        const name2 = Symbol('name')
        console.log(name === name2)  //结果为false
```

就算值相等但放在不同的内存地址，所以独一无二

```js
        let s1 = Symbol('s1')
        console.log(s1)
        let obj = {}
        obj[s1]='小马哥'
        console.log(obj)
```

也可以写成

```js
        let s1 = Symbol('s1')
        console.log(s1)
        let obj = {
            [s1]:'小马哥'    //一定要用中括号，这才表示其是变量取值
        }
        console.log(obj)
```

如果用Symbol作为对象的key（属性名），这时候我们是不能遍历对象的，也很难对对象进行操作

如果我们要获取，有以下两种方法

```js
        let s = Object.getOwnPropertySymbols(obj)
        console.log(s[0])

        let m = Reflect.ownKeys(obj)
        console.log(m[0])
```

# Map和Set

## Set

集合：表示无重复值的有序列表

```js
        //集合：表示无重复值的有序列表
        let set = new Set()
        console.log(set)
		//添加
        set.add(2)
        set.add('4')
        set.add('4')   //这个是重复元素，所以会被忽略
		//删除
		set.delete(2)
        console.log(set)
		//校验某个值是否存在
		console.log(set.has('4'))
```

在es6中，set中key和val是默认认为相同的，所以使用forEach遍历是没有意义的



将set转化为数组

```js
        let set2 = new Set([1,2,3,3,3,4])  //这里重复的3会被忽略
        let arr = [...set2]   //使用扩展运算符将其转化为数组
        console.log(arr)
```



set中对象的引用无法被释放

```js
        let set3 = new Set(),obj = {}
        set3.add(obj)
        //释放obj中的资源
        obj=null  //obj已经释放，但这时set中的对象并没有被释放
```

解决这种方法我们使用WeakSet，但这个类型比set少了很多方法，也有很多限制

- 不能传入非对象类型的参数
- 不可迭代
- 没有forEach()
- 没有size属性

## Map

Map类型时键值对的有序列表，键和值是任意类型

```js
        let map = new Map()
        map.set('name','张三')
        map.set('age',20)
        console.log(map.get('name'))  //取值
        console.log(map.has('name'))  //校验
        map.clear()                   //清除
        map.set(['a',[1,2,3]],'hello')  
```

也可以创建时直接定义

```js
new Map([['a',1],['c',2]])
```

剩余性质和Set一样，同样对象类型不能释放，也存在WeakMap,性质也和Set相同

# 数组的扩展功能

### 伪数组

伪数组的特点
伪数组拥有数组的属性，

- 具有 length 属性 但length属性不是动态的，不会随着成员的变化而变化
- 按索引方式储存数据
- 不具有数组的push()， forEach()等方法

伪数组本质是一个 Object，而真实的数组是一个 Array。
伪数组的原型 Object.__prototype__ 通过改变原型指向可以将伪数组转为真数组



 数组的常见伪数组

1. 函数内部的 arguments，扩展操作符可以将 arguments 展开成独立的参数
2. DOM 对象列表 如 通过 document.getElementByTags 获取的 dom元素
3. jQuery对象 如 $(‘div’)

### from()

将伪数组转化成真正的数组

```js
    function add() {
        console.log(arguments)
        //es5转换
        let arr = [].slice.call(arguments)
        console.log(arr)
        //es6写法
        let arr2 = Array.from(arguments)
        console.log(arr2)
    }
```

但转换方法还可以通过扩展运算符

```js
[...arguments] 
```

from()还可以接收第二个参数，用来对每个元素进行处理

```js
Array.from(lis,ele=>ele.textContext)  //lis为第二种伪数组，可以用第二个参数对每个元素进行一次处理
```

### of()

将一组值，转换成数组

```js
console.log(Array.of(3,11,20,'30'))
```



### copywithin()

将数组内部指定位置的元素复制到其他的位置，返回当前数组

```js
console.log([1,2,3,8,9,10].copyWithin(0,3))
//从3位置后的所有数值，替换从0开始的数值，是执行覆盖
```

### find()  findIndex()

```js
console.log([1,2,-10,-20,9,2].find(n => n<0))
```

find是找出第一个符合函数条件的数组值进行返回

而findIndex是全部返回

### entries()  keys()  values()  

这三个函数都返回一个遍历器，可以使用for... of 循环进行遍历

```js
    for (let index of ['a','b'].keys()){
        console.log(index)
    }  //取键

    for (let index of ['a','b'].values()){
        console.log(index)
    }  //取值

    for (let [index,ele] of ['a','b'].entries()){
        console.log(index,ele)
    }  //对键值对的遍历

```

### includes()

返回一个布尔值，表示某个数组是否包含给定的值

```js
console.log([1,2,3].includes(2))
```

之前是使用indexOf()返回的是1和-1，这里是布尔值,更方便

# 迭代器

Iterator

是一种新的遍历机制，两个核心

1.迭代器是一个接口，能快捷的访问数据，通过Symbol.iterator来创建迭代器，通过迭代器的next()来获取迭代

2.迭代器是用于遍历数据结构的指针(数据的游标)

```js
        const items = ['one','two','three']
        //创建新的迭代器
        const ite = items[Symbol.iterator]()
        console.log(ite.next())
        //返回一个对象
        // {
        //     value :'one',
        //     done:false
        // }
        //done如果为false代表没有遍历完
```

# 生成器

generator函数，可以通过yield关键字，将函数挂起，为了改变执行流程提供了可能，同时为了异步编程提供了方案

他与普通函数的区别
1.function后面 函数名之前有个 *
2.只能在函数内部使用yield表达式。让函数挂起

```js
function* func() {
    yield 2;
    yield 3;
}
let fn  = func();
console.log(fn.next());
console.log(fn.next());
```

函数初识是不会执行的

只有调用一次next(),才会向下执行到一个yield的地方

同时对next的赋值并不会在本次执行的yield处作为值，而是上一次yield的值

```js
function* add(){
    console.log('start')
    //x可真不是yield '2'的返回值，他是next调用的下次的值
    let x = yield '2'   
    console.log('one:'+x)
    let y = yield '3'
    console.log('two'+y)
    return x+y
}

console.log(0)
const fnx = add()
console.log(1)
console.log(fnx.next())
console.log(fnx.next(20))
console.log(fnx.next(30))  //这个返回值为50，done为true，可以认为return就是最后一个yield
```

使用场景：为不具备Interator接口的对象提供了遍历操作

```js
      function * objectEntries(obj){
          //获取对象的所有key保存到数组[name,age]
          const propKeys = Object.keys(obj)
          for (const propKey of propKeys) {
              yield [propKey,obj[propKey]]
          }
      }
      
      const obj = {
          name:'小马哥',
          age:18
      }
      obj[Symbol.iterator] = objectEntries
      console.log(obj)
      for (let [key,value] of objectEntries(obj)) {
          console.log(`${key},${value}`)
      }
```

# Promise对象

## 基本性质

Promise承诺

相当于一个容器，保存着未来才会结束的事件(异步操作)的一个结果

各种异步操作都可以用同样的方法进行处理 axios

一旦new进行创建，就会开始执行



特点：

1.对象的状态不受外接影响  处理异步操作 三个状态 Pending(进行中)   Resolved(成功)  Rejected(失败)

2.一旦状态改变，就不会再变，任何时候都可以得到这个结果

```js
let pro = new Promise(function (resolve, reject) {
    //执行异步操作
    let res = {
        code:201,
        data:{
            name:'小马哥'
        }
    }
    setTimeout(()=>{
        if (res.code === 200){
            resolve(res.data)
        }else {
            reject(res.code)
        }
    },1000)
})
console.log(pro)   //输出promise  Pending

pro.then((value)=>{
    console.log(value)
},(err)=>{
    console.log(err)
})
```

传参设置函数

```js
function timeOut(ms) {
    return new Promise((resolve, reject)=>{
        setTimeout(()=>{
            resolve('hello promise success')
        },ms)
    })
}
timeOut(2000).then(value => {
    console.log(value)
})
```

## 相关方法

then()方法

then() 第一个参数时relove回调函数，第二个参数时可选的,是reject状态回调

then()方法返回一个新的Promise实例，可以采用链式编程(当然，这个可读性不友好，一般也不用)

then的错误回调 --

- then()中写两个回调，一个成功，一个失败
- 继续.then,下一个用.then(null,err=>{错误处理})
- 使用.catch(err=>{错误处理})

```js
//定义getJSON是一个Promise对象
        let getJson = new Promise()
        getJson.then((data)=>{
            console.log(data)
        }).then(null,err=>{
            console.log(err)
        })

        //下方更好理解
        getJson.then((data)=>{
            console.log(data)
        }).catch(err=>{
            console.log(err)
        })
```



resolves()方法 能将现有的任何对象转换成promise对象

```js
        let p  = Promise.resolve('foo')   //就像相当于，执行一定是resolve状态，返回为foo
        console.log(p)
        p.then((data)=>{
            console.log(data)
        })
```

reject()方法，和resolve一样，但执行为reject状态而已



all()

```js
let promise1 = new Promise((resolve, reject)=>{})
let promise2 = new Promise((resolve, reject)=>{})
let promise3 = new Promise((resolve, reject)=>{})
let p4 = Promise.all([promise1,promise2,promise3])
p4.then(()=>{
    //三个都成功，才成功
}).catch(()=>{
    //如果有一个失败，则失败
})
```

应用:一些游戏类的素材比较多，等待图片、flash、静态资源文件都加载完场，才进行页面的初始化



race()  某个异步请求设置超时事件，并且在超时后执行相应的操作

```js
        function requestImg(imgSrc) {
            return new Promise((resolve, reject)=>{
                const img = new Image()
                img.onload = function () {
                    resolve(img)
                }
                img.src = imgSrc
            })
        }

        function timeout() {
            return  new Promise((resolve, reject)=>{
                setTimeout(()=>{
                    reject('图片请求超时')
                },3000)
            })
        }
        Promise.race([requestImg('https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F1113%2F011620120T1%2F200116120T1-6-1200.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1660546440&t=fea9af5e52ef25120613f0a24f97cb4a'),timeout()]).then(res =>{
            console.log(res)
            document.body.appendChild(res)
        }).catch(err=>{
            console.log(err)
        })
```

使用一个定时器来配合race函数进行超时设置



done()和finally()

就是不论失败还是成功都执行的一个方法

# async

作用：使得异步操作更加方便

基本操作  async它会返回一个Promise对象

他的return就是 返回一个Promise对象resolve的值。因此对async函数可以直接then，返回值就是then方法传入的函数。

async是Generator的一个语法糖

如果async函数中有多个await 那么then函数会等待所有的await指令运行完的结果才去执行

```js
async function f(){
    // return await 'hello async'
    let s = await 'hello world'
    let data = await s.split()
    return data
}
f().then(v=>{
    console.log(v)
}).catch(e=>{
    console.log(e)
})
```

```js
// async基础语法
async function fun0(){
    console.log(1);
    return 1;
}
fun0().then(val=>{
    console.log(val) // 1,1
})

async function fun1(){
    console.log('Promise');
    return new Promise(function(resolve,reject){
        resolve('Promise')
    })
}
fun1().then(val => {
    console.log(val); // Promise Promise
})

```



await
await 也是一个修饰符，只能放在async定义的函数内。可以理解为等待。

await 修饰的如果是Promise对象：可以获取Promise中返回的内容（resolve或reject的参数），且取到值后语句才会往下执行；

如果不是Promise对象：把这个非promise的东西当做await表达式的结果。
```js
async function fun(){
    let a = await 1;
    let b = await new Promise((resolve,reject)=>{
        setTimeout(function(){
            resolve('setTimeout')
        },3000)
    })
    let c = await function(){
        return 'function'
    }()
    console.log(a,b,c)
}
fun(); // 3秒后输出： 1 "setTimeout" "function"

```

**sync/await 的正确用法**

```js
// 使用async/await获取成功的结果

// 定义一个异步函数，3秒后才能获取到值(类似操作数据库)
function getSomeThing(){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            resolve('获取成功')
        },3000)
    })
}

async function test(){
    let a = await getSomeThing();
    console.log(a)
}
test(); // 3秒后输出：获取成功

```

个人理解，如果不使用await，就不会等待，因为那是Promise对象(异步)

所以会直接输出 一个Promise对象

# 类

## 类的创建

```js
//es5造类
function Person(name, age) {
    this.name = name  //构造方法，实例化的时候会立即被调用
    this.age = age
}  //通过函数造类
Person.prototype.sayName = function () {
    return this.name
}  //通过原型进行添加方法
let p1 = new Person('小马哥',28)
console.log(p1)
```

第一种添加方法

```js
//es6创建类 ，使用class
class Person{
    constructor(name,age) {
        this.name = name
        this.age = age
    }
    sayName(){
        return this.name
    }
}
let p1 = new Person('小马哥',28)

console.log(p1)
```

另一种方法--通过assign

```js
class Person{
    constructor(name,age) {
        this.name = name
        this.age = age
    }
}
Object.assign(Person.prototype,{
    sayName(){
        return this.name
    }
})
let p1 = new Person('小马哥',28)

console.log(p1)
```

## 类的继承

```js
class Dog extends Person{
    constructor(name,age,color) {
        super(name,age)
        this.color  = color
    }
}
```

继承构造函数中必须通过super来调用父类的构造函数

也可以从写父类的方法

# 模块化

es6模块功能主要有两个命令构成：export和import

export 用于规定模块的对外结构 import用于输入其他模块提供的功能

一个模块就是一个独立的文件

js中进行抛出

```js
export const name = '张山'
export function sayName() {
    console.log(name)
}
```

**注意：如果抛出一个函数 不能直接 export sayName**

**要么直接在函数前抛出**

**要么就 export {sayName} 这样以对象抛出**



- 导出默认模块

```javascript
let a = '呵呵';

export default a;

//导入默认模块
import 自定义名称 from 'url';
```

- 以别名的方式导出

```js
//以别名的方式导出模块
let a = '王五';
let b = 20;
let c = '男';


export {a as name,b as age,c as sex};

import { name,age,sex } from "./public.js";
alert(name + age + sex);
```



- 先定义模块，后导出模块

```js
//先定义模块
let name = "张三";
let age = 18;
let showName = () => {
    return name;
}
//导出模块
export {name,age,showName};
//导入模块
import {模块名,模块名} from '模块url';

```

## es6模块化和node.js模块化

### Node模块

Node使用CommonJS规范 ，加载方式为同步加载；它有四个重要的环境变量：module、exports、require、global。实际使用时，module变量代表当前模块，exports是module的属性，表示对外输出的接口，加载某个模块，实际上是加载该模块的module.exports属性。用require加载模块（同步）。
Node为每隔模块提供了一个exports变量，指向module.exports，这等同于每个模块头部有这样的一行代码：
var exports = module.exports
exports只是module.exports的一个引用，指向module.exports对象所在的地址

### ES6模块

在ES6模块化中，使用 import 引入模块，通过 export导出模块，但需要babel编译为浏览器可以识别的代码。
1.export与export default均可用于导出常量，函数，文件，模块等；
2.在一个文件或模块中，export，import可以有多个，export default只有一个；
3.通过export方式导出，在导入时需要加{}，export default不需要；

```js
import { Input } from 'element-ui'   //export
import Vue from 'vue'		//export default
```

4.export能导出变量表达式，export default不可以。

### 区别
CommonJS模块输出是一个值的拷贝，ES6模块输出是值的引用。
CommonJS模块是运行时加载，ES6模块是编译时输出接口。
CommonJS模块无论require多少次，都只会在第一次加载时运行一次，然后保存到缓存中，下次在require，只会去从缓存取。

require : node 和 es6 都支持的引入方式。
module.exports与exports 是node支持的导出方式。
import ，export与export default是ES6支持的导入和导出方式。

Polyfill : 解决浏览器对API的兼容问题的。
Babel : Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码。

# 闭包

闭包（**closure**）是Javascript语言的一个难点，也是它的特色。

闭包的作用:通过一系方法,将函数内部的变量**(局部变量)转化为全局变量。**

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域无非就是两种：**全局变量**和**局部变量**。
## 变量的作用域
Javascript语言的特殊之处，就在于函数内部可以直接读取全局变量。
```js
var n=999;

function f1(){
alert(n);
}

f1();    // 999
```

另一方面，在函数外部无法读取函数内的局部变量。
```js
function f1(){
var n=999;
}

alert(n);   // error

```

这里有一个地方需要注意， 函数内部声明变量的时候，一定要使用var命令(ler 或者 const都行，重要是要定义)。如果不用的话，你实际上声明了一个全局变量！ 
```js
function f1(){
n=999;

}

f1();

alert(n);   // 999

```
## 如何从外部读取局部变量
出于种种原因，我们有时候需要得到函数内的局部变量。但是，正常情况下，这是办不到的，只有通过变通方法才能实现。**那就是在函数的内部，再定义一个函数。**
```js
function f1(){

var n=999;   

function f2(){  
alert(n);    // 999  
}

}
```
在上面的代码中，函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是Javascript语言特有的"链式作用域"结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！
```js
function f1(){

var n=999;

function f2(){  
alert(n);   
}

return  f2;

}

var result=f1();

result(); // 999
```
## 延长函数环境的生命周期
当我们只调用函数时：
```js
function hd(){
    let n = 1;
    function sum(){
      console.log(++n);
    }
    sum();
  }
  hd();//2
  hd();//2
```
从上面例子我们可以知道，单纯的调用的话，这个函数是会在[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)里直接删除的。因为没有变量去指向它，这样会导致js内部觉得你不需要了。无论是全局还是局部，都会被删除

再加深下理解：
```js
function hd1(){
    let n = 1;
    return function sum(){
      // console.log(++n);
      let m = 7;
      function show(){
        console.log(++m);
      }
      show();
    }
    sum();
  }
  let tem1 = hd1();//8
  tem1();//8
  tem1();//8
  tem1();//8

```
此时我们发现我们创建了一个show方法，但是跟之前一样，使用完show方法后自动销毁了。**这是因为此时没有指向show方法的对象，没有引用他，没有保留show方法的内存空间**

```js
  function hd1(){
    let n = 1;
    return function sum(){
      let m = 7;
      return function show(){
        console.log(++m);
        console.log(n);//输出1

      }
      show();
    }
    sum();
  }
  let tem1 = hd1()();//此时时执行了sum函数,执行了之后返回了show()函数
  tem1();//8
  tem1();//9
  tem1();//10
  let tem2 = hd1()();//此时是新开辟了一个内存空间，m的起始值还是7m
  tem2();//8
  tem2();//9
  tem1();//11
```
这时show也保留

所以，如果定义一个对象指向一个方法的子元素，那么这个方法的内存空间作为父元素就会被保存

# 防抖和节流

节流和防抖的目的：都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，防止在短时间内频繁触发同一事件而出现延迟，假死或卡顿的现象。
节流和防抖的区别：
节流：目前有一事件A设置了定时器，那么在delay之前触发，都只会触发一次
防抖：如果不断在delay之前重新触发，那么定时器会不断重新计时，最终会在最后一次完后才执行，对于需要实时响应的，应该用节流。
以下分别是节流和防抖的实现代码
## 节流
```js
function throttle_1(fn,wait){
  var flag = true;
  return function(){
  	var context = this
    var args = arguments
    if(flag){
      flag = false
      setTimeout(() => {
        fn.apply(context,args)
        flag = true
      },wait)
    }
  }
}
```
## 防抖
```js
function debounce_1(fn,wait){
  var timerId = null;
  return function(){
  	var context = this
    var args = arguments
    clearTimeout(timerId);
    timerId = setTimeout(() => {
      fn.apply(context,args)
    },wait)
  }
}
```