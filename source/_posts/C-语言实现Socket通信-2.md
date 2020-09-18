---
title: C#语言实现Socket通信_2
date: 2020-09-18 19:56:37
tags:
- Socket
---

# C#语言实现Socket通信_2

## 序言

上一篇说的[c#语言实现Socket通信_1](https://junlu.club/2020/09/18/C-%E8%AF%AD%E8%A8%80%E5%AE%9E%E7%8E%B0Socket%E9%80%9A%E4%BF%A1/)存在以下问题：

> - 服务器端
>   1. 只能接收1个客户端的连接
>   2. 只能发送与接收各1条消息
> - 客户端
>   1. 只能发送与接收各1条消息

此篇文章使用Unity建立客户端，VS2019建立服务器端，实现双向通信，且不限制客户端的数量，不限制收发消息的数量

## 涉及知识

- Unity
- Ngui
- Socket
- 线程

## 具体实现

### 服务器端

```c#
using System;
using System.Collections.Generic;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp4
{
    class Client
    {
        private Socket clientSocket;
        private Thread t;
        private byte[] data = new byte[1024];//容器

        //构造函数
        public Client(Socket s)
        {
            clientSocket = s;
            //启动一个线程，处理客户端的数据接受
            t = new Thread(ReceiveMessage);
            t.Start();

        }
        private void ReceiveMessage()
        {
            //一直接收客户端数据
            while (true)
            {
                //接受数据前，判断socket是否断开
                if (clientSocket.Poll(10, SelectMode.SelectRead))
                {
                    clientSocket.Close();
                    break;//终止线程
                }
                int length = clientSocket.Receive(data);
                string message = Encoding.UTF8.GetString(data, 0, length);
                // 接受到数据后，把数据分发到客户端
                Program.BroadcastMessage(message);
                Console.WriteLine("recieved message :" + message);

            }
        }

        public void sendMessage(string message)
        {
            byte[] data = Encoding.UTF8.GetBytes(message);
            clientSocket.Send(data);
        }

        public bool Connected
        {
            get
            {
                return clientSocket.Connected;
            }
        }
    }


    /// <summary>
    /// 服务器端
    /// </summary>
    class Program
    {
        static List<Client> clientList = new List<Client>();
        /// <summary>
        /// 广播
        /// </summary>
        /// <param name="message"></param>
        public  static void BroadcastMessage(string message)
        {
            var notConnectedList = new List<Client>();
            foreach(var client in clientList)
            {
                if (client.Connected)
                    client.sendMessage(message);
                else
                {
                    notConnectedList.Add(client);
                }
            }
            foreach(var temp in notConnectedList)
            {
                clientList.Remove(temp);
            }
        }
        static void Main(string[] args)
        {
            //创建Socket
            Socket tcpSever = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
            //绑定IP与端口
            tcpSever.Bind(new IPEndPoint(IPAddress.Parse("192.168.31.125"),7788));
            //开始监听
            Console.WriteLine("server running ... ");
            tcpSever.Listen(100);
            while(true)
            {
                Socket clientSocket = tcpSever.Accept();
                Console.WriteLine("a client is connected  ");
                Client client = new Client(clientSocket);//把与客户端的通信逻辑放进client类中；
                clientList.Add(client);
            }
        }
    }
}

```

### 客户端

Unity项目界面如图所示：

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200918194720.png)

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200918195224.png)

```c#
using System.Collections;
using System.Collections.Generic;
using System.Net;
using System.Net.Sockets;
using System.Text;
using UnityEngine;
using UnityEngine.UI;
using System.Threading;

public class ChatManager : MonoBehaviour
{
    public string ipaddress = "192.168.31.125";
    public int port = 7788;
    public UIInput inputFieldArea;
    public UILabel outMessage;

    private Socket clientSocket;
    private Thread t;
    private byte[] data = new byte[1024];
    private string message = "";

    private void Start()
    {
        ConnectedToServer();
    }

    private void Update()
    {
        if (message != null && message != "")
        {
            outMessage.text = outMessage.text + "\nrecevce a message :" + message;
            message = "";
        }
    }

    private void ConnectedToServer()
    {
        //创建
        clientSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        //连接
        clientSocket.Connect(new IPEndPoint(IPAddress.Parse(ipaddress), port));
        //创建一个新的线程用来接受消息
        t = new Thread(receiveMessage);
        t.Start();
    }

    private void receiveMessage()
    {
        while (true)
        {
            if (clientSocket.Connected == false)
                break;

            int length = clientSocket.Receive(data);
            message = Encoding.UTF8.GetString(data, 0, length);
            //outMessage.text = outMessage.text + "\n" + message;
        }

    }

    private void SendMyMessage(string message)
    {
        byte[] data = Encoding.UTF8.GetBytes(message);
        clientSocket.Send(data);
    }

    public void OnSendButtonClick()
    {
        outMessage.text = outMessage.text + "\n" + inputFieldArea.value;
        SendMyMessage(inputFieldArea.value);
    }

    private void OnDestroy()
    {
        clientSocket.Shutdown(SocketShutdown.Both);
        clientSocket.Close();
    }

}

```

