# 1.概述

## 1.1、简介

Jsoup 是一款java的HTML解析器，可直接机械某个URL地址、HTML文本内容。他提供了一套非常省力的API。可以通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。

## 1.2、功能

1）从一个URL，文件或字符串中解析HTML

2）使用DOM或CSS选择器来查找、取出数据

3）可操作HTML元素、属性、文本

注意：jsoup是基于MIT协议发布的，可放心使用于商业项目。

## 1.3、导入



```Plain Text
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.15.2</version>
        </dependency>
```



# 2.输入功能

## 2.1、解析一个HTML字符串

对于一个网站html的字符串，想要对其解析

使用 **Jsoup.parse(html)**

```Java
String html = "<html><head><title>First parse</title></head>"
  + "<body><p>Parsed HTML into a doc.</p></body></html>";
Document doc = Jsoup.parse(html);
```



只要不是空字符串，就会返回一个Document,我们可以对Document这个类进行后续操作。



## 2.2、解析一个Body片段

对于一个HTML片段，比如,一个div包含一对p标签,一个不完整的HTML文档,想要对它进行解析

使用 **Jsoup.parseBodyFragment(String html)**

```Java
String html = "<div><p>Lorem ipsum.</p>";
Document doc = Jsoup.parseBodyFragment(html);
Element body = doc.body();
```



确实，通常这里使用 Jsoup.parse也可以得到相同的结果。

但明确将用户输入作为body片段，可以确保用户提供的任何糟糕的HTML都被解析成body元素。

Document.body() 方法能够取得文档body元素的所有子元素，与doc.getElementsByTag("body")相同。

## 2.3、从一个url加载一个Document

需要从一个网站获取和解析一个HTML文档，并查找其中的相关数据

使用**Jsoup.connect(String url)**

```Java
Document doc = Jsoup.connect("http://example.com/").get();
String title = doc.title();
```



这个方法只支持Web URLs (http和https 协议); 假如你需要从一个文件加载，可以使用 parse(File in, String charsetName) 代替。

## 2.4、从一个文件加载文档

本机硬盘上有一个HTML文件，需要对其进行数据抽取和修改

可以使用静态 **Jsoup.parse(File in, String charsetName, String baseUri)** 方法：

```Java
File input = new File("/tmp/input.html");
Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/";);
```


            parse(File in, String charsetName, String baseUri) 这个方法用来加载和解析一个HTML文件。如在加载文件的时候发生错误，将抛出IOException，应作适当处理。
            baseUri 参数用于解决文件中URLs是相对路径的问题。如果不需要可以传入一个空的字符串。 
            另外还有一个方法parse(File in, String charsetName) ，它使用文件的路径做为 baseUri。 这个方法适用于如果被解析文件位于网站的本地文件系统，
　　　　且相关链接也指向该文件系统。

   
            parse(File in, String charsetName, String baseUri) 这个方法用来加载和解析一个HTML文件。如在加载文件的时候发生错误，将抛出IOException，应作适当处理。
            baseUri 参数用于解决文件中URLs是相对路径的问题。如果不需要可以传入一个空的字符串。 
            另外还有一个方法parse(File in, String charsetName) ，它使用文件的路径做为 baseUri。 这个方法适用于如果被解析文件位于网站的本地文件系统，且相关链接也指向该文件系统。



# 3、数据抽取

html经过输入解析后就会作为Document类文件，我们可以对这个类进行操作。

## 3.1、使用DOM方法来遍历一个文档

Element提供了一系列类似于DOM的方法来查找元素并处理数据

A：查看元素

```Java
　　　　getElementById(String id)
　　　　getElementsByTag(String tag)
　　　　getElementsByClass(String className)
　　　　getElementsByAttribute(String key) (and related methods)
　　　　Element siblings: siblingElements(), firstElementSibling(), lastElementSibling(); nextElementSibling(), previousElementSibling()
　　　　Graph: parent(), children(), child(int index)
```

B：元素数据

```Java
　　　　attr(String key)获取属性attr(String key, String value)设置属性
　　　　attributes()获取所有属性
　　　　id(), className() and classNames()
　　　　text()获取文本内容text(String value) 设置文本内容
　　　　html()获取元素内HTMLhtml(String value)设置元素内的HTML内容
　　　　outerHtml()获取元素外HTML内容
　　　　data()获取数据内容（例如：script和style标签)
　　　　tag() and tagName()
```

C：操作HTML和文本

```Java
　　　　append(String html), prepend(String html)
　　　　appendText(String text), prependText(String text)
　　　　appendElement(String tagName), prependElement(String tagName)
　　　　html(String value)
```

## 3.2、使用选择器语法来查找元素

你想使用类似于CSS或[jQuery](https://so.csdn.net/so/search?q=jQuery&spm=1001.2101.3001.7020)的语法来查找和操作元素。

可以使用Element.select(String selector) 和 Elements.select(String selector) 方法实现：

```Java
Elements links = doc.select("a[href]"); //带有href属性的a元素
Elements pngs = doc.select("img[src$=.png]");//扩展名为.png的图片
Element masthead = doc.select("div.masthead").first();//class等于masthead的div标签
Elements resultLinks = doc.select("h3.r > a"); //在h3元素之后的a元素
```



select中写css选择器

## 3.3、从元素中抽取属性，文本和HTML

在解析获得一个Document实例对象，并查找到一些元素之后，你希望取得在这些元素中的数据

要取得一个属性的值，可以使用Node.attr(String key) 方法
对于一个元素中的文本，可以使用Element.text()方法
对于要取得元素或属性中的HTML内容，可以使用Element.html(), 或 Node.outerHtml()方法

```Java
　　String html = "<p>An <a href='http://example.com/'><b>example</b></a> link.</p>";
　　Document doc = Jsoup.parse(html);//解析HTML字符串返回一个Document实现
　　Element link = doc.select("a").first();//查找第一个a元素

　　String text = doc.body().text(); // "An example link"//取得字符串中的文本
　　String linkHref = link.attr("href"); // "http://example.com/"//取得链接地址
　　String linkText = link.text(); // "example""//取得链接地址中的文本

　　String linkOuterH = link.outerHtml(); 
 　　   // "<a href="http://example.com"><b>example</b></a>"
　　String linkInnerH = link.html(); // "<b>example</b>"//取得链接内的html内容
```



## 3.4、处理URLs

你有一个包含相对URLs路径的HTML文档，需要将这些相对路径转换成绝对路径的URLs。



在你解析文档时确保有指定base URI，然后
使用 abs: 属性前缀来取得包含base URI的绝对路径。代码如下：

```Java
Document doc = Jsoup.connect("http://www.open-open.com").get();

　　Element link = doc.select("a").first();
　　String relHref = link.attr("href"); // == "/"
　　String absHref = link.attr("abs:href"); // "http://www.open-open.com/"
```



在HTML元素中，URLs经常写成相对于文档位置的相对路径： <a href="/download">...</a>.

当你使用 Node.attr(String key) 方法来取得a元素的href属性时，它将直接返回在HTML源码中指定定的值。

假如你需要取得一个绝对路径，需要在属性名前加 abs: 前缀。这样就可以返回包含根路径的URL地址attr("abs:href")

**因此，在解析HTML文档时，定义base URI非常重要。**

如果你不想使用abs: 前缀，还有一个方法能够实现同样的功能 Node.absUrl(String key)。



# 4、数据修改

## 4.1、设置属性值

在你解析一个Document之后可能想修改其中的某些属性值，然后再保存到磁盘或都输出到前台页面。

可以使用属性设置方法 Element.attr(String key, String value), 和 Elements.attr(String key, String value).

假如你需要修改一个元素的 class 属性，可以使用 Element.addClass(String className) 和 Element.removeClass(String className) 方法。



Elements 提供了批量操作元素属性和class的方法，比如：要为div中的每一个a元素都添加一个 rel="nofollow" 可以使用如下方法：

```Plain Text
　doc.select("div.comments a").attr("rel", "nofollow");
```



与Element中的其它方法一样，attr 方法也是返回当 Element (或在使用选择器是返回 Elements 集合)。

　　　　这样能够很方便使用方法连用的书写方式。比如：

　　　　doc.select("div.masthead").attr("title", "jsoup").addClass("round-box");



## 5.2、设置一个元素的HTML内容



```Java
Element div = doc.select("div").first(); // <div></div>
div.html("<p>lorem ipsum</p>"); // <div><p>lorem ipsum</p></div>
div.prepend("<p>First</p>");//在div前添加html内容
div.append("<p>Last</p>");//在div之后添加html内容
// 添完后的结果: <div><p>First</p><p>lorem ipsum</p><p>Last</p></div>

Element span = doc.select("span").first(); // <span>One</span>
span.wrap("<li><a href='http://example.com/'></a></li>");
// 添完后的结果: <li><a href="http://example.com"><span>One</span></a></li>
```



Element.html(String html) 这个方法将先清除元素中的HTML内容，然后用传入的HTML代替。
Element.prepend(String first) 和 Element.append(String last) 方法用于在分别在元素内部HTML的前面和后面添加HTML内容
Element.wrap(String around) 对元素包裹一个外部HTML内容。



