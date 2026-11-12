# 第二十二章、JDBC和连接池

## 1、基本介绍

A、JDBC为访问不同的数据库提供了统一的接口，为使用者屏蔽了细节问题。

B、Java程序员使用JDBC，可以连接任何提供了JDBC驱动程序的数据库系统，从而完成对数据库的各种操作。

C、JDBC原理图：

![jdbc原理图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\jdbc原理图.png)

D、JDBC相关的类和接口在：java.sql 和 javax.sql 包中。



## 2、JDBC编写程序步骤

![JDBC编写程序步骤](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC编写程序步骤.png)

### 2.1、案例演示

![JDBC案例演示](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC案例演示.png)

```java
package com.cyl.jdbc;

import com.mysql.cj.jdbc.Driver; //mysql 8.0以上的版本使用新的驱动类，旧版本的驱动类为com.mysql.jdbc.Driver

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-12
 * @Description：演示JDBC的使用
 * @version：1.0
 */
public class Jdbc01 {
    public static void main(String[] args) throws SQLException {
        //在进行JDBC操作之前，需要在项目中先新建一个Directory，将jar包导入项目
        //1、注册驱动
        Driver driver = new Driver();

        //2、得到连接
        //(1)、jdbc:mysql:// 表示协议，通过jdbc的方式连接mysql数据库
        //(2)、主机地址 或 ip地址
        //(3)、3306 表示mysql服务监听的端口
        //(4)、表示mysql dbms的哪个数据库
        //(5)、mysql的连接本质就是socket的连接
        String url = "jdbc:mysql://localhost:3306/database01";
        //将用户名和密码写入到Properties对象
        Properties properties = new Properties();
        //user和password是规定好的关键字，后面的值根据实际情况写
        properties.setProperty("user", "root"); //用户
        properties.setProperty("password", "123456"); //密码
        ///获取到mysql连接
        Connection connect = driver.connect(url, properties);

        //3、执行SQL语句
        String sql = "insert into actor values(null, '刘德华', '男', '1970-11-11', '110')";
        //createStatement():用于执行静态sql语句，并返回其生成的结果对象
        Statement statement = connect.createStatement();
        //如果sql是dml语句，返回的就是数据库受影响的行数，如果返回0则表示添加失败
        int rows = statement.executeUpdate(sql);
        System.out.println(rows > 0 ? "成功" : "失败");

        //4、关闭资源
        statement.close();
        connect.close();
    }
}
```



## 3、JDBC获取数据库连接的方式

### 3.1、方式一：静态连接

静态连接的方式：灵活性较差（数据库连接信息直接写在代码中，环境变化后，需要修改代码，并且安全性较差，容易泄密）。

```java
public void connect01() throws SQLException {
    //在进行JDBC操作之前，需要在项目中先新建一个Directory，将jar包导入项目
    //1、注册驱动
    Driver driver = new Driver();

    //2、得到连接
    //(1)、jdbc:mysql:// 表示协议，通过jdbc的方式连接mysql数据库
    //(2)、主机地址 或 ip地址
    //(3)、3306 表示mysql服务监听的端口
    //(4)、表示mysql dbms的哪个数据库
    //(5)、mysql的连接本质就是socket的连接
    String url = "jdbc:mysql://localhost:3306/database01";
    //将用户名和密码写入到Properties对象
    Properties properties = new Properties();
    //user和password是规定好的关键字，后面的值根据实际情况写
    properties.setProperty("user", "root"); //用户
    properties.setProperty("password", "123456"); //密码
    ///获取到mysql连接
    Connection connect = driver.connect(url, properties);

    //com.mysql.cj.jdbc.ConnectionImpl@36a5cabc
    //成功获取到了一个数据库连接
    //方式1：com.mysql.cj.jdbc.ConnectionImpl@432038ec
	System.out.println("方式1：" + connect);
}
```



### 3.2、方式二：利用反射机制来连接

```java
//方式2：使用反射，动态创建数据库连接
//反射在运行时才会创建Class对象实例，只需要在配置文件中修改文件数据库驱动，再更新jar包就行了
public void connect02() throws ClassNotFoundException, InstantiationException, IllegalAccessException, SQLException {
    //通过反射机制来加载Driver类，动态加载，更灵活
    Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
    //通过Class对象实例来获取一个Driver对象实例
    Driver driver = (Driver) aClass.newInstance();
   
    String url = "jdbc:mysql://localhost:3306/database01";
    //将用户名和密码写入到Properties对象
    Properties properties = new Properties();
    //user和password是规定好的关键字，后面的值根据实际情况写
    properties.setProperty("user", "root"); //用户
    properties.setProperty("password", "123456"); //密码

    //这里会让driver根据url和properties去创建数据库连接
    Connection connect = driver.connect(url, properties);
    //方式2：com.mysql.cj.jdbc.ConnectionImpl@432038ec
    System.out.println("方式2：" + connect);
}
```



### 3.3、方式三：使用DriverManager来替换Driver

```java
//方式3：使用DriverManager来替换Driver
@Test
public void connect03() throws ClassNotFoundException, InstantiationException, IllegalAccessException, SQLException {
    //使用反射加载MySQL驱动
    Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
    Driver driver = (Driver) aClass.newInstance();

    //创建url、user、password
    String url = "jdbc:mysql://localhost:3306/database01";
    String user = "root";
    String password = "123456";

    //注册驱动（使用反射会自动注册，可以省略）
    DriverManager.registerDriver(driver);
    //获取数据库连接
    Connection connection = DriverManager.getConnection(url, user, password);
    //方式3：com.mysql.cj.jdbc.ConnectionImpl@7daa0fbd
    System.out.println("方式3：" + connection);
}
```



### 3.4、方式四：使用反射机制，自动注册驱动，并使用DriverManager创建数据库连接

```java
//方式4：使用反射机制，自动注册驱动，并使用DriverManager创建数据库连接
    //推荐使用
@Test
public void connect04() throws ClassNotFoundException, SQLException {
    //反射在创建Class对象实例的时候，就会触发类加载，不用等到创建该Class对象所对应的对象实例创建才加载
    //而在Driver类中的静态代码块中会自动完成驱动的注册，因此可以省略
    /*
        static {
            try {
                DriverManager.registerDriver(new Driver());
            } catch (SQLException var1) {
                throw new RuntimeException("Can't register driver!");
            }
        }
    */
    Class.forName("com.mysql.cj.jdbc.Driver");

    //创建url、user、password
    String url = "jdbc:mysql://localhost:3306/database01";
    String user = "root";
    String password = "123456";

    //获取数据库连接
    Connection connection = DriverManager.getConnection(url, user, password);
    //方式4：com.mysql.cj.jdbc.ConnectionImpl@432038ec
    System.out.println("方式4：" + connection);
}
```



### 3.5、方式五：利用反射机制，结合配置文件优化数据库连接

```java
//方式5：利用反射机制，结合配置文件优化数据库连接
@Test
public void connect05() throws IOException, SQLException, ClassNotFoundException {
    //通过Properties对象获取配置文件中的信息
    Properties properties = new Properties();
    properties.load(new FileInputStream("src/mysql.properties"));
    String user = properties.getProperty("user");
    String password = properties.getProperty("password");
    String url = properties.getProperty("url");
    String driver = properties.getProperty("driver");

    //可以省略(省略后会自动到jar包中的META-INF下包下的service包中去查找)，然后调用静态代码块去注册驱动
    Class.forName(driver);

    Connection connection = DriverManager.getConnection(url, user, password);
    //方式5：com.mysql.cj.jdbc.ConnectionImpl@432038ec
    System.out.println("方式5：" + connection);
}
```



### 3.6、课堂练习

![JDBC课堂练习](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC课堂练习.png)

```java
package com.cyl.jdbc.myjdbc;

import javax.swing.plaf.nimbus.State;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-13
 * @Description：JDBC练习题
 * @version：1.0
 */
public class JdbcExercise {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Statement statement = createMysqlConnect();
        String newDatabaseName = createNewDatabase(statement);
        changeDatabase(statement, newDatabaseName);
        createNewTable(statement);
        insertData(statement);
        updateTable(statement, 1);
        deleteTable(statement, 3);
    }

    //创建MySQL连接
    public static Statement createMysqlConnect() throws IOException, ClassNotFoundException, SQLException {
        //1、加载配置文件
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");

        String driver = properties.getProperty("driver");

        //注册驱动
        Class.forName(driver);
        //创建数据库连接
        Connection connection = DriverManager.getConnection(url, user, password);
        Statement statement = connection.createStatement();

        return statement;
    }

    //创建新的数据库
    public static String createNewDatabase(Statement statement) throws SQLException {
        String database_name = "jdbc_exercise";
        String create_database = "create database if not exists " + database_name;
        int result_create_database = statement.executeUpdate(create_database);
        if (result_create_database == 0) {
            System.out.println("数据库" + database_name + "创建成功");
        } else {
            System.out.println("数据库创建失败" + result_create_database);
        }

        return  database_name;
    }

    //切换到新的数据库
    public static void changeDatabase(Statement statement, String database_name) throws SQLException {
        //切换数据库
        String qh_table = "use " + database_name;
        int result_switch = statement.executeUpdate(qh_table);
        if (result_switch == 0) {
            System.out.println("成功切换到数据库" + database_name);
        } else {
            System.out.println(result_switch + " 数据库切换失败");
        }
    }

    //在新数据库中，创建actor表
    public static void createNewTable(Statement statement) throws SQLException {
        //创建演员actor表
        String create_table = "create table if not exists actor(id int primary key auto_increment," +
                "`name` varchar(32)," +
                "sex char(1) not null default '女'," +
                "born_date datetime," +
                "phone varchar(12)" +
                ");";
        int result_create_table = statement.executeUpdate(create_table);
        if (result_create_table == 0) {
            System.out.println("成功创建actor表");
        } else {
            System.out.println(result_create_table + " 数据库切换失败");
        }
    }

    //向actor表中插入5条数据
    public static void insertData(Statement statement) throws SQLException {
        //向actor表中插入5条数据
        String insert_data = "insert into actor (name, born_date, sex, phone) values" +
                "('张韶涵', '1990-1-1 18:14:05', '女', '11223'), " +
                "('周深', '1993-02-08 08:14:25', '男', '12723'), " +
                "('汪峰', '1846-01-07 21:03:15', '男', '87243'), " +
                "('Faker', '1636-08-27 13:03:12', '男', '44563'), " +
                "('Uzi', '1876-01-7 21:02:35', '男', '34688');";
        int result_insert = statement.executeUpdate(insert_data);
        if (result_insert > 0) {
            System.out.println("数据插入成功");
        } else {
            System.out.println(result_insert + " 数据插入失败");
        }
    }

    //修改表中id=1的记录
    public static void updateTable(Statement statement, int id) throws SQLException {
        String update_actor = "update actor set name = 'cyl' where id = " + id;
        int result_update = statement.executeUpdate(update_actor);
        if (result_update == 1) {
            System.out.println("记录更新完成");
        } else {
            System.out.println("记录更新失败");
        }
    }

    //删除表中id=3的记录
    public static void deleteTable(Statement statement, int id) throws SQLException {
        String delete_actor = "delete from actor where id = " + id;
        int result_delete = statement.executeUpdate(delete_actor);
        if (result_delete == 1) {
            System.out.println("删除记录成功");
        } else {
            System.out.println("删除失败");
        }
    }
}
```



## 4、ResultSet 结果集

### 4.1、基本介绍

![ResultSet结果集](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\ResultSet结果集.png)



### 4.2、应用案例

```java
package com.cyl.jdbc.resultset_;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-14
 * @Description：结果集的使用
 * @version：1.0
 */
public class ResultSet {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        //获取数据库连接
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");
        //加载驱动
        Class.forName(driver);
        //获取数据库连接
        Connection connection = DriverManager.getConnection(url, user, password);
        //获取statement
        Statement statement = connection.createStatement();
        //查询数据库
        String select_table = "select id, name, sex, born_date from actor";
        //返回查询记录的ResultSet结果集
        java.sql.ResultSet resultSet = statement.executeQuery(select_table);
        //利用while循环遍历
        while (resultSet.next()) {
            //返回结果集的第1列数据
            int id = resultSet.getInt(1);
            //返回结果集的第2列数据
            String name = resultSet.getString(2);
            //返回结果集的第3列数据
            String sex = resultSet.getString(3);
            //返回结果集的第4列数据
            Timestamp born_date = resultSet.getTimestamp(4);
            System.out.println(id + "\t" + name + "\t" + sex + "\t" + born_date);
        }
        //关闭连接
        //关闭结果集
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```



## 5、Statement 接口

### 5.1、基本介绍

![Statement](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\Statement.png)



## 6、PreparedStatement 预处理

### 6.1、基本介绍

![PreparedStatement](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\PreparedStatement.png)



### 6.2、使用预处理的好处

![预处理的优势](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\预处理的优势.png)



### 6.3、应用案例

```java
package com.cyl.jdbc.preparedstatement;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;
import java.util.Scanner;

/**
 * @Author：cyl
 * @Date：2025-09-14
 * @Description：预处理课堂练习
 * @version：1.0
 */
@SuppressWarnings("all")
public class PreparedStatementExercise {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Scanner scanner = new Scanner(System.in);

        //1、加载配置文件
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driver = properties.getProperty("driver");

        //2、注册驱动
        Class.forName(driver);

        //3、获取MySQL数据库连接
        Connection connection = DriverManager.getConnection(url, user, password);

        //4、通过预处理对象来执行SQL语句
        PreparedStatement preparedStatement = null;
        //4.1、向数据库中添加记录
        insertTable(preparedStatement, scanner, connection);
        //4.2、修改数据库中name = ppsm3的记录，将name修改为king
        updateTable(preparedStatement, connection);
        //4.3、删除数据库中name = king的记录
        deleteTable(preparedStatement, connection);
        //4.4、查询数据库中的所有记录
        selectTable(preparedStatement, connection);

        connection.close();
    }

    public static void insertTable(PreparedStatement preparedStatement, Scanner scanner, Connection connection) throws SQLException {
        for (int i = 1; i <= 5; i++) {
            System.out.print("请输入您要添加的用户：");
            String userName = scanner.nextLine();
            System.out.print("请输入您要添加的用户密码：");
            String userPassword = scanner.nextLine();

            //预处理SQL语句，避免SQL注入
            String sql = "insert into admin values (?, ?)";
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, userName);
            preparedStatement.setString(2, userPassword);

            //执行SQL语句
            int rows = preparedStatement.executeUpdate();
            if (rows > 0) {
                System.out.println("第" + i + "条记录插入成功");
            } else {
                System.out.println("第" + i + "条记录插入失败");
            }
        }
        preparedStatement.close();
    }

    public static void updateTable(PreparedStatement preparedStatement, Connection connection) throws SQLException {
        String sql = "update admin set name = ? where name = ?";
        preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, "king");
        preparedStatement.setString(2, "ppsm3");

        int rows = preparedStatement.executeUpdate();
        if (rows > 0) {
            System.out.println("更新记录成功");
        } else {
            System.out.println("更新记录失败");
        }

        preparedStatement.close();
    }

    public static void deleteTable(PreparedStatement preparedStatement, Connection connection) throws SQLException {
        String sql = "delete from admin where name = ?";
        preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, "king");

        int rows = preparedStatement.executeUpdate();
        if (rows > 0) {
            System.out.println("删除记录成功");
        } else {
            System.out.println("删除记录失败");
        }

        preparedStatement.close();
    }

    public static void selectTable(PreparedStatement preparedStatement, Connection connection) throws SQLException {
        String sql = "select * from admin";
        preparedStatement = connection.prepareStatement(sql);
        ResultSet resultSet = preparedStatement.executeQuery();
        while (resultSet.next()) {
            String name = resultSet.getString("name");
            String password = resultSet.getString("password");
            System.out.println(name + "\t" + password);
        }
        preparedStatement.close();
    }
}
```



## 7、JDBC API

![JDBC_API_1](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC_API_1.png)

![JDBC_API_2](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC_API_2.png)



![JDBC_API_3](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\JDBC_API_3.png)



## 8、JDBCUtils

在JDBC操作中，==数据库的连接 和 资源的释放== 是经常使用到的，可以将其封装到JDBC的连接工具类JDBCUtils。

### 8.1、JdbcUtils.java

```java
package com.cyl.jdbc.utils;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-14
 * @Description：工具类，完成mysql的连接和关闭
 * @version：1.0
 */
public class JdbcUtils {
    //定义相关的属性
    private static String user; //用户名
    private static String password; //密码
    private static String url; //url
    private static String driver; //驱动

    //在静态代码块中对属性进行初始化
    static {
        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("src/mysql.properties"));
            //获取相关的信息
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            url = properties.getProperty("url");
            driver = properties.getProperty("driver");
        } catch (IOException e) {
            //e.printStackTrace();
            //在实际开发中，通常会将该编译异常转换为运行时异常
            //调用者可以根据实际情况选择：捕获该异常来做相应处理 或 默认处理该异常
            throw new RuntimeException(e);
        }
    }

    //连接数据库，返回一个数据库连接connection
    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    //关闭相关资源
    //可能要关闭的资源
    //a、ResultSet
    //b、Statement 或 PreparedStatement
    //c、connection
    //对于需要关闭的资源，传入相应的对象即可，如果不想关闭则传入null
    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        //判断是否为null
        try {
            if (resultSet != null) {
                resultSet.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



### 8.2、JdbcUtils_Use

```java
package com.cyl.jdbc.utils;

import java.sql.*;

/**
 * @Author：cyl
 * @Date：2025-09-15
 * @Description：演示JdbcUtils工具类的使用，完成dml和查询语句的使用
 * @version：1.0
 */
public class JdbcUtils_Use {
    public void test_select() {
        //1、得到数据库连接
        Connection connection = null;
        //2、sql语句
        String sql = "select * from actor";
        //3、获取预处理对象PreparedStatement
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = JdbcUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            //执行sql语句
            resultSet = preparedStatement.executeQuery();
            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                Timestamp bornDate = resultSet.getTimestamp("born_date");
                String phone = resultSet.getString("phone");
                System.out.println("id = " + id + " name = " + name + " born_date = " + bornDate + " phone = " + phone);
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            //关闭资源
            JdbcUtils.close(resultSet, preparedStatement, connection);
        }
    }

    public void test_dml() {
        //1、得到数据库连接
        Connection connection = null;
        //2、sql语句
        String sql = "update actor set name = ? where id = ?";
        //3、获取预处理对象PreparedStatement
        PreparedStatement preparedStatement = null;
        try {
            connection = JdbcUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            //给占位符赋值
            preparedStatement.setString(1, "周星驰");
            preparedStatement.setInt(2, 4);
            //执行sql语句
            preparedStatement.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            //关闭资源
            JdbcUtils.close(null, preparedStatement, connection);
        }
    }
}
```



## 9、事务

### 9.1、基本介绍

![事务](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\事务.png)



### 9.2、应用案例

![事务案例](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\事务案例.png)

```java
package com.cyl.jdbc.transaction;

import com.cyl.jdbc.utils.JdbcUtils;
import org.junit.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * @Author：cyl
 * @Date：2025-09-15
 * @Description：演示事务的使用
 * @version：1.0
 */
@SuppressWarnings("all")
public class Transaction_ {
    @Test
    //1、在不使用事务的情况下进行转账交易
    //  可能出现的问题：转账后双方金额不同步
    public void noTransaction() {
        //1、得到数据库连接
        Connection connection = null;
        //2、sql语句
        String sql1 = "update account set balance = balance - 100 where id = ?";
        String sql2 = "update account set balance = balance + 100 where id = ?";
        //3、获取预处理对象PreparedStatement
        PreparedStatement preparedStatement = null;
        try {
            connection = JdbcUtils.getConnection(); //在默认情况下connection默认自动提交(commit)
            //给马云减少100
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.setInt(1, 1);
            preparedStatement.executeUpdate();

            //这里手动制造异常，捕获异常后，后面的sql就直接不执行了，造成两个账户的金额更新不同步
            int i = 1 / 0;

            //给马化腾增加100
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.setInt(1, 2);
            preparedStatement.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            //关闭资源
            JdbcUtils.close(null, preparedStatement, connection);
        }
    }

    @Test
    //使用事务的方式来解决
    public void useTransaction() {
        //1、得到数据库连接
        Connection connection = null;
        //2、sql语句
        String sql1 = "update account set balance = balance - 100 where id = ?";
        String sql2 = "update account set balance = balance + 100 where id = ?";
        //3、获取预处理对象PreparedStatement
        PreparedStatement preparedStatement = null;
        try {
            //在默认情况下connection默认自动提交(commit)
            connection = JdbcUtils.getConnection();
            //开启事务，并手动设置connection对象不自动commit
            connection.setAutoCommit(false);

            //给马云减少100
            preparedStatement = connection.prepareStatement(sql1);
            preparedStatement.setInt(1, 1);
            preparedStatement.executeUpdate();

            //这里手动制造异常，捕获异常后，可以进行事务回滚，还原数据
            int i = 1 / 0;

            //给马化腾增加100
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.setInt(1, 2);
            preparedStatement.executeUpdate();

            //如果没有产生异常，手动提交事务
            connection.commit();
        } catch (SQLException e) {
            //connection设置为不自动commit后，如果触发异常，可以调用rollback进行事务回滚
            //rollback默认回滚到事务开始的地方
            //可以指定savepoint回滚到指定的地方
            System.out.println("执行sql发生了异常，回滚到事务开始的地方");
            try {
                connection.rollback();
            } catch (SQLException ex) {
                throw new RuntimeException(ex);
            }
            throw new RuntimeException(e);
        } finally {
            //关闭资源
            JdbcUtils.close(null, preparedStatement, connection);
        }
    }
}
```



## 10、批处理

### 10.1、基本介绍

![批处理](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\批处理.png)



### 10.2、应用案例

![批处理应用案例](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\批处理应用案例.png)

```java
package com.cyl.jdbc.batch_;

import com.cyl.jdbc.utils.JdbcUtils;
import org.junit.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * @Author：cyl
 * @Date：2025-09-15
 * @Description：java批处理SQL语句
 * @version：1.0
 */
public class Batch_ {
    @Test
    //1、不使用批处理向数据库中添加5000条数据
    public void noBatch() throws SQLException {
        Connection connection = JdbcUtils.getConnection();
        String sql = "insert into admin2 values (null, ?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        System.out.println("开始执行");
        long start_time = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            preparedStatement.setString(1, "jack" + i);
            preparedStatement.setString(2, "666");
            preparedStatement.executeUpdate();
        }
        long end_time = System.currentTimeMillis();
        System.out.println("传统方法(不使用批处理方式执行大量SQL语句)耗时：" + (end_time - start_time));

        //关闭资源
        JdbcUtils.close(null, preparedStatement, connection);
    }

    @Test
    public void useBatch() throws SQLException {
        Connection connection = JdbcUtils.getConnection();
        String sql = "insert into admin2 values (null, ?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        System.out.println("开始执行");
        long start_time = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            preparedStatement.setString(1, "jack" + i);
            preparedStatement.setString(2, "666");

            //将SQL语句先加入到批处理包中
            preparedStatement.addBatch();
            //当批处理包中有1000条记录时再执行
            if ((i + 1) % 1000 == 0) {
                preparedStatement.executeBatch();
                //将批处理包中的数据清空
                preparedStatement.clearBatch();
            }
        }
        long end_time = System.currentTimeMillis();
        //如果需要使用批处理方式，需要在配置文件的url中添加配置，否则执行效率并不会提高
        //url=jdbc:mysql://localhost:3306/jdbc_exercise?rewriteBatchedStatements=true
        System.out.println("批量处理方式执行SQL语句)耗时：" + (end_time - start_time));

        //关闭资源
        JdbcUtils.close(null, preparedStatement, connection);
    }
}
```



## 11、数据库连接池

### 11.1、基本介绍

![数据库连接池](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\数据库连接池.png)



### 11.2、数据库连接池示意图

![数据库连接池示意图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\数据库连接池示意图.png)



### 11.3、数据库连接池种类

![数据库连接池的种类](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\数据库连接池的种类.png)

#### 11.3.1、==C3P0==  

##### A、应用案例

```java
package com.cyl.jdbc.datasource;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.beans.PropertyVetoException;
import java.io.FileInputStream;
import java.io.IOException;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-17
 * @Description：演示数据库连接池C3P0的使用
 * @version：1.0
 */
@SuppressWarnings("all")
public class C3P0_ {
    @Test
    //方式1：相关参数，在程序中手动指定：user，url，password等
    //c3p0 从 0.9.2 开始就拆成了两个 jar 包：
    //c3p0-x.x.x.jar
    //mchange-commons-java-x.x.x.jar
    public void testC3P0_01() throws PropertyVetoException, SQLException, IOException {
        //1、创建一个数据源对象
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();

        //2、通过配置文件mysql.properties获取相关连接信息
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/mysql.properties"));
        String url = properties.getProperty("url");
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String driver = properties.getProperty("driver");

        //3、给数据源 comboPooledDataSource 设置相关的参数信息
        //a、使用数据库连接池来管理数据库连接，因此需要获取到连接数据库的相关信息
        comboPooledDataSource.setDriverClass(driver);
        comboPooledDataSource.setJdbcUrl(url);
        comboPooledDataSource.setUser(user);
        comboPooledDataSource.setPassword(password);

        //b、设置数据库连接池初始化的连接数
        comboPooledDataSource.setInitialPoolSize(10);

        //c、设置数据库连接池的最大连接数
        //当数据库连接数 > 数据库连接池的最大连接数时 就会进入等待队列，知道有连接被释放
        comboPooledDataSource.setMaxPoolSize(50);
        //这个方法就是：实现了DataSource接口的方法
        //测试连接池的效率
        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            Connection connection = comboPooledDataSource.getConnection();
            connection.close();
        }
        long end = System.currentTimeMillis();
        //使用C3P0连接池耗时：673ms
        //传统方式(使用PreparedStatement手动连接数据库耗时：7997ms)
        System.out.println("使用C3P0连接池连接5000次数据库耗时：" + (end - start) + "ms");
    }

    //方式2：使用配置文件模板来连接数据库
    //a、将C3P0提供的的配置文件c3p0.config.xml拷贝到src目录下
    //b、该配置文件指定了：连接数据库 和 连接池 的相关参数
    @Test
    public void testC3P0_02() throws SQLException {
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("Connect-pool-01");

        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            Connection connection = comboPooledDataSource.getConnection();
            //System.out.println("连接成功");
            connection.close();
        }
        long end = System.currentTimeMillis();
        //连接池使用配置文件模板来连接数据库，耗时：561ms，本质就是第1种方式
        System.out.println("连接池使用配置文件模板来连接数据库，耗时：" + (end - start) + "ms");
    }
}
```



#### 11.3.2、==德鲁伊（Druid）== 

##### A、应用案例 1

```
package com.cyl.jdbc.datasource;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.junit.Test;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.io.IOException;
import java.sql.Connection;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-17
 * @Description：德鲁伊连接池使用案例
 * @version：1.0
 */
public class Druid_ {
    @Test
    public void testDruid() throws Exception {
        //1、使用德鲁伊连接池需要先导入相应的jar包：druid-x.x.x.jar
        //2、创建配置文件：druid.properties
        //3、创建Properties对象，读取配置文件
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/druid.properties"));

        //4、创建一个指定参数的数据库连接池
        long start = System.currentTimeMillis();
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        for (int i = 0; i < 5000; i++) {
            Connection connection = dataSource.getConnection();
            //System.out.println("数据库连接成功...");
            connection.close();
        }
        long end = System.currentTimeMillis();
        //德鲁伊连接池创建5000次数据库连接耗时：889ms
        System.out.println("德鲁伊连接池创建5000次数据库连接耗时：" + (end - start) + "ms");
    }
}
```



##### B、应用案例 2 （创建工具类）

```java
package com.cyl.jdbc.utils;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

/**
 * @Author：cyl
 * @Date：2025-09-18
 * @Description：使用德鲁伊连接池创建数据库连接和关闭相关资源
 * @version：1.0
 */
public class JdbcUtilsByDruid {
    private static DataSource dataSource;

    //连接池在静态代码块中完成配置文件的加载
    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("src/druid.properties"));
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    //获取数据库连接
    public static Connection druid_connect() throws SQLException {
        return dataSource.getConnection();
    }

    //关闭相关资源(将空闲的连接池释放，并没有关闭与数据库的连接)
    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        try {
            if (resultSet != null) {
                resultSet.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
//调用相关方法即可
```



## 12、Apache ----- DBUtils 工具类

### 12.1、问题引出

![结果引出](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\结果引出.png)

**==示意图==**：

![Apache](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\Apache.png)



### 12.2、基本介绍

![Apache基本介绍](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\Apache基本介绍.png)

![方法](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\方法.png)



### 12.3、应用案例

![Apache_DBUtils](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\Apache_DBUtils.png)

```java
package com.cyl.jdbc.apache_;

import com.cyl.jdbc.datasource.Actor;
import com.cyl.jdbc.utils.JdbcUtilsByDruid;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.apache.commons.dbutils.handlers.ScalarHandler;
import org.junit.Test;

import java.sql.Connection;
import java.sql.SQLException;
import java.util.List;

/**
 * @Author：cyl
 * @Date：2025-09-18
 * @Description：
 * @version：1.0
 */
public class ApacheDBUtils_Use {
    //使用Apache DBUtils工具类 + Druid连接池的方式完成对表的crud操作

    //1、查询actor表中的多行记录
    @Test
    public void testQueryMany() throws SQLException {
        //(1)、首先通过德鲁伊连接池获取到数据库连接
        Connection connection = JdbcUtilsByDruid.druid_connect();

        //(2)、使用DBUtils类和接口，需要先导入相关的jar包，commons-dbutils-1.8.1.jar

        //(3)、创建QueryRunner对象
        QueryRunner queryRunner = new QueryRunner();

        //(4)、通过queryRunner对象调用相关方法，返回Arraylist结果集内容
        String sql = "select * from actor where id >= ?";

        //代码解析
        //a、query()方法：通过创建的connection执行sql语句，得到resultSet结果集内容，并封装到ArrayList集合中去
        //                并返回结果集内容的集合
        //b、connection：通过德鲁伊连接池创建的数据库连接
        //c、sql：需要执行的sql语句
        //d、new BeanListHandler<>(Actor.class)：
        //      将结果集resultSet的每行内容 ---> 封装到Actor对象中，再添加到ArrayList集合中
        //   Actor.class：底层会通过反射机制来获取到Actor类中的相关成员，将结果集中的内容进行保存
        //   1 代表给sql语句中?赋值(可以有多个值)，因为是可变参数Object... params
        //e、底层会得到ResultSet结果集 和 预处理对象PreparedStatement ，使用后并进行了关闭
        List<Actor> list = queryRunner.query(connection, sql, new BeanListHandler<>(Actor.class), 1);
        for (Actor actor : list) {
            System.out.println(actor);
        }
        JdbcUtilsByDruid.close(null, null, connection);
    }

    //2、使用Apache DBUtils + druid连接池，完成数据库：单行记录(单个对象)的查询
    public void testQuerySingle() throws SQLException {
        //(1)、首先通过德鲁伊连接池获取到数据库连接
        Connection connection = JdbcUtilsByDruid.druid_connect();

        //(2)、使用DBUtils类和接口，需要先导入相关的jar包，commons-dbutils-1.8.1.jar

        //(3)、创建QueryRunner对象
        QueryRunner queryRunner = new QueryRunner();

        //(4)、查询的记录是单行记录
        //  通过queryRunner对象调用相关方法，返回单个对象
        String sql = "select * from actor where id = ?";
        Actor actor = queryRunner.query(connection, sql, new BeanHandler<>(Actor.class), 4);

        //(5)、释放资源
        JdbcUtilsByDruid.close(null, null, connection);
    }

    //3、使用Apache DBUtils + druid连接池，完成数据库：单行单列数据的查询(某一行记录中某一列记录的查询)
    //  返回Object对象
    public void testQueryScalar() throws SQLException {
        //(1)、首先通过德鲁伊连接池获取到数据库连接
        Connection connection = JdbcUtilsByDruid.druid_connect();

        //(2)、使用DBUtils类和接口，需要先导入相关的jar包，commons-dbutils-1.8.1.jar

        //(3)、创建QueryRunner对象
        QueryRunner queryRunner = new QueryRunner();

        //(4)、查询单行单列的记录，即：某一行记录中某一列的记录
        //  通过queryRunner对象调用相关方法，返回单个对象
        String sql = "select name from actor where id = ?";
        Object obj = queryRunner.query(connection, sql, new ScalarHandler<>(), 4);

        //(5)、释放资源
        JdbcUtilsByDruid.close(null, null, connection);
    }

    //演示Apache DBUtils + druid完成数据库的dml操作(增加、删除、修改)
    public void testDml() throws SQLException {
        //(1)、首先通过德鲁伊连接池获取到数据库连接
        Connection connection = JdbcUtilsByDruid.druid_connect();

        //(2)、使用DBUtils类和接口，需要先导入相关的jar包，commons-dbutils-1.8.1.jar

        //(3)、创建QueryRunner对象
        QueryRunner queryRunner = new QueryRunner();

        //(4)、完成dml操作
        String sql = "update actor set name = ? where id = ?";
        int affectedRow = queryRunner.update(connection, sql, "张三丰", 4);
        System.out.println(affectedRow > 0 ? "更新成功" : "数据库没有受影响");

        //(5)、释放资源
        JdbcUtilsByDruid.close(null, null, connection);
    }
}
```



## 13、BasicDao （数据访问对象）

### 13.1、问题分析

![BasicDao](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\BasicDao.png)

**==BasicDAO示意图==**：

![BasicDao示意图](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\BasicDao示意图.png)



### 13.2、基本介绍

![basicDao基本介绍](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\basicDao基本介绍.png)



### 13.3、BasicDAO 应用案例

#### 13.3.1、BasicDAO 设计

![BasicDao设计](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\BasicDao设计.png)

```
package com.cyl.dao_.dao;

import com.cyl.dao_.utils.JdbcUtilsByDruid;
import com.cyl.jdbc.utils.JdbcUtils;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.apache.commons.dbutils.handlers.ScalarHandler;

import javax.xml.ws.handler.Handler;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.List;

/**
 * @Author：cyl
 * @Date：2025-09-20
 * @Description：开发BasicDAO，是其他domain类的父类
 * @version：1.0
 */
public class BasicDAO<T> {
    private QueryRunner queryRunner = new QueryRunner();

    //设计通用的dml方法，针对任意的表都能够使用

    //1、dml语句操作数据库
    public int update(String sql, Object... parameters) {
        //获取数据库连接
        Connection connection = null;
        try {
            connection = JdbcUtilsByDruid.druid_connect();
            int affectRows = queryRunner.update(connection, sql, parameters);
            return affectRows;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JdbcUtilsByDruid.close(null, null, connection);
        }
    }

    //2、设计：针对任意表，返回多个对象(即：查询到多条记录)
    /**
     *
     * @param sql sql语句，可以有?
     * @param clazz 传入一个Class对象(某张数据库表所对应的domain/javabean类的Class类对象)，如：Actor.class
     * @param parameters 传入?所匹配的值，可以有多个
     * @return 返回Actor.class多对应的domain类元素组成的ArrayList集合
     */
    public List<T> queryMultiply(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JdbcUtilsByDruid.druid_connect();
            //这里使用List集合来接收query方法返回的ArrayList集合
            //如果query方法更新后不是使用的ArrayList集合来实现，而是其他的List集合，仍然能够运行
            return queryRunner.query(connection, sql, new BeanListHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JdbcUtilsByDruid.close(null, null, connection);
        }
    }

    //3、设计：查询单行结果的DAO方法
    public T querySingle(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JdbcUtilsByDruid.druid_connect();
            //这里使用List集合来接收query方法返回的ArrayList集合
            //如果query方法更新后不是使用的ArrayList集合来实现，而是其他的List集合，仍然能够运行
            return queryRunner.query(connection, sql, new BeanHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JdbcUtilsByDruid.close(null, null, connection);
        }
    }

    //4、查询返回单行单列的方法
    public Object queryScalar(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JdbcUtilsByDruid.druid_connect();
            //这里使用List集合来接收query方法返回的ArrayList集合
            //如果query方法更新后不是使用的ArrayList集合来实现，而是其他的List集合，仍然能够运行
            return queryRunner.query(connection, sql, new ScalarHandler<>(), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JdbcUtilsByDruid.close(null, null, connection);
        }
    }
}
```



#### 13.3.2、课后练习

![BasicDAO课后练习](C:\Users\31197\Desktop\Directory\Typroa\picture\Chapter22\BasicDAO课后练习.png)











