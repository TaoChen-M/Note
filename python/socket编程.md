### Python socket编程

#### TCP连接的三次握手和四次握手

https://draveness.me/whys-the-design-tcp-three-way-handshake/

#### Socket函数

- 服务器与客户端之间发送数据的时候不能发送列表、元素、字典等带有数据结构类型的数据，发送的内容必须都是字符串类型

##### 服务器端函数

| Socket函数        | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| s.bind (address)  | 将套接字绑定到地址，在AF_INET下，以元组(host,port)方式传入，如s.bind((host,port)) |
| s.listen(backlog) | 开始监听TCP传乳连接，backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数，该值最少是1，大部分程序设计为5就可以了 |
| s.accept()        | 接收TCP连接并返回(conn,address)，其中conn是新的套接字对象，可以用来接收和发送数据，address是连接客户端的地址 |

##### 客户端Socket函数

| socket函数  | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| s.connect() | 连接到address处的套接字，一般address的格式为元组(host,port)，如果连接出错，则返回socket.error错误 |

##### 公共Socket函数

| socket函数      | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| s.recv(bufsize) | 接收TCP套接字的数据，数据以字符串的形式返回，buffsize指定要接收的最大数据量 |
| s.send(string)  | 发送TCP数据                                                  |
| s.close()       | 关闭套接字                                                   |

##### Socket编程思想

###### TCP服务器

1. 创建套接字，绑定套接字到IP地址和端口

   ```python
   s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
   s.bind
   ```

2. 开始监听连接

   ```python
   s.listen()
   ```

3. 进入循环，不断接收客户端的连接请求

   ```python
   while True：
   	s.accept()
   ```

4. 接收客户端的数据，并且发送给对方

   ```python
   s.recv()
   s.sendall()
   ```

5. 传输完毕，关闭套接字

   ```python
   s.close()	
   ```

###### TCP客户端

1. 创建套接字并连接至远端地址

   ```python
   s=socket.socket(socket.AF_INET,socket.SOCKET_STREAM)
   s.connect()
   ```

2. 连接后发送和接收数据

   ```python
   s.send()
   s.recv()
   ```

3. 传输完毕关闭连接

   ```python
   s.close()
   ```

   

##### Socket编程实践服务器端代码

```python
import socket

HOST = '192.168.1.100'
PORT = 8001

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((HOST, PORT))
s.listen(5)

print 'Server start at: %s:%s' %(HOST, PORT)
print 'wait for connection...'

while True:
    conn, addr = s.accept()
    print 'Connected by ', addr

    while True:
        data = conn.recv(1024)
        print data

        conn.send("server received you message.")

# conn.close()
```

##### Socket编程实践之客户端代码

```python
import socket
HOST = '192.168.1.100'
PORT = 8001

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

while True:
    cmd = raw_input("Please input msg:")
    s.send(cmd)
    data = s.recv(1024)
    print data

    #s.close()
```

