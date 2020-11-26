---
title: java笔记
date: 2020-11-08 20:31:15
tags: java
categories: java
---

# java笔记

---

[toc]

### 数据类型

![image-20201115230836668](java笔记/image-20201115230836668.png)

>
>
>float 类型定义需要在后面加f：`float x =  3.14f`
>
>char类型用`''` 字符串类型用`""`
>
>==常量的定义==
>
>> `final double PI = 3.14`
>>
>> 定义后不可改变
>
>***var关键字***
>
>> 类型太长可以省略,利用var代替,编译器自动识别.例如对象

#### 变量的作用域

> java中利用`{}`包裹起来的控制语句 在其中***定义*** 的变量在块结束的时候结束.
>
> ```java
> {
>     ...
>     int i = 0; // 变量i从这里开始定义
>     ...
>     {
>         ...
>         int x = 1; // 变量x从这里开始定义
>         ...
>         {
>             ...
>             String s = "hello"; // 变量s从这里开始定义
>             ...
>         } // 变量s作用域到此结束
>         ...
>         // 注意，这是一个新的变量s，它和上面的变量同名，
>         // 但是因为作用域不同，它们是两个不同的变量:
>         String s = "hi";
>         ...
>     } // 变量x和s作用域到此结束
>     ...
> } // 变量i作用域到此结束
> ```
>
> 

***自增和自减***

> `++n`表示先加一在用n;`n++`表示先用n再加一

***强制类型转化***

> `short s  = (int) i ;`

#### 布尔类型运算

> - 比较运算符：`>`，`>=`，`<`，`<=`，`==`，`!=`
> - 与运算 `&&`
> - 或运算 `||`
> - 非运算 `!`

#### 三元运算符

> `b ? x:y`

#### 字符串

> ***字符串连接***
>
> `"Hellow"+"world"`
>
> 对于其他数据类型会自动转化为字符串

> ***多行字符串***
>
> `String s = """
>
> hello
>
> world"""`
>
> ```java
> public class Main {
>     public static void main(String[] args) {
>         String s = "hello";
>         String t = s;
>         s = "world";
>         System.out.println(t); // t是"hello"
>     }
> }
> 
> ```
>
> 因为初始s指向的是hello所在内村空间,s赋值给t的指向也是hello所在的位置

### 数组

---

***java数组特点***

> * 初始为默认值,整数都是***0***,浮点数是***0.0***,布尔数值是***false***
> * 数组创建后大小不可变

***数组定义***

> `int[] ns = new int[5]`
>
> ` int[] ns = {1,2,3,5};`
>
> 字符串是引用类型：

#### 多维数据

> ***二维数组的定义和输出***
>
> ```java
>         int[][] ns = {{1,2,3},{4,5,6}};
>         for (int[] arr:ns){
>             for (int a : arr){
>                 System.out.println(a);
>             }
>         }
> 		 System.out.println(Arrays.deepToString(ns));//打印多维数组
> ```
>
> 

### 输入输出

> `println:print line`:输出并换行
>
> `pirnt`:输出不换行

#### 格式化输出

> ```java
> public class Main {
>     public static void main(String[] args) {
>         double d = 3.1415926;
>         System.out.printf("%.2f\n", d); // 显示两位小数3.14
>         System.out.printf("%.4f\n", d); // 显示4位小数3.1416
>         int n = 12345000;
>         System.out.printf("n=%d, hex=%08x", n, n); //格式化n并输出8位并用0补位的16进制
>     }
> }
> ```
>
> ![image-20201116193135512](java笔记/image-20201116193135512.png)
>
> 

#### 输入

> ```java
> import java.util.*;
> public class first{
>     public static void main(String[] args) {
>         Scanner scanner = new Scanner(System.in);
>         System.out.print("input your name:");
>         String name =  scanner.nextLine();
>         System.out.print("input your age:");
>         int age = scanner.nextInt();
>         System.out.printf("your name is %s,your age is %d",name,age);
> 
>     }
> 
> }
> ```

### 流程控制

#### if

> 对于引用类型的比较要用
>
> ```java
> public class Main {
>     public static void main(String[] args) {
>         String s1 = "hello";
>         String s2 = "HELLO".toLowerCase();
>         System.out.println(s1);
>         System.out.println(s2);
>         if (s1.equals(s2)) { //字符串是引用类型
>             System.out.println("s1 equals s2");
>         } else {
>             System.out.println("s1 not equals s2");
>         }
>     }
> }
> 
> ```
>
> 

#### switch

>```java
>    public static void main(String[] args) {
>        String s = "test";
>        switch(s){
>            case "test":
>                System.out.println("yes");
>                break;
>            case "sss":
>                System.out.println("llll");
>                break;
>            default :
>                System.out.println("lslls");
>                break;
>
>            case "test" -> System.out.println("java新的switch");
>
>        }
>        int opt = switch (test) {
>            case "test" -> 1;
>            case "sss" ->{
>                string x = "返回负责的表达式用yield";
>                yield x;
>            }
>            default -> 2;//执行赋值操作
>
>
>        }
>
>    }
>
>}
>```
>
>

#### 循环

> ***foreach***
>
> > ```java
> >     public static void main(String[] args) {
> >         int[] s = {1,2,3,6,5};
> >         for (int i : s) {
> >             System.out.println(i);
> >         }
> > 
> >     }
> > 
> > ```

### 命令行参数

> ```java
> public class Main {
>     public static void main(String[] args) {//字符串类型的参数 参数第一个就是参数
>         for (String arg : args) {
>             System.out.println(arg);
>         }
>     }
> }
> ```
>
> 

----

---



## java面向对象



### 可变参数

>
>
>```java
>    class person{
>        private String name;
>        private int age;
>        
>        public void setNameAndAgeg(String...names){ //数据类型...数组名
>            this.name = names[0]; //names是数组
>            System.out.println(this.name);
>        }
>        
>    }
>    person test =  new person();
>    test.setNameAndAgeg("你好呀");
>    
>    }
>```
>
>

### 参数绑定

>调用方法时候如果传入的是引用数据类型,修改数组会引起里面赋值的变化

### 构造方法

> ```java
>         public person(){  //可以构造多个构造方法 编译器可以自动识别
> 
>         }
>         public person(String name,int age){
>             this.name = name;
>             this.age = age;
>         }
> ```
>
> 

### 方法重载

> * 一般重载的返回类型相同
> * 重载用的都是同样的函数名
> * 多个构造方法就是重载的一种表现

### 继承

> * 继承的关键字为`extends`
>
> * 继承中无法在子类中定义和父类相同的字段
>
> * 继承中无法访问父类的`private`所以建议用你`protected`
>
> * 继承中会在子类的构造函数执行之前自己调用`super()`如果木有定义无参数的构造方法,编译出错
>
>   ```java
>   public student(int score,int age,String name){
>               super(name,age);//调用父类的构造方法
>               this.score = score;
>           }
>   ```
>
>   

#### 阻止继承

> ***sealed和permits****
>
> > ```public sealed class Shape permits Rect, Circle, Triangle ```
> >
> > 只有```Rect, Circle, Triangle ```可以继承`shape`
> >
> > 

#### 向上转型

>student继承自person,person继承自object
>
>所以可以用`person s = new student()`和`object o = new person()`,因为student继承自person自然就有person的所有方法和字段

#### 向下转型

> 通常会失败

###  多态

> * 调用父类被复写的方法用`super.`
>
> * 用`final`标记的方法不能被子类复写
>
>   `public final String hello()`
>
> 多态运行是运行的复写方法由new时候的类所定义,并不会因为父类的引用类型所影响



### 抽象类

>抽象类的背景是:对于多态,父类的方法可以不用定义,但是不能删除父类方法的定义.为了让子类去复写.
>
>```java
>abstract class person{
>            public abstract void run(); //方法和类都必须定义为抽象类
>        }
>```
>
>* 抽象类不可以实例化
>* 抽象类中的抽象方法必须在子类中复写才有作用

### 接口

> ≈没有字段的抽象类
>
> ***java中一个类只能继承一个类,但是可以继承多个接口***
>
> ```java
>         interface test{  //interface定义接口
>             void run();
>             //接口可以有静态字段 只能是public static final 可以省略直接写
>             int MAN = 1;
> 
>         }
>         class studnet implements test{  //用implements继承接口
>             @Override
>             public void run() {
>                 
>             }
>         }
>         interface test2 extends test{
>             
>         }//接口的继承
> interface Person {
>     String getName();
>     default void run() { //default定义的若子类没有复写可以默认
>         System.out.println(getName() + " run");
>     }
> }
> ```
>
> ***用具体的子类去实例化对象,但是引用的话用的是向上转型的父类或者接口***
>
> 

### 静态字段和静态方法

> ```java
> class person{
>     public string name;
>     public static int number; //静态字段,所有实例共享,修改则都修改
>     public static void setNumber(int value) {//静态方法不能访问实例的字段,只能访问静态字段
>         number = value;
>     }
> }
> //一般利用类名来引用静态字段
> person.number;
> 
> ```
>
> 

### 匿名对象

> 



### 包

> 解决命名冲突而设计

### 常用api



### 作用域

> 定义 为`public`的calss可以被其他类访问

## IDEA

### 快捷键

> 向下复制一行:`ctrl D`
>
> 删除一行:`ctrl y`
>
> 提示`ctrl space`
>
> 自动修复导入包`alt enter`
>
> 格式化代码`ctrl alt l`
>
> 单行注释:`ctrl /`
>
> 多行注释:`ctrl shift /`
>
> 移动当前行:`alt shift 上/下`
>
> main函数:`psvm`
>
> out:`sout`
>
> arraylist遍历:`list.fori`
>
> 

### Arraylist

> 基本类型 ---> 包装类
>
> 

### 字符串

>`==`对于基本类型来说是数值比较,对于引用类型来说是地址的比较

#### 字符串常量池

> 字符串常量池位于堆中:***字节数组的地址位于池中,字符串对象中用的是也是此地址***