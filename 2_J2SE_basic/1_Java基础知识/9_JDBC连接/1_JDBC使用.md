### JDBC

### 1.基本定义

JDBC (Java Database Connectivity) API，即Java数据库编程接口，是一组标准的Java语言中的接口和类，使用这些接口和类，Java客户端程序可以访问各种不同类型的数据库。比如建立数据库连接、执行SQL语句进行数据的存取操作。

### 2.使用步骤

```java
/**
* JDBC应用步骤
* 1. 加载驱动程序
* 2. 获得数据库连接
* 3. 创建Statement对象
* 4. 执行SQL语句
* 5. 处理结果
* 6. 关闭JDBC对象
**/
public class DatabaseForJava {

    private static final String URL = "jdbc:mysql://127.0.0.1:3306/nretail_tlink_server";
    private static final String USERNAME = "root";
    private static final String PASSWORD = "123456";

    public static void main(String[] args){

        try {
            // 1. 加载驱动程序
            Class.forName("com.mysql.jdbc.Driver");

            // 2. 获得数据库连接
            Connection conn = DriverManager.getConnection(URL,USERNAME,PASSWORD);

            /**
             * 创建Statement对象
             * 3. 创建Statement对象
             * 4. 执行SQL语句
             * 5. 处理结果
             * 6. 关闭JDBC对象
             */
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery("select * from tlink_product_info");
            while (rs.next()) {
                System.out.println(rs.getString("SKU_CODE")+","+rs.getString("SKU_NAME"));
            }
            rs.close();
            stmt.close();
            conn.close();


            /**
             * 创建PreparedStatement对象
             * 3. 创建PreparedStatement对象
             * 4. 设置参数执行SQL语句
             * 5. 处理结果
             * 6. 关闭JDBC对象
             */
            String sql = "select * from tlink_product_info where SKU_CODE = ?";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setString(1,"0101380319MW");
            ResultSet rp = pstmt.executeQuery();
            while (rp.next()) {
                System.out.println(rp.getString("SKU_CODE")+","+rp.getString("SKU_NAME"));
            }
            rp.close();
            pstmt.close();
            conn.close();

        } catch (ClassNotFoundException c) {
            System.out.println("加载驱动程序失败!");
            c.printStackTrace();
        } catch (SQLException s) {
            System.out.println("SQL执行失败!");
            s.printStackTrace();
        }

    }
}
```

### 3.Statement接口

- 标准的statement类有：Statement、PreparedStatement、CallableStatement

  1. Statement是最基本的用法，采用字符串拼接的方式，存在注入漏洞。
  2. PreparedStatement对Statement中的SQL语句进行预编译，同时检查合法性，效率高。
  3. CallableStatement 接口扩展 PreparedStatement，用来调用存储过程,它提供了对输出和输入/输出参数的支持。CallableStatement 接口还具有对 PreparedStatement 接口提供的输入参数的支持。

- BatchedStatement用于批量操作数据库，BatchedStatement不是标准的Statement类。

- PreparedStatement与Statement的区别

  1. 一般而言，PreparedStatement比Statement执行效率更高。PreparedStatement 接口继承 Statement ，

      PreparedStatement 实例包含已编译的 SQL 语句，所以其执行速度要快于 Statement 对象。

  2. Statement每次都会解析/编译SQL，确立并优化数据获取路径。

  3. PreparedStatement有预编译的过程，已经绑定sql，之后无论执行多少遍，都不会再去进行编译。

### 4.ResultSet接口

- 结果集(ResultSet)是数据中查询结果返回的一种对象，可以说结果集是一个存储查询结果的对象，但是结果集并不仅仅具有存储的功能，他同时还具有操纵数据的功能，可能完成对数据的更新等。
- 结果集读取数据的方法主要是getXXX() ，他的参数可以使整型表示第几列（是从1开始的），还可以是列名。返回的是对应的XXX类型的值。如果对应那列时空值，XXX是对象的话返回XXX型的空值，如果XXX是数字类型，如Float等则返回0，boolean返回false。

### 5.JDBC中的设计模式

- 桥接模式

  JDBC连接 数据库 的时候，在各个数据库之间进行切换，基本不需要动太多的代码，甚至丝毫不动，原因就是JDBC提供了统一接口，每个数据库提供各自的实现，用一个叫做数据库驱动的程序来桥接就行了。
  
- 具体体现

  ```java
  /**
  * Driver接口中有对应不同数据库的驱动类
  * Connection接口有对应不同数据库的连接类
  * 传统桥接是Driver实现类中持有一个Connection的声明,并且在构造方法中构造出来
  * JDBC中DriverManager是外显的桥接类,将Driver和Connection的实现类桥接起来
  **/
  Connection conn = DriverManager.getConnection(URL,USERNAME,PASSWORD);
  ```

### 4.一般关系数据模型和对象数据模型

- 表对应类，记录对应对象，表的字段对应类的属性