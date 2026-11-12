# 第十一章、异常（Exception）

## 1、异常的引出

```java
package com.cyl.exception;

/**
 * @author cyl
 * @version 8u451
 */
public class Exception01 {
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 0;

        //1、num1 / num2 ---> 10 / 0
        //2、当程序执行到 num1 / num2 时，由于 num2 = 0 会须此时会抛出ArithmeticException算术异常(/ by zero)
        //3、当抛出异常后，程序就退出了，下面的代码就不再执行了
        //4、当一个软件系统出现了这样一个不算致命的问题，就导致程序崩溃，程序的健壮性太差
        //5、因此Java设计者提供了 异常处理机制 来解决该问题
        //6、如果程序员认为一段代码可能会出现异常问题，可以使用try-catch异常处理机制来处理，从而保证系统健壮性
        //7、生成try-catch代码块快捷方式：选中要处理的代码，按快捷键Ctrl + Alt + T（Surround with 围绕在...周围），选择try-catch
        int result = 0;
        try {
            result = num1 / num2;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        System.out.println(result);
    }
}

```



## 2、异常的基本介绍

在 Java 中，将程序执行中发生的不正常情况称为 "异常" （开发过程中，产生的 语法错误 和 代码逻辑错误 不属于异常）。

### 2.1、异常的分类

#### #1、Error

==Error（错误）==：==Java虚拟机无法解决的严重问题==。如：JVM系统内部错误、资源耗尽等严重情况。Error是严重错误，会导致程序崩溃。

​			  例：StackOverFlowError 栈溢出、OOM（out of memory）内存溢出



#### #2、Exception

==Exception（异常）==：==因编程错误 或 偶然的外在因素导致的一般性问题==，可以使用针对性的代码进行处理。

Exception异常分为两大类：

##### 	 编译时异常（==编程时，编译器检查出来的异常==）。

​					SQLException、IOException

##### 	 运行时异常（==程序运行时，发生的异常==）。

​			IllegalArgumentException（参数传入非法，传入一个不在取值范围内的数）

| 场景                 | 示例代码                                                     |
| -------------------- | ------------------------------------------------------------ |
| **参数超出有效范围** | `Thread.sleep(-1000)` → 睡眠时间不能为负数                   |
| **空值不允许**       | 某些工具类要求非空参数却传了 `null`                          |
| **枚举值不匹配**     | `Enum.valueOf()` 传入不存在的枚举名称                        |
| **集合容量非法**     | `new ArrayList<>(-1)` → 初始容量不能为负                     |
| **自定义校验**       | 业务方法中手动 `throw new IllegalArgumentException("年龄不能为负数")` |

​			NumberFormatException（数字格式异常，试图将字符出转换为数字）

```java
public class Test {
    public static void main(String[] args) {
        // 模拟从前端或文件读取的用户输入
        String userInput = "18岁"; 

        // 尝试将字符串转为整数
        int age = Integer.parseInt(userInput); 
        
        System.out.println("年龄是：" + age);
    }
}
```

​			ClassCastException（试图将对象强制转换为不兼容的类型）、NullPointerException、ArithemeticException

​			ArrayIndexOutOfBoundsException（数组越界）

​	           	 例：空指针访问、试图读取不存在的文件、网络连接中断 等。



## 3、异常体系图

### 3.1、异常体系图

![异常体系图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter11\异常体系图.png)

![Serializable](C:\Users\31197\Desktop\Serializable.png)

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter11\异常体系图说明.png" alt="异常体系图说明"  />

==编译时异常==：在通过 javac 指令进行编译时，产生的异常。

==运行时异常==：在通过 java 指令进行编译时，产生的异常。



### 3.2、异常体系图总结

A、异常（Exception）分为两大类：编译时异常 和 运行时异常；

B、==运行时异常==：==编译器不要求强制处理的异常==。一般是指编程时的逻辑错误，是==程序员应当避免其出现的异常==。

​				java.lang.RuntimeException类 以及 其子类都是运行时异常。

​				==对于运行时异常，可以不做处理==，因为这类异常很普遍，全处理可能对程序的可读性 和 运行效率 造成影响。

C、编译时异常：==编译器要求必须处理的异常==。

```java
//编译异常案例
package com.cyl.exception;

import java.io.FileInputStream;

/**
 * @author cyl
 * @version 8u451
 */
public class Exception01 {
    public static void main(String[] args) {
        FileInputStream files;
        //Unhandled exception: java.io.FileNotFoundException
        //未处理的异常：java.io.FileNotFoundException
        files = new FileInputStream("d:\\a.jpg");
        String len;
        
        //Unhandled exception: java.io.IOException
        while ((len = files.read()) != -1) {
            System.out.printf(len);
        }

        //Unhandled exception: java.io.IOException
        files.close();
    }
}
```



### 3.3、常见的运行异常（RuntimeException及其子类）

#### 3.3.1、NullPointerException

**NullPointerException**：空指针异常，当程序试图在需要对象的地方，使用 null 时，会抛出该异常。

```java
package com.cyl.exception;

public class NullPointerException_ {
    public static void main(String[] args) {
        String string = null;
        System.out.println(string.length());
    }
}
```



#### 3.3.2、ArithmeticException

**ArithmeticException**：算术异常，当出现异常的运算条件时，会抛出该异常。例：一个整数  “除以零” 。



#### 3.3.3、ArrayIndexOutOfBoundsException

ArrayIndexOutOfBoundsException：数组下标越界异常，如果索引为 负 或 大于等于数组长度大小，则该索引为非法索引，会抛出该异常。

```java
package com.cyl.exception;

public class ArrayIndexOutOfBoundsException_ {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4};
        //数组下表取值范围为[0 - arr,length - 1]，不能取到arr.length
        for (int i = 0; i <= arr.length; i++) {
            System.out.println(arr[i]);
        }
    }
}
```



#### 3.3.4、ClassCastException 

**ClassCastException（类型转换异常）**：试图将对象转换为不是该实例的子类时，会抛出该异常。

```java
package com.cyl.exception;

public class ClassCastException_ {
    public static void main(String[] args) {
        //向上转型
        A a = new B();
        //向下转型
        B b = (B)a;

        //编译时，a的引用类型为A类，C类继承A类，因此编译器允许这样写，不会报错
        //当运行时，a是B类对象实例的引用，运行类型为B类，B类和C类没有任何关系
        //B类无法向下转型为C类，会抛出ClassCastException
        C c = (C)a;
    }
}

class A {}

class B extends A {}

class C extends A {}
```



#### 3.3.5、IllegalArguementException

**IllegalArguementException**：非法参数异常，



#### 3.3.6、NumberFormatException

**NumberFormatException**：数据格式异常。

  	      **NumberFormatException** extends **IllegalArguementException**  

​		当程序 ==将字符串转换为某个数值类型== 时，该字符串 ==不能转换为与数据类型相匹配的类型== 时，会抛出该异常。

```java
package com.cyl.exception;

public class NumberFormatException_ {
    public static void main(String[] args) {
        String name = "1234海";
        int i = Integer.parseInt(name); //会抛出异常
    }
}
```



### 3.4、常见的编译异常（RuntimeException外的异常）

#### 3.4.1、SQLException

**SQLException**：操作数据库时，查询表可能产生的异常。



#### 3.4.2、IOException

**IOException**：操作文件时，可能产生的异常。



#### 3.4.3、FileNotFoundException

**FileNotFoundException**：当操作一个不存在的文件时，产生的异常。



#### 3.4.4、ClassNotFoundException

**ClassNotFoundException**：加载类的时候，该类不存在产生的异常。



#### 3.4.5、EOFException

**EOFException**：操作文件，到文件末尾，产生的异常。



#### 3.4.6、NoSuchFieldException

**NoSuchFieldException**：在类中找不到指定名称的字段时抛出的异常。

​	通常在使用 ==反射机制访问类的字段== 时，==如果字段不存在，会抛出此异常==。



#### 3.4.7、NoSuchMethodException

**NoSuchMethodException**：在类中找不到指定名称和参数类型的方法时抛出的异常。

​	通常在使用 ==反射机制调用类的方法== 时，==如果方法不存在，会抛出此异常==。



## 4、异常处理

### 4.1、异常处理的方式

#### 4.1.1、try - catch - finally

程序员在程序中捕获异常进行处理。

```java
try {
    //1、可能出现异常的代码
} catch(Exception e) {
    //2.1、当捕获到异常时，系统会将异常传递给 Exception 对象引用 e
    //2.2、在获得异常后，程序员自己处理异常
    //2.3、如果没有异常发生，catch代码块不会被执行
} finally { //可以不写finallY代码块，语法也能通过
    //3.1、不管是否有异常发生，该代码块都要执行
	//3.2、通常将释放资源等操作放到该代码块
}
```



#### 4.1.2、throws（默认处理方式）

将程序中产生的异常抛出，交给方法的调用者来处理该异常，异常最顶级的处理着是 JVM 虚拟机。

例：JVM调用main方法，main方法调用f1()方法，f1()方法调用f2()方法

​	当f2()方法产生异常时，可以使用 try-catch-finally 处理异常 或 throws给f1()方法 进行异常处理

​	同理，f1()方法也有两种处理方式，当 throws 到 JVM虚拟机时，会 ==打印异常信息 并 退出程序==。

如果没有使用 try-catch-finally 显式处理异常，会==默认使用 throws 来处理异常==。

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter11\throws异常处理机制.png" alt="throws异常处理机制" style="zoom:67%;" />



### 4.2、try - catch 使用细节

A、如果 ==异常发生了== ，异常后面的代码就不会继续执行了，而是进入到 catch 代码块中处理异常；

B、如果 ==异常没有发生== ，则会 ==顺序执行 try 代码块中的代码==，不会进入到 catch 代码块；

C、如果希望 ==不管是否发生异常== ，==都能够执行某段代码==（如：关闭连接、释放资源等操作），则 ==可以添加 finally 代码块==。

```java
package com.cyl.exception;

public class TryCatchDetail {
    public static void main(String[] args) {
        //1、try代码块中发生异常后，会进入到catch代码块进行异常处理
        try {
            //String name = "高斯";
            String name = "123";
            int num = Integer.parseInt(name);
            System.out.println("转换后的信息:" + num);
        } catch (NumberFormatException e) {
            System.out.println("异常信息：" + e.getMessage());
        } finally {
            System.out.println("finally代码块被执行...");
        }

        //2、对发生的异常使用try-catch进行处理后，程序不会终止，会继续往下执行
        System.out.println("程序继续执行...");
    }
}

```

D、可以 有多个 catch 代码块，用于 ==捕获不同的异常==（进行不同的业务处理），==要求子类异常在前，父类异常在后==。

​	如：Exception在后，NullPointerException在前，如果发生异常，只会匹配一个异常（匹配更准确的异常）。

​	如果前面写父类的异常，后面的子类异常永远都不会被执行到。

```java
package com.cyl.exception;

public class TryCatchDetail {
    public static void main(String[] args) {
        try {
            Person person = new Person();
            person = null;
            System.out.println(person.getName()); //NullPointerException

            int n1 = 10;
            int n2 = 0;
            int result = n1 / n2; //ArithmeticException
        } catch (NullPointerException e) {
            System.out.println("空指针异常" + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("算术异常" + e.getMessage());
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}

class Person {
    private String name = "Jack";
    public String getName() {
        return name;
    }
}
```

E、可以 try - finally 配合使用，这种用法在发生异常时，==没有使用 catch 进行异常处理，程序会直接退出==。

​	**finally 代码块不管是否有异常发生，该代码块都会被执行，当 try - catch 代码块中有 return 语句时，仍然会执行，finally 会覆盖之前的返回值**。

​	==应用场景==：执行一段代码，不管是否发生异常，都必须执行某个逻辑。

```java
package com.cyl.exception;

public class TryCatchDetail {
    public static void main(String[] args) {
        try {          
            int n1 = 10;
            int n2 = 0;
            int result = n1 / n2; //会产生ArithmeticException
        } finally {
            System.out.println("finally代码块被执行...");
        }
        
        System.out.println("程序继续执行..."); //代码不会被执行，因为try代码块中产生了算术异常，没有进行异常处理，会导致程序崩溃
    }
}
输出结果：
    finally代码块被执行
    ArithmeticException异常信息
```



### 4.3、try - catch 课堂练习

#### 4.3.1、Exercise01

```java
package com.cyl.exception;

public class ExceptionExercise01 {
    public static int method() {
        try {
            String[] name = new String[3];
            if (name[1].equals("jack")) {
                System.out.println(name[1]);
            } else {
                name[3] = "bob";
            }
            return 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            return 2;
        } catch (NullPointerException e) {
            return 3;
        } finally {
            //try catch 代码块会优先执行，虽然有return语句
            //但是finally代码块不管是否有异常发生都会执行，因此会覆盖返回值
            return 4;
        }
    }

    public static void main(String[] args) {
        System.out.println(method());
    }
}
```



#### 4.3.2、Exercise02

```java
package com.cyl.exception;

public class ExceptionExercise01 {
    public static int method() {
        int i = 1;
        try {
            i++;
            String[] name = new String[3];
            if (name[1].equals("jack")) {
                System.out.println(name[1]);
            } else {
                name[3] = "bob";
            }
            return 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            return 2;
        } catch (NullPointerException e) {
            return ++i; //3保存到一个临时变量中，当执行完finally代码块发现并没有返回语句，就会返回异常处理中的返回语句3
        } finally {
            ++i;
            System.out.println("i = " + i); //finally代码块中没有return语句，输出 i = 4，然后去执行return语句，返回3
        }
    }

    public static void main(String[] args) {
        //i = 4
        //3
        System.out.println(method());
    }
}
```



#### 4.3.3、Exercise03

要求：如果用户输入的不是一个整数，就一直提示他重新输入，知道输入为一个整数为止

```java
package com.cyl.exception;

import java.util.Scanner;

public class LoopInput {
    public static void main(String[] args) {
        //如果用户输入的不是一个整数，就一直提示他重新输入，知道输入为一个整数为止

        //思路分析
        //1、创建一个Scanner对象
        //2、使用无限循环去接收一个输入
        //3、然后将输入的值转换成一个int
        //4、在转换的时候，如果抛出异常，说明输入的内容不是一个能够转换成int类型的数据，提示用户重新输入
        //5、在转换的时候，如果没有抛出异常，说明用户输入的内容是一个int类型数据，break跳出循环
        Scanner scanner = new Scanner(System.in);
        String inputNum = "";

        while (true) {
            try {
                System.out.print("请输入一个整数：");
                inputNum = scanner.next();
                int i = Integer.parseInt(inputNum);
                System.out.println("您输入的整数为：" + inputNum);
                break;
            } catch (NumberFormatException e) {
                System.out.println("请输入一个整数...");
            }
        }
    }
}
```



## 5、throws异常处理

### 5.1、基本介绍

A、如果一个方法（中的语句执行时）可能生成某种异常，但是并不能确定如何处理这些异常

​	则此方法应显式的声明抛出异常，表明该方法将不对这些异常进行处理，而==由该方法的调用者负责处理==。

B、在方法中声明中可以 使用 **throws** 声明==抛出异常的列表==，==throws后面的异常类型== 可以是 ==方法中产生的异常类型==，也可以是==它的父类类型==。

```java
package com.cyl.exception;

import java.io.FileInputStream;
import java.io.FileNotFoundException;

public class Throws {
    public static void main(String[] args) {

    }

    // 在方法中声明中可以 使用 throws 声明抛出异常的列表
    // throws后面的异常类型可以是方法中产生的异常类型，也可以是它的父类类型。
    public void f2() throws FileNotFoundException, NullPointerException, ArithmeticException {
        FileInputStream fileInputStream = new FileInputStream("d://a.txt");
    }
}
```



### 5.2、Throws 使用细节

A、对于 ==编译异常==，程序中==必须显式的进行处理==，如：try - catch 或 throws；

B、对于 ==运行异常==，程序中如果没有进行显式的处理，==默认会使用 throws 进行异常处理==；

C、子类重写父类的方法时，对抛出异常的规定：

​	==子类重写的方法所抛出的异常，要么和父类抛出的异常一致，要么为父类抛出的异常类型的子类型==。

```java
class Father {
    pulic void method() throws RuntimeException {
        
    }
}

class Son {
    @Override
    public void method() throws NullPointerException {
        
    }
}
```

D、在 throws 过程中，如果有方法 try - catch ，就相当于处理了异常，可以不用再 throws 抛出异常了。



## 6、自定义异常

### 6.1、自定义异常步骤

A、定义类：自定义异常类名 继承 Exception 或者 RuntimeException；

B、如果 继承Exception类，属于 编译异常；

​      如果 继承RuntimeException类，属于 运行异常。

```java
要求：当接收一个Person对象年龄时，要求范围在 18 - 120 岁之间，否则抛出一个自定义异常，并给出相应提示
package com.cyl.exception;

public class CustomException {
    public static void main(String[] args) {
        Person1 jack = new Person1("Jack", 20);
        System.out.println(jack);
    }
}

class Person1 {
    private String name;
    private int age;

    public Person1(String name, int age) {
        this.name = name;
        setAge(age);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        if (!(age >= 18 && age <=120)) {
            throw new IllegalAgeException("年龄输入有误，需在(18 - 120)之间"); //使用 throw 关键字抛出自定义异常
        }
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person1{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

class IllegalAgeException extends RuntimeException {
    public IllegalAgeException(String message) {
        super(message);
    }
}
```



### 6.2、throw 和 throws 的区别

![throw和throws的区别](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter11\throw和throws的区别.png)



### 6.3、课堂练习

#### 6.3.1、Exercise01

![Exerciseo1](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter11\Exerciseo1.png)

```java
//执行流程分析：
//  	(1)、调用methodA()方法：
//    		A、输出：进入方法A
//    		B、抛出 RuntimeException("制造异常"); 异常
//			C、在异常真正抛出之前，会先去执行 finally 代码块：输出 用A方法的finally
//			D、在执行完 finally 代码块之后，将异常抛给main方法。
//		(2)、在main方法中try代码块中捕获到异常后，会输出异常信息：制造异常
//		(3)、调用methodB()方法：
//			A、输出：进入方法B
//			B、虽然遇到了 return 语句，但在返回之前必须执行finally代码块，输出：调用B方法的finally
//			C、然后执行return语句
```



#### 6.3.2、Exercise02

```java
编写程序，接收命令行的两个参数（整数），计算两数相除，要求使用方法cal（int n1，int n2）
    
对数据格式不正确（NumberformatException）、缺少命令行参数（ArrayIndexOutOfBoundsException）、除 0 （ArithmeticException）进行异常处理。
```

```java
package com.cyl.homework;

public class HomeWork01 {
    public static void main(String[] args) {
        try {
            if (args.length != 2) {
                throw new ArrayIndexOutOfBoundsException("参数输入有误，请正确输入两个参数");
            }

            int num1 = Integer.parseInt(args[0]);
            int num2 = Integer.parseInt(args[1]);

            double result = cal(num1, num2);
            System.out.println("两数相处结果为：" + result);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println(e.getMessage());
        } catch (NumberFormatException e) {
            System.out.println("参数格式异常，请正确输入两个整数");
        } catch (ArithmeticException e) {
            System.out.println("算术异常" + e.getMessage());
        }
    }

    public static double cal(int n1, int n2) {
        return n1 / n2;
    }
}
```

