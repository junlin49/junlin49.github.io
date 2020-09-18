---
title: ' C#语言实现Socket通信'
date: 2020-09-18 15:08:41
tags:
---

# C#语言实现Socket通信

## 目标

实现1个服务器端，1个客户端，两者相互通信

## 主要步骤

### 服务器端

1. 创建socket

   ```c#
   /*
    * AddressFamily.InterNetwork -- 内网
    * SocketType.Stream -- 流形式
    * ProtocolType.Tcp -- Tcp协议
    */
   Socket tcpSever = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
   ```

2. 绑定自身IP，分配端口号

   ```c#
   //IP地址：两台计算机之间的识别，局域网一般是192.168.xxx
   //端口号：两台计算机上的软件之间的通讯（一个软件占用一个端口号），一般分布在0-6w之间
   //端口号一般申请4位数，不容易被软件使用
   //一个计算机上可能有多个IP地址（网卡多）
   IPAddress ipaddress = new IPAddress(new byte[] { 192,168,1,107 });
   EndPoint point = new IPEndPoint(ipaddress,7788);//ipendpoint是对IP和端口做了一层封装的类
   tcpSever.Bind( point );//向操作系统申请一个可用的IP和端口用来通信
   ```

3. 开始监听

   ```c#
   tcpSever.Listen(100);//最大链接数100
   Console.WriteLine("开始监听");
   ```

4. 建立连接

   ```c#
   Socket clientSocket = tcpSever.Accept();//暂停当前线程，直到有一个客户端链接过来，进行下方代码
   Console.WriteLine("1个客户端已经连接");
   ```

5. 向客户端发送数据

   ```c#
   string message = "Good news everyone , you have been a PhD";
   byte[] data = Encoding.UTF8.GetBytes(message);//对字符串编码（UTF8），得到字符串的字节数组
   clientSocket.Send(data);//发送byte数组
   Console.WriteLine("1条数据已经发送");
   ```

### 客户端

1. 创建Socket

   ```c#
   /*
    * AddressFamily.InterNetwork -- 内网
    * SocketType.Stream -- 流形式
    * ProtocolType.Tcp -- Tcp协议
    */
   Socket tcpClient = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
   ```

2. 发起请求，请求建立连接

   ```c#
   IPAddress ipaddress = IPAddress.Parse("192.168.1.107");//可以把一个字符串的IP地址转化为ipaddress对象
   EndPoint point = new IPEndPoint(ipaddress, 7788);//ipendpoint是对IP和端口做了一层封装的类
   tcpClient.Connect(point);//通过IP：端口号，定位一个要链接到的服务器端（服务器端的IP与端口号）
   ```

3. 接受服务器发送的数据

   ```c#
   //定义存放数据的data；
   byte[] data = new byte[1024];
   int length = tcpClient.Receive(data);
   //把接受的0-length字节转化为string（length返回值表示接收到多少字节）
   string message = Encoding.UTF8.GetString(data,0,length);
   Console.WriteLine("接受到一条消息：" + message);
   ```

## 代码

### 服务器端

```c#
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp2
{
    class Program
    {
        //服务器端
        static void Main(string[] args)
        {
            //1.创建socket
            //-------------------------------------------------------------------------------------------------------
            /*
             * AddressFamily.InterNetwork -- 内网
             * SocketType.Stream -- 流形式
             * ProtocolType.Tcp -- Tcp协议
             */
            Socket tcpSever = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

            //2.绑定IP和端口号
            //---------------------------------------------------------------------------------------------------                                      
            //IP地址：两台计算机之间的识别，局域网一般是192.168.xxx
            //端口号：两台计算机上的软件之间的通讯（一个软件占用一个端口号），一般分布在0-6w之间
            //端口号一般申请4位数，不容易被软件使用
            //一个计算机上可能有多个IP地址（网卡多）
            IPAddress ipaddress = new IPAddress(new byte[] { 192,168,1,107 });
            EndPoint point = new IPEndPoint(ipaddress,7788);//ipendpoint是对IP和端口做了一层封装的类
            tcpSever.Bind( point );//向操作系统申请一个可用的IP和端口用来通信

            //3.开始监听（等待客户端链接）
            //--------------------------------------------------------------------------------------------------------
            tcpSever.Listen(100);//最大链接数100
            Console.WriteLine("开始监听");

            //4.建立链接
            //-----------------------------------------------------------------------------------------------------------
            Socket clientSocket = tcpSever.Accept();//暂停当前线程，直到有一个客户端链接过来，进行下方代码
            //-----------------------------------------------------------------------------------------------------------
            Console.WriteLine("1个客户端已经连接");

            //使用返回的 clientSocket 与客户端做通信
            //-----------------------------------------------------------------------------------------------------------
            string message = "Good news everyone , you have been a PhD";
            byte[] data = Encoding.UTF8.GetBytes(message);//对字符串编码（UTF8），得到字符串的字节数组
            clientSocket.Send(data);//发送byte数组
            Console.WriteLine("1条数据已经发送");

            //5.接受客户端发送的消息
            //-------------------------------------------------------------------------------------------------------------
            byte[] data2 = new byte[1024];//创建字节数组，存放客户端发送的数据
            int length = clientSocket.Receive(data2);//向data2中存放数据;length表示接收了多少字节
            string message2 = Encoding.UTF8.GetString(data2, 0, length);//把接收的0-length字节转化为string
            Console.WriteLine("接受到一条消息："+message2);


            Console.ReadKey();
        }
    }
}

```

### 客户端

```c#
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        //客户端
        static void Main(string[] args)
        {
            //1.创建 socket
            //-----------------------------------------------------------------------------------------------------------------
            /*
             * AddressFamily.InterNetwork -- 内网
             * SocketType.Stream -- 流形式
             * ProtocolType.Tcp -- Tcp协议
             */
            Socket tcpClient = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

            //2.发起建立链接的请求
            //------------------------------------------------------------------------------------------------------------------------
            IPAddress ipaddress = IPAddress.Parse("192.168.1.107");//可以把一个字符串的IP地址转化为ipaddress对象
            EndPoint point = new IPEndPoint(ipaddress, 7788);//ipendpoint是对IP和端口做了一层封装的类
            tcpClient.Connect(point);//通过IP：端口号，定位一个要链接到的服务器端（服务器端的IP与端口号）

            //3.接收服务器端发出的消息
            //------------------------------------------------------------------------------------------------------------------------
            //定义存放数据的data；
            byte[] data = new byte[1024];
            int length = tcpClient.Receive(data);
            //把接受的0-length字节转化为string（length返回值表示接收到多少字节）
            string message = Encoding.UTF8.GetString(data,0,length);
            Console.WriteLine("接受到一条消息：" + message);

            //4.向服务器端发送消息
            //-----------------------------------------------------------------------------------------------------------------------
            string message2 = Console.ReadLine();//读取用户输入
            tcpClient.Send(Encoding.UTF8.GetBytes(message2));//把字符串转换为byte数组，发送服务器端

            Console.ReadKey();
        }
    }
}

```

