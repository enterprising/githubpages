---
layout: post
title: 计算机网络--socket编程
date: 2017-3-15 22:25
categories: 计算机网络
tags: [计算机网络]
---
# socket的概念
网络上的两个程序通过一个双向的通信来实现数据的交换，这个双向链路的一端称为一个Socket。
Socket也叫套接字。
由IP地址和端口号组成
# 用socket实现客户端和服务器端的通信，要求客户发送数据后能够回显相同的数据。
## 服务器端
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
	public static void main(String[] args) {
		BufferedReader br = null;
		PrintWriter pw = null;
		try {
			ServerSocket server = new ServerSocket(2000);
			Socket socket = server.accept();
			// 获取输入流
			br = new BufferedReader(new InputStreamReader(
					socket.getInputStream()));
			// 获取输出流
			pw = new PrintWriter(socket.getOutputStream(), true);
			String s = br.readLine(); // 获取接收的数据
			pw.println(s); // 发送相同的数据给客户端
			System.out.println("这边是server，我收到了你发来的：");
			System.out.println(s);
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				br.close();
				pw.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
}
```
## 客户端
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;

public class Client {

	public static void main(String[] args) {
		BufferedReader br = null;
		PrintWriter pw = null;
		try {
			Socket socket = new Socket("127.0.0.1", 2000);
			// 获取输入流与输出流
			br = new BufferedReader(new InputStreamReader(
					socket.getInputStream()));
			pw = new PrintWriter(socket.getOutputStream(), true);
			// 想服务器发送请求
			pw.println("Hello server,I am Client.");
			String s = null;
			while (true) {
				s = br.readLine();
				if (s != null)
					break;
			}
			System.out.println(s);
		} catch (UnknownHostException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				br.close();
				pw.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

}
```
## 运行效果
> 服务器端开着，然后运行客户端程序
### 服务器端显示：
![enter description here][1]


  ### 客户端显示：
  ![enter description here][2]


  [1]: http://omphwvjh0.bkt.clouddn.com/1489587881807.jpg
  [2]: http://omphwvjh0.bkt.clouddn.com/1489587979757.jpg 