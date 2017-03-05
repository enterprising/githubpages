---
layout: post
title: Java+SqlServer的增删改查 
date: 2015-11-22 15:25
categories: Java
tags: [Java,数据库,SqlServer]
---
1. 记得导入SqlServer2005的JDBC驱动
2. 在数据库中已有一个叫做**DataStudents**的数据库
3. 其他的建表什么的就交给程序吧
4. 还有那个改的功能大同小异，偷懒没写

```java
import java.sql.*;  
import java.util.Scanner;  
  
public class Content {  
  
    public Content() throws SQLException {  
        FirstConnection();  
        Scanner s = new Scanner(System.in);  
        // TODO Auto-generated constructor stub  
        while (true) {  
            System.out.println("Sql测试小程序");  
            System.out.println(" 1.新增数据");  
            System.out.println(" 2.修改数据");  
            System.out.println(" 3.删除数据");  
            System.out.println(" 4.查询数据");  
            System.out.println(" 5.退出程序");  
            int choose = s.nextInt();  
            switch (choose) {  
            case 1:  
                dataAdd();  
            case 2:  
                ;  
            case 3:  
                dataDel();  
            case 4:  
                dataSelect();  
            case 5:  
                System.exit(0);  
            }  
        }  
    }  
  
    public static boolean Charge(ResultSet s) throws SQLException {  
        while (s.next()) {  
            if (s.getObject(3).toString().equals("DataStudents"))  
                return true;  
        }  
        return false;  
    }  
  
    public Connection getSqlConnection() {  //获取数据库的连接
        Connection con = null;  
        try {  
            Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");  
            con = DriverManager.getConnection(  
                    "jdbc:sqlserver://localhost:1433;DatabaseName=student",  
                    "sa", "123456");  
            System.out.println("获取数据库连接成功");  
        } catch (ClassNotFoundException e) {  
            // TODO Auto-generated catch block  
            System.out.println("抱歉找不到驱动");  
            System.exit(0);  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
            System.exit(0);  
        }  
        return con;  
    }  
  
    public void FirstConnection() throws SQLException {  //第一次连接数据库的时候要建表
        Connection con = this.getSqlConnection();  
        ResultSet rs = con.getMetaData().getTables(null, null,  
                "DataStudents".toUpperCase(), null);  
        if (Content.Charge(rs)) {  
            System.out.println("学生数据库连接成功");  
        } else {  
            String sql = "create table DataStudents(id int primary key,name varchar(10))";  
            PreparedStatement st = con.prepareStatement(sql);  
            System.out.println("正在创建学生数据库...    ...");  
            st.execute();  
            System.out.println("学生数据库创建成功");  
        }  
    }  
  
    public void dataAdd() throws SQLException {  //添加数据
        Scanner s = new Scanner(System.in);  
        Connection con = getSqlConnection();  
        System.out.println("请输入学生学号");  
        int num = s.nextInt();  
        System.out.println("请输入学生姓名");  
        String name = s.next();  
        String sql = "insert into DataStudents(id,name) values(?,?)";  
        PreparedStatement st = con.prepareStatement(sql);  
        st.setInt(1, num);  
        st.setString(2, name);  
        int flag = st.executeUpdate();  
        if (flag > 0) {  
            System.out.println("添加学生信息成功");  
        }  
        st.close();  
        con.close();  
    }  
  
    public void dataDel() throws SQLException {  //删除数据
        Scanner s = new Scanner(System.in);  
        Connection con = getSqlConnection();  
        System.out.println("请输入学生学号");  
        String num = s.next();  
        String sql = "delete from DataStudents where id = " + num;  
        PreparedStatement st = con.prepareStatement(sql);  
        int flag = st.executeUpdate();  
        if (flag > 0) {  
            System.out.println("删除学生信息成功");  
        }  
        st.close();  
        con.close();  
    }  
      
    public void dataSelect(){  //查询数据
        Connection con = getSqlConnection();  
        String sql = "select * from DataStudents order by id";  
        try {  
            PreparedStatement st = con.prepareStatement(sql);  
            ResultSet rs = st.executeQuery();  
            System.out.println("id" + " " + "name" + " ");  
            while (rs.next()) {  
                System.out.println(rs.getString("id") + " "  
                        + rs.getString("name") + " ");  
            }  
            rs.close();  
            st.close();  
            con.close();  
        } catch (SQLException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
    }  
  
    public static void main(String[] args) throws SQLException {  
        // TODO Auto-generated method stub  
        new Content();  
    }  
}
```