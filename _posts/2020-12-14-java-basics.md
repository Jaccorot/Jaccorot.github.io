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

