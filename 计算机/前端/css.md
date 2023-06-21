# CSS



## 常用选择器

### 标签选择器

作用：根据标签名来选中指定的元素
语法：标签名{}

```css
h1{
    color: green;
  } 
```

### id选择器

作用：根据元素的id属性值选中一个元素
语法：#id属性值{}

```css
#red{
    color: red;
    }
```

### 类选择器

作用：根据元素的class属性值选中一组元素
语法：.class属性值

```css
.blue{
    color: blue;
    }
```

### 通配选择器

选中所有元素

```css
*{
    color: red;
}
```



## 复合选择器

连着写的常用选择器会形成and关系（一定要连着写）

```css
div.red{
    font-size: 30px;
}
.a.b.c{
            color: blue
        }
```

逗号隔开的则时或的关系

```css
h1, span{
    color: green
} //满足h1或span就赋予这属性
```



## 关系选择器

### 子元素选择器

作用：选中指定父元素的指定子元素
语法：父元素 > 子元素

```css
div.box > span{
    color: orange;
    } //选中一个类名为box的div元素的span子元素 但必须为直接儿子，不能为后代
```

### 后代元素选择器

作用：选中指定元素内的指定后代元素
语法：祖先 后代

```css
div span{
    color: skyblue
    }
```



### 兄弟元素选择器

```css
p + span{
    color: red;
} //p标签的前一个兄弟span


p ~ span{
    color: red;
}p标签的后一个兄弟span
```



## 属性选择器

[属性名] 选择含有指定属性的元素
[属性名=属性值] 选择含有指定属性和属性值的元素
[属性名^=属性值] 选择属性值以指定值开头的元素
[属性名$=属性值] 选择属性值以指定值结尾的元素
[属性名*=属性值] 选择属性值中含有某值的元素的元素

```css
p[title*=e]{
    color: orange;
}
```



## 伪类元素选择器

:first-child 第一个子元素
:last-child 最后一个子元素
:nth-child() 选中第n个子元素

```
以上这些伪类都是根据所有的子元素进行排序
```

:first-of-type
:last-of-type
:nth-of-type()

```
- 这几个伪类的功能和上述的类似，不通点是他们是在同类型元素中进行排序
```

:not() 否定伪类

```
- 将符合条件的元素从选择器中去除
```

```css
ul > li:first-of-type{
    color: red;
}
```

特殊问题：上边代码是选择ul子元素的的第一个li

而下边的代码则是选中ul的第一个元素，在选择li

```css
ul >li:first-child{
    color: red;
}
```



## a标签伪类

```css
:link 用来表示没访问过的链接（正常的链接）
a:link{
            color: red;
            
        }
```

```css
/* 
    :visited 用来表示访问过的链接
    由于隐私的原因，所以visited这个伪类只能修改链接的颜色
*/
a:visited{
    color: orange; 
    /* font-size: 50px;   */
}
```

```css
/* 
    :hover 用来表示鼠标移入的状态
 */
 a:hover{
     color: aqua;
     font-size: 50px;
 }
```

```css
/*
   :active 用来表示鼠标点击
*/
a:active{
    color: yellowgreen;
    
}
```



## 伪元素选择器

伪元素，表示页面中一些特殊的并不真实的存在的元素（特殊的位置）

```css
伪元素使用 :: 开头

::first-letter 表示第一个字母
::first-line 表示第一行
::selection 表示选中的内容
::before 元素的开始 
::after 元素的最后
    - before 和 after 必须结合content属性来使用
    div::before{
            content: 'abc';
            color: red;
        }
```

## 继承

样式的继承，我们为一个元素设置的样式同时也会应用到它的后代元素上

继承是发生在祖先后后代之间的

继承的设计是为了方便我们的开发，
    利用继承我们可以将一些通用的样式统一设置到共同的祖先元素上，
        这样只需设置一次即可让所有的元素都具有该样式

注意：并不是所有的样式都会被继承：
    比如 背景相关的，布局相关等的这些样式都不会被继承。

## 选择器的权重

样式的冲突
    - 当我们通过不同的选择器，选中相同的元素，并且为相同的样式设置不同的值时，此时就发生了样式的冲突。

发生样式冲突时，应用哪个样式由选择器的权重（优先级）决定

选择器的权重
    内联样式        1,0,0,0
    id选择器        0,1,0,0
    类和伪类选择器   0,0,1,0
    元素选择器       0,0,0,1
    通配选择器       0,0,0,0
    继承的样式       没有优先级

比较优先级时，需要将所有的选择器的优先级进行相加计算，最后优先级越高，则越优先显示（分组选择器是单独计算的）,
    选择器的累加不会超过其最大的数量级，类选择器在高也不会超过id选择器
    如果优先级计算后相同，此时则优先使用靠下的样式

可以在某一个样式的后边添加 !important ，则此时该样式会获取到最高的优先级，甚至超过内联样式，
    注意：在开发中这个玩意一定要慎用！

## 单位

```
长度单位：
    像素
        - 屏幕（显示器）实际上是由一个一个的小点点构成的
        - 不同屏幕的像素大小是不同的，像素越小的屏幕显示的效果越清晰
        - 所以同样的200px在不同的设备下显示效果不一样
        但windows会在高像素的机器上进行一定程度的放大

    百分比
        - 也可以将属性值设置为相对于其父元素属性的百分比
        - 设置百分比可以使子元素跟随父元素的改变而改变

    em
        - em是相对于元素的字体大小来计算的
        - 1em = 1font-size
        - em会根据字体大小的改变而改变
        修改font-size会导致em改变

    rem
        - rem是相对于根元素的字体大小来计算
```

## 颜色

```css
.box1{
    width: 100px;
    height: 100px;
    /* 
        颜色单位：
            在CSS中可以直接使用颜色名来设置各种颜色
                比如：red、orange、yellow、blue、green ... ...
                但是在css中直接使用颜色名是非常的不方便

            RGB值：
                - RGB通过三种颜色的不同浓度来调配出不同的颜色
                - R red，G green ，B blue
                - 每一种颜色的范围在 0 - 255 (0% - 100%) 之间
                - 语法：RGB(红色,绿色,蓝色)

            RGBA:
                - 就是在rgb的基础上增加了一个a表示不透明度
                - 需要四个值，前三个和rgb一样，第四个表示不透明度
                    1表示完全不透明   0表示完全透明  .5半透明

            十六进制的RGB值：
                - 语法：#红色绿色蓝色
                - 颜色浓度通过 00-ff
                - 如果颜色两位两位重复可以进行简写  
                    #aabbcc --> #abc
            
            HSL值 HSLA值
                H 色相(0 - 360)
                S 饱和度，颜色的浓度 0% - 100%
                L 亮度，颜色的亮度 0% - 100%

     */
    background-color: red;
    background-color: rgb(255, 0, 0);
    background-color: rgb(0, 255, 0);
    background-color: rgb(0, 0, 255);
    background-color: rgb(255,255,255);
    background-color: rgb(106,153,85);
    background-color: rgba(106,153,85,.5);
    background-color: #ff0000;
    background-color: #ffff00;
    background-color: #ff0;
    background-color: #bbffaa;
    background-color: #9CDCFE;
    background-color: rgb(254, 156, 156);
    background-color: hsla(98, 48%, 40%, 0.658);
}
```

## 文档流

文档流（normal flow）

网页是一个多层的结构，一层摞着一层

通过CSS可以分别为每一层来设置样式

作为用户来讲只能看到最顶上一层

这些层中，最底下的一层称为文档流，文档流是网页的基础

我们所创建的元素默认都是在文档流中进行排列

对于我们来元素主要有两个状态
在文档流中
不在文档流中（脱离文档流）

- 元素在文档流中有什么特点：
    - 块元素
        - 块元素会在页面中独占一行(自上向下垂直排列)
        - 默认宽度是父元素的全部（会把父元素撑满）
        - 默认高度是被内容撑开（子元素）

    - 行内元素
        - 行内元素不会独占页面的一行，只占自身的大小
        - 行内元素在页面中左向右水平排列，如果一行之中不能容纳下所有的行内元素
            则元素会换到第二行继续自左向右排列（书写习惯一致）
        - 行内元素的默认宽度和高度都是被内容撑开

## 盒模型

盒模型、盒子模型、框模型（box model）
    - CSS将页面中的所有元素都设置为了一个矩形的盒子
        - 将元素设置为矩形的盒子后，对页面的布局就变成将不同的盒子摆放到不同的位置
        - 每一个盒子都由一下几个部分组成：
            内容区（content）
            内边距（padding）
            边框（border）
            外边距（margin）

```css
.box1{
    /* 
        内容区（content），元素中的所有的子元素和文本内容都在内容区中排列  
            内容区的大小由width 和 height两个属性来设置
                width 设置内容区的宽度
                height 设置内容区的高度          
     */
    width: 200px;
    height: 200px;
    background-color: #bfa;

    /* 
        边框（border），边框属于盒子边缘，边框里边属于盒子内部，出了边框都是盒子的外部
            边框的大小会影响到整个盒子的大小
        要设置边框，需要至少设置三个样式：
            边框的宽度 border-width
            边框的颜色 border-color
            边框的样式 border-style
     */

     border-width: 10px;
     border-color: red;
     border-style: solid;
}
```

### 边框

```css
.box1{
    width: 200px;
    height: 200px;
    background-color: #bfa;

    /* 
        边框
            边框的宽度 border-width
            边框的颜色 border-color
            边框的样式 border-style
     */

     /* 
     border-width: 10px; 
        默认值，一般都是 3个像素
        border-width可以用来指定四个方向的边框的宽度
            值的情况
                四个值：上 右 下 左
                三个值：上 左右 下
                两个值：上下 左右
                一个值：上下左右
            
        除了border-width还有一组 border-xxx-width
            xxx可以是 top right bottom left
            用来单独指定某一个边的宽度

            
     */
     /* border-width: 10px; */
     /* border-top-width: 10px;
     border-left-width: 30px; */

    color: red;


     /* 
        border-color用来指定边框的颜色，同样可以分别指定四个边的边框
            规则和border-width一样

        border-color也可以省略不写，如果省略了则自动使用color的颜色值
      */
     /* border-color: orange red yellow green;
     border-color: orange; */


    /* 
        border-style 指定边框的样式
            solid 表示实线
            dotted 点状虚线
            dashed 虚线
            double 双线

            border-style的默认值是none 表示没有边框

     */
     /* border-style: solid dotted dashed double;
     border-style: solid; */

     /* border-width: 10px;
     border-color: orange;
     border-style: solid; */

     /* 
        border简写属性，通过该属性可以同时设置边框所有的相关样式，并且没有顺序要求

        除了border以外还有四个 border-xxx
            border-top
            border-right
            border-bottom
            border-left
      */
      /* border: solid 10px orange; */
      /* border-top: 10px solid red;
      border-left: 10px solid red;
      border-bottom: 10px solid red; */

      border: 10px red solid;
      border-right: none;
}
```



### 内边距

```css
.box1{
    width: 200px;
    height: 200px;
    background-color: #bfa;
    border: 10px orange solid;

    /* 
        内边距（padding）
            - 内容区和边框之间的距离是内边距
            - 一共有四个方向的内边距：
                padding-top
                padding-right
                padding-bottom
                padding-left

            - 内边距的设置会影响到盒子的大小
            - 背景颜色会延伸到内边距上

        一共盒子的可见框的大小，由内容区 内边距 和 边框共同决定，
            所以在计算盒子大小时，需要将这三个区域加到一起计算
     */

     /* padding-top: 100px;
     padding-left: 100px;
     padding-right: 100px;
     padding-bottom: 100px; */

     /* 
        padding 内边距的简写属性，可以同时指定四个方向的内边距
            规则和border-width 一样
      */
      padding: 10px 20px 30px 40px;
      padding: 10px 20px 30px ;
      padding: 10px 20px ;
      padding: 10px ;
}
```

**内边距的设置会影响到盒子的大小**

**背景颜色会延伸到内边距上**

我们之前设置的大小只是内容区的大小

一共盒子的可见框的大小，由内容区 内边距 和 边框共同决定，
 所以在计算盒子大小时，需要将这三个区域加到一起计算

### 外边距

```css
.box1{
    width: 200px;
    height: 200px;
    background-color: #bfa;
    border: 10px red solid;

    /* 
        外边距（margin）
            - 外边距不会影响盒子可见框的大小
            - 但是外边距会影响盒子的位置
            - 一共有四个方向的外边距：
                margin-top
                    - 上外边距，设置一个正值，元素会向下移动
                margin-right
                    - 默认情况下设置margin-right不会产生任何效果
                margin-bottom
                    - 下外边距，设置一个正值，其下边的元素会向下移动
                margin-left
                    - 左外边距，设置一个正值，元素会向右移动

                - margin也可以设置负值，如果是负值则元素会向相反的方向移动

            - 元素在页面中是按照自左向右的顺序排列的，
                所以默认情况下如果我们设置的左和上外边距则会移动元素自身
                而设置下和右外边距会移动其他元素

            - margin的简写属性
                margin 可以同时设置四个方向的外边距 ，用法和padding一样

            - margin会影响到盒子实际占用空间
     */

     /* margin-top: 100px;
     margin-left: 100px;
     margin-bottom: 100px; */

     /* margin-bottom: 100px; */
     /* margin-top: -100px; */
     /* margin-left: -100px; */
     /* margin-bottom: -100px; */

     /* margin-right: 0px; */

     margin: 100px;
}
```

### 水平布局

```css
.inner{
            /* width: auto;  width的值默认就是auto*/
            width: 200px;
            height: 200px;
            background-color: #bfa;
            margin-right: auto;
            margin-left: auto;
            /* margin-left: 100px;
            margin-right: 400px */
            /* 
                元素的水平方向的布局：
                    元素在其父元素中水平方向的位置由以下几个属性共同决定“
                        margin-left
                        border-left
                        padding-left
                        width
                        padding-right
                        border-right
                        margin-right

                    一个元素在其父元素中，水平布局必须要满足以下的等式
					margin-left+border-left+padding-left+width+padding-right+border-right+margin-right = 其父元素内容区的宽度 （必须满足）

                0 + 0 + 0 + 200 + 0 + 0 + 0 = 800
                0 + 0 + 0 + 200 + 0 + 0 + 600 = 800


                100 + 0 + 0 + 200 + 0 + 0 + 400 = 800
                100 + 0 + 0 + 200 + 0 + 0 + 500 = 800
                    - 以上等式必须满足，如果相加结果使等式不成立，则称为过度约束，则等式会自动调整
                        - 调整的情况：
                            - 如果这七个值中没有为 auto 的情况，则浏览器会自动调整margin-right值以使等式满足
                    - 这七个值中有三个值和设置为auto
                        width
                        margin-left
                        maring-right
                        - 如果某个值为auto，则会自动调整为auto的那个值以使等式成立
                            0 + 0 + 0 + auto + 0 + 0 + 0 = 800  auto = 800
                            0 + 0 + 0 + auto + 0 + 0 + 200 = 800  auto = 600
                            200 + 0 + 0 + auto + 0 + 0 + 200 = 800  auto = 400

                            auto + 0 + 0 + 200 + 0 + 0 + 200 = 800  auto = 400


                            auto + 0 + 0 + 200 + 0 + 0 + auto = 800  auto = 300

                        - 如果将一个宽度和一个外边距设置为auto，则宽度会调整到最大，设置为auto的外边距会自动为0
                        - 如果将三个值都设置为auto，则外边距都是0，宽度最大
                        - 如果将两个外边距设置为auto，宽度固定值，则会将外边距设置为相同的值
                            所以我们经常利用这个特点来使一个元素在其父元素中水平居中
                            示例：
                                width:xxxpx;
                                margin:0 auto;



             */
        }
```



### 垂直布局（overflow）

```css
/*
如果没有设置父元素的高度
默认情况下，父元素的高度被内容撑开
*/

.box1{
    width: 200px;
    height: 200px;
    background-color: #bfa;
    /* 
        子元素是在父元素的内容区中排列的，
            如果子元素的大小超过了父元素，则子元素会从父元素中溢出
            使用 overflow 属性来设置父元素如何处理溢出的子元素

            可选值：
                visible，默认值 子元素会从父元素中溢出，在父元素外部的位置显示
                hidden 溢出内容将会被裁剪不会显示
                scroll 生成两个滚动条，通过滚动条来查看完整的内容
                auto 根据需要生成滚动条

        overflow-x: 
        overflow-y:
     */
    overflow: auto;

    
}
```

### 相邻垂直方向的外边距的重叠

```css
/* 
    垂直外边距的重叠（折叠）
        - 相邻的垂直方向外边距会发生重叠现象
        - 兄弟元素
            - 兄弟元素间的相邻垂直外边距会取两者之间的较大值（两者都是正值）
            - 特殊情况：
                如果相邻的外边距一正一负，则取两者的和
                如果相邻的外边距都是负值，则取两者中绝对值较大的

            - 兄弟元素之间的外边距的重叠，对于开发是有利的，所以我们不需要进行处理


        - 父子元素
            - 父子元素间相邻外边距，子元素的会传递给父元素（上外边距）
            - 父子外边距的折叠会影响到页面的布局，必须要进行处理
            -所以想在父元素中移动边缘子元素的位置需要改动父元素的padding，尽量别改动紫云寺的margin

 */
```

### 行内元素的特点（display）

```
/* 
    行内元素的盒模型
        - 行内元素不支持设置宽度和高度
        - 行内元素可以设置padding，但是垂直方向padding不会影响页面的布局
        - 行内元素可以设置border，垂直方向的border不会影响页面的布局
        - 行内元素可以设置margin，垂直方向的margin不会影响布局
        -垂直方向只会盖住其他元素，但水平方向会影响布局
 */
 
             /* 
                display 用来设置元素显示的类型
                    可选值：
                        inline 将元素设置为行内元素
                        block 将元素设置为块元素
                        inline-block 将元素设置为行内块元素 
                                行内块，既可以设置宽度和高度又不会独占一行
                        table 将元素设置为一个表格
                        none 元素不在页面中显示

                visibility 用来设置元素的显示状态
                    可选值：
                        visible 默认值，元素在页面中正常显示
                        hidden 元素在页面中隐藏 不显示，但是依然占据页面的位置
             */
```

### 默认样式

```css
/* 
    默认样式：
        - 通常情况，浏览器都会为元素设置一些默认样式
        - 默认样式的存在会影响到页面的布局，
            通常情况下编写网页时必须要去除浏览器的默认样式（PC端的页面）
 */
           *{
            margin: 0;
            padding: 0;
            }
            
            /* 去除项目符号 * /
            list-style:none;  可以去除列表的点符号
```



### 盒子的大小

```css
.box1{
    width: 100px;
    height: 100px;
    background-color: #bfa;
    padding: 10px;
    border: 10px red solid;
    /* 
        默认情况下，盒子可见框的大小由内容区、内边距和边框共同决定

            box-sizing 用来设置盒子尺寸的计算方式（设置width和height的作用）
                可选值：
                    content-box 默认值，宽度和高度用来设置内容区的大小
                    border-box 宽度和高度用来设置整个盒子可见框的大小
                        width 和 height 指的是内容区 和 内边距 和 边框的总大小
     */
    
    box-sizing: border-box;
}
```

### 轮廓

轮廓不会影响布局，只会产生显示效果

```css
.box1{
            width: 200px;
            height: 200px;
            background-color: #bfa;

            /* box-shadow 用来设置元素的阴影效果，阴影不会影响页面布局 
                第一个值 水平偏移量 设置阴影的水平位置 正值向右移动 负值向左移动
                第二个值 垂直偏移量 设置阴影的水平位置 正值向下移动 负值向上移动
                第三个值 阴影的模糊半径
                第四个值 阴影的颜色
            */

            box-shadow: 0px 0px 50px rgba(0, 0, 0, .3) ; 

/* 

            outline 用来设置元素的轮廓线，用法和border一模一样
                轮廓和边框不同的点，就是轮廓不会影响到可见框的大小    
 */
            
        }

         .box1:hover{
            outline: 10px red solid;
        } 
```

### 圆角

```css
.box2{
    width: 200px;
    height: 200px;
    background-color: orange;

    /* border-radius: 用来设置圆角 圆角设置的圆的半径大小*/

    /* border-top-left-radius:  */
    /* border-top-right-radius */
    /* border-bottom-left-radius:  */
    /* border-bottom-right-radius:  */
    /* border-top-left-radius:50px 100px; */

    /* 
        border-radius 可以分别指定四个角的圆角
            四个值 左上 右上 右下 左下
            三个值 左上 右上/左下 右下 
            两个个值 左上/右下 右上/左下  
      */
    /* border-radius: 20px / 40px; */

    /* 将元素设置为一个圆形 */
    border-radius: 50%;
}
```

## 浮动

### 基本内容

```css
.box1{
    width: 400px;
    height: 200px;
    background-color: #bfa;

    /* 
        通过浮动可以使一个元素向其父元素的左侧或右侧移动
            使用 float 属性来设置于元素的浮动
                可选值：
                    none 默认值 ，元素不浮动
                    left 元素向左浮动
                    right 元素向右浮动

            注意，元素设置浮动以后，水平布局的等式便不需要强制成立
                元素设置浮动以后，会完全从文档流中脱离，不再占用文档流的位置，
                    所以元素下边的还在文档流中的元素会自动向上移动

            浮动的特点：
                1、浮动元素会完全脱离文档流，不再占据文档流中的位置
                2、设置浮动以后元素会向父元素的左侧或右侧移动，
                3、浮动元素默认不会从父元素中移出
                4、浮动元素向左或向右移动时，不会超过它前边的其他浮动元素
                5、如果浮动元素的上边是一个没有浮动的块元素，则浮动元素无法上移
                6、浮动元素不会超过它上边的浮动的兄弟元素，最多最多就是和它一样高

            简单总结：
                浮动目前来讲它的主要作用就是让页面中的元素可以水平排列，
                    通过浮动可以制作一些水平方向的布局    
     */

    float: left;
}
```

### 浮动特点

```
/* 
    浮动元素不会盖住文字，文字会自动环绕在浮动元素的周围，
        所以我们可以利用浮动来设置文字环绕图片的效果
 */
 /*
                元素设置浮动以后，将会从文档流中脱离，从文档流中脱离后，元素的一些特点也会发生变化

                脱离文档流的特点：
                    块元素：
                        1、块元素不在独占页面的一行
                        2、脱离文档流以后，块元素的宽度和高度默认都被内容撑开

                    行内元素：
                        行内元素脱离文档流以后会变成块元素，特点和块元素一样

                    脱离文档流以后，不需要再区分块和行内了
              */
```

### 高度塌陷问题

```css
.outer{
    border: 10px red solid;

    /* 
        BFC(Block Formatting Context) 块级格式化环境
            - BFC是一个CSS中的一个隐含的属性，可以为一个元素开启BFC
                开启BFC该元素会变成一个独立的布局区域
            - 元素开启BFC后的特点：
                1.开启BFC的元素不会被浮动元素所覆盖
                2.开启BFC的元素子元素和父元素外边距不会重叠
                3.开启BFC的元素可以包含浮动的子元素

            - 可以通过一些特殊方式来开启元素的BFC：
                1、设置元素的浮动（不推荐）
                2、将元素设置为行内块元素（不推荐）
                3、将元素的overflow设置为一个非visible的值
                    - 常用的方式 为元素设置 overflow:hidden 开启其BFC 以使其可以包含浮动元素
    
     */

     /* float: left; */
     /* display: inline-block; */
     overflow: hidden;
}

.inner{
    width: 100px;
    height: 100px;
    background-color: #bfa;

    /* 
        高度塌陷的问题：
            在浮动布局中，父元素的高度默认是被子元素撑开的，
                当子元素浮动后，其会完全脱离文档流，子元素从文档流中脱离
                将会无法撑起父元素的高度，导致父元素的高度丢失

            父元素高度丢失以后，其下的元素会自动上移，导致页面的布局混乱

            所以高度塌陷是浮动布局中比较常见的一个问题，这个问题我们必须要进行处理！
     */
    float: left;
}
```

### clear

```
如果我们不希望某个元素因为其他元素浮动的影响而改变位置，
    可以通过clear属性来清除浮动元素对当前元素所产生的影响

clear
    - 作用：清除浮动元素对当前元素所产生的影响
    - 可选值：
        left 清除左侧浮动元素对当前元素的影响
        right 清除右侧浮动元素对当前元素的影响
        both 清除两侧中最大影响的那侧

    原理：
        设置清除浮动以后，浏览器会自动为元素添加一个上外边距，
            以使其位置不受其他元素的影响
```

### :after解决塌陷（最终方案）

```css
.box1::after{
    content: '';
    display: block;
    clear: both;
}
在盒子添加after样式
设置内容content为空，设置display将其转化为块级元素，并清除浮动，就可以在浮动的元素下边添加一个看似存在的东西
将盒子撑起来
```

### 解决外边距重叠和高度塌陷

```css
.clearfix::before,
.clearfix::after{
    content: '';
    display: table;
    clear: both;
}
```



## 定位

### 相对定位

```
/*
    定位（position）
        - 定位是一种更加高级的布局手段
        - 通过定位可以将元素摆放到页面的任意位置
        - 使用position属性来设置定位
            可选值：
                static 默认值，元素是静止的没有开启定位
                relative 开启元素的相对定位
                absolute 开启元素的绝对定位
                fixed 开启元素的固定定位
                sticky 开启元素的粘滞定位

        - 相对定位：
            - 当元素的position属性值设置为relative时则开启了元素的相对定位
            - 相对定位的特点：
                1.元素开启相对定位以后，如果不设置偏移量元素不会发生任何的变化
                2.相对定位是参照于元素在文档流中的位置进行定位的
                3.相对定位会提升元素的层级
                4.相对定位不会使元素脱离文档流
                5.相对定位不会改变元素的性质块还是块，行内还是行内

        - 偏移量（offset）
            - 当元素开启了定位以后，可以通过偏移量来设置元素的位置
                top
                    - 定位元素和定位位置上边的距离
                bottom
                    - 定位元素和定位位置下边的距离

                    - 定位元素垂直方向的位置由top和bottom两个属性来控制
                        通常情况下我们只会使用其中一
                    - top值越大，定位元素越向下移动
                    - bottom值越大，定位元素越向上移动
                left
                    - 定位元素和定位位置的左侧距离
                right
                    - 定位元素和定位位置的右侧距离

                    - 定位元素水平方向的位置由left和right两个属性控制
                        通常情况下只会使用一个
                    - left越大元素越靠右
                    - right越大元素越靠左

  */
```

### 绝对定位

```
/* 
 绝对定位
     - 当元素的position属性值设置为absolute时，则开启了元素的绝对定位
     - 绝对定位的特点：
         1.开启绝对定位后，如果不设置偏移量元素的位置不会发生变化
         2.开启绝对定位后，元素会从文档流中脱离
         3.绝对定位会改变元素的性质，行内变成块，块的宽高被内容撑开
         4.绝对定位会使元素提升一个层级
         5.绝对定位元素是相对于其包含块进行定位的

         包含块( containing block )
             - 正常情况下：
                 包含块就是离当前元素最近的祖先块元素
                 <div> <div></div> </div>
                 <div><span><em>hello</em></span></div>

             - 绝对定位的包含块:
                 包含块就是离它最近的开启了定位的祖先元素，
                     如果所有的祖先元素都没有开启定位则根元素就是它的包含块

             - html（根元素、初始包含块）

 */
```

### 固定定位

```
固定定位：
    - 将元素的position属性设置为fixed则开启了元素的固定定位
    - 固定定位也是一种绝对定位，所以固定定位的大部分特点都和绝对定位一样
        唯一不同的是固定定位永远参照于浏览器的视口进行定位
        固定定位的元素不会随网页的滚动条滚动
```

### 粘滞定位

```
/* 
    粘滞定位
        - 当元素的position属性设置为sticky时则开启了元素的粘滞定位
        - 粘滞定位和相对定位的特点基本一致，
            不同的是粘滞定位可以在元素到达某个位置时将其固定
 */
```

### 绝对定位的布局

```
/* 
    水平布局
        left + margin-left + border-left + padding-left + width + padding-right + border-right + margin-right + right = 包含块的内容区的宽度

    - 当我们开启了绝对定位后:
        水平方向的布局等式就需要添加left 和 right 两个值
            此时规则和之前一样只是多添加了两个值：
                当发生过度约束：
                    如果9个值中没有 auto 则自动调整right值以使等式满足
                    如果有auto，则自动调整auto的值以使等式满足

            - 可设置auto的值
                margin width left right

            - 因为left 和 right的值默认是auto，所以如果不指定left和right
                则等式不满足时，会自动调整这两个值

        垂直方向布局的等式的也必须要满足
            top + margin-top/bottom + padding-top/bottom + border-top/bottom + height = 包含块的高度


 */
```

### 定位元素的层级

```
/* 
    对于开启了定位元素，可以通过z-index属性来指定元素的层级
        z-index需要一个整数作为参数，值越大元素的层级越高
            元素的层级越高越优先显示

        如果元素的层级一样，则优先显示靠下的元素

        祖先的元素的层级再高也不会盖住后代元素
 */
 /* z-index: 3; */
```

## 字体

```
/* 
字体相关的样式 
    color 用来设置字体颜色
    font-size 字体的大小
        和font-size相关的单位
        em 相当于当前元素的一个font-size
        rem 相对于根元素的一个font-size
    font-family 字体族（字体的格式）
        可选值：
            serif  衬线字体
            sans-serif 非衬线字体
            monospace 等宽字体
                - 指定字体的类别，浏览器会自动使用该类别下的字体

        - font-family 可以同时指定多个字体，多个字体间使用,隔开
            字体生效时优先使用第一个，第一个无法使用则使用第二个 以此类推

            Microsoft YaHei,Heiti SC,tahoma,arial,Hiragino Sans GB,"\5B8B\4F53",sans-serif


*/
/* 
        font-face可以将服务器中的字体直接提供给用户去使用 
            问题：
                1.加载速度
                2.版权
                3.字体格式
        
        */
        @font-face {
                /* 指定字体的名字 */
            font-family:'myfont' ;
            /* 服务器中字体的路径 */
            src: url('./font/ZCOOLKuaiLe-Regular.ttf') format("truetype");
        }
```

### 图标字体

```
<!-- 
    图标字体（iconfont）
        - 在网页中经常需要使用一些图标，可以通过图片来引入图标
            但是图片大小本身比较大，并且非常的不灵活
        - 所以在使用图标时，我们还可以将图标直接设置为字体，
            然后通过font-face的形式来对字体进行引入
        - 这样我们就可以通过使用字体的形式来使用图标

    fontawesome 使用步骤
        1.下载 https://fontawesome.com/
        2.解压
        3.将css和webfonts移动到项目中
        4.将all.css引入到网页中
        5.使用图标字体
            - 直接通过类名来使用图标字体
                class="fas fa-bell"
                class="fab fa-accessible-icon"

 -->
 <i class="fas fa-bell-slash"></i>
 一般使用这样一个i标签引入一个图标
 
         li::before{
            /* 
                通过伪元素来设置图标字体
                    1.找到要设置图标的元素通过before或after选中
                    2.在content中设置字体的编码
                    3.设置字体的样式
                        fab
                        font-family: 'Font Awesome 5 Brands';

                        fas
                        font-family: 'Font Awesome 5 Free';
                        font-weight: 900; 

            */
            content: '\f1b0';
            /* font-family: 'Font Awesome 5 Brands'; */
            font-family: 'Font Awesome 5 Free';
            font-weight: 900; 
            color: blue;
            margin-right: 10px;
        }
```

### 阿里的图标字体库（icon font）

https://www.iconfont.cn/collections/index?spm=a313x.7781069.1998910419.5&type=1

可以使用阿里的图标库，彩色的必须使用图片引入，单色的我们可以使用图标库引入

具体可以看项目的教程

### 行高

```
/* 
    行高（line height）
        - 行高指的是文字占有的实际高度
        - 可以通过line-height来设置行高
            行高可以直接指定一个大小（px em）
            也可以直接为行高设置一个整数
                如果是一个整数的话，行高将会是字体的指定的倍数
        - 行高经常还用来设置文字的行间距
            行间距 = 行高 - 字体大小

    字体框
        - 字体框就是字体存在的格子，设置font-size实际上就是在设置字体框的高度

    行高会在字体框的上下平均分配

*/
```

### 字体的简写

```
/* 
    font 可以设置字体相关的所有属性
        语法：
            font: 字体大小/行高 字体族
            行高 可以省略不写 如果不写使用默认值

*/
如果不写行高，会设置默认值，所以如果要修改行高，需要在font这个下边写
在上边写会被覆盖为默认值
```

### 文本的样式

```
/* 
    text-align 文本的水平对齐
        可选值：
            left 左侧对齐
            right 右对齐
            center 居中对齐
            justify 两端对齐
*/
```

```
/*
vertical-align 设置元素垂直对齐的方式
    可选值：
        baseline 默认值 基线对齐
        top 顶部对齐
        bottom 底部对齐
        middle 居中对齐
*/
```

```
/* 
    text-decoration 设置文本修饰
        可选值：
            none 什么都没有
            underline 下划线
            line-through 删除线
            overline 上划线
*/
```

```css
/* 
    white-space 设置网页如何处理空白
        可选值：
            normal 正常
            nowrap 不换行
            pre 保留空白

*/
 white-space: nowrap;
//溢出的隐藏
overflow: hidden;
//将溢出的文本设置省略号
text-overflow: ellipsis;
使用以上三个样式就可设置文本省略样式
```

## 背景

```css
.box1{
    width: 500px;
    height: 500px;
    /* 
        background-color 设置背景颜色
     */
    background-color: #bfa;

    /* 
        background-image 设置背景图片 
            - 可以同时设置背景图片和背景颜色，这样背景颜色将会成为图片的背景色
            - 如果背景的图片小于元素，则背景图片会自动在元素中平铺将元素铺满
            - 如果背景的图片大于元素，将会一个部分背景无法完全显示
            - 如果背景图片和元素一样大，则会直接正常显示
            
    */
    background-image: url("./img/1.png");

    /* 
        background-repeat 用来设置背景的重复方式
            可选值：
                repeat 默认值 ， 背景会沿着x轴 y轴双方向重复
                repeat-x 沿着x轴方向重复
                repeat-y 沿着y轴方向重复
                no-repeat 背景图片不重复
     */
    background-repeat: no-repeat;

    /*
        background-position 用来设置背景图片的位置
            设置方式：
                通过 top left right bottom center 几个表示方位的词来设置背景图片的位置
                    使用方位词时必须要同时指定两个值，如果只写一个则第二个默认就是center

                通过偏移量来指定背景图片的位置：
                    水平方向的偏移量 垂直方向变量
    */
    /* background-position: center; */
    background-position: -50px 300px;



}
```

```css
.box1{
    width: 500px;
    height: 500px;
    overflow: auto;
    background-color: #bfa;
    background-image: url("./img/2.jpg");
    background-repeat: no-repeat;
    background-position: 0 0;
    padding: 10px;

    /*
         设置背景的范围 
            background-clip 
                可选值：
                    border-box 默认值，背景会出现在边框的下边
                    padding-box 背景不会出现在边框，只出现在内容区和内边距
                    content-box 背景只会出现在内容区

            background-origin 背景图片的偏移量计算的原点
                    padding-box 默认值，background-position从内边距处开始计算
                    content-box 背景图片的偏移量从内容区处计算
                    border-box 背景图片的变量从边框处开始计算
    */
    /* background-origin: border-box;
    background-clip: content-box; */

    /* 
        background-size 设置背景图片的大小
            第一个值表示宽度 
            第二个值表示高度
            - 如果只写一个，则第二个值默认是 auto

            cover 图片的比例不变，将元素铺满
            contain 图片比例不变，将图片在元素中完整显示
    */
    background-size: contain;

    /* 
        background-color
        background-image
        background-repeat
        background-position
        background-size
        background-origin
        background-clip
        background-attachment

        - backgound 背景相关的简写属性，所有背景相关的样式都可以通过该样式来设置
            并且该样式没有顺序要求，也没有哪个属性是必须写的

            注意：
                background-size必须写在background-position的后边，并且使用/隔开
                    background-position/background-size

                background-origin background-clip 两个样式 ，orgin要在clip的前边
        

    
     */




}
```

```css
/* 
background-attachment
    - 背景图片是否跟随元素移动
    - 可选值：
        scroll 默认值 背景图片会跟随元素移动
        fixed 背景会固定在页面中，不会随元素移动
 */
```

### 渐变

```css
.box1{
    width: 200px;
    height: 200px;
    /* background-color: #bfa; */
    /* 
        通过渐变可以设置一些复杂的背景颜色，可以实现从一个颜色向其他颜色过渡的效果
        ！！渐变是图片，需要通过background-image来设置

        线性渐变，颜色沿着一条直线发生变化
            linear-gradient()

            linear-gradient(red,yellow) 红色在开头，黄色在结尾，中间是过渡区域
            - 线性渐变的开头，我们可以指定一个渐变的方向
                to left
                to right
                to bottom
                to top
                deg deg表示度数
                turn 表示圈

            - 渐变可以同时指定多个颜色，多个颜色默认情况下平均分布，
                也可以手动指定渐变的分布情况

            repeating-linear-gradient() 可以平铺的线性渐变
     */
    
    /* background-image: linear-gradient(red,yellow,#bfa,orange); */
    /* background-image: linear-gradient(red 50px,yellow 100px, green 120px, orange 200px); */
    background-image: repeating-linear-gradient(to right ,red, yellow 50px);

}
```

### 径向渐变

```css
.box1{
            width: 300px;
            height: 300px;

/* 
            radial-gradient() 径向渐变(放射性的效果) */
            /* 
                 默认情况下径向渐变的形状根据元素的形状来计算的
                    正方形 --> 圆形
                    长方形 --> 椭圆形
                    - 我们也可以手动指定径向渐变的大小
                    circle
                    ellipse

                    - 也可以指定渐变的位置
                    - 语法：
                        radial-gradient(大小 at 位置, 颜色 位置 ,颜色 位置 ,颜色 位置)
                            大小：
                                circle 圆形
                                ellipse 椭圆
                                closest-side 近边    
                                closest-corner 近角
                                farthest-side 远边
                                farthest-corner 远角

                            位置：
                                top right left center bottom
                                


             */

            background-image: radial-gradient(farthest-corner at 100px 100px, red , #bfa)
        }
```

## 过渡

```css
.box2{
    background-color: #bfa;
    /* margin-left: auto; */
    /* transition:all 2s; */
    /* 
        过渡（transition）
            - 通过过渡可以指定一个属性发生变化时的切换方式
            - 通过过渡可以创建一些非常好的效果，提升用户的体验
     */

     /* 
     transition-property: 指定要执行过渡的属性  
        多个属性间使用,隔开 
        如果所有属性都需要过渡，则使用all关键字
        大部分属性都支持过渡效果，注意过渡时必须是从一个有效数值向另外一个有效数值进行过渡
     */
    /* transition-property: height , width; */
    /* transition-property: all; */

     /*
      transition-duration: 指定过渡效果的持续时间
        时间单位：s 和 ms  1s = 1000ms
      */
     /* transition-duration: 100ms, 2s; */
     /* transition-duration: 2s; */

     /* 
     transition-timing-function: 过渡的时序函数
        指定过渡的执行的方式  
        可选值：
            ease 默认值，慢速开始，先加速，再减速
            linear 匀速运动
            ease-in 加速运动
            ease-out 减速运动
            ease-in-out 先加速 后减速
            cubic-bezier() 来指定时序函数
                https://cubic-bezier.com
            steps() 分步执行过渡效果
                可以设置一个第二个值：
                    end ， 在时间结束时执行过渡(默认值)
                    start ， 在时间开始时执行过渡
     */
     /* transition-timing-function: cubic-bezier(.24,.95,.82,-0.88); */
     /* transition-timing-function: steps(2, start); */


     /* 
     transition-delay: 过渡效果的延迟，等待一段时间后在执行过渡  
     */
     /* transition-delay: 2s; */
     

     /* transition 可以同时设置过渡相关的所有属性，只有一个要求，如果要写延迟，则两个时间中第一个是持续时间，第二个是延迟 */
     transition:2s margin-left 1s cubic-bezier(.24,.95,.82,-0.88);
}
```

## 动画
### animation
```css
/*
animation
 可以设置动画的运行状态
 
*/
.div{
	/* 动画名称 */
	animation-name: move;  /*这里是关键帧动画的名称*/
	/* 持续时间 */
	animation-duration: 4s;
	/* 动画速度曲线 */
	animation-timing-function: ease;
	/* 动画开始时间 */
	animation-delay: 1s;
	/* 动画播放次数 */
	/* animation-iteration-count: 2; */
	/* 动画播放方向 */
	/* animation-direction: alternate; */
	/* 动画结束后状态 */
	animation-fill-mode: forwards;
}

/*简写*/
.div{
 /* animation: 动画名称 持续时间 运动曲线 何时开始 播放次数 是否反方向 起始与结束状态 */
   animation: name duration timing-function delay iteration-count direction fill-mode
}

简写里面不包含 animation-paly-state
暂停动画 animation-paly-state: paused; 经常和鼠标经过等其他配合使用
要想动画走回来，而不是直接调回来：animation-direction: alternate
盒子动画结束后，停在结束位置：animation-fill-mode: forwards


```
### 关键帧
```css
/* 
动画
    动画和过渡类似，都是可以实现一些动态的效果，
        不同的是过渡需要在某个属性发生变化时才会触发
        动画可以自动触发动态效果
        
    设置动画效果，必须先要设置一个关键帧，关键帧设置了动画执行每一个步骤
*/
@keyframes test {
    /* from表示动画的开始位置 也可以使用 0% */
    from{
        margin-left: 0;
        background-color: orange;
    } 

    /* to动画的结束位置 也可以使用100%*/
    to{
        background-color: red;
        margin-left: 700px;
    }
}
```

```css
.box2{
    background-color: #bfa;
    

    /* 设置box2的动画 */
    /* animation-name: 要对当前元素生效的关键帧的名字 */
    /* animation-name: test; */

    /* animation-duration: 动画的执行时间 */
    /* animation-duration: 4s; */

    

    /* 动画的延时 */
    /* animation-delay: 2s; */

    /* animation-timing-function: ease-in-out; */

    /* 
    animation-iteration-count 动画执行的次数 
        可选值：
            次数
            infinite 无限执行
    */
    /* animation-iteration-count: 1; */

    /*
     animation-direction
        指定动画运行的方向
            可选值：
            normal 默认值  从 from 向 to运行 每次都是这样 
            reverse 从 to 向 from 运行 每次都是这样 
            alternate 从 from 向 to运行 重复执行动画时反向执行
            alternate-reverse 从 to 向 from运行 重复执行动画时反向执行

    */
    /* animation-direction: alternate-reverse; */

    /* 
        animation-play-state: 设置动画的执行状态 
        可选值：
            running 默认值 动画执行
            paused 动画暂停
    */
    /* animation-play-state: paused; */

    /* 
    animation-fill-mode: 动画的填充模式
        可选值：
            none 默认值 动画执行完毕元素回到原来位置
            forwards 动画执行完毕元素会停止在动画结束的位置
            backwards 动画延时等待时，元素就会处于开始位置
            both 结合了forwards 和 backwards
    */
    /* animation-fill-mode: both; */
    animation: test 2s 2 1s alternate;

    
}
```

## 变形效果

```
变形就是指通过CSS来改变元素的形状或位置
-   变形不会影响到页面的布局
- transform 用来设置元素的变形效果
```

### 平移

```
- 平移：
    translateX() 沿着x轴方向平移
    translateY() 沿着y轴方向平移
    translateZ() 沿着z轴方向平移
        - 平移元素，百分比是相对于自身计算的
        transform: translateX(100%);
        z轴平移，调整元素在z轴的位置，正常情况就是调整元素和人眼之间的距离，
                    距离越大，元素离人越近
                z轴平移属于立体效果（近大远小），默认情况下网页是不支持透视，如果需要看见效果
                    必须要设置网页的视距
```

### 利用变形的居中效果

```
left: 50%;
top: 50%;
transform: translateX(-50%) translateY(-50%);
```

### 倾斜

```
transform: skew(-11deg);
```



### 旋转

```css
/*
    通过旋转可以使元素沿着x y 或 z旋转指定的角度
        rotateX()
        rotateY()
        rotateZ()
*/
/* transform: rotateZ(.25turn); */
/* transform: rotateY(180deg) translateZ(400px); */
/* transform: translateZ(400px) rotateY(180deg) ; */
```

### 缩放

```css
.box1:hover{
    /* 
        对元素进行缩放的函数：
            scaleX() 水平方向缩放
            scaleY() 垂直方向缩放
            scale() 双方向的缩放
    */
    transform:scale(2)
}
```

## less

```css
<!-- 
    less是一门css的预处理语言
        - less是一个css的增强版，通过less可以编写更少的代码实现更强大的样式
        - 在less中添加了许多的新特性：像对变量的支持、对mixin的支持... ...
        - less的语法大体上和css语法一致，但是less中增添了许多对css的扩展，
            所以浏览器无法直接执行less代码，要执行必须向将less转换为css，然后再由浏览器执行

    div{
        width:100px;
    }

    div
        width 100px
 -->
 
less就是一种css的语法形式，更加简单，需要时可以进行查看
```

## flex弹性盒

https://blog.csdn.net/Liveinmylife/article/details/51838939

https://blog.csdn.net/u010358168/article/details/107151859

```
<!-- 
    flex(弹性盒、伸缩盒)
        - 是CSS中的又一种布局手段，它主要用来代替浮动来完成页面的布局
        - flex可以使元素具有弹性，让元素可以跟随页面的大小的改变而改变
        - 弹性容器
            - 要使用弹性盒，必须先将一个元素设置为弹性容器
            - 我们通过 display 来设置弹性容器
                display:flex  设置为块级弹性容器
                display:inline-flex 设置为行内的弹性容器

        - 弹性元素
            - 弹性容器的子元素是弹性元素（弹性项）
            - 弹性元素可以同时是弹性容器

 -->
```

### 容器的属性

#### flex-direction

```
/* 
   flex-direction 指定容器中弹性元素的排列方式
    可选值：
        row 默认值，弹性元素在容器中水平排列（左向右）
            - 主轴 自左向右
        row-reverse 弹性元素在容器中反向水平排列（右向左）
            - 主轴 自右向左
        column 弹性元素纵向排列（自上向下）
        column-reverse 弹性元素方向纵向排列（自下向上）

    主轴：
        弹性元素的排列方向称为主轴
    侧轴：
        与主轴垂直方向的称为侧轴

*/
```

#### justify-content

```
/*  
    justify-content
        - 如何分配主轴上的空白空间（主轴上的元素如何排列）
        - 可选值：
            flex-start 元素沿着主轴起边排列
            flex-end 元素沿着主轴终边排列
            center 元素居中排列
            space-around: flex items之间的距离相等，flex items与main start、main end之间的距离是flex items之间距离			                的一半
            space-between: flex items之间距离相等，与main start、main end两端对齐
            space-evenly: flex items之间距离相等，与main start、main end之间的距离等于flex items之间的距离

*/
/* justify-content: center; */
```

#### flex-wrap

```
/* flex-wrap: 
    设置弹性元素是否在弹性容器中自动换行
    可选值：
        nowrap 默认值，元素不会自动换行
        wrap：换行，第一行在上方。
        wrap-reverse：换行，第一行在下方。
*/
```

#### flex-flow

flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

#### align-items

```
/*
 align-items: 
    - 元素在交叉轴（纵轴）上如何对齐
    - 元素间的关系
        - 可选值：
            stretch 默认值，当flex items在cross axis方向的size为auto时，会自动拉伸至填充flex container，当未设置                         item1 item2 item3高度时，会自动拉伸填满：
            flex-start 元素不会拉伸，沿着辅轴起边对齐
            flex-end 沿着辅轴的终边对齐
            center 居中对齐
            baseline 与第一行文字的基准线对齐

 */
```

#### align-content属性

align-content属性定义了多根轴线（多行）的对齐方式。如果项目只有一根轴线（一行），该属性不起作用。
如果flex-direction的值是column，则该属性定义了多列的对齐方式。如果项目只有一列，该属性不起左右。

```
/*  
    justify-content
        - 如何分配交叉轴上的空白空间（主轴上的元素如何排列）
        - 可选值：
            flex-start 元素沿着交叉轴起边排列
            flex-end 元素沿着交叉轴终边排列
            center 元素居中排列
            space-around: flex items之间的距离相等，flex items与start、 end之间的距离是flex items之间距离			                      的一半
            space-between: flex items之间距离相等，与start、 end两端对齐
            space-evenly: flex items之间距离相等，与 start、 end之间的距离等于flex items之间的距离

*/
/* justify-content: center; */
```

### 弹性元素的属性

#### order

order决定了flex items的排布顺序。

- 可以设置任意整数(正整数 负整数 0) ，值越小，就越排在前面
- 默认值为0

####  flex-grow

lex-grow属性定义项目的放大比例，默认为0。

flex-grow属性定义项目的放大比例

- 默认为0，可以设置任意非负数字

- 当flex container在main axis方向上有剩余size时，flex-grow属性才会有效
- 如果所有flex items的flex-grow总和sum超过1，每个flex item扩展的size=flex container的剩余size * flex-grow / sum
- 如果所有flex items的flex-grow总和sum小于1，每个flex items扩展的size=flex container的剩余size * flex-grow
- flex items扩展后的最终size不能超过max-width\max-height

如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

#### flex-shrink

flex-shrink决定了flex-items如何收缩。

- 默认值为1，可以设置任意非负数字，负值对该属性无效

- 当flex items在main axis方向上超过了flex container的size，flex-shrink属性才会有效

- 如果所有flex items的flex-shrink总和sum超过1，每个flex items收缩的size= flex items超出flex container的size * 收缩比例 /所有flex item的收缩比例之和

- 如果所有flex items的flex-shrink总和sum不超过1，每个flex item收缩的size= flex item的size - flex items超出flex container的size * flex-shrink

- flex items收缩后的最终size不能小于min-width/min-height

- 如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

#### flex-basis

flex-basis用来设置flex items在main asis方向上的空间大小

- auto: 默认值，即项目本身大小

- 具体宽度数值: 100px , 200px

决定flex items最终base size的因素，优先级从高到低为：

- max-width\max-height\min-width\min-height
- flex-basis
- width/height
- 内容本身的size

#### align-self

ex items可以通过align-self覆盖flex container中设置的align-items。

默认值为auto，遵从flex container的align-items设置
stretch、flex-start、flex-end、center、baseline效果与align-items一致

#### flex

flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。

# 使用方式

## 自适应图片

 根据图片实际宽高和需要显示的容器宽高等比例拉伸或缩小
   1.父容器如div设置固定width和height，设置 overflow: hidden，设置相对定位;
   2. img设置绝对定位，设置最大宽度max-width:100%，left、top、right、bottom值为0，
   3. 设置margin:auto。这样可以解决不同尺寸的图片在同一个盒子里垂直水平居中（多个图片情况下）
      4. 看起来又不会显得图片变形

实例

```css
.image-box {
    width: 300px;/*如果不限制宽度的话建议不设置宽度，自动适应更好*/
    height: 300px; 
    overflow:hidden;
    position:  relative /* 绝对定位需要相对于最近的开启定位的包含块  */
    
}
.image {
    position: absolute;
    max-height: 100%;
    max-width: 100%;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
}
```

## 单行文本溢出

```css
    max-width: 400px; /*设置最大宽度 */
    white-space: nowrap;  /* 不允许换行  */
    overflow: hidden;  /* 溢出隐藏 */
    text-overflow: ellipsis;   /*文本溢出省略号代替 */ 
```

## 多行文本溢出

```css
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical; /*设置其对其模式*/
  -webkit-line-clamp: 2;  /* 设置行数*/
```

