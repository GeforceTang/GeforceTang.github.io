---
title: Java入门学习-JAVA基础
date: 2019-03-06 19:30:38
categories: JAVA学习
tags: JAVA
---

## 前言
有C#的内力在，对JAVA上手还是比较轻松的，前期准备把JAVA基础和语法大致做下了解，后面重心放在Java Web、JVM等一些重要模块上。

## JAVA环境准备
JAVA和C#不同，C#的运行环境是.NET FRAMEWORK，Windows已经自带了，而JAVA必须安装JAVA的运行环境，也就是JRE，当然做开发的话肯定是安装JDK。

先理解下术语：
1. JDK（Java Development Kit）：Java开发工具
2. JRE（Java Runtime Environment）：Java运行时环境
3. SE（Standard Endition）：桌面或简单服务器应用平台
4. EE（Enterprise Endition）：复杂服务器应用平台
5. ME（Micro Endition）：小型设备应用平台

<!--more-->

### 安装配置JDK
首先需要下载JDK，放到本地文件夹中，配置下环境变量：
>配置环境变量：右键计算机、属性、高级系统配置、环境变量

| 属性      | 值                                                                            |
|:--------- |:----------------------------------------------------------------------------- |
| JAVA_HOME | E:\tools\jdk\jdk1.8.0_101(<font color='red'>填上实际的路径</font>)            |
| Path      | ;%JAVA_HOME%\bin(<font color='red'>不能把原来的配置删除，只能后面添加</font>) |
| classpath | .;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar                            |

配置完后在cmd中：执行`java –version`可以使用则安装成功

### 配置Maven
Maven是个仓库管理系统，用于管理Jar包的，通过Maven可以大幅降低开发环境的部署工作量。
Maven和JDK的配置类似，下载Maven后放到本地文件夹中，并配置环境变量：
>右击计算机 —> 属性 —> 高级系统设置 —> 环境变量。

| 属性      | 值                                                                            |
|:--------- |:----------------------------------------------------------------------------- |
| MAVEN_HOME | E:\tools\mvn\apache-maven-3.2.5(<font color='red'>填上实际的路径</font>)            |
| Path      | ;%M2_HOME%\bin;(<font color='red'>不能把原来的配置删除，只能后面添加</font>) |

### 安装IDE
由于刚入门，目前了解到的Java IDE有Eclipse、MyEclipse、IntelliJ IDEA。
说实话，用惯了Visual Studio现在让我用这些IDE，感觉真的搓。就简单接触下来IntelliJ IDEA是其中最好的一个。
我现在用的IDE是eclipseNeon4.6，和VS不同的是他的工作空间（WorkSpace）会关联各种配置，也就是说新的一个工作空间，字体之类的必须重写配置一遍。必须要配置的几个东西：
1. 配置工作空间的编码方式：General —> Workspace：改成`Other：UTF-8`
2. 配置property的编码方式：General —> Content Types：将Java Properties File的Default encoding改为`UTF-8`
3. 配置Maven路径：Window —> Preference —> Maven —> Installation —> Add（找到Maven文件夹），并勾选后点击Apply
4. 配置Maven配置文件：Window —> Preference —> Maven —> User Settings —>（指向maven—>conf—>settings.xml）
5. 配置JDK：Java —> Installed JREs（在此处进行新增Jre）
另外可以考虑将Validation全部取消掉，可以加载编译速度提升开发效率。

## Java基础

### 跑个Hello World
学习一个程序，首先就来写一个Hello World.
创建一个HelloWord.java文件，里面输入下述代码
```java
public class HelloWord{
  public static void main(String[] args){
    System.out.println("Hello World");
  }
}
```
通过命令行工具执行
```bash
javac HelloWord.java
java HelloWrod
```

可以发现：C#的输出语句是`Console.WriteLine`，而Java的是`System.out.println`，另外C#的`string`是小写的而JAVA是大写的。

### 基本语法

#### 注释
和C#一样，可以用`//`，也可以用`/**/`。

#### 数据类型

##### 常见数据类型

| 字段定义   | 类型                  | 描述                                       |
|:---------- |:--------------------- |:------------------------------------------ |
| int        | 正整形（4字节）       | -2147483648 ~ 2147483647                   |
| short      | 短整形（2字节）       | -32768 ~ 32767                             |
| long       | 长整形（8字节）       | -9223372036854775808 ~ 9223372036854775807 |
| byte       | 字节（8位bit）        | -128 ~ 127                                 |
| float      | 单精度浮点型（4字节） | -3.40E+38 ~ +3.40E+38     注意NaN非数字    |
| double     | 双精度浮点型（8字节） | -1.79E+308 ~ +1.79E+308   注意NaN非数字    |
| char       | 字符类型（2字节）     | 通过Unicode编码进行标识                    |
| boolean    | 布尔                  | true or false                              |
| BigInteger | 大数据整形            | 可以表示任意整数                           |
| BidDecimal | 大数据浮点型          | 可以表示任意浮点数                         |

##### 枚举类型
```java
// 定义枚举类型和C#一致
enum Size {SMALL,MEDIUM,LANGER};
Size s = Size.SMALL;
```

##### 字符串类型
```java
// 注意是String不是string，这个语法和C#差不多
String s = "abc";

// 由于String的不可变性，效率上会比较差，所以有了StringBulider类
StringBulider sb = new StringBulider();
sb.append("abc");
```

##### 数组类型
```java
// 也是一致的，不过C#可以定义为int[] lst = new int[]{1,2,3};，不知道JAVA是否可以。
int[] ilst = new int[10];
```

##### 大数据类型
```java
// 大数据类型没法直接使用+ - * /，只能通过对应的方法来执行
BigInteger a = BigInteger.valueOf(100);
BigInteger c = a.add(b);  // c=a+b
BigInteger d = c.multiply(b.add(BigInteger.valueOf(2)));  // d=c*(b+2)
```

大数据类型常用方法：

| 方法名                                     | 用途                                        |
|:------------------------------------------ |:------------------------------------------- |
| add(BigInteger other)                      | +                                           |
| subtract(BigInteger other)                 | -                                           |
| multiply(BigInteger other)                 | *                                           |
| divide(BigInteger other)                   | /                                           |
| mode(BigInteger other)                     | %                                           |
| compareTo(BigInteger other)                | 对比，相等返回0，小于返回负数，大于返回正数 |
| valueOf(long x)                            | 获取大数据值                                |
| divide(BigDecimal other,RoundingMode mode) | BigDecimal的方法，支持进行四舍五入          |


#### 运算符
Java的运算符一样的存在`+ - * / ++ -- | & !`，包含自增运算符、关系运算符、三元运算符、位运算符、括号运算符等。

##### 自增运算符
```java
//自增运算符 ++ --的用法和C#一致
int a = 1;
a++;
a--;
++a;
--a;
```

##### 关系运算符
```java
//关系运算符 && || !=与或非也是一样的
if(a != 2 || a<2 && a>0){
  a*=a/a+a-a;
}
```

##### 三元运算符
```java
//三元运算符?:
condition?expression1:expression2;
a==b?a++:a--;
```

##### 括号运算符
```java
//()
if((a != 2 || a!=3) && a>1){
  // DO SOMETHING.
}
```

##### 位运算符
```java
// &"与" |"或" ~"非" ^"异或"
//这些平时用的比较少，另外还有>>、>>>这种位移运算符
```

##### 数学函数
```java
// 主要是依靠于Math类
Math.sqrt(x); //计算平方根
Math.pow(x,2);  //计算幂

//还有一些三角函数，自然数等
Math.sin
Math.cos
Math.tan
...
Math.PI
Math.E
```

#### 常用语法
##### 常量的定义
Java定义常量使用的关键字是`final`，并不是C#中的`const`

##### 控制流程语句
```java
//条件语句
if(condition) {
  statement
}

//循环语句
while(condition) {
  statement
}

do{
  statement
}
while(condition)

//确定循环
for(int i=1;i<10;i++) statement

//多从选择
switch(choice){
  case 1:
    ...
    break;
  default:
    ...
    break;
}


//循环中断控制器
  break;  //跳出
  continue; //继续
```

##### foreach循环
```java
//Java的语法为:
for(variable : collection) statement

//而非C#的:
foreach (var item in collection)
```

#####  输入输出
```java
// 输入语句
Scanner in = new Scanner(System.in);
in.nextLine();  //获取一行字符
in.next();  //获取一个字符
in.nextInt(); //获取一个整形
in.hasNext(); //是否还有其他单纯
...

// 输出语句
System.out.println();
```

## 面向对象编程
面向对象（OPP）的核心为：封装、继承、多态。

### 认识下术语
| 名称                  | 描述                                                                                       |
|:--------------------- |:------------------------------------------------------------------------------------------ |
| 类（Class）           | 构造对象的蓝图                                                                             |
| 实例（Instance）      | 由类创建出来的对象                                                                         |
| 封装（Encapsulation） | 将数据和行为进行组合，通过对象隐藏具体的实现，赋予对象的黑盒特征                           |
| 继承（Inheritance）   | 类可以建立在另一个类的基础上，可以使得子类具有父类的属性和方法或者重新定义、追加属性和方法 |
| 多态（Polymorphism）  | 通过继承，父类或者接口可以通过子类进行实例化，从而或者各个子类的方法实现                   |

### 对象与类

#### 类之间的关系
类之间场景的主要关系有
- 依赖（dependence）：uses-a
- 聚合（aggregation）：has-a
- 继承（inheritance）：is-a

#### 类的建模
通过UML（Unified Modeling Language，统一建模语言）来绘制类型，描述类之间的关系。
集体可以细化成：
