# 概述

![[image 1.png]]

## 特点

JUnit 是一个开放的资源框架，用于编写和运行测试。

- 提供注解来识别测试方法。

- 提供断言来测试预期结果。

- JUnit 测试允许你编写代码更快，并能提高质量。

- JUnit 优雅简洁。没那么复杂，花费时间较少。

- JUnit测试可以自动运行并且检查自身结果并提供即时反馈。所以也没有必要人工梳理测试结果的报告。

- JUnit测试可以被组织为测试套件，包含测试用例，甚至其他的测试套件。

- JUnit在一个条中显示进度。如果运行良好则是绿色；如果运行失败，则变成红色。

---



# Junit4



## 介绍

> 最好的资料依然在Junit官方网站，以下我帮你总结下Junit相关的官方网址。

- 官网地址

https://junit.org/junit4/

- 官方入门文档

https://github.com/junit-team/junit4/wiki/Assertions

- 官方github

https://github.com/junit-team

---



## 引入

```XML
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>
```



## 常用注解

- **@Test**

在junit3中，是通过对测试类和测试方法的命名来确定是否是测试，且所有的测试类必须继承junit的测试基类。在junit4中，定义一个测试方法变得简单很多，只需要在方法前加上@Test就行了。

注意：测试方法必须是public void，即公共、无返回数据。可以抛出异常。

- **@Ignore**

有时候我们想暂时不运行某些测试方法\测试类，可以在方法前加上这个注解。在运行结果中，junit会统计忽略的用例数，来提醒你。但是不建议经常这么做，因为这样的坏处时，容易忘记去更新这些测试方法，导致代码不够干净，用例遗漏。使用此标注的时候不能与其它标注一起使用，如：**和@Test 标注一起使用，那就没用了**

- **@BeforeClass**

当我们运行几个有关联的用例时，可能会在数据准备或其它前期准备中执行一些相同的命令，这个时候为了让代码更清晰，更少冗余，可以将公用的部分提取出来，放在一个方法里，并为这个方法注解@BeforeClass。意思是在测试类里所有用例运行之前，运行一次这个方法。例如创建数据库连接、读取文件等。

注意：方法名可以任意，但必须是public static void，即公开、静态、无返回。这个方法只会运行一次。

- **@AfterClass**

跟@BeforeClass对应，在测试类里所有用例运行之后，运行一次。用于处理一些测试后续工作，例如清理数据，恢复现场。

注意：同样必须是public static void，即公开、静态、无返回。这个方法只会运行一次。

- **@Before**

与@BeforeClass的区别在于，@Before不止运行一次，它会在每个用例运行之前都运行一次。主要用于一些独立于用例之间的准备工作。

比如两个用例都需要读取数据库里的用户A信息，但第一个用例会删除这个用户A，而第二个用例需要修改用户A。那么可以用@BeforeClass创建数据库连接。用@Before来插入一条用户A信息。

注意：必须是public void，不能为static。不止运行一次，根据用例数而定。

- **@After**：与@Before对应。

- **@Runwith**

  - 首先要分清几个概念：测试方法、测试类、测试集、测试运行器。

  - 其中测试方法就是用@Test注解的一些函数。

  - 测试类是包含一个或多个测试方法的一个Test.java文件。

  - 测试集是一个suite，可能包含多个测试类。

  - 测试运行器则决定了用什么方式偏好去运行这些测试集/类/方法。

  - 而@Runwith就是放在测试类名之前，用来确定这个类怎么运行的。也可以不标注，会使用默认运行器。常见的运行器有：

    - @RunWith(Parameterized.class) 参数化运行器，配合@Parameters使用junit的参数化功能

    - @RunWith(Suite.class) @SuiteClasses({ATest.class,BTest.class,CTest.class})测试集运行器配合使用测试集功能

    - @RunWith(JUnit4.class) junit4的默认运行器

    - @RunWith(JUnit38ClassRunner.class) 用于兼容junit3.8的运行器

    - 一些其它运行器具备更多功能。例如@RunWith(SpringJUnit4ClassRunner.class)集成了spring的一些功能

- **@Parameters**： 用于使用参数化功能。

---



## 编写单元测试

### Hello World

```Java
public class HelloWorldTest {

    @Test
    public void firstTest() {
        assertEquals(2, 1 + 1);
    }
}
```



> @Test注解在方法上标记方法为测试方法，以便构建工具和 IDE 能够识别并执行它们。JUnit 4 需要测试方法为public，这和Junit 5 有差别



### 断言测试

- **断言测试注解有哪些**

|断言|描述|
|-|-|
|void assertEquals([String message],expected value,actual value)|断言两个值相等。值类型可能是int，short，long，byte，char，Object，第一个参数是一个可选字符串消息|
|void assertTrue([String message],boolean condition)|断言一个条件为真|
|void assertFalse([String message],boolean condition)|断言一个条件为假|
|void assertNotNull([String message],java.lang.Object object)|断言一个对象不为空（null）|
|void assertNull([String message],java.lang.Object object)|断言一个对象为空（null）|
|void assertSame([String message],java.lang.Object expected,java.lang.Object actual)|断言两个对象引用相同的对象|
|void assertNotSame([String message],java.lang.Object unexpected,java.lang.Object actual)|断言两个对象不是引用同一个对象|
|void assertArrayEquals([String message],expectedArray,resultArray)|断言预期数组和结果数组相等，数组类型可能是int，short，long，byte，char，Object|

---

```Java
public class AssertionTest {

    @Test
    public void test() {
        String obj1 = "junit";
        String obj2 = "junit";
        String obj3 = "test";
        String obj4 = "test";
        String obj5 = null;

        int var1 = 1;
        int var2 = 2;

        int[] array1 = {1, 2, 3};
        int[] array2 = {1, 2, 3};

        Assert.assertEquals(obj1, obj2);

        Assert.assertSame(obj3, obj4);
        Assert.assertNotSame(obj2, obj4);

        Assert.assertNotNull(obj1);
        Assert.assertNull(obj5);

        Assert.assertTrue(var1 < var2);
        Assert.assertFalse(var1 > var2);

        Assert.assertArrayEquals(array1, array2);

    }
}
```

在以上类中我们可以看到，这些断言方法是可以工作的。

- assertEquals() 如果比较的两个对象是相等的，此方法将正常返回；否则失败显示在JUnit的窗口测试将中止。

- assertSame() 和 assertNotSame() 方法测试两个对象引用指向完全相同的对象。

- assertNull() 和 assertNotNull() 方法测试一个变量是否为空或不为空(null)。

- assertTrue() 和 assertFalse() 方法测试if条件或变量是true还是false。

- assertArrayEquals() 将比较两个数组，如果它们相等，则该方法将继续进行不会发出错误。否则失败将显示在JUnit窗口和中止测试。

---



### 异常测试

```Java
public class ExceptionTest {

    @Test(expected = ArithmeticException.class)
    public void exceptionTest() {
        System.out.println("in exception success test");
        int a = 0;
        int b = 1 / a;
    }

    @Test(expected = NullPointerException.class)
    public void exceptionFailTest() {
        System.out.println("in exception fail test");
        int a = 0;
        int b = 1 / a;
    }
}
```



```Plain Text
in exception success test
in exception fail test

java.lang.Exception: Unexpected exception, expected<java.lang.NullPointerException> but was<java.lang.ArithmeticException>...
```



> 输出结果如上，可以看到第一个异常和测试一样，success，第二个异常不一样，就fail并报错



### 时间测试

JUnit提供了一个暂停的方便选项，如果一个测试用例比起指定的毫秒数花费了更多的时间，那么JUnit将自动将它标记为失败，timeout参数和@Test注解一起使用，例如@Test(timeout=1000)。

---

```Java
public class TimeoutTest {

    @Test(timeout = 1000)
    public void testCase1() throws InterruptedException {
        TimeUnit.SECONDS.sleep(5000);
        System.out.println("in timeout exception");
    }
}
```



超时就会报错



### 测试顺序

自定义测试方法的顺序，比如按照方法的名字顺序：

```Java
@FixMethodOrder(MethodSorters.NAME_ASCENDING)
public class TestMethodOrder {

    @Test
    public void testA() {
        System.out.println("first");
    }

    @Test
    public void testC() {
        System.out.println("third");
    }

    @Test
    public void testB() {
        System.out.println("second");
    }
}
```



# Junit5

## 介绍

> 最好的资料依然在Junit官方网站，以下我帮你总结下Junit相关的官方网址

- 官网地址

https://junit.org/junit5/

- 官方入门文档

https://junit.org/junit5/docs/current/user-guide/#overview

- 官方例子

https://github.com/junit-team/junit5-samples

- 官方github

https://github.com/junit-team

---

著作权归@pdai所有 原文链接：https://pdai.tech/md/develop/ut/dev-ut-x-junit5.html



## 架构

与以前版本的JUnit不同，JUnit 5由三个不同子项目中的几个不同模块组成。

> JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

- **JUnit Platform**是基于JVM的运行测试的基础框架在，它定义了开发运行在这个测试框架上的TestEngine API。此外该平台提供了一个控制台启动器，可以从命令行启动平台，可以为Gradle和 Maven构建插件，同时提供基于JUnit 4的Runner。

- **JUnit Jupiter**是在JUnit 5中编写测试和扩展的新编程模型和扩展模型的组合.Jupiter子项目提供了一个TestEngine在平台上运行基于Jupiter的测试。

- **JUnit Vintage**提供了一个TestEngine在平台上运行基于JUnit 3和JUnit 4的测试。

---



## 引入

```XML
    <dependencies>
        <!-- Only needed to run tests in a version of IntelliJ IDEA that bundles older versions -->
        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <version>1.7.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.7.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
            <version>5.7.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.7.0</version>
        </dependency>

        <!-- lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
    </dependencies>
```



springboot的test启动器包含Junit，具体是哪个版本需要看spingboot版本

```XML
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

## 常用注解

**@Test** 表示方法是一种测试方法。 与JUnit 4的@Test注解不同，此注释不会声明任何属性。

**@ParameterizedTest** 表示方法是参数化测试

**@RepeatedTest** 表示方法是重复测试模板

**@TestFactory** 表示方法是动态测试的测试工程

**@DisplayName** 为测试类或者测试方法自定义一个名称

**@BeforeEach** 表示方法在每个测试方法运行前都会运行 ，**@AfterEach** 表示方法在每个测试方法运行之后都会运行

**@BeforeAll** 表示方法在所有测试方法之前运行 ，**@AfterAll** 表示方法在所有测试方法之后运行

**@Nested** 表示带注解的类是嵌套的非静态测试类，**@BeforeAll**和 **@AfterAll**方法不能直接在@Nested测试类中使用，除非修改测试实例生命周期。

**@Tag** 用于在类或方法级别声明用于过滤测试的标记

**@Disabled** 用于禁用测试类或测试方法

**@ExtendWith** 用于注册自定义扩展，该注解可以继承

**@FixMethodOrder(MethodSorters.NAME_ASCENDING)**，控制测试类中方法执行的顺序，这种测试方式将按方法名称的进行排序，由于是按字符的字典顺序，所以以这种方式指定执行顺序会始终保持一致；不过这种方式需要对测试方法有一定的命名规则，如 测试方法均以testNNN开头（NNN表示测试方法序列号 001-999）

---



## 编写单元测试

### Hello World

```Java
public class HelloWorldTest {

    @Test
    void firstTest() {
        assertEquals(2, 1 + 1);
    }
}
```



> @Test注解在方法上标记方法为测试方法，以便构建工具和 IDE 能够识别并执行它们。JUnit 5不再需要手动将测试类与测试方法为public，包可见的访问级别就足够了



### 断言测试

> 准备好测试实例、执行了被测类的方法以后，断言能确保你得到了想要的结果。一般的断言，无非是检查一个实例的属性（比如，判空与判非空等），或者对两个实例进行比较（比如，检查两个实例对象是否相等）等。无论哪种检查，断言方法都可以接受一个字符串作为最后一个可选参数，它会在断言失败时提供必要的描述信息。如果提供出错信息的过程比较复杂，它也可以被包装在一个 lambda 表达式中，这样，只有到真正失败的时候，消息才会真正被构造出来。

- **常用断言 Assertions**

  - `assertEquals` 断言预期值和实际值相等

  - `assertAll` 分组断言,执行其中包含的所有断言

  - `assertArrayEquals` 断言预期数组和实际数组相等

  - `assertFalse` 断言条件为假

  - `assertNotNull` 断言不为空

  - `assertSame` 断言两个对象相等

  - `assertTimeout` 断言超时

  - `fail` 使单元测试失败

---



断言示例:

```Java
    @Test
    void exceptionTesting() {
        Throwable exception = assertThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    }

    @Test
    void timeoutNotExceeded() {
        // The following assertion succeeds.
        assertTimeout(ofMinutes(2), () -> {
            // Perform task that takes less than 2 minutes.
        });
    }

    @Test
    void timeoutNotExceededWithResult() {
        // The following assertion succeeds, and returns the supplied object.
        String actualResult = assertTimeout(ofMinutes(2), () -> {
            return "a result";
        });
        assertEquals("a result", actualResult);
    }
    @Test
    void timeoutExceededWithPreemptiveTermination() {
        // The following assertion fails with an error message similar to:
        // execution timed out after 10 ms
        assertTimeoutPreemptively(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            Thread.sleep(100);
        });
    }

```



这里注意下:`assertTimeoutPreemptively()` 和 `assertTimeout()` 的区别为: 两者都是断言超时，前者在指定时间没有完成任务就会立即返回断言失败；后者会在任务执行完毕之后才返回。

---



### 异常测试

我们代码中对于带有异常的方法通常都是使用 try-catch 方式捕获处理，针对测试这样带有异常抛出的代码，而 JUnit 5 提供方法 `Assertions#assertThrows(Class<T>, Executable)` 来进行测试，第一个参数为异常类型，第二个为函数式接口参数，跟 Runnable 接口相似，不需要参数，也没有返回，并且支持 Lambda表达式方式使用，具体使用方式可参考下方代码:

```Java
package tech.pdai.junit5;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertThrows;

/**
 * Exception Test.
 */
public class ExceptionTest {

    // 标准的测试例子
    @Test
    @DisplayName("Exception Test Demo")
    void assertThrowsException() {
        String str = null;
        assertThrows(IllegalArgumentException.class, () -> {
            Integer.valueOf(str);
        });
    }

    // 注:异常失败例子，当Lambda表达式中代码出现的异常会跟首个参数的异常类型进行比较，如果不属于同一类异常，则失败
    @Test
    @DisplayName("Exception Test Demo2")
    void assertThrowsException2() {
        String str = null;
        assertThrows(NullPointerException.class, () -> {
            Integer.valueOf(str);
        });
    }
}
```

---





其他测试方法用以本人能力暂时用不到，用到再学！



