---
title: 基于Java的TCP Socket通信详解
cover: 'https://static.ivan.fun/blog/tcp_index.jpg'
index_img: 'https://static.ivan.fun/blog/tcp_index.jpg'
tags:
  - Java
categories: -Java
abbrlink: 59c157cb
feature: true
date: 2020-05-15 15:30:00
---
# 基于Java的TCP Socket通信详解
## TCP Socket通信是一种比较常用的基于连接的网络通信方式。本文通过Java实现TCP Socket通信，并将其用于计算机端,Android手机端,硬件设备端,同时做到代码规范化，实现代码最大化复用。
### | 本文代码可在GitHub下载，建议对照源码阅读文章 https://github.com/IVanMissAya/tcp_server

# TCP连接的建立   
客户端和服务器间通过 **三次握手** 建立TCP连接。在Java中，连接建立完成后，服务器端和客户端分别获取到一个Socket实例，之后就可以通过这个Socket实例进行通信。服务器端和客户端使用不同的方法获取Socket实例。

1. #### 服务器端 
   在服务器端，通过ServerSocket实现对指定端口的监听，代码如下。其中port为int型端口数值，取值0~65535，0~1024为系统保留端口，这里取值1234。如果发生错误将会抛出异常。 
    ```
    int port = 1234;
    ServerSocket server = new ServerSocket(port);
    ```
    通过ServerSocket.accept()方法接受客户端连接。这个方法是阻塞的，从调用时开始监听端口，直到客户端连接建立时，执行结束并返回Socket实例。连接建立失败会抛出异常。
    ```
    Socket socket = server.accept();
    ```   

2. #### 客户端 
    客户端直接通过实例化的形式，产生Socket实例。实例化的过程中，尝试连接指定的服务器主机。连接成功则实例化完成，连接失败则抛出异常。hostIP为主机的IP地址，port为端口号，和服务器主机监听的端口号保持一致。
    ```
    String hostIP = "127.0.0.1";
    int port = 1234;
    Socket socket = new Socket(hostIP, port);
    ```
## 连接的建立过程    
以上代码的执行顺序是：
+ 服务器端实例化ServerSocket：new ServerSocket(port);
+ 服务器端执行accept()，监听指定端口，此方法阻塞等待客户端连接：server.accept();
+ 客户端实例化Socket实例，尝试连接服务器：new Socket(hostIP, port);
+ TCP三次握手成功，服务器端的accept()返回Socket实例，同时客户端的Socket实例化成功。

# Socket的读写 
以收发字符串为例来说明Socket的读写。

向Socket对象写入数据，则会发送至TCP连接的另一方。这个操作在服务器端和客户端是一样的。可通过获取Socket的输出流来写入UTF8格式编码的字符串，代码如下。写入完成后，就会被发送到连接的另一端。

```
private DataOutputStream out;
out = new DataOutputStream(socket.getOutputStream());
String s = "Test";
out.writeUTF(s);
out.flush();
```

在接收端，通过获取Socket的输入流，就可以读取字符串数据，代码如下。readUTF()方法是阻塞的，直到对方发送完一个字符串，该方法才会执行结束并返回收到的字符串。如果连接中断，或强制关闭Socket的输入流，即执行socket.shutdownInput()，该方法会抛出异常。

```
private DataInputStream in;
in = new DataInputStream(socket.getInputStream());
String s = in.readUTF();
```

在建立了TCP连接后，由于无法确定对方的数据发送时间，为了保证及时接收到数据，通过一个新线程不断调用in.readUTF()方法读取数据（相当于轮询法）；并在接收到数据后回调相关函数，对数据进行处理。

# TCP连接的断开
TCP Socket连接是双向的，通过 **四次挥手** 的方式断开，双方分别调用Socket.close()方法断开连接。连接断开的过程中，一般一方A先断开连接，另一方B发现A断开连接后，也断开连接。为方便表述，将先断开连接的一方A称为“主动断开连接”；后断开的一方B，则为“被动断开连接”。

在一方B阻塞执行in.readUTF()方法时，如果对方A主动断开Socket连接，这个方法会抛出异常。从而在B处理异常时，可以被动的断开这边的连接。

为保证主动断开连接的一方不会阻塞在in.readUTF()方法中，需要先执行socket.shutdownInput()。所以主动断开连接的代码如下。

```
socket.shutdownInput();
in.close();
socket.close();
```

被动断开连接的一方，在捕获到in.readUTF()的异常后，断开Socket连接。

```
try {
    String s = in.readUTF();
} catch (IOException e) {
    // 连接被断开(被动)
    try {
        in.close();
        socket.close();
        in = null;
        socket = null;
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

## SocketTransceiver的实现
考虑到在服务器端和客户端，Socket对象的操作是完全一样的，所以实现了一个SocketTransceiver（收发器），实现对Socket的直接操作，其他代码则通过SocketTransceiver间接操作Socket对象实现数据收发、断开连接等。

SocketTransceiver实现的功能有：
+ 开启新线程不断查询Socket是否收到数据；
+ 将字符串、文件等类型的数据进行打包，并通过Socket发送；
+ 从Socket接收数据，并自动解析出数据（字符串、文件等），接收完成后回调相应的方法；
+ 在发生错误、连接被动断开时，自动断开连接并进行相关处理，并回调相应方法。
***SocketTransceiver.class*** 使用抽象类实现，回调方法是抽象的，实例化时对抽象方法进行实现，处理回调。完整代码见附件。

## TcpServer的实现
TcpServer为TCP Socket服务器端程序。为了让服务器能同时接受并处理来自多个客户端的TCP连接请求：
+ TcpServer中用一个监听线程对端口进行监听，即阻塞执行server.accept()方法，等待接受客户端连接；
+ 服务器端每次与一个客户端建立连接，即accept()方法执行结束并返回一个Socket对象，就会用一个SocketTransceiver对这个Socket进行操作；
+ 连接建立后，监听线程再次执行server.accept()方法，继续监听端口并等待下一个连接；
+ 服务器端有一个List<SocketTransceiver>，保存当前连接的每个客户端对应的SocketTransceiver对象，在需要时可取出并进行操作。

## TcpClient的实现
TcpClient为TCP Socket客户端程序。主要工作是进行Socket的连接，并利用SocketTransceiver对Socket进行操作。

### 另附 windows 下tcp 测试工具

1. sokit 是一款开源免费的 TCP / UDP 测试（调试）工具， 可以用来接收，发送或转发TCP/UDP数据包。 下载地址: http://sqdownd.onlinedown.net/down/sokit-1.3-win32-chs.zip

2. TCPUDP测试工具用于开发网络通讯程序时，在服务器或客户端测试TCP/UDP通讯连接和测试数据的接收和发送情况。 下载地址: http://fastsoft.onlinedown.net/down/TCPUDPDebug102_Setup.exe