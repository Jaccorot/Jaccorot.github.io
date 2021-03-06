---
layout: post
title:  "java总结"
date:   2020-12-14 11:00:00 +0800
categories: java 总结
tags: java总结
excerpt: java知识总结
---

* content
{:toc}
# 基础语法

## 八大基本数据类型

  Java语言提供了8种基本数据类型。分别是 byte、short、int、long、float、double、boolean、char。

 **注**：String 是对象，不属于基本数据类型

  8种基本数据按类型分可以分为

- 4个 整数型：byte、short、int、long
- 2个浮点型：float、double
- 1个字符类型：char
- 1个布尔型：boolean

| 基本数据类型 | 默认值   | 大小（字节）                                        | 取值范围          | 示例             |
| ------------ | -------- | --------------------------------------------------- | ----------------- | ---------------- |
| byte         | 0        | 1                                                   | -2^7 ~ 2^7-1      | byte a= 10;      |
| boolean      | false    | 1                                                   | true/false        | boolean b=true;  |
| char         | '\u0000' | 2                                                   | 0 ~ 2^16-1        | char c='c';      |
| short        | 0        | 2                                                   | -2^15 ~ 2^15-1    | short d=10;      |
| int          | 0        | 4                                                   | -2^31 ~ 2^31-1    | int e=10;        |
| long         | 0        | 8                                                   | -2^63 ~ 2^63 -1   | long f = 10L;    |
| float        | 0.0f     | 4：1bit（符号位） 8bits（指数位） 23bits（尾数位）) | -2^128 ~ +2^127   | float g = 10.0F; |
| double       | 0.0d     | 8：1bit（符号位） 11bits（指数位） 52bits（尾数位） | -2^1024 ~ +2^1023 | double h = 10.0; |



  **注意：** （1） 基本数据类型 "==" 比较都是值。

​        （2）Boolean 《Java虚拟机规范》给出了4个字节,但还要看虚拟机实现是否按照规 范来，所以1个字节、4个字节都是有可能的。

### 类型转换和精度丢失问题

1. 自动类型提升

   * 不同类型的数据在运算的时候，会向高精度的数据类型转换。

   * byte/short/char运算时结果为int

   * double类型数值的计算经常会出现精度丢失，尤其是有小数点的情况下。

     因为计算机是通过二进制进行运算的，而计算机在表示小数的二进制是会有精度问题的。
     所以我们在运算高精度的数据的时候，可以使用 `java.math.BigDecimal` 类

   ```java
   byte a = 5;
   int b =2;
   float c  = 6f;
   double d =0.03;
   double d2 =300.03;
   System.out.println(a/b); // 2  a会转化为int类型
   System.out.println(b/c); // 0.33333334 b会转化为float类型  
   System.out.println(a+d+d2);//301.05999999999995 a会转化为double类型,但是结果理应是301.06
   short s1 = 5;
   char c1 = 'a'; //95
   int s2 = s1 + c1; //102  byte/short/char运算时结果为int
   
   // 编码情况
   // 整形默认为int类型
   //浮点型默认为double类型
   long l1 = 123123;   //未加L，整形默认为int类型，自动进行类型提升转化为long
   //long l2 = 123123123123123;  // 默认为int时不支持，编译报错！！！!!
   //float f2 = 12.3; //未加f，浮点型默认为double类型，double转float有丢失精度风险，所以编译报错；！！！！
   
   byte b1 = 12;
   //byte b2 = b1 + 1;     //编译失败，1为int
   //float f1 = b + 12.3;   //编译失败， 12.3位double
   ```

   ![img](../images/all/java-basic-data-transfer.image)

2. 强制类型转换

   * 需要使用强转符
   * 可能会导致精度损失

   ```java
   double d1 = 12.9;
   int i1 = (int)d1;
   System.out.println(i1); // 12 精度损失
   
   int i2 = 128;
   byte b = (byte)i2;
   System.out.println(b);   //-128   精度损失
   ```

   

### 拆箱和装箱问题

​     **1）拆箱和装箱概念**

装箱就是自动将基本数据类型转换为包装器类型；使用`Integer.valueOf`方法。

​       拆箱就是自动将包装器类型转换为基本数据类型；使用`Integer.intValue`方法。

​       举个例子：

```
Integer total = 99; 
//执行上面那句代码的时候，系统为我们执行了，即自动装箱
Integer total = Integer.valueOf(99);int totalprim = total; 
//执行上面那句代码的时候，系统为我们执行了，即自动拆箱
int totalprim = total.intValue();
复制代码
```

  **2）范围问题**

​      如下题目   

```
Integer i = 400; 
Integer j = 400; 
System.out.println(i==j);  //false
Integer o = 12; 
Integer k = 12;
System.out.println(o==12);  //true
复制代码
```

   上面提到，使用 `Integer`去创建数据，其实是一个`Integer.valueOf` 过程，`Integer.valueOf` 源码如下：  

```
public static Integer valueOf(int i) {    
  if (i >= IntegerCache.low && i <= IntegerCache.high) {    
    return IntegerCache.cache[i + (-IntegerCache.low); 
  }   
  return new Integer(i);
}
复制代码
```

分析：如果值的范围在-128到127之间，它就从高速缓存返回实例。否则 new 一个Integer对象。`new Integer` 就是一个装箱的过程了，装箱的过程会创建对应的对象，这个会消耗内存，所以装箱的过程会增加内存的消耗，影响性能。
 所以说最后是i 和 j 两个对象比较，内存地址不一样，结果就是false了。

  **3）==的值比较问题**

   例子如下：

```
int a =200;
Integer b = new Integer(200);
Integer c = 200;
System.out.println(a==b); //true
System.out.println(a==c); //true
System.out.println(b==c); //false
复制代码
```

分析： `a==b，a==c`，只要和基本数据类型（即 int）比较，Integer就会调用`value.intValue()`拆箱成基本数据类型，你也可以理解为：当有基本数据类型，只比较值`b==c`，这两个是永远不会相等的，拆箱装箱只是针对基本数据类型的比较才有，**Integer并不是基本数据类型**，b、c两者存放的内存地址不一样，所以不相等。

**总结：**

①、无论如何，Integer与new Integer不会相等。不会经历拆箱过程，因为它们存放内存的位置不一样。

②、两个都是非new出来的Integer，如果数在-128到127之间，则是true,否则为false。

③、两个都是new出来的，即两个new Integer比较，则为false。

④、int与Integer、new Integer()进行==比较时，结果永远为true，因为会把Integer自动拆箱为int，其实就是相当于两个int类型比较。

### int 和 Integer 

- Integer 继承了Object类，是对象类型，有自己的属性和方法，是 int 的包装类。int是java基本数据类型。
- Integer默认值null，int默认值 0。
- int 可以直接做运算，Integer 不能直接运算，拆箱转化为int才能进行运算。

### 默认值问题

   在java中：

- 整数的默认类型是 int。
- 浮点数默认类型是 double，否则需要 在后面加f、d

  浮点数如果不加`f`，默认就是double类型的，前面再用 float修饰，就会报错。可以使用两种方法解决: 1)末尾加f  2)使用float进行强转

### Integer.parseInt()和Integer.valueOf()的区别

parseInt() 和 valueOf() 都是Integer 对象的方法。入参都是一个String字符串。

 **parseInt**

   public static int parseInt(String s) throws NumberFormatException 将字符串参数作为带符号十进制整数来转换。如果无法转换，抛出 NumberFormatException。

 **valueOf** 

   public static Integer valueOf(String s) throws NumberFormatException 返回初始化为指定String值的新的Integer对象，如果无法转换，抛出 NumberFormatException。

```
String str = "-12";
int  num = Integer.parseInt(str);
System.out.println(num); // -12
Integer  num2 = Integer.valueOf(str);
System.out.println(num2); // -12
int num3 =Integer.parseInt("HaC"); //java.lang.NumberFormatException
复制代码
```

**int 与 Integer转换**

int a=A.intValue();

Integer A=Integer.valueOf(a);

注意： **Integer派别**：Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。

​     **Double派别**：Double、Float的valueOf方法的实现是类似的。**每次都返回不同的对象。**



### 进制转换

```java
int num1 = 0b110;   //2进制   0b123
int num2 = 110;    //10进制    123
int num3 = 0120;  // 8进制    0123
int num4 = 0x11A; // 16进制   0x123
System.out.println(num1); //6
System.out.println(num2); //110
System.out.println(num3); //80
System.out.println(num4);  //282
```

* 二进制：计算机底层是以补码的方式存储数据

  * 正数： 三码一致

  * 负数：

    * 原码：正数原码 + 符号位取1

    * 反码：除符号位，各个位取反

    * 补码：反码的基础上加1

### 留几个题目分析  

**1） 设有下面两个赋值语句**

```
a = Integer.parseInt("1024");
b = Integer.valueOf("1024").intValue();
复制代码
```

下述说法正确的是（）

A a是整数类型变量，b是整数类对象。
 B a是整数类对象，b是整数类型变量。
 C a和b都是整数类对象并且它们的值相等。
 D a和b都是整数类型变量并且它们的值相等。

答案是 **D**，intValue()是把Integer对象类型变成int的基础数据类型；



## 三个引用数据类型

### 类class

#### String

* String可以和8种基本数据类型变量做运算，且运算只能是连接运算： +

* 字符串与整数拼接问题

```java
String a = "1";
int b = 1;
int c = 2;
System.out.println(a + b + c); //112  a + b + c从左到右按顺序运算，a是String, a+b 的结果也是String的拼接是字符串11 ，然后再拼接 c，c也被强转String了，最后是 String，其值112
System.out.println(b + c + a); //31   b + c + a 从左到右按顺序运算，b + c 的结果是 3，是一个int ，然后拼接a，变成 String。
```

### 接口interface

### 数组array

## 运算符

### 逻辑运算符

* & 
  * && 短路与
* | 
  * || 短路或
* ^

### 位运算符

```java
<<  左移：空位补0，移除的高位丢弃；
>>  右移：高位是0时，补0； 高位是1时，补1；
>>> 无符号右移：高位无论是0,还是1，均补0
& 二进制与运算
| 二进制或运算
^ 二进制异或运算
~ 按补码取反（无论正数还是负数）

int i = -21;
System.out.println(i<<1);  //-42
System.out.println(i>>1);  //-11
int j = 21;
System.out.println(j<<1);  // 42
System.out.println(j>>1);  // 10
```

三元运算符

```
(条件表达式)？表达式1：表达式2
```

### Scanner

```
import java.util.Scanner;

Scanner scan = new Scanner(System.in);
String name = scan.next();
char first = name.charAt(0)
int age = scan.nextInt();

System.out.println(name);
```

## switch

* switch表达式中的值依次匹配各个case中的常量
* 表达式类型： byte/ short/ char/ int/ 枚举 (jdk 5.0 +)/ String （Jdk 7.0)
* case只能声明常量，不能声明范围

```java
int number =1;
switch(number){
    case 0:
        System.out.println("0");
        break;
    case 1:
        System.out.println("1");
        break;
    case 2:
    case 3:
        System.out.println("2 or 3");
    default:
        System.out.println("default");
}

// month月day日是该年的第几天
int day = 20;
int month = 8;
int days = 0;
switch (month) {
    case 12:
        days += 31;
    case 11:
        days += 30;
    case 10:
        days += 31;
        // 9,8,7,6,5,4,3,2
    case 1:
        days += day;
}
System.out.println(month + "月" + day + "日是当年的第" + days + "天");
```

## 循环

```java
        label:for (int i = 0; i < 10; i++) {
            for (int j = 0; j < 10; j++) {
                if(j % 4==0){
                    break;   // break 当层
                    //break label;  // break外层
                    //continue;  // continue 当层
                    //continue label; // continue 外层
                }
            }
            System.out.println(i);
        }
```

## 数组

* 特点：
  * 有序
  * 引用类型
  * 内存中是一块连续的空间
  * 长度一旦初始化时确定，不能修改
* 默认值：
  * 一维数组
    * 基本类型
      * 整型：0
      * 浮点：0.0
      * char："\u0000"  ， '0'
      * bool: fasle
    * 引用数据类型：null
      * 数组：null
  * 二维数组
    * 指定大小动态初始化或静态初始化 （new int[4\][3\])
      * 第一层初始值为地址空间
      * 第二层初始值为：同一维数组初始化相同
    * 未指定大小的动态初始化(new int[4\][\])
      * 第一层初始值为null
      * 第二层不存在，访问会报空指针
* 内存：
  * 栈：数组变量
  * 堆：数组对象（二维数组的第一层）
  * 方法区
    * 常量池
    * 静态空间

```java
int[] ids = new int[]{1, 2, 3, 4}; //静态初始化：数组的初始化和元素的赋值同时进行
String[] names = new String[5]; // 动态初始化：数组的初始化和元素赋值分开
String[] names2 = {"a","b","c"}; // 类型推断，可以不用显式创建
names[0] = "a";
for (int i = 0; i < names.length; i++) {
    System.out.println(names[i]);
}
for (String name : names) {
    System.out.println(name);
}
char[] c = new char[1];
System.out.println(c[0] == 0); //true

//二维数组
int[][] arr21 = new int[][]{ \{1,2,3},{4,5},{6,7,8\} };
String[][] arr22 = new String[3][2]; //动态初始化1,内存中第一层默认为地址值（[I@15db9742），第二层默认值为0
String[][] arr23 = new String[3][];  //动态初始化2,未指定第二层大小时不会创建，内存中第一层为null，第二层不存在，访问会空指针
String[] arr24[] = new String[][]{\{"a","b"},{"c","d","e"\}}; //内存中第一层为地址值([Ljava.lang.String;@7852e922)，第二层默认值为null
String[] arr25[] = {\{"a","b"},{"c","d","e"\}};
for (int i = 0; i < arr25.length; i++) {
    for (int j = 0; j < arr25[i].length; j++) {
        System.out.print(arr25[i][j] + " ");
    }
    System.out.println();

}
```

```java
String[] strings = new String[]{"a","b","c"};
System.out.println(strings);   //[Ljava.lang.String;@1540e19d  
char[] chars = new char[]{'a', 'b', 'c'};
System.out.println(chars);    //abc  println(char[])特殊处理，遍历输出内容
```



### Arrays 工具类

* equals 
* fill
* sort
  * quick sort
* toString
* binarySearch

```java
        int[] arr = new int[]{1, 5, 4, 2, 6, -1, 0, 9};
        boolean b = Arrays.equals(arr,arr);
        System.out.println(b);
//        Arrays.fill(arr,2);
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
        System.out.println(Arrays.binarySearch(arr, -1));
```

# 面向对象

## 三大特性

### 封装

* 通过四种权限修饰符实现
* 隐藏内部实现，提供对外访问
  * 将类的属性私有化，提供公共的方法来获取和设置属性的值
  * 不对外暴露的私有方法
  * 单例模式（构造器私有化）

### 继承

* 类单继承；接口多继承

#### Object

* 无属性

* 空参构造器

* 方法

  * clone() ： 复制
    * 返回Object
  * equals(Object obj)
    * 返回boolean

  > 在Java中，equals和==都是用于检测两个字符串是否相等，返回类型也都是boolean值，但是二者内部处理却不一样。
  >
  > 1）对于==，比较的是值是否相等
  >
  > ​	如果作用于基本数据类型的变量，则直接比较其存储的 “值”是否相等；
  >
  > ​	如果作用于引用类型的变量，则比较的是所指向的对象的地址
  >
  > 2）对于equals方法，注意：equals方法不能作用于基本数据类型的变量，equals继承Object类，比较的是是否是同一个对象
  >
  > ​    如果没有对equals方法进行重写，则比较的是引用类型的变量所指向的对象的地址；
  >
  > ​	诸如String、Date等类对equals方法进行了重写的话，比较的是所指向的对象的内容。

  ```java
      @Override
      public boolean equals(Object o) {
          if (this == o) return true;
          if (o == null || getClass() != o.getClass()) return false;
          ManKind manKind = (ManKind) o;
          return sex == manKind.sex &&
                  salary == manKind.salary;
      }
  ```

  

  * hashCode
  * toString()
  * getClass()
  * finalize()
    * void
    * 对象在被回收前被调用
  * notify()
  * notifyAll()
  * wait()/   wait(long timeout)  /   wait(long  timeout, int nanos)

### 多态

* 父类引用指向子类对象
* 属性：属性不适用多态
  * 编译和运行都是父类的属性，即使用父类的属性
* 方法：调用父类的方法，子类有重写时优先使用子类重写后的方法
  * 虚拟方法调用：运行时行为
    * 编译期只能调用父类声明的方法：编译看左边
    * 运行期实际使用子类重写方法：运行看右边
    * 动态绑定

## 关键字

### package

### import

* 导入
  * import 导入指定包下类或接口
  * import static 导致入定类或接口中的静态结构：属性或方法
* 如果在源文件中实用了不同包下同名的类，需要使用全类名的形式导入

> com.eqxiu.User user = new com.eqxiu.User()

* instanceof
  * a instanceof A 
    * 判断对象a是否是类A 或者 A父类的实例，返回boolean

### this

* 当前对象

  * this.属性  //形参和属性不一致时可省略

  * this.方法()  

  * this(...);   //构造器

    * 必须放在首行
    * 显式调用其他构造器，不能调用自己
    * 避免循环递归调用（编译不通过）

    ```java
        public Person(){
            System.out.println("init");
        }
    
    
        public Person(int age){
            this();  // 调用无参构造器
            this.age = age;
            System.out.println("init age " + age);
        }
    
        public Person(String name, int age) {
            this(age); //调用  (int age) 构造器
            this.name = name;
            System.out.println("init age and name");
        }
    ```

### super

* 属性或方法
  * 在子类中显式的调用父类的属性或方法
    * super.属性
    * super.方法()
* 构造器
  * 调用父类指定构造器：
    * super(形参列表)
    * 在子类构造器的首行
    * 在类的构造器中， this 或者super 只能用一个
    * 在子类构造器首行没有显式调用super，则默认调用super()，即父类空构造器

### static

* 属性
  * 静态变量
    * 多个对象共享
  * 静态变量随着类的加载而加载，可以通过class.静态变量   进行调用
  * 静态变量加载早于对象的创建
  * 在内存中只存在一份，存在于方法区的静态域中
* 方法
  * 静态方法
    * 静态方法只能使用静态方法或属性
    * 非静态方法可以使用静态、非静态 方法或属性
  * 静态方法随着类的加载而加载，可以通过 class.静态方法（） 进行调用
* 代码块
* 内部类
* 注意
  * 静态方法内，不能使用this、super 关键字
  * 子类和父类同一个方法都用static修饰，不构成重写

### final

* 类
  * 无法被继承（比如String/System/StringBuffer）
* 变量
  * final在对变量进行修饰时一定要赋值，且不允许被再次赋值
  * 属性(成员变量)：常量
    * 修改位置：
      * 显示初始化
      * 代码块初始化
      * 构造器中初始化
  * 局部变量：常量
    * 形参：方法体内的形参只能读，不允许修改
* 方法
  * 无法被重写（比如Object 的getClass()）

### abstract

* 抽象类
  * 无法实例化
  * 有构造器，用于子类对象实例化

* 抽象方法

  * 只有方法声明，无方法体

* 不能修饰 属性、构造器；

* 不能修饰私有方法、静态方法、final方法；

* 不能修饰final类；

  

* 包含抽象方法的类一定是抽象类，反之抽象类不一定有抽象方法

* 子类重写了父类中所有抽象方法（包括父类及祖先类的抽象方法），则此子类可实例化；否则，子类也是一个抽象类，需要abstract修饰；

### throw and throws

* throw 生成异常对象环节，表示抛出一个异常类的对象，声明在方法体内
* throws 捕获处理异常环节，属于异常处理的一种方式，声明在方法的声明中

## 类

### 内存分布

![image-20201128122434982](../images/all/java-vm.png)



### 1. 属性：成员变量 vs 局部变量

相同点：

* 定义变量的格式： 数据类型  变量名 = 变量值；
* 先声明，后使用；
* 变量都有其对应作用域；

不同：

1. 声明位置不同
   * 成员变量：类内
   * 局部变量：方法内、方法形参、构造器内、构造器形参

2. 权限修饰符规则不同
   * 成员变量：private/public/缺省/protected （默认使用缺省）
   * 局部变量：不允许使用权限修饰符

3. 默认初始化值不同
   * 成员变量：根据其类型，都有默认初始化值；
     * 基本：0，0.0，false,0('\u0000')
     * 引用：null
   * 局部变量：无默认初始化值；

4. 内存中加载的位置不同
   * 成员变量：加载到堆空间（非static）
   * 局部变量：加载到栈空间

对属性可以赋值的位置：

* 默认初始化
* 显示初始化   /  在代码块中赋值  （先写哪个就先执行哪个）
* 构造器初始化
* 对象.属性或 对象.方法  进行赋值

### 2. 方法

> 权限修饰符  返回值类型  方法名（形参）{
>
> ​	方法体
>
> }
>

#### 匿名对象

* 创建的对象，没有显示的赋值给一个变量名，即为匿名对象
* 匿名对象只能使用一次

```java
//有名字的对象
Student s = new Student();
		
//匿名对象:
new Student()
```

#### 匿名类

```java
public class OuterClass {
    public static void main(String[] args) {
        /**
         *     object1 = new Type(parameterList) {
         *          // 匿名类代码
         *     };
         */


        //创建匿名子类的对象
        //1. 通过继承MyPerson类，创建的匿名类
        MyPerson p2 = new MyPerson() {
            @Override
            public void shout() {
                System.out.println("shout");
            }
        };
        p2.shout();

        //2. 通过实现接口，创建的匿名类
        Human p3 = new Human() {
            @Override
            public void eat() {
                System.out.println("eat");
            }
        };
        p3.eat();
    }
}


abstract class MyPerson {
    public abstract void shout();
}

interface Human {
    void eat();
}
```



#### 方法重载

* 在一个类中，方法名相同，参数列表不同（参数个数 或 参数类型）

* 方法的权限修饰符、返回值类型、抛出不同异常 可以不相同，重载和这些无关

* 可变参数与普通参数之间也构成重载，可变参数和数组参数不构成重载

  ```java
  //1和2不构成重载，编译器认为是同一个方法
  // 1和3  或者 2 和3 构成重载
  public void show(String... strs) {
      System.out.println(1);
  }
  
  public void show(String[] strs) {
      System.out.println(2);
  }
  public void show(String s) {
      System.out.println(3);
  }
  ```

  

```java
public static void main(String[] args) {
        Main m = new Main();
        int a = 1;
        int b = 2;
        m.getSum(a,b);   // sout 2    变量自动提升
    }

//    public void getSum(int i, int j) {
//        System.out.println(1);
//    }

    public void getSum(double i, double j) {
        System.out.println(2);
    }
```

#### 方法重写

* 子类继承父类后，对父类中的同名参数的方法进行覆盖操作
* 重写后，默认调用子类的重写方法；
* 规则：
  * 重写方法的方法名和形参列表应与父类相同
  * 权限修饰符
    * 子类重写方法的权限修饰符应大于等于父类
    * 子类不能重写父类声明为private的方法
  * 返回值类型
    * 父类被重写的方法返回值类型是void，子类只能是void
    * 父类被重写的方法返回值类型是基本数据类型，子类必须是相同的基本数据类型
    * 父类被重写的方法返回值类型是A类型，子类可以是A类 或A类的子类
  * 异常
    * 父类被重写的方法异常类型A，子类抛出的异常是A类或 A类的子类（父类大于等于子类重写方法）
  * 静态方法不允许被覆盖
    * 子类和父类中的同名同参数方法，要么都声明非static （考虑重写）
    * 要么都声明为static(不是重写)

#### 可变形参

* jdk5.0新增
  * 5.0之前   public static void test(int a, String[] books);
  * 5.0之后   public static void test(int a, String ...  books);
* 允许直接定义能和多个实参相匹配的形参，从而更简单的传递个数可变的实参。（0个或多个）
* 优先考虑精准匹配
* 在方法的声明中，只能声明在末尾
* 同一个方法，最多只能声明一个可变形参

```java
public class Main {
    public static void main(String[] args) {
        new Main().show("a", "b", "c");   // a b c 
        new Main().show();   // 空，也走可变形参
        new Main().show("a");   // 2 ，优先精准匹配
    }
    
    // 可以以  .show("a", "b", "c") 访问
    //也可以以  .show(new arr[]{"a","b","c"}) 访问'
    // 都会放在数组变量 strs中
    public void show(String... strs) {
        for(int i = 0; i < strs.length;i++){
            System.out.print(strs[i]+ " ");
        }
    }
    public void show(String s) {
        System.out.println(2);
    }
    public void show(int i, String... s) {
        System.out.println(2);
    }
}
```

#### 方法参数的值传递

* 变量赋值
  * 基本数据类型的变量，赋值的是变量所保存的数据值；
  * 引用数据类型的变量，赋值的是变量所保存的数据地址值；
* 形参传递机制
  * 形参是基本数据类型，将实参基本数据类型变量的“数据值”传递给形参
  * 形参是引用数据类型，将实参引用数据类型变量的“地址值”传递给形参

#### 权限修饰符

* 类
  * public
  * 缺省

* 类的内部结构：属性、方法、构造器、内部类
  * private
  * 缺省
  * protected
  * public

  ![image-20201128185214883](../images/all/visit-priviledge.png)

#### 向下转型

多态强制转换

* 可使用 instanceof 判断

### 3. 构造器

* 没有显式定义类的构造器时，系统默认提供一个空参的构造器

* 一旦显示的定义了类的构造器，系统就不再提供空参构造器

* 一个类可以定义多个构造器，构成重载

* 默认构造器权限和类的权限相同

* 格式：   

  > 权限修饰符  类名（形参列表）{
  >
  > }

### 4. 代码块

* 可定义多个，按照声明的先后顺序执行
* 代码块早于构造器执行
* 先父后子，静态先行

* 静态代码块 （static)
  * 随着类的加载而执行，只执行一次
  * 可以对类进行初始化
  * 只能调用静态属性或方法
* 非静态代码块
  * 随着对象的创建而执行，每次创建对象都会执行
  * 可以对对象进行初始化（初始化在构造之前）
  * 可以调用静态或非静态结构

### 内部类

* 成员内部类
  * 作为成员
    * 可以调用外部类的结构    外部类.this.属性   or   外部类.this.方法
    * 可以被static修饰
    * 可以被4种权限修饰符修饰
  * 作为类
    * 类内可以定义属性、方法、构造器，可以被继承
    * 可以被final修饰，标识此类不能被继承；
    * 可以被abstract修饰
  
* 局部内部类（方法内、代码块内、构造器内）

  * 在局部内部类的方法(比如show)中，如果调用局部内部类所声明方法(比如method)的局部变量（比如num），则要求该变量为final
    * JDK1.7及之前版本必须显式声明
    * JDK1.8及之后可以不声明，默认为final

  ```java
  public class InnerTest {
      
      public void method(){
          int num = 10;
          class AA{
              public void show(){
                  System.out.println(num);
              }
          }
      }
  }
  
  ```

  

* 匿名内部类

* 静态内部类

* 字节码文件名：

  * 成员内部类：外部类名$内部类名.class
  * 局部内部类：外部类名$数字+内部类名.class

```java
public static void main(String[] args){
    //创建静态的内部类成员
    Person.Dog dog = new Person.Dog();
    dog.show();
    
    //创建非静态的内部类成员
    Person person = new Person();
    Person.bird bird =  person.new Bird();
    bird.sing();
    
    //内部类调用外部类结构
    外部类.this.属性； // 无重名时直接调用  属性  即可
    外部类.this.方法(); // 无重名时直接调用  方法  即可
    
    //使用情景
    // 返回一个实现了Comparable接口的类的对象
    public Comparable getComparable(){
        // 方式一：
        //创建一个实现了Comparable接口的类：局部内部类
        class MyComparable implements Comparable{
            @Override
            public int compareTo(Object o){
                return 0;
            }
        }
        return new MyComparable();
        
        
         //方式二：
    	return new Comparable(){
            @Override
            public int compareTo(Object o){
                return 0;
        	}
    	}
    }
   
}
```



### 包装类

* 数值型包装类 均继承自 Number类

* 自动装箱与自动拆箱    jdk  5.0 +

  ```java
  int num2 = 10;
  Integer num3 = num2;   //自动装箱
  int num4 = num3;   // 自动拆箱
  ```

* 转化

  * Integer =   Integer.paserInt(String)
  * String =  String.valueOf(Integer)

![image-20201129215417308](../images/all/baozhuanglei-transfer.png)

* Integer

  * 内部有IntegerCache，保存了 [-128 , 127]的整数，范围内的数字可以直接使用数组内的元素，不会new

  ```java
  Integer i1 = 1;
  Integer i2 = 1;
  System.out.println(i1 == i2);   //true   IntegerCache，使用已有对象，地址空间一致
  
  
  Integer i3 = 128;
  Integer i4 = 128;
  System.out.println(i3 == i4);   // false    超过IntegerCache，新建对象
  
  
  Integer i5 = new Integer(1);
  Integer i6 = new Integer(1);  // false   新建对象，地址空间不一致
  
  public static Integer valueOf(int i) {
      if (i >= IntegerCache.low && i <= IntegerCache.high)
          return IntegerCache.cache[i + (-IntegerCache.low)];
      return new Integer(i);
  ```

### 单例

​	只生成一个实例，减少了系统性能开销

* 饿汉式
  * 好处：线程安全
  * 坏处：对象加载时间过长
* 懒汉式
  * 好处：延迟对象的创建
  * 坏处：线程不安全

```java
//饿汉式1
public class Singleton {
    private static Singleton instance = new Singleton();
    private Singleton(){
    }

    public static Singleton getInstance(){
        return instance;
    }
    
}
// 饿汉式2 ： 静态代码块
public class Singleton {
    private static Singleton instance = null;
    private Singleton(){
    }
		static {
      instance = new Singleton();
    }
    public static Singleton getInstance(){
        return instance;
    }
    
}


//懒汉式
public class Singleton {
    private static Singleton instance = null;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// 线程安全:同步优化1
public class Singleton {
    private static Singleton instance = null;
    private Singleton() {
    }

    public static Singleton getInstance() {
      	synchronized(Singleton.class){
        	if (instance == null) {
              instance = new Singleton();
          }
          return instance;
        }
    }
}

// 线程安全:同步优化2,效率相对更高
public class Singleton {
    private static Singleton instance = null;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null){
          synchronized(Singleton.class){
            if (instance == null) {
                instance = new Singleton();
            }
          }
       return instance;
       }
    }
}
```



### 模板方法

```java
package test.alltests;

// TemplateTest.class
public class TemplateTest {
    public static void main(String[] args) {
        Template t = new SubTemplate();
        System.out.println(t.spendTime());
    }
}


// Template.class
public abstract class Template {
    public long spendTime(){
        long strat = System.currentTimeMillis();
        code();
        long end = System.currentTimeMillis();
        return end - strat;
    }

    public abstract void code();

}


class SubTemplate extends Template {
		//求质数
    @Override
    public void code() {
        for (int i = 2; i < 1000; i++) {
            boolean flag = true;
            for (int j = 2; j <= Math.sqrt(i); j++) {
                if (i % j == 0){
                    flag = false;
                    break;
                }
            }
            if(flag){
                System.out.print(i + " ");
            }
        }
    }
}
```

### 代理模式

```java
package test.alltests;

public class NetworkTest {

    public static void main(String[] args) {
        Server server = new Server();
        ProxyServer proxyServer = new ProxyServer(server);
        proxyServer.browse();

    }

}

interface Network {
    void browse();
}

class Server implements Network {

    @Override
    public void browse() {
        System.out.println("server 访问网路");
    }
}

class ProxyServer implements Network {
    private Network network;

    public ProxyServer(Network network) {
        this.network = network;
    }

    public void check() {
        System.out.println("联网之前检查网络");
    }

    @Override
    public void browse() {
        check();
        network.browse();
    }
}
```

### 工厂模式

* 简单工厂模式
* 工厂方法模式
* 抽象工厂模式





## 接口

**interface**

### ~~构造器~~：

* 接口中不可以有构造器，所以无法实例化

### 方法：

* 都是public
* 1.7及之前
  * 方法是隐式抽象的,接口中的方法会被隐式的指定为 **public abstract**
  * 方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法
  * 不能含有静态方法
* 1.8及之后
  * 可以定义静态方法
    * 该静态方法只能通过接口调用，无法通过实现类 或实现类实例 调用
  * 可以定义默认方法
    * 可以通过实现类对象调用
    * 父类优先：
      * 如果父类实现一个方法，同时接口中也实现了同名同参方法，而子类未重写该方法：则子类会调用父类的方法
        * 子类可以通过 super.func() 调用父类方法
        * 子类可以通过  interface.super.func() 调用接口方法
    * 接口冲突：
      * 如果类实现多个接口，且多个接口有同名同参方法，而子类未重写该方法：则编译报错

### 变量

* 接口中变量全部会被隐式的指定为 **public static final** 变量（并且只能是 public，用 private 修饰会报编译错误）

* 代码块
  * 不能含有静态代码块

### 类

* 类实现接口，支持多实现
* 如果接口中的方法都实现了，则该类可实例化；
* 如果j接口中的方法有未实现，则该类为抽象类；

```java
... class 类名称 implements 接口名称[, 其他接口名称, 其他接口名称..., ...] ...
```

### 特性

* 接口是隐式抽象的，当声明一个接口的时候，不必使用**abstract**关键字。
* 接口中每一个方法也是隐式抽象的，声明时同样不需要**abstract**关键字。
* 接口和接口之间可以多继承

```java
[可见度] interface 接口名称 [extends 其他的接口名,其他的接口名,其他的接口名] {
```

* 接口中的方法都是公有的

```java
interface{}{
	public static final int SPEED = 1;
	int SPEED = 2;  //书写时可以省略  public static final
  
	public abstract void fly();
	void wing(); // 书写时可省略  public abstract
}
```

* JDK1.7之前
  *  可定义全局常量
  * 可定义抽象方法
* JDK1.8以后
  * 可定义全局常量
  * 可定义抽象方法
  * 可定义静态方法
  * 可定义默认方法

## 异常

### Throwable

* Error
* Exception
  * 受检异常（Checked|编译时异常)
    * IOException
      * FileNotFoundException
  * 非受检异常（Unchecked| 运行时异常RuntimeException）
    * NullPointerException
    * ArrayIndexOutOfBoundsException

![img](../images/all/exception.png)



```java
try{
  // 程序代码
}catch(异常类型1 异常的变量名1){
  // 程序代码
}catch(异常类型2 异常的变量名2){
  // 程序代码
}catch(异常类型3 e){
  
}finally{
  // 程序代码
}


public class className {
  public void deposit(double amount) throws RemoteException,IOException {
    // Method implementation
    throw new RemoteException();
  }
  //Remainder of class definition
}


public class ExceptionTest {
    public static void main(String[] args) {
        try {
            devide();
        } catch (ArithmeticException e) {
            System.out.println(e.getMessage());
            throw new RuntimeException("除数不能为0");
        }
    }

    public static void devide() throws ArithmeticException {
        int i = 1;
        System.out.println(i / 0);
    
        //不执行
        System.out.println("error.....");
    }

}
```

### 相关要点

* 异常捕获从上到下，父类写到子类下面

* 异常相关内容

  * e.printStackTrace();
  * e.getMessage()

* try中定义的变量，在try结构外面无法使用

* finally 代码块中的内容始终会被执行，无论程序是否出现异常的原因就是，**编译器会将 finally 块中的代码复制两份并分别添加在 try 和 catch 的后面**。

  * try中有return，finally中有return

    * 最终返回return的值，不返回try中的return

  * try中有return，但是finally改变 return 的值

    * 在 return 语句返回之前，虚拟机会将待返回的值压入操作数栈，等待返回，即使 finally 语句块对 i 进行了修改，但是待返回的值已经确实的存在于操作数栈中了，所以不会影响程序返回结果。

      ```java
       public static void main(String[] args){
          int result = test3();
          System.out.println(result);
      }
      
      public static int test3(){
          //try 语句块中有 return 语句时的整体执行顺序
          int i = 1;
          try{
              i++;
              System.out.println("try block, i = "+i);
              return i;
          }catch(Exception e){
              i ++;
              System.out.println("catch block i = "+i);
              return i;
          }finally{
              i = 10;
              System.out.println("finally block i = "+i);
          }
      }
      
      //try block, i = 2
      //finally block i = 10
      //2
      ```

   ```
  
   ```

* 如果一个方法**没有捕获一个检查性异常**，那么该方法**必须使用 throws 关键字来声明**

* 约定：尽量在某个集中的位置进行统一处理，不要到处的使用 try-catch，否则会使得代码结构混乱不堪

* 自定义异常

  * 继承现有异常
    * 所有异常都必须是 Throwable 的子类。
    * 如果希望写一个检查性异常类，则需要继承 Exception 类。
    * 如果写一个运行时异常类，那么需要继承 RuntimeException 
  * 提供序列版本号（serialVersionUID）
  * 重载构造器

# 多线程

![image-20201205182527893](../images/all/jvm-easy.png)

* 每个线程，拥有自己独立的栈、程序计数器；
* 多个线程，共享同一个进程中的结构：方法区、堆；
* 一个Java程序，至少有三个线程：main主线程；gc 垃圾回收线程；异常处理线程；

## 创建

1. 继承Thread类，重写run方法；通过子类实例对象的start() 启动；

2. 实现Runnable接口，实现run方法；将实例对象传给Thread类的带参构造器生成Thread实例，通过 Thread实例的start()方法启动；

3. 实现callable接口，重写call方法（jdk5.0+）；

   > 1. 创建一个实现Callable的实现类；
   > 2. 实现call方法，将此线程需要执行的操作声明在call()中；
   > 3. 创建Callable接口的实现类对象；
   > 4. 将此Callable接口实现类的对象传递到FutureTask构造器中，创建FutureTask对象
   > 5. 将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start
   > 6. 获取Callable中call方法的返回值；

   * FutureTask 是Future接口的唯一实现类
     * 实现了Runnable，可以作为Runnable被线程执行；
     * 实现了Future，可以作为Future得到Callable的返回值
   * 优点：
     * 可以有返回值
     * 可以抛出异常，被外面捕获或获取异常信息；
     * 支持泛型

4. 使用线程池

   * ExecutorService：线程池接口，常见子类ThreadPoolExecutor
     * void execute(Runnable command)
       * 执行任务，没有返回值，一般用来执行Runnable
     * <T> Future<T> submit(Callable<T> task):
       * 执行任务，有返回值时，一般用来执行Callable
   * Executors：工具类、线程池的工厂类，用于创建并返回不同类型的线程池
     * Executors.newCachedThreadPool():创建一个可根据需要创建新线程的线程池；
     * Executors.newFixedThreadPool(n):创建一个可重用固定线程数的线程池；
     * Executors.newSingleThreadExecutor():创建一个只有一个线程的线程池；
     * Executors.newScheduledThreadPool(n):创建一个线程池，可安排在给定延迟后运行命令或定期地执行

   * 好处
     * 提高响应速度：减少创建的时间
     * 降低资源消耗：重复利用线程池中的资源，不需要每次都重新创建
     * 便于线程管理
       * corePoolSize：核心池的大小
       * maximumPoolSize：最大线程数
       * keepAliveTime：线程没有任务时最多保持多长时间后会终止
   * 

```java
public class ThreadTest {
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();

        MyThread2 myThread2 = new MyThread2();
        Thread thread2 = new Thread(myThread2);
        thread2.start();
        
        
        //3. 创建Callable接口的实现类对象；
        MyThread3 myThread3 = new MyThread3();
        //4. 将此Callable接口实现类的对象传递到FutureTask构造器中，创建FutureTask对象
        FutureTask futureTask = new FutureTask(myThread3);
        // 5. 将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start
        Thread thread3 = new Thread(futureTask);
        thred3.start();
        try {
            // 6. 获取Callable中call方法的返回值；
            Object sum = futureTask.get();
            System.out.println("总和为：" + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("in MyThread");
    }
}

class MyThread2 implements Runnable {
    @Override
    public void run() {
        System.out.println("in MyThread2");
    }
}

//1. 创建一个实现Callable的实现类；
class MyThread3 implements Callable {
    //2. 实现call方法，将此线程需要执行的操作声明在call()中；
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i < 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }

}




//线程池
public class MyThreadPool {
    public static void main(String[] args) {
        ExecutorService service = Executors.newFixedThreadPool(5);
        service.execute(new Runnable() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName() + "runnable");
            }
        });
        NumThread2 numThread2 = new NumThread2();
        service.submit(numThread2);
        service.shutdown();
    }

}

class NumThread2 implements Callable {
    //2. 实现call方法，将此线程需要执行的操作声明在call()中；
    @Override
    public Object call() throws Exception {
        int sum = 0;
        System.out.println(Thread.currentThread().getName() + "runnable");
        return sum;
    }

}
```
## 常用方法

### 基本方法

* start()
* run()
* currentThread()  
  * 静态方法，返回当前线程
* getName()
* setName()
* yield()   
  * 释放当前CPU
* join()  
  * 在线程A中调用线程B的join()方法，此时线程A进入阻塞状态，线程B执行完后，线程A才会结束阻塞状态
* sleep(long millitime)
  * 静态方法，单位毫秒
* stop() 
  * depreciate，结束线程
* isAlive()
  * 判断当前线程是否存活
* setDaemon(true)
  * 守护线程

### 调度方法

* 同优先级的线程组成先进先出队列，使用时间片策略
* 对高优先级，使用优先调度的抢占式策略  
  * 高优先级只是高概率抢占到执行权，而非绝对
  * 并不意味着高优先级线程执行完毕后，才会开始执行低优先级的线程

#### 优先级方法

* getPriority()
* setPriority(int newPriority)  

#### 优先级等级

* MAX_PRIORITY: 10
* MIN_PRIORITY:   1
* NORM_PRIORITY:  5

## 线程生命周期

* 新建NEW
* 就绪WAITING/ TIMED_WAITING
* 运行RUNNABLE
* 阻塞BLOCKED
* 死亡TERMINATED

![image-20201207101215811](../images/all/thread-lifetime.png)

## 线程同步

* synchronize：

  1. 隐式锁，出了作用于自动的释放同步监视器
  2. 支持代码块和方法

  * 同步代码块

  ```java
  synchronized (同步监视器){
      //需要同步的方法
  }
  
  //同步监视器，即锁，任何一个类的对象都可以是锁
  // synchronized(this){}    当前实例对象
  //通过实现runnable接口创建多线程，可以考虑使用this当锁
  
  // synchronnized(current_class_name.class){}   当前类对象
  ```

  * 同步方法

  ```java
  [修饰符] [static] synchronized 返回值 funName(){
   // 需要同步的内容  
  }
  
  // 非static方法的锁为this （当前实例对象）
  // static方法的锁为类对象（类本身）
  
  ```

* Lock锁（ JDK 5.0 新增）

  1. 显式锁，需要手动开启和结束同步，自由灵活
  2. 只支持代码块
  3. 相对synchronize,Lock将耗费更少的时间调度线程，性能更好；且更易扩展（提供更多子类）

  * ReentrantLock：

  ```java
  class Ticket implements Runnable {
  
      private int tickets = 100;
      ReentrantLock lock = new ReentrantLock();
  
      @Override
      public void run() {
          while (true) {
              try {
                  lock.lock();
                  if (tickets > 0) {
                      System.out.println(Thread.currentThread().getName() + " ticket left: " + tickets);
                      tickets--;
                  } else {
                      break;
                  }
              }finally {
                  lock.unlock();
              }
          }
      }
  }
  ```

## 线程通信

* 同步代码块/同步方法  中线程通信

  wait/notify/notifyall

  * wait
    * 当前线程进入阻塞状态，并释放同步监视器
  * notify
    * 唤醒某个处于wait状态的线程。如果有多个则唤醒优先级高的
  * notifyall
    * 唤醒所有处于wait状态的线程。
  * 调用者必须是同步代码中的同步监视器，否则会报IllegalMonitorStateException

```java
class Number implements Runnable {
    private int num = 1;
    Object o1 = new Object();

    @Override
    public void run() {
        while (true) {
            synchronized (o1) {
//            synchronized (o1) {
              // notify();
                o1.notify();
                if (num <= 100) {
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + " : " + num);
                    num++;
                    try {
                      // wait();
                        o1.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else {
                    break;
                }
            }
        }
    }
}
```

### sleep 和 wait  的异同

* 相同
  * 都会让当前线程处于阻塞状态
* 不同
  * 声明位置不同
    * Thread类的sleep
    * Object类的wait
  * 调用的前提不同
    * sleep在任何需要的场景调用
    * wait必须在同步代码块或同步方法中调用
  * 同步监视器处理不同
    * sleep不会释放当前的同步监视器
    * wait会释放同步监视器（锁）

# 常用类

## String

* String声明

  * 为final，不可被继承

* String实现

  * 实现了serializable接口：表示可以被序列化；
  * 实现了Comparable接口：表示可以比较大小

* String内部

  * 定义了final char[] value用于存储字符串数据

* String代表不可变的字符序列

  * 当对字符串重新赋值时，需要重新指定内存区域赋值，不能使用原有value进行赋值
  * 当对现有字符串进行连接操作时，也需要重新赋值；
  * 当对现有字符串进行修改操作时，也需要重新赋值；

* String赋值

  * 通过”字面量的形式“给字符串赋值，此时声明在  方法区的 ”字符串常量池“中；
    * 字符串常量池不会存储相等的内容，重复的会复用，引用地址相同；
    * 字符串常量池JVM存放位置
      * jdk 1.6 : 字符串常量池在方法区（永久区）
      * jdk 1.7：字符串常量池在堆空间
      * jdk 1.8：字符串常量池在方法区（元空间）
  * 通过”new + 构造器“给字符串赋值，此时声明在堆空间中；
    * 堆空间对象可以重复，多个对象引用地址不同；
  * String s = new String("abc");  一共创建多几个对象？
    * 2个
      * 一个是堆空间中new的对象；
      * 一个是char[]对应的常量池中的数据”abc“;

  ```java
  String s1 = "abc";  //字面量的定义方式
  String s2 = "abc";
  s1 == s2  // true  
    
    
  Person p1 = new Person("Tom", 12);
  Person p2 = new Person("Tom", 12);
  
  p1.name.equals(p2.name)  // true
  p1.name == p2.name  // true   p1和p2的name都是通过字面量的方式定义，引用自方法区
  
  ```

  * String 拼接
    * 字面量与字面量的拼接结果还是类似字面量赋值：存在方法区常量池；
    * 变量与字面量、变量与变量的拼接结果，存在堆空间；
    * 如果拼接的结果调用了”intern“方法，则返回结果在常量池中

  ```java
  String s1 = "javaEE";
  String s2 = "hadoop";
  
  String s3 = "javaEEhadoop";
  String s4 = "javaEE" + "hadoop";
  String s5 = s1 + "hadoop";
  String s6 = "javaEE" + s2;
  String s7 = s1 + s2;
  
  s3 == s4;  //true
  s3 == s5;  //false 
  s3 == s6;  //false
  s3 == s7;  //false
  s5 == s6;  //false
  s5 == s7;  //false
  s6 == s7;  //false
  
  String s8 = s5.intern();
  s3 == s8 // true
  ```

  

* String的创建

```java
String s00 = "strs";

//本质上是this.value = new char[0];
String s1 = new String();

//this.value = original.value
String s2 = new String(String original);

//this.value = Arrays.copyof(value, value.length);
String s3 = new String(char[] a);
String s4 = new String(char[] a, int startIndex, int count);
```

* String 常用方法
  * int length()
    *  返回字符串的长度
  * char charAt(int index)
    * 返回某所引出的字符
  * boolean isEmpty()
    * 判断是否是空字符串
  * String toLowerCase()
    * 将String中的所有字符转换为小写，并生成副本
  * String toUpperCase()
  * String trim()
    * 返回字符串的副本，忽略前导空白和尾部空白
  * boolean equals(Object obj)
    * 比较字符串的内容是否相同
  * boolean equalsIgnoreCase(String anotherString)
    * 忽略大小写比较
  * String concat(String str)
    * 将制定字符串连接到字符串的结尾，等价于用”+“
  * int compareTo(String anotherString)
    * 比较两个字符串的大小
  * String substring(int beginIndex)
    * 返回一个新的字符串，它是此字符串的从beginIndex开始截取
  * String substring(int beginIndex, int endIndex)
    * 返回一个新的字符串，它是此字符串的从beginIndex开始截取，从endIndex（不包含）截止
  * boolean endsWith(String suffix)
    * 测试此字符串是否以指定的后缀结束
  * boolean startsWith(String prefix)
    * 测试此字符串是否以指定的前缀开始
  * boolean startsWith(String prefix, int toffset)
    * 测试此字符串从指定索引开始的子字符串，是否以指定前缀开始
  * boolean contains（CharSequence s)
    * 当且仅当此字符串包含指定的char值序列时，返回true
  * int indexOf(String str)
    * 返回指定子字符串在此字符串第一次出现处的索引,未找到-1
  * int indexOf(String str, int fromIndex)
    * 返回指定子字符串在此字符串从指定索引开始，第一次出现处的索引,未找到-1
  * int lastIndexOf(String str)
    * 从后往前
  * int lastIndexOf(String str, int fromIndex)
    * 返回指定子字符串在此字符串从指定索引开始，从后往前，第一次出现处的索引,未找到-1
  * String replace(char oldChar, char newChar)
    * 返回一个新的字符串，它是通过用newChar替换此字符串中出现的所有oldChar得到的
  * String replace(CharSequence target, CharSequence replacement)
    * 使用指定的字面值替换序列，替换此字符串所有匹配字面值目标序列的子字符串
  * String replaceAll(String regex, String replacement)
    * 使用给定的replacement替换此字符串所有匹配给定的正则表达式的子字符串
  * String replaceFirst(String regex, String replacement)
    * 使用给定的replacement替换此字符串匹配给定的正则表达式中第一个子字符串
  * boolean matches(String regex)
    * 此字符串是否匹配给定的正则表达式
  * String[] split(String regex)
    * 根据给定正则表达式的匹配拆分此字符串
  * String[] split(String regex,int limit)
    * 根据给定正则表达式的匹配拆分此字符串，最多不超过limit次；如果超过了，剩下的全部放到最后一个元素中

```java
String s1 = "helloworld";
s1.charAt(100);   // Exception ：StirngIndexOutOfBoundsException

s1.lastIndexOf("o"); // 6
s1.lastIndexOf("o", 5); //4
```

* String与其他数据结构转换
  * 基本数据类型、包装类
    * String --> 基本数据类型、包装类
      * 调用包装类的静态方法：parseXxx(str)
    * 基本数据类型、包装类 --> String
      * 调用String重载的valueOf方法
  * 字符数组
    * String -- > char[]
      * 调用String方法：String.toCharArray()
    * char[] -->String
      * 调用String构造器：new String(char[])
  * 字节数组
    * 编码： 字符串->字节
    * 解码：字节->字符串
    * String --> byte[]：编码
      * 调用String方法： 
        * getBytes() ：默认字符集进行转换
        * getBytes(String charsetName) ：指定字符集，进行转换（编码）
    * byte[] -->String：解码
      * 调用String构造器：
        * new String(byte[])
        * new String(byte[] , charsetName)

```java
String str1 = "123"
int num = Integer.parseInt(str1); 
String str2 = String.valueOf(num);

char[] charArray = str1.toCharArray(); //["1","2","3"]
String str3 = new String(charArray);

byte[] bytes = str1.getBytes();  // [49, 50, 51]
byte[] bytes2 = str1.getBytes("gbk");  // [49, 50, 51], 非ASCII会不一样

String str4 = new String(bytes); // "123"
String str5 = new String(bytes2,"gbk"); // "123"
```

### String/StringBuffer/StringBuilder

相同：底层都使用char[] 存储

效率排序：  StringBuilder  >  StringBuffer   > String

* String

  * 不可变的字符序列

  * final 修饰，不可继承

    

* StringBuffer

  * 无final修饰
  * 可变的字符序列
  * 线程安全（synchronized），效率低
  * 初始化大小：capacity 16： new char[16]
  * 扩容：默认扩容至“原长度 * 2 + 2”，并将原数组的内容复制到新数组中

* StringBuilder

  * 无final修饰
  * 可变的字符序列
  * 线程不安全，效率高 （JDK 1.5新增）
  * 初始化及扩容逻辑同StringBuffer
    * 初始化大小：capacity 16： new char[16]
    * 扩容：默认扩容至“原长度 * 2 + 2”，并将原数组的内容复制到新数组中

#### 常用方法

* append(xxx)
  * 插入其他类型会转化成String
* delete(int start, int end)
  * 删除指定位置的内容
* replace(int start, int end, String str)
  * 把[start, end)的内容替换
* insert(int offset, xxx)
  * insert(1, false)   // 会插入"false"字符串
* reverse
* indexOf
* substring
* length
* charAt
* setCharAt
* toString

```java
StringBuffer sb = new StringBuffer();
sb.append(1);
sb.append("1");
sb.append(false);
System.out.println(sb);
System.out.println(sb.length());

// 11false
// 7
```

## 时间

* #### java.lang.System

  * currentTimeMillis()   # 时间戳

```java
long curTime = System.currentTimeMillis();
System.out.println(curTime);  
// 1615608205700
```

* #### java.util.Date 

  * 实例化
    * new Date()    # 创建当前时间的对象
    * new Date(long timestamp)  #创建指定时间戳的对象
  * 常用方法
    * date.toString()  # 当前对象的年月日时分秒
    * date.getTime()  # 当前对象的时间戳

```java
Date date1 = new Date();
System.out.println(date1.toString());  // 当前年月日时分秒
System.out.println(date1.getTime());    // 时间戳
// Sat Mar 13 12:03:25 CST 2021
// 1615608205700

Date date2 = new Date(1615608340539L);
System.out.println(date2);
// Sat Mar 13 12:05:40 CST 2021
```



* #### java.sql.Date  

  * java.util.Date的子类，对应着数据库中的Date类型
  * 实例化
    * new Date(long timestamp)  #创建指定时间戳的对象
  * 常用方法
    * 转换
      * sql -> util
        * java.sql.Date是java.util.Date的子类，所以直接通过多态赋值就可以将java.sql.Date 转化为java.util.Date
      * util -> sql
        * 通过util的getTime，再进行创建

  ```java
  java.sql.Date date3 = new java.sql.Date(1615608340539L);
  System.out.println(date3.toString());
  System.out.println(date3.getTime());
  // 2021-03-13  
  // 1615608340539
  
  // sql -> util
  java.util.Date date4 = new java.sql.Date(1615608340539L);
  
  // util -> sql
  java.util.Date date5 = new java.util.Date();
  java.sql.Date date6 = new java.sql.Date(date5.getTime());
  ```

  

* #### java.text.SimpleDateFormat

  * 对Date类型进行格式化和解析

  * 实例化

    * new SimpleDateFormat(String formatStyle)

  * 常用方法

    * 格式化： 日期Date ->字符串
      * simpleDateFormat.format(date);
    * 解析： 字符换->日期Date
      * simpleDateFormat.parse(formattedDate)

    ```java
    Date date = new Date();
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    String formattedDate = simpleDateFormat.format(date);
    System.out.println(formattedDate);
    
    Date date2 = simpleDateFormat.parse(formattedDate);
    System.out.println(date2);
    ```

* #### java.util.Calendar

  * 抽象类

  * 实例化

    * 调用其静态方法
      * Calendar.getInstance();
    * 创建子类其子类对象（不常用，静态方法底层也是调用该方法）
      * new GregorianCalendar();

  * 常用方法

    * get(int field)
      * Calendar.DAY_OF_YEAR
      * Calendar.DAY_OF_MONTH
      * Calendar.DAY_OF_WEEK
        * 周日是1 ，周一是2 。。。。周六为7
      * Calendar.MONTH
        * 月份从0开始，1月为0， 。。。。 12月为11
    * set(int field, int i)
    * add(int field, int i)
    * getTime()   
      * 返回Date对象
    * setTime(Date  date)   

    ```java
    Calendar calendar1 = Calendar.getInstance();
    int days = calendar1.get(Calendar.DAY_OF_YEAR);
    calendar1.get(Calendar.DAY_OF_MONTH); 
    calendar1.get(Calendar.DAY_OF_WEEK);  // 周日是1 ，周一是2 。。。。周六为7
    calendar1.get(Calendar.MONTH); // 月份从0开始，1月为0， 。。。。 12月为11
    
    callendar1.set(Calendar.DAY_OF_MONTH, 22)
      
    calendar1.add(Calendar.DAY_OF_MONTH, 2)
      
    Date date = calendar1.getTime();
    
    Date date2 = new Date();
    calendar1.setTime(date2);
    ```

    

    



# 枚举类& 注解

# 集合

# 泛型

# IO流

# 网络编程

# 反射
```

```