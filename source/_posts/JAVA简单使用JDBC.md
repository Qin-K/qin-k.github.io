---
title: JAVA简单使用JDBC
date: 2019-08-30 16:29:12
tags: 
	- java
	- 数据库
categories:
	- javaEE
---

**jdbc的定义**

> JDBC（Java DataBase Connectivity,java数据库连接）是一种用于执行SQL语句的Java API，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。JDBC提供了一种基准，据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序

**准备环境**

- jdk 1.8
- mysql-5.7.23-winx64
- mysql-connector-java-5.1.7-bin.jar

**使用jdbc的基本步骤**

1. 注册驱动

   ```java
   Class.forName("com.mysql.jdbc.Driver");
   ```

2. 建立数据库连接

   ```java
   Connection conn = DriverManager.getConnection(url, username, password);
   ```

3. 创建Statement(PrepareStatement)对象

   ```java
   PrepareStatement ps = conn.prepareStatement(sql);
   ```

4. 执行sql语句得到ResultSet结果集

   *PrepareStatement基本使用*

   - executeQuery() 	*查询*
   - executeUptate()  *增删改*

   ```java
   ResultSet rs = ps.executeQuery();
   ```

5. 遍历结果集

   *ResultSet获取结果*

   ```java
   while (rs.next()) {/*获取结果*/}
   ```

6. 释放资源 *释放资源时的顺序 ResultSet->PrepareStatement->Connection*

**简单JDBC实例**

(1) 工具类，获取连接对象，和释放资源

```java
// 获取Connection对象
public static Connection getConnection() {
    Connection conn = null;
    try {
        Class.forName("com.mysql.jdbc.Driver"); // 加载数据库驱动
        conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbctest", "root", "admin"); // 建立数据库连接
    } catch (Exception e) {
        e.printStackTrace();
    }
    return conn;
}

// 释放资源
public static void release(Connection conn, Statement st, ResultSet rs) {
    try {
        if (rs != null) {
            rs.close();
        }
        if (st != null) {
            st.close();
        }
        if (conn != null) {
            conn.close();
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

（2）使用jdbc实现基本增删改查

- 准备表格

  <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g47g9tyti8j30e703ha9w.jpg"/>

1. insert 增加

   向表中插入两条数据, qinkai 20, zhangsan 20, id根据表中自增长。

   ```java
   @Test
   public void insert() {
       Connection conn = JDBCUtil.getConnection();
       String sql = "insert into student values(null,?,?)";
       PreparedStatement ps = null;
       try {
           ps = conn.prepareStatement(sql);
           ps.setString(1,"qinkai");
           ps.setInt(2,20);
           ps.executeUpdate();
   
           ps.setString(1, "zhangsan");
           ps.setInt(2,  20);
           ps.executeUpdate();
       } catch (SQLException e) {
           e.printStackTrace();
           JDBCUtil.release(null, ps, conn);
       }
   }
   ```

   插入成功表中数据

   <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g47gamgxlhj30e8037q2t.jpg"/>

2. query 查询

   查询表所有数据，并打印

   ```java
   @Test
   public void query() {
       Connection conn = JDBCUtil.getConnection();
       String sql = "select * from student";
       PreparedStatement ps = null;
       ResultSet rs = null;
       try {
           ps = conn.prepareStatement(sql);
           rs = ps.executeQuery();
           while(rs.next()) {
               int id = rs.getInt("id");
               String name = rs.getString("name");
               int age = rs.getInt("age");
               System.out.println(id + " " +  " " + name + " " + age);
           }
       } catch (SQLException e) {
           e.printStackTrace();
           JDBCUtil.release(rs, ps, conn);
       }
   }
   ```

   控制台结果显示

   <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g47gb0ffk4j30he03sdfs.jpg"/>

3. update 修改

   通过id修改id为4的姓名为lisi，修改完成后查询。

   ```java
   @Test
   public void update() {
       Connection conn = JDBCUtil.getConnection();
       String sql = "update student set name=? where id =?";
       PreparedStatement ps = null;
       try {
           ps = conn.prepareStatement(sql);
           ps.setString(1, "lisi");
           ps.setInt(2, 4);
           ps.executeUpdate();
           query();
       } catch (SQLException e) {
           e.printStackTrace();
           JDBCUtil.release(null, ps, conn);
       }
   }
   ```

   修改后，查询控制台显示

   <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g47gbe8q5jj30h703w0so.jpg"/>

4. delete 删除

   通过name条件删除lisi的数据，删除后查询

   ```java
   @Test
   public void delete() {
       Connection conn = JDBCUtil.getConnection();
       String sql = "delete from student where name=?";
       PreparedStatement ps = null;
       try {
           ps = conn.prepareStatement(sql);
           ps.setString(1, "lisi");
           ps.executeUpdate();
   
           query();
       } catch (SQLException e) {
           e.printStackTrace();
           JDBCUtil.release(null, ps, conn);
       }
   }
   ```

   删除后，控制台显示结果。

   <img src="https://ws1.sinaimg.cn/large/006Cx571gy1g47gdf8moyj30gh03d747.jpg"/>

