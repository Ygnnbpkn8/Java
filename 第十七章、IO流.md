# 第十七章、IO流

## 1、文件

### 1.1、文件流

文件在程序中是以流的形式来操作的。

![文件流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\文件流.png)

流：数据在数据源（文件）和 程序（内存）之间 经历的路径

==输入流==：数据 从 数据源（文件）到 程序（内存）的路径。

==输出流==：数据 从 程序（内存）到 数据源（文件）的路径。



### 1.2、常用的文件操作

#### 1.2.1、创建文件对象的相关 构造器 和 方法

a、new File（String pathname）：根据 路径 构造一个File对象。

b、new File（File parent，String child）：根据 父目录文件 + 子路径 构建一个File对象。

c、new File（String parent，String child）：根据 父目录 + 子路径 构建一个File对象。

d、creatNewFile：创建新文件。

```java
package com.cyl.file_;

import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：创建文件对象
 * @version：1.0
 */
public class FileCreate {
    public static void main(String[] args) {

    }

    //1、方式一
    //使用：new File(String pathname)创建文件
    @Test
    public void create01() {
        String filePath = "e:\\news1.txt";
        File file = new File(filePath);
        try {
            file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //2、方式二
    //使用：new File(File parent，String child)创建文件
    @Test
    public void create02() {
        File file = new File("e:\\create02\\haha");
        if (!(file.exists())) {
            file.mkdirs();
        }
        String fileName = "news2.txt";
        //这里只是在内存中创建了File对象，并没有和磁盘发生任何关系
        File file1 = new File(file, fileName);
        try {
            file1.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //获取
        System.out.println(file1.getName());
        //返回的是String类型的文件路径
        System.out.println(file1.getAbsolutePath());
        //返回的是File类型的文件路径，可以将绝对路径的File对象转换为完整路径，会去调用toString来输出文件路径
        System.out.println(file1.getAbsoluteFile());
        //获取文件的父级目录
        System.out.println(file.getParent());
        //判断File对象是否是一个文件
        System.out.println(file.isFile()); //false
        //判断File对象是否是一个路径
        System.out.println(file.isDirectory()); //true
    }

    //3、方式三
    //使用：new File(String parent，String child)创建文件
    public void create03() {
        String parentPath = "e:\\";
        String fileName = "news3.txt";
        File file = new File(parentPath, fileName);
        try {
            boolean newFile = file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



#### 1.2.2、目录的操作 和 文件的删除

a、mkdir（）：创建一级目录。

b、mkdirs（）：创建多级目录。

c、delete（）：删除 空目录 或 文件。

```java
public void m() {
    String directoryPath = "D:\\demo\\a\\b\\c";
    File file = new File(directoryPath);
    if(file.exists()) {
        System.out.println(directoryPath + "已经存在");
    } else {
        if(file.mkdirs()) {
            System.out.println("创建成功");
        } else {
            System.out.println("创建失败");
        }
    }
}
```



## 2、IO流的原理及流的分类

### 2.1、Java IO流原理

A、I/O 是 Input/Output 的缩写，I/O技术是非常实用的技术，用于处理数据传输。如：读/写文件，网络通讯等。

B、Java程序中，对于数据的输入/输出操作以”流（stream)”的方式进行。

C、java.io包下，提供了各种“流”类和接口，用以获取不同种类的数据，并通过方法输入或输出数据。



D、输入input：读取外部数据（磁盘、光盘、数据库、网络等存储设备的数据）到 程序（内存）中。

E、输出output：将程序（内存）数据 输出到 磁盘、光盘、数据库、网络等存储设备中。

![IO流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\IO流.png)



### 2.2、==IO流的分类==

![Java IO流体系图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\Java IO流体系图.png)

#### 2.2.1、按照操作数据单位不同分类

按照操作数据单位不同分为：==字节流（8bit）==，==字符流（按字符的编码不同，对应不同的字节数）==。

字节流方式用于操作：二进制文件，可以做到无损处理。

字符流方式用于操作：文本文件。

==字节流 和 字符流，按照操作方式（输入、输出）分为==：

​		字节输入流（InputStream）、字节输出流（OutputStream）

​		字符输入流（Reader）、字符输出流（Writer）

![字符流和字节流按照输入输出分类](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\字符流和字节流按照输入输出分类.png)



#### 2.2.2、按照数据流的流向不同分类

按照数据流的流向不同，分为：==输入流== 和 ==输出流==。



#### 2.2.3、按照流的角色不同分类

按照流的角色不同，分为：节点流、处理流 / 包装流。



## 3、IO流的常用类

### 3.1、==字节输入流和字节输出流==

#### #1、字节输入流：InputStream

![字节输入流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\字节输入流.png)

##### A、FileInputStream

```java
package com.cyl.inputstream;

import org.junit.jupiter.api.Test;
import java.io.FileInputStream;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：演示FileInputStream的使用(字节输入流：将文件 -传入到-> 程序中)
 * @version：1.0
 */
public class FileInputStreamExercise {
    public static void main(String[] args) {

    }

    @Test
    public void readFile01() {
        //1、设置要读取的文件路径
        String filePath = "e:\\hello.txt";
        int readData = 0;
        FileInputStream fileInputStream = null;
        try {
            //2、创建FileInputStream对象，用于读取文件
            fileInputStream = new FileInputStream(filePath);
            //3、read() 方法从输入流中读取1个字节的数据，返回值为(0-255)，如果没有等待到要读取的数据，此方法将阻塞
            //(如：等待用户输入，用户一直没输入数据，就会导致阻塞)。
            
            //  如果当前没有可用数据，它会阻塞，直到：有数据可读、输入流被关闭 或 发生I/O异常。
            
            //  如果流已到达末尾，则返回 -1，读取某个文件时，会直接读取文件数据不会阻塞。
            
            //  如果文件为空，会直接返回-1结束
            
            //使用read()每次读取一个字节的数据，直到文件读取到末尾
            while ((readData = fileInputStream.read()) != -1) {
                System.out.print((char) readData);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //关闭流
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    //使用read()读取文件，每次只读取一个字节的数据，效率较低
    //可以使用read(byte[] b)来读取文件，每次读取b.length个字节的数据
    @Test
    public void readFile02() {
        String filePath = "e:\\hello.txt";
        int readLen = 0;
        byte[] buf = new byte[8]; //一次读取8个字节的数据
        FileInputStream fileInputStream = null;
        try {
            //创建FileInputStream对象用于读取文件
            fileInputStream = new FileInputStream(filePath);
            //从该字节流中一次性读取buf.length个字节的数据到字节数组。此方法会阻塞等待可读取的数据。
            //如果返回-1，表示读取完毕
            //fileInputStream.read(buf)，如果正常读取，返回的实际读取到的数据(数据不够b.length，最多读取数据的字节数)
            while ((readLen = fileInputStream.read(buf)) != -1) {
                //将byte数组中[0-readLen)位置的数据转换为字符串输出
                System.out.print(new String(buf, 0, readLen));
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



#### #2、字节输出流：OutputStream

##### A、FileOutPutStream

![字节输出流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\字节输出流.png)

```java
package com.cyl.outputstream;

import java.io.FileOutputStream;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：演示字节输出流的使用
 * @version：1.0
 */
public class FileOutputStreamExercise {
    public static void main(String[] args) {

    }

    public void writeFile() {
        //1、创建写入的文件路径
        String filePath = "e:\\hello.txt";
        //2、创建FileOutputStream对象
        FileOutputStream fileOutputStream = null;
        //3、使用字节输出流对象fileOutputStream操作文件filePath
        try {
            //new FileOutputStream(filePath); 写入的数据会覆盖原来文件中的数据
            //new FileOutputStream(filePath, true); 写入的数据会添加到原来文件的末尾
            fileOutputStream = new FileOutputStream(filePath);
            //4.1、使用write(int b)写入1个字节
            fileOutputStream.write('X');

            //4.2、使用write(byte[] b)写入字符串
            String writeStr = "Jack";
            fileOutputStream.write(writeStr.getBytes());

            //4.3、使用write(byte[] b, int off, int len)，从索引位置0开始,写入writeStr.length()个字节的数据
            fileOutputStream.write(writeStr.getBytes(), 0, writeStr.length());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



#### #3、字节输入流和字节输出流 实现文件的拷贝

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\IO流实现文件的拷贝.png" alt="IO流实现文件的拷贝" style="zoom: 80%;" />

**思路分析**：先将文件使用输入流读取到程序中，再使用输出流写入到指定位置。

```java
package com.cyl.outputstream;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：演示文件拷贝
 * @version：1.0
 */
public class FileCopy {
    public static void main(String[] args) {
        //将e:\\hutao.png 拷贝到 d:\\
        //思路分析
        //1、创建文件的输入流，将文件读取到程序中
        String srcFilePath = "e:\\hutao.png";
        String destFilePath = "d:\\";
        byte[] buf = new byte[1024]; //一次读取1024个字节提高效率
        int readLen = 0; //记录每次输入流实际从文件中读取到的字节数
        FileInputStream fileInputStream = null;
        FileOutputStream fileOutputStream = null;

        try {
            fileInputStream = new FileInputStream(srcFilePath);
            fileOutputStream = new FileOutputStream(destFilePath);
            if ((readLen = fileInputStream.read(buf)) != -1) {
                //将读取到的文件数据通过File写入到指定位置
                fileOutputStream.write(buf, 0, readLen);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //关闭输入流和输出流，释放资源
                if (fileInputStream != null) {
                    fileInputStream.close();
                }
                if (fileOutputStream != null) {
                    fileOutputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        //2、创建文件的输出流，键文件拷贝到指定位置

    }
}
```



### 3.2、==字符输入流 和 字符输出流==

#### #1、字符输入流：Reader

##### A、FileReader

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\字符输入流.png" alt="字符输入流" style="zoom:80%;" />

==循环读取，每次读取一个字符==：

```java
package com.cyl.filereader;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：演示字节输入流的使用
 * @version：1.0
 */
public class FileReaderExercise {
    public static void main(String[] args) {
        String filePath = "e:\\story.txt";
        FileReader fileReader = null;
        int data = 0;
        //1、创建字节输入流对象操作文
        try {
            fileReader = new FileReader(filePath);
            //2、循环读取文件
            while (true) {
                try {
                    //每次读取一个字符交给data
                    while ((data = fileReader.read()) != -1) {
                        System.out.print((char) data);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (fileReader != null) {
                try {
                    fileReader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```



==循环读取，每次读取多个字符==：

```java
package com.cyl.filereader;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-18
 * @Description：演示字节输入流的使用
 * @version：1.0
 */
public class FileReaderExercise {
    public static void main(String[] args) {

    }

    public void test() {
        char[] buf = new char[8];
        int readLen = 0;
        String filePath = "e:\\story.txt";
        FileReader fileReader = null;
        //1、创建字节输入流对象操作文
        try {
            fileReader = new FileReader(filePath);
            //2、循环读取文件
            while (true) {
                try {
                    //每次读取多个字符，将读取到的字符个数保存
                    while((readLen = fileReader.read(buf)) != -1) {
                        //从buf数组读取readLen个字符转换为String输出
                        System.out.print((new String(buf, 0, readLen)));
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (fileReader != null) {
                try {
                    fileReader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



#### #2、字符输出流：Writer

##### A、FileWriter

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\字符输出流.png" alt="字符输出流" style="zoom:67%;" />

```java
package com.cyl.filewriter;

import org.junit.jupiter.api.Test;

import java.io.FileWriter;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-19
 * @Description：演示字符输出流的使用
 * @version：1.0
 */
public class FileWriterExercise {
    public static void main(String[] args) {
        
    }

    @Test
    public void test() {
        String filePath = "e:\\note.txt";
        FileWriter fileWriter = null;
        char[] chars = {'a', 'b', 'c'};
        //1、创建FileWriter对象，对note.txt文件进行写入操作
        try {
            try {
                fileWriter = new FileWriter(filePath); //默认是覆盖
                //(1)、写入单个字符
                fileWriter.write('H');
                //(2)、写入指定字符串
                fileWriter.write(chars);
                //(3)、从char[]的索引off位置开始，写入len个字符
                fileWriter.write(chars, 0, 2);
                //(4)、写入整个字符串
                fileWriter.write("你好北京...");
                //(5)、从字符串的索引off位置开始，写入len个字符
                fileWriter.write("梦回S8", 0, 2);
            } catch (IOException e) {
                e.printStackTrace();
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //对于FileWriter来说，一定要关闭流 或者 flush 才能真正的把数据写入到文件中
            try {
                fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



### 3.2、==节点流 和 处理流（包装流）==

#### 3.2.1、基本介绍

##### #1、节点流

节点流：可以对一个特定的数据源进行读写数据，如：FileReader、FileWriter。

![节点流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\节点流.png)



##### #2、处理流（包装流）

处理流：又叫包装流，是连接在已存在的流（节点流 或 处理流）之上，为程序提供更为强大的读写功能。如·：BufferReader、BufferWriter。

![处理流](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\处理流.png)

##### #3、节点流 和 处理流 一览图

![节点流和处理流一览图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\节点流和处理流一览图.png)



##### #4、节点流 和 处理流 的区别和联系

A、==节点流==：底层流 / 低级流，直接跟数据源相接。

B、==处理流==（包装流）：==可以消除不同节点流的实现差异==，也可以==提供更多便捷的方法完成数据的输入 和 输出==。

​      ==处理流==（包装流）：==对节点流进行了包装，使用了修饰器设计模式，**不会直接与数据源相连**==。



==处理流的功能主要体现在以下两个方面==：

（1）、性能的提高：主要以：==增加缓冲的方式来提高输入输出的效率==。

（2）、操作的便捷：==处理流通过对已有的输入输出流进行包装，提供了效率更高来输出输出数据==，使用起来更加灵活方便。



#### 3.2.2、==字符缓冲流== BufferedReader 和 BufferedWriter

##### #1、BufferedReader

```java
/* 使用 BufferedReader 读取文本文件，并显示在控制台 */
```

```java
package com.cyl.reader_;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.util.LinkedHashSet;

/**
 * @Author：cyl
 * @CreateTime：2025-06-19
 * @Description：演示BufferedReader的使用
 * @version：1.0
 */
public class BufferedReader_ {
    public static void main(String[] args) throws Exception {
        String filePath = "e:\\note.txt";
        //1、创建BufferedReader包装流
        BufferedReader bufferedReader = new BufferedReader(new FileReader(filePath));
        //2、使用BufferedReader流读取文件信息
        String lineStr;
        //按行读取文件数据，当返回null时代表数据读取完毕
        while ((lineStr = bufferedReader.readLine()) != null) {
            System.out.println(lineStr);
        }
        //3、关闭最外层的包装流(处理流)就可以了，底层会自动关闭节点流
        bufferedReader.close();
    }
}
```



##### #2、BufferedWriter

```java
/* 使用 BufferedWriter 将 "HelloWorld" 写入到文件中 */
```

```java
package com.cyl.reader_;

import java.io.BufferedWriter;
import java.io.FileWriter;

/**
 * @Author：cyl
 * @CreateTime：2025-06-19
 * @Description：演示BufferedWriter的使用
 * @version：1.0
 */

public class BufferedWriter_ {
    public static void main(String[] args) throws Exception {
        String filePath = "e:\\note.txt";
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(filePath, true));
        
        bufferedWriter.write("HelloWorld1");
        bufferedWriter.newLine();
        
        bufferedWriter.write("HelloWorld2");
        bufferedWriter.newLine();
        
        bufferedWriter.write("HelloWorld3");
        bufferedWriter.newLine();
        
        bufferedWriter.close();
    }
}
```



##### #3、使用 BufferedReader 和 BufferedWriter 完成文件的拷贝

```java
package com.cyl.reader_;

import java.io.*;

/**
 * @Author：cyl
 * @CreateTime：2025-06-20
 * @Description：完成文件的拷贝
 * @version：1.0
 */
public class BufferedCopy {
    public static void main(String[] args) {
        String srcFilePath = "e:\\a1.txt";
        String destFilePath = "e:\\a2.txt";

        BufferedReader bufferedReader = null;
        BufferedWriter bufferedWriter = null;
        String lineStr = "";

        try {
            //BufferedReader 和 BufferWriter用于操作字符文件，如果操作字节(二进制)文件会造成文件损坏
            //常见的二进制文件：声音、视频、doc文档、pdf文档
            bufferedReader = new BufferedReader(new FileReader(srcFilePath));
            bufferedWriter = new BufferedWriter(new FileWriter(destFilePath));
            //1、将文件数据读取到程序中
            while ((lineStr = bufferedReader.readLine()) != null) {
                //2、每读取一行数据到程序中， 将文件数据拷贝到指定位置
                bufferedWriter.write(lineStr);
                //3、会根据不同的系统插入换行符
                bufferedWriter.newLine();
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //最终关闭的仍然是FileReader和FileWriter
                if (bufferedReader != null) {
                    bufferedReader.close();
                }
                if (bufferedWriter != null) {
                    bufferedWriter.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



#### 3.2.3、==字节缓冲流== BufferedInputStream 和 BufferedOutputStream

##### #1、BufferedInputStream

##### #2、BufferedOutputStream

##### #3、使用 BufferedInputStream 和 BufferedOutputStream 完成对 图片/视频 的拷贝

```java
package com.cyl.outputstream;

import java.io.*;

/**
 * @Author：cyl
 * @CreateTime：2025-06-20
 * @Description：使用BufferedInputStream和BufferedOutputStream完成对图片的拷贝
 * @version：1.0
 */
public class BufferedInputStreamAndOutputStream {
    public static void main(String[] args) {
        String srcPath = "e:\\hutao.png";
        String destPath = "e:\\hutao1.png";
        BufferedInputStream bufferedInputStream = null;
        BufferedOutputStream bufferedOutputStream = null;
        int readLen = 0;
        try {
            bufferedInputStream = new BufferedInputStream(new FileInputStream(srcPath));
            bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(destPath));
            byte[] buf = new byte[1024];
            //循环的读取文件，并写入到destPath
            while ((readLen = bufferedInputStream.read(buf)) != -1 ) {
                bufferedOutputStream.write(buf, 0, readLen);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (bufferedInputStream != null) {
                    bufferedInputStream.close();
                }
                if (bufferedOutputStream != null) {
                    bufferedOutputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



### 3.5、==对象处理流==  ObjectInputStream  和  ObjectOutputStream

#### 3.5.1、需求分析

A、将 int num = 100; 这个 int类型 的数据保存到文件中，这里的100不只是单纯的数字100，而是 int 类型的 100，能够从文件中直接恢复 int num = 100;

B、将 Dog dog = new Dog(“小黄”, 3); 这个Dog对象保存到文件中，并且能够通过文件恢复该对象；

==上诉需求需要：将 基本数据类型 或者 对象 进行 序列化和反序列化的操作==。



#### ==3.5.2、序列化（ObjectOutputStream） 和 反序列化（ObjectInputStream  ）==

==序列化==：==把 Java 对象的值 和 类型信息 转换为字节序列，以便保存到文件 或 通过网络传输==。

==反序列化==：==把字节序列恢复为原来的 Java 对象，包括对象的值和类型==。

如果希望某个对象支持序列化机制，则：

​	必须让其类时可序列化的，为了让某个类是可序列化的，该类必须实现接口：Serializable 或 Externalizable。

​	Serializable：这是一个标记接口，没有方法需要实现。

​	Externalizable：该接口有方法需要实现，一般选择Serializable序列化接口。



#### 3.5.3、基本介绍

ObjectInputStream  和  ObjectOutputStream：提供了对 基本数据类型 和 对象类型 的序列化和反序列化的方法。

ObjectInputStream：用于反序列化（读对象）。

ObjectOutputStream：用于序列化（写对象）。



#### 3.5.4、应用案例

使用 ObjectInputStream 和 ObjectOutputStream 序列化 一个基本数据类型 和 一个Dog对象（name, age），并保存到 data.dat 文件中。

##### #1、文件序列化

```java
package com.cyl.outputstream;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.Serializable;

/**
 * @Author：cyl
 * @CreateTime：2025-06-21
 * @Description：演示ObjectOutputStream的使用
 * @version：1.0
 */
public class ObjectOutputStreamExercise {
    public static void main(String[] args) {
        //序列化后，保存到文件格式不是纯文本，而是按照序列化规定的格式保存
        String filePath = "e:\\data.ha";
        ObjectOutputStream objectOutputStream = null;
        //将文件进行序列化
        try {
            objectOutputStream = new ObjectOutputStream(new FileOutputStream(filePath));
            objectOutputStream.writeInt(100);
            objectOutputStream.writeByte(1);
            objectOutputStream.writeBoolean(true);
            objectOutputStream.writeObject(new Dog("金毛", 6));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        try {
            objectOutputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

class Dog implements Serializable {
    private String name;
    private int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



##### #2、文件反序列化

```java
package com.cyl.inputstream;

import com.cyl.outputstream.Dog;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;

/**
 * @Author：cyl
 * @CreateTime：2025-06-21
 * @Description：演示反序列化的使用
 * @version：1.0
 */
public class ObjectInputStreamExercise {
    public static void main(String[] args) {
        String srcFilePath = "e:\\data.ha";
        ObjectInputStream objectInputStream = null;
        try {
            objectInputStream = new ObjectInputStream(new FileInputStream(srcFilePath));
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            System.out.println(objectInputStream.readInt());
            System.out.println(objectInputStream.readByte());
            System.out.println(objectInputStream.readBoolean());
            Object object = objectInputStream.readObject();
            Dog dog = (Dog) object;
            System.out.println(dog.getName() + dog.getAge());
            System.out.println(object); //这里底层会将Object转换为Dog对象
            objectInputStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```



#### 3.5.5、序列化 和 反序列化 使用细节

A、读写顺序要一致（序列化写入的数据是按照顺序进行的，反序列化需按照顺序来读取）。

B、要求实现序列化 或 反序列化 的对象，需要实现Serializable。

C、==序列化的类中建议添加SerialVersionUID，可以提高版本的兼容性==。

D、序列化对象时，默认将里面所有属性都进行了序列化，除了：==static 或 transient 修饰的成员不参与序列化==。

E、序列化对象时，要求里面属性的类型也需要实现序列化接口。

F、序列化具备可继承性，也就是如果某类已经实现了序列化，则它的所有子类也已经默认实现了序列化。



### 3.6、==标准输入流 和 标准输出流==

#### 3.6.1、基本介绍

|    类型    |                          实现类                          | 默认设备 |
| :--------: | :------------------------------------------------------: | :------: |
| 标准输入流 | 编译类型为InputStream，实际运行类型为BufferedInputStream |   键盘   |
| 标准输出流 |           编译类型 和 运行类型都是 PrintStream           |  显示器  |

==System.in==：

![image-20250623091250306](C:\Users\31197\AppData\Roaming\Typora\typora-user-images\image-20250623091250306.png)

```java
System类下面的静态属性：public static final InputStream in = null;
System.in：
    编译类型为：InputStream 字节输入流
    运行类型为：BufferedInputStream 字节处理流（通过包装某个已有的字节输入流（如：FileInputStream），在其上面加入缓冲机制，来提高读取效率）
```



==System.out==：

![image-20250623091320927](C:\Users\31197\AppData\Roaming\Typora\typora-user-images\image-20250623091320927.png)

```java
System类下面的静态属性：public static final PrintStream out = null;
System.in：
    编译类型 和 运行类型都是：PrintStream。
```



### 3.7、==转换流== InputStreamReader 和 OutputStreamWriter

InputStreamReader：把字节输入流转换为字符输入流。

OutputStreamWriter：把字节输出流转换为字符输出流。

#### 3.7.1、乱码问题

![乱码问题](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\乱码问题.png)

```java
package com.cyl;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：演示转换流的使用
 * @version：1.0
 */
public class TransFormation {
    public static void main(String[] args) {
        //读取e:\\a.txt文件到程序中
        //1、创建字符输入流BufferedReader读取文件
        String filePath = "e:\\a.txt";
        String redLine;
        try {
            BufferedReader bufferedReader = new BufferedReader(new FileReader(filePath));
            while ((redLine = bufferedReader.readLine()) != null) {
                System.out.println(redLine);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

在使用字符处理流来处理UTF-8文件时不会出现乱码问题（IDEA默认编码格式）

使用字符处理流来处理其他编码格式的文件时，如：ANSI等编码会出现乱码，需要指定字节流的编码格式，可以使用转换流来指定编码格式。



#### 3.7.2、InputStreamReader 和 InputStreamWriter

##### #1、InputStreamReader

**InputStreamReader**：Reader的子类，可以将 InputStream（字节输入流）包装成Reader（字符输入流）。	

![InputStreamReader](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\InputStreamReader.png)

```java
将字节流FileInputStream 转换为 InputStreamReader 对文件进行读取（按照UTF-8的格式），进而包装为为BufferedReader
```

```java
package com.cyl.transformation;

import java.io.*;
import java.nio.charset.Charset;
import java.nio.charset.StandardCharsets;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：将字节流FileInputStream 转换为 InputStreamReader 对文件进行读取（按照UTF-8的格式），进而包装为BufferedReader
 * @version：1.0
 */
public class InputStreamReaderExercise {
    public static void main(String[] args) throws IOException {
        String filePath = "e:\\a.txt";
        InputStreamReader inputStreamReader;
        String readLine = null;

        inputStreamReader = new InputStreamReader(new FileInputStream(filePath), Charset.defaultCharset());
        BufferedReader bufferedReader = new BufferedReader(inputStreamReader);


        while ((readLine = bufferedReader.readLine()) != null) {
            System.out.println(readLine);
        }

        inputStreamReader.close();

    }
}
```





##### #2、OutputStreamWriter

**OutputStreamWriter**：Writer的子类，可以将 OutputStream(字节输出流）包装成Writer（字符输出流）

![OutputStreamWriter](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\OutputStreamWriter.png)

```java
将字节流FileOutputStream包装为字符流OutputStreamWriter，对文件进行写入（按照jbk格式 或者 utf-8）
```

```java
package com.cyl.transformation;

import java.io.BufferedWriter;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：将字节流FileOutputStream包装为字符流OutputStreamWriter，对文件进行写入（按照jbk格式 或者 utf-8）java
 * @version：1.0
 */
public class OutputStreamWriterExercise {
    public static void main(String[] args) throws IOException {
        String filePath = "e:\\a.txt";
        char[] add = {'韩', 'G'};
        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(new FileOutputStream(filePath), "GBK");
        BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
        bufferedWriter.write("1234");
        bufferedWriter.write('A');
        bufferedWriter.write(add);

        bufferedWriter.close();
    }
}
```



### 3.8、==打印流 PrintStream 和 PrintWriter==

打印流只有输出流，没有输入流。

#### 3.8.1、PrintStream

==PrintStream 是 OutputStream 的子类，是字节输出流，数据只能按照字节进行输出==。

![PrintStream](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\PrintStream.png)

```java
package com.cyl.printstream;

import java.io.IOException;
import java.io.PrintStream;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：演示PrintStream（字节输出流）的使用
 * @version：1.0
 */
public class PrintStreamExercise {
    public static void main(String[] args) throws IOException {
        PrintStream out = System.out;
        
        //默认情况下，PrintStream输出数据的标准位置是：标准输出，即：显示器
        out.println("hello world"); //底层调用的是write方法来输出数据
        out.write("欢迎来到我的世界".getBytes());
        out.close();
        
        //如何修改打印流输出的位置
        //即：将标准输出流 System.out 替换为用户提供的 PrintStream 实例
        //1、指定打印流输出的位置
        System.setOut(new PrintStream("e:\\hello.txt"));
        //2、输出内容
        System.out.println("时间会治愈所有伤口");
    }
}
```



#### 3.8.2、PrintWriter

![PrintWriter](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\PrintWriter.png)

```java
package com.cyl.printwriter;

import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：演示PrintWriter的使用
 * @version：1.0
 */
public class PrintWriterExercise {
    public static void main(String[] args) throws IOException {
        //1、字符打印流的位置设置为标准输出（控制台）
        PrintWriter printWriter = new PrintWriter(System.out);
        printWriter.print("你好北京");
        //2、如果希望字符打印流的位置在某个特定的文件位置
        //PrintWriter printWriter1 = new PrintWriter("e:\\beijing.txt"); 这种创建方式默认会创建FileWriter对象传入给Writer out
        //默认的方式无法指定编码格式、字符加入/覆盖
        PrintWriter printWriter1 = new PrintWriter(new FileWriter("e:\\beijing.txt"));
        printWriter1.print("北京欢迎您");
        
        printWriter.close(); //在关闭流的时候才会将数据写入指定位置
        printWriter1.close();
    }
}
```



### 3.9、==Properties类（文件配置）==

#### 3.9.1、基本介绍

![Properties](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\Properties.png)

A、Properties是 Hashtable的子类，是==以键值对格式来存储数据==，并且==不能存放null键和null值==，用于专门读取配置文件的集合类。

B、==键值对不需要有空格，值不需要用引号括起来，默认是String类型==。

C、==一般用户名 和 密码等信息都写在配置文件properties中，如果需要修改会涉及到properties文件的读取和写入操作==。

![在程序中对配置文件进行读写操作](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\在程序中对配置文件进行读写操作.png)

```java
//使用Properties类来读取文件
package com.cyl.properties_;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-22
 * @Description：演示对Properties文件的使用
 * @version：1.0
 */
public class PropertiesExercise {
    public static void main(String[] args) throws IOException {
        //1、读取配置文件mysql.properties，并读取/修改相应的信息
        //为了实现跨平台，建议将路径修改为："src/mysql.properties"，\在java中是转义字符， JVM 会自动把/转换成操作系统的实际路径分隔符
        BufferedReader bufferedReader = new BufferedReader(new FileReader("src/mysql.properties"));
        String line = "";
        while ((line = bufferedReader.readLine()) != null) {
            String[] split = line.split("="); //将按行读取到的字符串，按照 = 划分成多个字符串
            if (split.length == 2) {
                String str1 = split[0].trim(); //trim()去掉字符串中的首尾空格
                String str2 = split[1].trim();
                System.out.println(str1 + "值为：" + str2);
            }
        }

        bufferedReader.close();
    }
}
```



#### 3.9.2、Properties常用方法

```java
load：加载配置文件的键值对到Properties
list：将数据显示到指定设备
getProperty(key)：根据键获取相对应的值
setProperty(key, value)：设置键值对到Properties对象
store：将Properties中的键值对存储到配置文件，在idea中，保存信息到配置文件，如果含有中文，会使用Unicode编码存储
```



##### #1、使用Properties类来读取配置文件

```java
//使用Properties类来读取配置文件
package com.cyl.properties_;

import java.io.*;
import java.util.Properties;

/**
 * @Author：cyl
 * @CreateTime：2025-06-23
 * @Description：使用Properties来读取配置文件
 * @version：1.0
 */
public class Properties01 {
    public static void main(String[] args) throws IOException {
        //1、创建Properties对象
        Properties properties = new Properties();
        //2、加载指定的配置文件
        properties.load(new FileReader("src/mysql.properties"));
        //3、把k-v显示到指定位置
        //list(PrintStream out)
        //list(PrintWriter out)
        //3.1、字节打印流的out：public final static PrintStream out = null;
        //3.2、字符打印流的out：protected Writer out;
        properties.list(System.out);
        //4、根据key获取对应的value
        String user = properties.getProperty("user");
        String pwd = properties.getProperty("pwd");
        System.out.println("用户名：" + user + " 密码：" + pwd);
    }
}

```



##### #2、添加新的键值对到配置文件中

```java
package com.cyl.properties_;

import java.io.*;
import java.util.Properties;

/**
 * @Author：cyl
 * @CreateTime：2025-06-23
 * @Description：演示Properties增加键值对到配置文件中
 * @version：1.0
 */
public class Properties02 {
    public static void main(String[] args) throws IOException {
        //1、创建Properties对象
        Properties properties = new Properties();
        //2、创建新的键值对
        //如果key不存在则是添加，如果key已经存在了，则是替换相应的value值
        properties.setProperty("charset", "utf-8");
        properties.setProperty("user2", "马克");
        properties.setProperty("age", "18");
        properties.setProperty("pws", "10086");
        //3、使用store()将新的键值对添加到配置文件中，null代表不在配置文件顶部生成文件注释
        /*
            #Mon Jun 23 09:41:12 CST 2025
            age=18
            user=tom
            pwd=888888
            user2=\u9A6C\u514B
         */
        //Properties采用 ISO-8859-1 编码格式，不支持直接写入非ASCII码的字符
        //(1)当使用字节流 FileOutputStream，会把非ASCII码字符(如：中文)转换为Unicode编码
        properties.store(new FileOutputStream("src/mysql2.properties"), null);

        //(2)当使用字符流 FileWriter 写入中文时，系统默认会使用UTF-8或默认编码格式进行处理，会导致中文乱码
        System.out.println("添加成功");
    }
}
```



## 4、本章作业

### 4.1、HomeWork01

![HomeWork01](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\HomeWork01.png)

```java
package com.cyl.homework;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;

/**
 * @Author：cyl
 * @CreateTime：2025-06-23
 * @Description：
 * @version：1.0
 */
public class HomeWork01 {
    public static void main(String[] args) throws IOException {
        //1、判断E盘下面是否存在目录mytemp，如果没有就创建mytemp
        String directoryPath = "e:/mytemp";
        File file = new File(directoryPath);
        if (!file.exists()) {
            file.mkdirs();
            System.out.println(directoryPath + "文件创建成功");
        } else {
            System.out.println(directoryPath + "文件已经存在,不再重复创建");
        }

        String filePath = directoryPath + "/hello.txt";
        file = new File(filePath);
        if (!file.exists()) {
            //创建文件
            if (file.createNewFile()) {
                System.out.println(filePath + "文件创建成功");
            } else {
                System.out.println(filePath + "文件创建失败");
            }
        } else {
            System.out.println(filePath + "文件已经存在，不再重复创建");
        }
    }
}
```



### 4.2、HomeWork02

![HomeWork02](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\HomeWork02.png)

```java
package com.cyl.homework;

import java.io.*;

/**
 * @Author：cyl
 * @CreateTime：2025-06-24
 * @Description：HomeWork02
 * @version：1.0
 */
public class HomeWork02 {
    public static void main(String[] args) {
        String filePath = "e:\\test.txt";
        InputStreamReader inputStreamReader = null;
        BufferedReader bufferedReader = null;
        String readLine = null;
        int count = 0;

        try {
            inputStreamReader = new InputStreamReader(new FileInputStream(filePath), "gbk");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }

        bufferedReader = new BufferedReader(inputStreamReader);
        try {
            while ((readLine = bufferedReader.readLine()) != null) {
                System.out.println(++count + "、" + readLine);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                inputStreamReader.close();
                bufferedReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
}
```



### 4.3、HomeWork03

![HomeWork03](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter17\HomeWork03.png)

```java
package com.cyl.homework;

import java.io.*;
import java.util.Properties;

/**
 * @Author：cyl
 * @CreateTime：2025-06-24
 * @Description：
 * @version：1.0
 */
public class HomeWork03 {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        //1、设置配置文件路径
        String filePath = "src/dog.properties";
        //2、创建Properties对象操作配置文件
        Properties properties = new Properties();
        //3、加载文件路径
        properties.load(new FileReader(filePath));
        //4、输出配置文件信息
        //properties.list(System.out);//将配置文件信息，使用标准输出到控制台
        String name = properties.getProperty("name");
        String age = properties.getProperty("age");
        String color = properties.getProperty("color");

        Dog dog = new Dog(name, Integer.parseInt(age), color);
        System.out.println(dog);

        //序列化
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream("e:/dog.dat"));
        objectOutputStream.writeObject(dog);
        objectOutputStream.close();

        //反序列化后
        method();
    }

    public static void method() throws IOException, ClassNotFoundException {
        String srcFilePath = "e:/dog.dat";
        ObjectInputStream objectInputStream = null;
        try {
            objectInputStream = new ObjectInputStream(new FileInputStream(srcFilePath));
        } catch (IOException e) {
            e.printStackTrace();
        }
        Dog dog = (Dog) objectInputStream.readObject();
        
        //输出反序列化后的Dog对象
        System.out.println(dog);

        objectInputStream.close();
    }
}

class Dog implements Serializable {
    private String name;
    private int age;
    private String color;

    public Dog(String name, int age, String color) {
        this.name = name;
        this.age = age;
        this.color = color;
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
        this.age = age;
    }

    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", color='" + color + '\'' +
                '}';
    }
}
```



## 5、==flush 和 shutdownOutput 的区别==

|        方法        |                         作用                          |
| :----------------: | :---------------------------------------------------: |
|     `flush()`      |   把写入的数据立刻发出，不能等 close 才发，避免延迟   |
| `shutdownOutput()` | 告诉服务器“我写完了”，否则服务器会阻塞在 `readLine()` |

==服务器端==：

```java
import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8888);
        System.out.println("服务器启动，等待连接...");
        Socket socket = serverSocket.accept();
        System.out.println("客户端已连接");

        // 接收客户端发送的数据
        InputStream inputStream = socket.getInputStream();
        BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));

        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println("收到客户端消息: " + line);
        }
        System.out.println("客户端已发送完毕");

        reader.close();
        socket.close();
        serverSocket.close();
    }
}
```

==客户端==：

```java
import java.io.*;
import java.net.Socket;

public class SocketClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 8888);
        OutputStream outputStream = socket.getOutputStream();
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(outputStream));

        writer.write("你好，服务器！\n");
        writer.write("这是客户端发送的第二行\n");
        writer.flush(); // 刷新缓冲区，确保数据立即发送

        socket.shutdownOutput(); // 通知服务器我已经写完了

        writer.close();
        socket.close();
    }
}
```



```java
BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
writer.write("hello world\n"); // 有换行
writer.flush(); // 发送数据了，但没有关闭输出流
// writer.close(); // ← 没调用这行，也没 shutdownOutput()

BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
String line;
while ((line = reader.readLine()) != null) {
    System.out.println("接收到: " + line);
}
System.out.println("客户端结束输入");
```

