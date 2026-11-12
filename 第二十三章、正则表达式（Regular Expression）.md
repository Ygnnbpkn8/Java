# 第二十三章、正则表达式（Regular Expression）

## 1、基本介绍

<img src="C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\基本介绍.png" alt="基本介绍" style="zoom:80%;" />

![正则表达式底层实现](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\正则表达式底层实现.png)

```java
package com.cyl.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * @Author：cyl
 * @Date：2025-09-24
 * @Description：正则表达式原理
 * @version：1.0
 */
public class RegExpTheory {
    public static void main(String[] args) {
        String content = "1998 年 12 月 8 日，第二代 Java 平台的企业版 J2EE 发布。1999 年6 月，Sun 公司发布了" +
                    "第二代 Java 平台（简称为 Java2）的 3 个版本：J2ME（Java2 Micro Edition，Java2 平台的微型" +
                    "版），应用于移动、无线及有限资源的环境；J2SE（Java 2 Standard Edition，Java 2 平台的" +
                    "标准版），应用于桌面环境；J2EE（Java 2Enterprise Edition，Java 2 平台的企业版），应" +
                    "用 3443 于基于 Java 的应用服务器。Java 2 平台的发布，是 Java 发展过程中最重要的一个" +
                    "里程碑，标志着 Java 的应用开始普及 9889 ";
        //匹配所有四个数字
        //1、匹配规则：\\d表示任意一个数字
        String regString = "(\\d\\d)(\\d\\d)";
        //2、创建模式对象(即：正则表达式对象)
        Pattern pattern = Pattern.compile(regString);
        //3、创建匹配器
        //创建匹配器matcher，按照正则表达式规则(pattern)去匹配字符串content
        Matcher matcher = pattern.matcher(content);
        //4、循环匹配
        /**
         * matcher.find() 完成的任务：
         * 1、matcher根据指定的规则来定位满足规则的字符串(例：1998)
         * 2、在找到匹配的字符串后，将子字符串的开始索引记录到matcher对象的属性int[] group中，即：group[0] = 0;
         *   然后将子字符串的结束索引+1的值记录到group[1] = 4;
         * 3、同时将属性matcher对象中oldLast的属性值设置为：子字符串结束索引+1的值，oldLast = 4，下一次执行find方法会从索引4开始匹配
         *
         * matcher.group(0) 完成对任务：
         *  public String group(int group) {
         *         if (first < 0)
         *             throw new IllegalStateException("No match found");
         *         if (group < 0 || group > groupCount())
         *             throw new IndexOutOfBoundsException("No group " + group);
         *         if ((groups[group*2] == -1) || (groups[group*2+1] == -1))
         *             return null;
         *         return getSubSequence(groups[group * 2], groups[group * 2 + 1]).toString();
         *     }
         * 1、根据 group[0] 和 group[1] 记录的位置，从content字符串开始截取子字符串(substring)，即[0,4)
         *
         */
        while (matcher.find()) {
            System.out.println("找到：" + matcher.group(0));
            System.out.println("第1组匹配到的值：" + matcher.group(1));
            System.out.println("第2组匹配到的值：" + matcher.group(2));
        }
    }
}
```



## 2、元字符

### 2.1、基本介绍

![元字符基本介绍](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\元字符基本介绍.png)



### 2.2、转义号 \\\\

```java
package com.cyl.regexp;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * @Author：cyl
 * @Date：2025-09-24
 * @Description：
 * @version：1.0
 */
public class RegularExpression {
    public static void main(String[] args) {
        String content = "abc$(abc(123(";
        //如何匹配(
        String regularStr = "\\(";
        Pattern pattern = Pattern.compile(regularStr);
        Matcher matcher = pattern.matcher(content);
        while (matcher.find()) {
            System.out.println("找到：" + matcher.group(0));
        }
    }
}
```



### 2.3、字符匹配符

![字符匹配符](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\字符匹配符.png)

![字符匹配符-2](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\字符匹配符-2.png)



#### 2.3.1、应用案例

![字符匹配符应用案例](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\字符匹配符应用案例.png)

![字符匹配符-3](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\字符匹配符-3.png)

![字符匹配符-4](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\字符匹配符-4.png)



### 2.4、选择匹配符

![选择匹配符](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\选择匹配符.png)



### 2.5、限定符

![限定符](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\限定符.png)

![限定符-2](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\限定符-2.png)

![限定符-3](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\限定符-3.png)



### 2.6、定位符

![定位符](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter24\定位符.png)