## 第二章、Java概述

### 1、什么是程序

为了让计算机 解决 某个操作 或 问题 而编写的一系列 ==有序指令的集合==。



### 2、简单的程序示例

```java
public class Test {
    public static void mian(String[] args) {
        System.out.println("Hello World~");
    }
}
```



### 3、Java的发展历程

1995年，sun公司发布了Java的第一个版本

2009年，甲骨文公司收购了sun公司

Java 8 和 Java 11 是 LTS（Long Term Support）长期支持版本，其他的 Java 版本都是 non - LTS（非长期支持版）



### 4、Java的重要特性

A、Java是面向对象的语言；

> 面向对象：只暴露核心特征，但是隐藏实现细节。
>
> 封装：将对象的数据进行封装，隐藏内部实现细节，只允许通过特定的方法来访问。
>
> 继承：通过继承子类可以调用父类的属性和方法，提高代码的复用性和拓展功能。
>
> 多态：子类通过继承父类并重写父类方法，实现同一个父类引用，在运行时指向不同的子类对象，调用同一个方法时，实现不同的功能。
>
> 接口：定义共同的方法，实现接口的类必须重写接口方法。

B、Java语言具有健壮性；

​      Java的 ==强类型机制、异常处理机制、垃圾的自动回收机制== 是Java健壮性的重要保证。

C、Java语言具有跨平台性；

​	不同的操作系统，拥有不同的JVM解释器，能够将 .java源代码 编译后生成的 .class字节码文件，解释为不同平台下的机器码，屏蔽了不同操作系统间的差异，实现了"一次编译，到处运行"。

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\image-20250426110705513.png" alt="image-20250426110705513" style="zoom:50%;" />

D、Java是解释型语言

​	解释型语言：Java、JavaScript、php

​	编译型语言：C、C++

编译型语言：源代码经过编译后，生成的二进制文件能够直接被机器识别，执行效率高；

解释型语言：源代码经过编译后，生成的文件还不能被机器直接识别，需要通过解释器来 ==一边翻译== 为机器能够识别的二进制代码，

​			==一边执行==，执行效率低。



### 5、JDK 和 JRE 的基本介绍

JDK：Java开发工具包，包括：JRE 和 Java 开发工具（java、javac、javadoc等指令）；

JRE：Java运行环境，包括：JVM 和 Java核心类库。

JDK是提供给开发人员使用的，如果用户想运行一个已经开发好的Java程序，只需要将 .java源代码编译后生成的,class字节码文件将给用户，并在计算机中安装JRE就行了



### 6、Java程序执行流程

xxx.java源文件  --->  通过 javac指令 编译生成 xxx.class字节码文件  --->  通过 java指令 运行 程序

==本质是将xxx.class字节码文件装载到不同的JVM解释运行==

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\image-20250426110724208.png" alt="image-20250426110724208" style="zoom:50%;" />



### 7、Java开发注意事项

A、Java源文件以 .java 为扩展名，源文件的基本组成部分是 类(class)，如：Hello类；

B、Java应用程序的执行入口是main()方法，它有固定的书写格式：

```java
public static void main(String[] args) {
    .....
}
```

C、Java严格区分大小写；

D、Java方法由一条条语句构成，每条语句以 " ; " 结尾；

E、大括号成对出现，缺一不可；

F、一个源文件最多有一个public类，其它类的个数不限(源文件中有多个类，每个类在编译后都会对应生成一个.class文件)；
     如果源文件中有public类时，则文件名必须以该类名命名；

G、可以将main方法写在非public类中，然后指定运行非public类，这样程序的入口就是非public类中的main方法。

​	在使用命令行时，JVM虚拟机不会在意类的修饰符，只会寻找main方法。

```java
public class Animal {
    public static void main(String[] args) {
        System.out.println("Animal...");
    }
}

class Dog {
    public static void main(String[] args) {
        System.out.println("Dog...");
    }
}

class Cat {
    public static void main(String[] args) {
        System.out.println("Cat...");
    }
}
```

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\image-20250426110742657.png" alt="image-20250426110742657" style="zoom: 50%;" />



### 8、注释（comment）

A、单行注释：//

B、多行注释：/**/ ，多行注释中不允许有多行注释嵌套

C、文档注释：

​	文档注释中的内容能够被JDK提供 javadoc 指令所解析，生成一套以网页文件形式体现的该程序的说明文档，一般写在类的上方。

​	基本格式：javadoc -d 文件存放路径 -x -y TestCommnet.java 
​		           javadoc -d 文件存放路径：将要生成的文档注释放在指定的文件存放路径

​			 	  -x -y：生成的说明文档中，要生成的哪些标签。

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\image-20250426110757968.png" alt="image-20250426110757968" style="zoom: 67%;" />



### 9、Java代码规范

A、==类和方法的注释== 以 ==javadoc== 的方式来写；
B、非 javadoc 的注释 往往是给 代码的维护者 来看的，着重告诉读者为什么这样写，如何修改，注意什么问题；
C、tab键整体右移动两个字符，Shift + tab整体左移两个字符；
D、运算符 和 = 两边通常各加一个空格，例：1 + 2 + 3 = 6 ；
E、源文件使用 UTF-8 进行编码；
F、行宽不要超过80个字符；
G、代码通常使用 次行风格 和 ==行尾风格==。



### 10、相对路径和绝对路径

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\image-20250426111431508.png" alt="image-20250426111431508" style="zoom:50%;" />

==相对路径==：从当前目录开始定位，形成的一个路径  
==绝对路径==：从根目录开始定位，形成的一个路径



==案例==：如果想从 abc\test100 访问到文件 hello.txt
		相对路径：..\abc2\test200\hello.txt
		绝对路径：d:\abc2\test200\hello.txt

| **DOS窗口使用** | **符号** |       **示例**        |
| --------------- | :------: | :-------------------: |
| 文件路径        |   `\`    | `cd C:\Temp\test.txt` |
| 命令行参数选项  |   `/`    |   `dir /s /b *.txt`   |
