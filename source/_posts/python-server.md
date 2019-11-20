---
title: 简单HTTP服务器的python实现
date: 2019-11-20 14:03:56
tags: [python]
---
# 简介
本文的目标是实现一个能够处理HTTP请求, 返回相应HTML文件或404页面的HTTP服务器。
其中涉及到一些`python`中的`Socket编程`以及`多线程编程`的知识, 需要具备`TCP协议`、`UDP协议`、`HTTP协议`以及计算机网络分层模型的基础知识。

# Socket基础
## Socket是什么?
`Socket`又称`套接字`，应用程序通常通过`套接字`向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。`Socket`一般是在操作系统底层实现的。像TCP的三次握手四次挥手等操作, 会在调用特定的方法后, 由操作系统内核自动完成, 无需额外编程。
## python中的Socket
### 创建Socket对象
在`python`中, 一般通过`socket()`函数来创建套接字对象：
```python
socket.socket([family[, type[, protocol]]])
```
参数列表：
* **_family_** : 套接字家族, 一般使用`AF_UNIX`和`AF_INET`。`AF_UNIX`用于同一台机器上的进程间通信, `AF_INET`用于IPV4类型的TCP和UDP通信。
* **_type_** : 套接字类型, 有面向连接和非连接两种, 用`SOCK_STREAM`和`SOCK_DGRAM`表示, 分别对应`TCP`和`UDP`。
* **_protocol_** : 一般不填, 默认为0。

### Socket对象的内建方法
内建方法可大致分为三种：服务器端套接字方法、客户端套接字方法以及公共套接字方法。
#### 服务器端套接字方法
方法名 | 方法描述
:- | :-
`bind(host, port)` | 绑定`(host, port)`地址到套接字, 指定`host`主机的`port`端口用于接收向该套接字发送的数据或连接请求。`(host, port)`元组可表示唯一地址。
`listen(max_connection_num)` | 开始`TCP`监听。参数为可接受的最大连接数目, 默认为1。
`accept()` | 开始接受客户端`TCP`连接, 返回一个由作为连接套接字的套接字对象和客户端地址组成的元组。

#### 客户端套接字方法
方法名 | 方法描述
:- | :-
`connect(hostname, port)` | 向名为`hostname`的主机的`port`端口发送`TCP`连接请求。如果连接出错, 则返回`Socket.error`错误。
`connect_ex(hostname, port)` | `connect()`函数的升级版本, 出错时返回错误码而不是抛出异常。

#### 公共套接字方法
方法名 | 方法描述
:- | :-
`recv(max_size[, flags])` | 接收`TCP`数据, 以字节形式返回。`max_size`参数指定要接收的最大数据长度, `flags`用于提供其他信息, 一般可忽略。
`send(bytes[, flags])` | 发送`TCP`数据到与当前套接字连接的套接字, 返回值是发送的字节数量。接收字节类型的数据作为参数并发送。返回值可能比发送的长度要短。该函数执行一次并不一定能发送完所有给定的数据, 可能需要重复执行多次才能完成发送。
`sendall(bytes[, flags])` | 完整的发送`TCP`数据, 成功的话返回`None`, 失败的话抛出异常。
`recvfrom(max_size)` | 接收`UDP`数据, 返回值是`(data, address)`元组, `data`是包含数据的字节类型数据, `address`是发送数据的套接字地址。
`sendto(bytes, address)` | 发送`UDP`数据, 将数据发送到目标套接字。`address`的形式为`(ipaddress, port)`组成的元组, 二者合一可指定唯一地址。返回值是发送的字节数。
`close()` | 关闭套接字。

# Socket编程的简单实例
`Socket`编程主要分为两部分, 一部分为服务器端, 一部分为客户端。针对于`TCP`和`UDP`, 两者服务器端和客户端的行为都是不同的。这里分别针对`TCP`和`UDP`写一个简单实现, 将客户端传到服务器的文本转化为大写后返回。下面分开来看：
## UDP
对于`UDP`这种无连接协议, 其客户端和服务器端的代码都相对简单。其基本流程如下：
![](/images/python-server/01.png)
针对上述流程, 可以写出代码实现, [示例地址](https://github.com/xdyushenli/Computer-Network-A-Top-Down-Approch/tree/master/UDP)：
## TCP
`TCP`的客户端实现与`UDP`差不多, 但服务器端相差较大。
在使用`TCP`传输的过程中, 服务器端要创建两个套接字, 一个是欢迎套接字, 用于监听客户端发送的连接请求; 另一个是根据连接请求创建的连接套接字, 用于向客户端发送和接收数据。
客户端代码和服务器端代码流程如下, [示例地址](https://github.com/xdyushenli/Computer-Network-A-Top-Down-Approch/tree/master/TCP)
![](/images/python-server/02.png)

# HTTP服务器
由上文我们可以看到, **Socket编程针对的是TCP和UDP, 最大的作用是传输报文**。上述示例中的服务器功能都比较简单, 只是把客户端传来的字符串变为大写后再传回去而已, 而这些操作都是在收到数据`recv()`之后、发送数据`send()`之前的数据处理部分完成的。
一个`HTTP服务器`, 需要对客户端传来的`HTTP报文`进行解析, 并执行一系列操作(如文件存取等), 这些功能也都是在数据处理部分完成的。
上面的示例都同时实现了客户端代码和服务器端代码。在这个场景中, 浏览器相当于客户端。当我们在浏览器中输入地址时, 浏览器会自动发送`HTTP请求`、建立`TCP连接`并接收回传数据, 因此我们只要实现`HTTP服务器端`就好。
实现如下([示例地址](https://github.com/xdyushenli/Computer-Network-A-Top-Down-Approch/tree/master/HTTP))：
```python
from socket import *

# 构造HTTP报文
def generateHTTPResponse(response_start_line, response_header, response_body):
    return (response_start_line + response_header + '\r\n' + response_body).encode()

# 创建TCP欢迎套接字
serverSocket = socket(AF_INET, SOCK_STREAM)
# 将欢迎套接字绑定到指定端口
serverSocket.bind(('', 8000))
# 开始监听来自客户端的连接请求, 设置最大连接数为1
serverSocket.listen(1)

# 持续监听来自客户端的连接请求
while True:
    # 收到客户端连接请求, 创建连接套接字
    connectionSocket, address = serverSocket.accept()
    # 获取客户端发送的HTTP报文
    http_message = (connectionSocket.recv(1024)).decode()
    
    try:
        # 获取请求的文件路径
        path = http_message.split(' ')[1]
        if (path == '/' or path == '/index.html'):
            # 读取文件
            f = open('C:\\Users\\Admin\\Desktop\\Computer-Network-A-Top-Down-Approch\\HTTP\\index.html')
            output_data = f.read()
            
            # HTTP文件报文
            response_start_line = 'HTTP/1.1 200 OK\r\n'
            response_header = 'Server: Web Server\r\n'
            response_body = output_data
        else:
            # 目标路径没有文件, 抛出错误,返回404
            raise Exception('No Such File!')
    except BaseException as e:
        # 404HTTP报文
        # print(e)
        response_start_line = 'HTTP/1.1 404 Not Found\r\n'
        response_header = 'Server: Web Server\r\n'
        response_body = ''

    # 构造HTTP报文
    response = generateHTTPResponse(response_start_line, response_header, response_body)
    # 向用户发送HTTP报文
    connectionSocket.send(response)
    # 关闭连接套接字
    connectionSocket.close()
```

# 注意
* `import` 和 `from import` 有什么区别?
> `import xx`相当于导入某个模块。对于模块中的函数，每次调用需要`模块.函数`来用。
> `from xx import fun`直接导入模块中某函数，直接`fun()`就可用。

