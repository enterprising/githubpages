---
layout: post
title: Java Swing 实现带抖动提示功能的登录窗口
date: 2015-11-23 20:35
categories: Java
tags: [Java,Swing]
---
我实现的基于**Java swing**的登录窗口，其中实现的功能包括基本的登录正确判断，错误提示，错误出现的时候窗口抖动，界面背景图片添加。  
*注：因为大家用的数据库都不一样，所以数据库连接部分的代码就注释掉了，可以自己加入自己的数据库连接方法，默认的用户名admin密码123456*
```java
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

import javax.swing.*;

public class LoginFrame extends JFrame implements ActionListener {
    // Toolkit kit = Toolkit.getDefaultToolkit();// 工具，可以获得屏幕等硬件的相关信息

    // public static int SCREEN_WIDTH, SCREEN_HEIGTH;
    Container c = getContentPane();

    Connection con = null;
    JButton Yes, No;
    JTextField UserName;
    JPasswordField PassWord;
    JLabel JAcharge;
    String TempUsername = "admin", TempPassword = "123456";
//  String TempUsername, TempPassword;
    JPanel temp;

    public LoginFrame() {
        // SCREEN_WIDTH = kit.getScreenSize().width;
        // SCREEN_HEIGTH = kit.getScreenSize().height;
//      getSqliteConnection();

        temp = new JPanel() {// 背景图片设置
            @Override
            protected void paintComponent(Graphics g) {// 重写背景绘制方法
                // TODO Auto-generated method stub
                ImageIcon icon = new ImageIcon("back.jpg");// 找到图片并用ImageIcon装载
                // 图片随窗体大小而变化
                g.drawImage(icon.getImage(), 0, 0, this.getSize().width,// 绘制
                        this.getSize().height, this);
            }
        };
        temp.setOpaque(false);// 设置JPanel为不透明
        c.add(temp);

        setTitle("登录");// 标题
        setSize(320, 160);// 窗口大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);// 设置窗口关闭按钮

        temp.setLayout(null);// 布局管理器设置为没有布局，采用绝对布局的方式绘制界面
        JLabel JAusername = new JLabel("用户名");// 用户名输入框标签
        JAusername.setForeground(Color.white);
        JLabel JApassword = new JLabel("密    码");// 密码输入框标签
        JApassword.setForeground(Color.white);
        JAcharge = new JLabel("×");// 输入错误的提示符号
        JAcharge.setForeground(Color.RED);// 设置小X前景色为红色
        JAcharge.setFont(new Font(Font.DIALOG, Font.BOLD, 30));// 设置小X的大小（即设置字体属性，第三个数字为字体大小）
        UserName = new JTextField("");// 用户名输入框
        PassWord = new JPasswordField();// 密码输入框
        Yes = new JButton("确认");// 确认按钮
        No = new JButton("取消");// 取消按钮
        temp.add(JAusername).setBounds(60, 20, 60, 16);// 以下为设置各个界面元素的位置
        temp.add(JApassword).setBounds(60, 55, 60, 16);
        temp.add(UserName).setBounds(120, 18, 120, 24);
        temp.add(PassWord).setBounds(120, 53, 120, 24);
        temp.add(Yes).setBounds(70, 90, 70, 24);
        temp.add(No).setBounds(170, 90, 70, 24);

        No.addActionListener(this);// 为两个按钮添加按钮点击事件
        Yes.addActionListener(this);

        setLocationRelativeTo(null);// 设置窗体创建在屏幕中央
        setResizable(false);// 不允许改变登录窗口的大小
        setVisible(true);// 设置窗体为可见
    }

    public static void main(String[] args) {
        new LoginFrame();// 主函数
    }

    private void ErrorShow(int x) { // 错误标志显示的函数，用来判断小X是显示在用户名输入框后面还是密码输入框后面
        switch (x) {
        case 1:
            temp.add(JAcharge).setBounds(241, 21, 20, 20);
            ShaveOneShave();
            break;
        case 2:
            temp.add(JAcharge).setBounds(241, 56, 20, 20);
            ShaveOneShave();
            break;
        }
    }

    @SuppressWarnings("deprecation")
    @Override
    public void actionPerformed(ActionEvent e) { // 按钮点击事件的响应
        if (e.getSource() == No) { // 如果点击取消按钮就退出程序
            System.exit(0);
        } else if (e.getSource() == Yes) { // 如果点击确定按钮就看是判断
            String tempusername = UserName.getText().toString();// 获得两个输入框的输入的内容，是String的类型
            String temppassword = new String(PassWord.getPassword());

            if (tempusername.equals("")) {
                this.setTitle("用户名不能为空！");// 判断用户名输入框和密码输入框是否为空，在标题栏给出错误提示，并显示小红X在输入框的后面
                ErrorShow(1);
            } else if (temppassword.equals("")) {
                this.setTitle("密码不能为空！");
                ErrorShow(2);
            } else {
                if (tempusername.equals(TempUsername)) {
                    if (temppassword.equals(TempPassword)) {
                        JOptionPane.showMessageDialog(null, "登陆成功", "重要提示",
                                JOptionPane.INFORMATION_MESSAGE);// 弹窗显示成功
                        // 当用户名和密码的数据都相等的时候，就执行主窗口，并发送用户名
                        this.dispose();// 关闭自己
                    } else {
                        this.setTitle("密码错误！请查证后输入");// 显示密码错误的提示
                        ErrorShow(2);
                    }
                } else {
                    this.setTitle("此用户名未注册");// 显示用户名错误的提示
                    ErrorShow(1);
                }
            }
        }
    }

//  public void getSqliteConnection() {// 数据库连接方法
//      try {
//          try {
//              try {
//                  Class.forName("org.sqlite.JDBC");// 加载驱动
//              } catch (ClassNotFoundException e) {
//                  e.printStackTrace();
//              }
//              con = DriverManager.getConnection("jdbc:sqlite:user.db");
//              Statement statement = con.createStatement();
//              ResultSet rs = statement.executeQuery("select * from person");
//              TempUsername = rs.getString("username");// 获取数据库中的用户名
//              TempPassword = rs.getString("password");// 获取数据库中的密码
//          } catch (SQLException e) {
//              e.printStackTrace();
//          }
//      } finally {
//          try {
//              if (con != null)
//                  con.close();// 如果连接不为空，即当前处于连接数据库的状态，就关闭连接
//          } catch (SQLException e) {
//              System.err.println(e);
//          }
//      }
//  }

    public void ShaveOneShave() {// 窗口抖动方法，当用户输入的用户名或者密码错误或者没有输入的时候，抖动窗体引起大家的注意
        int x = this.getX();// 获取窗体当前的位置
        int y = this.getY();
        for (int i = 0; i < 10; i++) {// 抖10次
            if ((i & 1) == 0) {// 控制窗体是抖出去还是抖回来
                x += 7;
                y += 7;
            } else {
                x -= 7;
                y -= 7;
            }
            this.setLocation(x, y);
            try {
                Thread.sleep(35);// 用来对抖动进行一个延迟，让其有抖动的效果
            } catch (Exception e1) {
                e1.printStackTrace();
            }

        }
    }
}
```
实际的界面和各类错误提示如下：  
界面：  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/36847791-file_1488505476361_17408.png)  
输入框为空的提示 :  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/38650620-file_1488505523958_aa4f.png)  
用户名输入错误的提示 :  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/42830483-file_1488505527148_c768.png)  
因为不一定只有一个用户，所以就没有说用户名错误，而是没有注册  

密码错误提示 :  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/86630498-file_1488505530180_14ba1.png)  
成功提示 :  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/42580537-file_1488505533396_4b93.png)