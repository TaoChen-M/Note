

#                                              Webserver

## 工具安装

- 虚拟机安装：https://zhuanlan.zhihu.com/p/141033713
- Vscode
- Mobaxterm

## 环境配置

- ifconfig命令：`sudo apt-get install nettools`
- 配置ssh：`sudo apt-get install openssh-server`
- git安装：`sudo apt install git`

- GCC/G++编译器安装：`sudo apt-get install build-essential`

- MySqL数据库安装：https://www.jianshu.com/p/13d71125eec4
- apt镜像源配置：https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b115GD0vT

## Linux常用命令

- 查看IP地址：`sudo ifconfig`
- 安装需要的数据包：`sudo apt-get install ***`有的时候如果无法安装，可以先更新apt，`sudo apt update`

## GCC

- C++代码编译流程

![](C:\Users\Tchen\Desktop\研究生\Webserver\编译流程.png)

- 将源文件编译成可执行：`gcc hello.c -o hello`
- https://zhuanlan.zhihu.com/p/111500914

## Makefile

```makefile
src=$(wildcard ./*.c) //wildcard表示遍历文件夹目录中的所有文件 ./指定是当前文件夹  *.c是文件筛选
objs=$(patsubst %.c, %.o, $(src))//patsubst 将所有.c转化成.o文件，%是通配符匹配，$(src)那些文件
target=app//编译生成的目标
$(target):$(objs)
	$(CC) $(objs) -o $(target)//CC指明使用的编译器
	
%.o:%.c
	$(CC) -c $^ -o $@//$^编译的源文件是：之后所有的，$@生成的目标是：之前的

.PHONY:clean//清除所有的.o文件
clean:
	rm $(objs) -f
```

## 进程

##### 进程创建

```C++
pid_t pid=fork();
//在父进程中返回大于0，子进程中返回值为0，fork是Linux系统函数
//子进程只执行创建进程之后的代码
```

父子进程对数据的操作采用的方法是读时共享，写时拷贝

##### 进程退出

```c++
void exit(int status);
```

##### 孤儿进程

子进程依然还在运行，但是父进程已经结束，此时该子进程就叫孤儿进程，孤儿进程由init进程接管

##### 僵尸进程

每个进程结束都会释放自己的用户区数据，但是PCB中的数据需要由父进程进行释放，如果子进程已经运行结束，但是父进程依然还在运行的时候，父进程中如果没有调用wait()函数或者waitpid()函数，子进程中保留的PCB数据会一直存在占用进程号，如下图所示

![](C:\Users\Tchen\Desktop\研究生\Webserver\僵尸进程.png)

![僵尸进程1](C:\Users\Tchen\Desktop\研究生\Webserver\僵尸进程1.png)

##### 进程回收

wait()和waitpid()都是进程回收函数，wait()函数会阻塞，waitpid()不会阻塞并且可以指定回收那个进程。

通常一个wait()或者waitpid()只能回收一个子进程，如果回收多个，循环调用

## Socket

### 主机字节序和网络字节序

通常对于一个32位的整数，如果一个整数的高位放在内存的高地址处，低位放在内存的低地址处，称为小端序也叫主机序；如果一个整数的高位放在内存的低地址处，整数的低位放在内存的高地址处，成为大端序也叫网络序。

因为不同主机的字节序可能会不同，因此规定发送端总要把数据转化成网络字节序再发送，接收端在收到数据后，知道数据是网络字节序将其转化成自己的主机字节序

检查主机是大端机还是小端机的代码如下

```C++
union
{
    short value;
    char arr[sizeof(short)];
}test;

test.value = 0x0102;
if((test.arr[0]==2)&&(test.arr[1]==1))
    cout << "小端机";
if((test.arr[0]==1)&&(test.arr[1]==2))
    cout << "大端机";
```

![](C:\Users\Tchen\Desktop\研究生\Webserver\字节序.png)

htonl表示将长整型转化为网络字节序，htonl意为host to net long，长字节通常用来转换IP地址，short用来转换端口号

### Socket相关

#### socket创建

首先要创建socket，在linux系统中一切都是文件，因此创建socket之后返回一个socket文件描述符

```C++
int socket(int domain,int type,int protocol);
```

其中domain设置为AF_INET，表示IPv4，type可以为SOCK_STREAM表示底层是TCP协议，SOCK_UGRAM表示是UDP协议，protocol现在没啥用通常是0.

#### 绑定

创建socket之后，还要将其绑定在特定的IP地址上，第三个参数是指IP地址的长度，绑定成功返回0，失败返回-1，如下所示

```C++
int bind(int sockfd,const struct sockaddr* myaddr,socklen *addrlen);
```

```c++
sockaddr_in serAddr;//这里使用sockaddr_in是因为可以直接指定IP地址和端口，绑定时强转一下
memset(&serAddr,0,sizeof(sockaddr_in));//一开始只是声明，并没有分配空间，这里分配空间并进行初始化
serAddr.sin_family=AF_INET;
serAddr.sin_addr.s_addr=inet_addr("192.168.154.128");//将字符串形式的IP地址转为网络可以识别的01
serAddr.sin_port=htons(8888);//端口号转为网络序
bind(s,(sockaddr*)&serAddr,sizeof(sockaddr));
```

#### 监听

```C++
int listen(int sockfd,int backlog);
```

sockfd表明监听的socket，backlog是监听队列的最大长度，如果队列长度超过最大长度，则后续不再受理新的连接。成功返回0，失败返回-1；

#### 接受连接

```C++
int accept(int sockfd,struct sockaddr* addr,socklen *addrlen);
```

sockfd是执行过系统监听调用的socket，sddr是被接受的远端socket地址，地址长度由addrlen指出。成功后返回一个新的唯一的socket标识符，标识了被连接的那个客户端socket，服务器可以读写该socket来与客户端通信

```C++
sockaddr_in cliAddr;//声明一个客户端的sock变量，用来保存链接的客户端socket相关数据
socklen_t cliAddrLen=sizeof(sockaddr);
int su=accept(s,(sockaddr*)&cliAddr,&cliAddrLen);
```



#### 主动连接

客户端需要调用connect主动同服务器建立连接，sockfd是创建socket的标识符，addr是服务端的地址，如下所示

```C++
int connect(int sockfd,struct sockaddr* addr,socklen* addrlen);
```

连接成功后返回0，此时的sockfd就唯一的标识了这个连接，客户端可以独写该sockfd与服务端通信

#### 关闭连接

```C++
int socket(int sockfd);
```

sockfd是待关闭的socket，使用这个函数时，只是将待关闭的socket的引用计数减一，只有当引用计数是0时才真正关闭。多进程程序中，系统调用fork()函数创建子进程默认将socket引用计数加一，应该在父子进程中同时调用close函数。

#### 数据独写

##### TCP数据读写

```C++
size_t send(int sockfd,void* buff,size_t len,int flag);
size_t recv(int sockfd,void* buff,size_t len,int flag);
```

recv读取sockfd上的数据，buff和len是缓存冲区的位置和大小，函数返回值是成功读取的数据长度，返回长度可能小于我们期望的长度，因此多次调用该函数。如果返回0，表明客户端关闭连接，返回-1表明出错。

send往sockfd上写数据，buff和len指明缓冲区位置和大小，函数调用成功返回成功写入的数据长度，失败返回-1。

flag通常都是0。

##### UDP

```C++
size_t recvfrom(int sockfd,void* buff,size_t len,int flag,struct sockaddr* addr, socklen* len);
size_t sendto(int sockfd,void* buff,size_t len,int flag,struct sockaddr* addr, socklen* len);
```

基本上和TCP发送数据是相同的，但是UDP是没有建立连接的，所以需要指定接收端和发送端的IP地址。

### Select、Poll、Epoll介绍

#### select

```C++
int select (int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

select可以监视三种文件描述符，读readfds，写writefds，异常exceptfds。调用select函数之后，用户进程会阻塞，直到某个文件描述符就绪或者达到timeout的指定时间，如果设置为NULL表示立即返回的意思。

select函数返回之后可以通过遍历fd_set的方式来找到已经就绪的文件描述符。Linux中select可以监视的最大文件描述符个数是1024。

```C++
int main(){
    //创建socket
    int sockfd=socket(AF_INET,SOCK_STREAM,0);

    //绑定地址和端口号 申明IP地址、端口号
    sockaddr_in ser;
    memset(&ser,0,sizeof(sockaddr_in));
    ser.sin_family=AF_INET;
    ser.sin_addr.s_addr=inet_addr("192.168.154.128");
    ser.sin_port=htons(6666);
    int ret=bind(sockfd,(sockaddr*)&ser,sizeof(sockaddr));

    //监听端口
    listen(sockfd,5);

    //等待链接  因为创建了可以监听5个，所以这里指定可以接收5个链接成功后的文件描述符
    int count=1;
    int sucfds[count];
    //创建远端的socket地址
    sockaddr_in cli;
    //记录最大的文件描述符
    int maxfd=-1;
    printf("%s","创建成功");
    for(int i=0;i<count;i++){
        bzero(&cli,sizeof(sockaddr_in));
        socklen_t clilen=sizeof(sockaddr);
        //accept是阻塞函数，这里如果没有连到五个很有可能一直阻塞
        sucfds[i]=accept(sockfd,(sockaddr*)&cli,&clilen);
        if(sucfds[i]>maxfd)maxfd=sucfds[i];
    }

    //设置select读文件描述符集合
    fd_set rset;
    while (1)
    {
        //首先将描述符集合中的内容全部清零
        FD_ZERO(&rset);
        for(int i=0;i<count;i++){
            FD_SET(sucfds[i],&rset);
        }
        //rset实际上是一个bitmap，比如000000000，它的数量是1024个，这里FD_SET根据文件描述符具体是多少，将rset的对应位置置为1

        //监听所有感兴趣的文件描述符上的可读可写或者说异常事件
        select(maxfd+1,&rset,NULL,NULL,NULL);//最后一个参数是时间，传递NULL将会一直阻塞直到某个事件发生，第一个参数设置为最大的文件描述符加一，因为是从0开始计算的
        int MAXBUFF=1024;
        char buff[MAXBUFF];
        for(int i=0;i<count;i++){
            if(FD_ISSET(sucfds[i],&rset)){
                bzero(buff,'\0');
                recv(sucfds[i],buff,MAXBUFF,0);
                printf("%s",buff);
            }
        }
    }
}
```



#### poll

```C++
int poll (struct pollfd *fds, unsigned int nfds, int timeout);
```

```C++
struct pollfd {
    int fd; /* file descriptor */
    short events; /* requested events to watch */
    short revents; /* returned events witnessed */
};
```

fd是指定的文件描述符，events成员告诉poll监听fd上的哪些事件，比如说POLLIN表示数据可读，EPOLLOUT表示数据可写，revents成员由内核修改，以通知应用程序fd上实际发生了哪些事件。

nfds指定被监听事件集合fds的大小

timeout如果被指定为-1，将会永远阻塞，直到某个事件发生；为0则立即返回

最终也要使用遍历的方式来查看那个文件描述符已经就绪。

```C++
int main(){
    //创建socket
    int socfd=socket(AF_INET,SOCK_STREAM,0);

    //绑定
    sockaddr_in ser;
    ser.sin_family=AF_INET;
    ser.sin_addr.s_addr=inet_addr("192.168.154.128");
    ser.sin_port=htons(5555);
    int ret=bind(socfd,(sockaddr*)&ser,sizeof(sockaddr));

    //监听
    int listcount=1;
    listen(socfd,listcount);

    //poll接收链接
    sockaddr_in cli;
    cli.sin_family=AF_INET;
    pollfd pollfds[listcount];
    for(int i=0;i<listcount;i++){
        memset(&cli,0,sizeof(sockaddr_in));
        socklen_t len=sizeof(sockaddr_in);
        //指明需要监听文件描述符
        pollfds[i].fd=accept(socfd,(sockaddr*)&cli,&len);
        //事件为数据可读
        pollfds[i].events=POLLIN;
    }

    while (1)
    {
        poll(pollfds,listcount,-1);
        int BUFF=1024;
        char buff[BUFF];
        for(int i=0;i<listcount;i++){
            //revents由内核进行复写，判断revents的情况是否是POllIN或者时POLLOUT
            if(pollfds[i].revents&POLLIN){
                pollfds[i].revents=0;
                memset(buff,0,BUFF);
                recv(pollfds[i].fd,buff,BUFF,0);
            }
        }
    } 
}
```

#### epoll模式

epoll将用户关心的文件描述符上的事件放在内核的一个事件表中因此就不要像select或者poll那样每次都需要传入文件描述符集或者事件集，但是epoll需要使用一个额外的文件描述符来标识这个内核中的时间表，使用`int epoll_create(int size)`来进行创建。`size`参数并不起作用，只是个内核一个提示，告诉事件表可能会有多大，这个函数返回的文件描述符将会用做其他所有的epoll系统调用的第一个参数。

epoll操作过程具有三个接口，如下所示：

`int epoll_ctl(int epfd,int op,int fd,struct epoll_event *event)`其中epfd表示创建epoll时返回的文件描述符，op表示将要对socket fd执行的操作，主要有三种，EPOLL_CTL_ADD（增加新的fd到epfd中），EPOLL_CTL_MOD（修改已经注册的fd），EPOLL_CTL_DEL（从epfd中删除fd）。第三个参数是需要监听的fd，event表示需要监听什么事情，和poll的基本相同，但是epoll具有两个额外的事件类型EPOLLET和EPOLLONESHOT这两个是epoll高效运作的关键。

```C++
struct epoll_event{
    __uint32_t events;//epoll事件
    epoll_data_t data;//用户数据
}
```

`data`成员用来存储用户数据

`epoll_wait(int epfd,struct epoll_event* events,int maxevents,int timeout)`，epfd是创建的epoll文件标识符，epoll会将发生的事件复制到创建好的events数组中，max_events通常和预分配的events一样大，timeout是指没有事件发生时的最大等待时间，单位是毫秒。

```C++
int main(){
    //创建epoll文件描述符
    int epofd=epoll_create(10);

    epoll_event ev;
    sockaddr_in cli;
    socklen_t len=sizeof(cli);
    for(int i=0;i<5;i++){
        memset(&cli,0,sizeof(cli));
        //获取链接的fd，指明事件，并将其增加到内核中epoll的事件表中
        ev.data.fd=accept(sockfd,(sockaddr*)&cli,&len);
        ev.events=EPOLLIN;
        epoll_ctl(epofd,EPOLL_CTL_ADD,ev.data.fd,&ev);
    }

    int maxevents=5;
    epoll_event readyevents[maxevents];
    int BUFF=1024;
    char buff[BUFF];
    while (1)
    {
        //epoll_wait会将已经就绪的事件放入readyevents这个数组中，maxevents指明最多监听多少个事件，返回已经就绪的事件个数
        int nfds=epoll_wait(epofd,readyevents,maxevents,-1);
        for(int i=0;i<nfds;i++){
            memset(buff,0,BUFF);
            recv(readyevents[i].data.fd,buff,BUFF,0);
        }
    }  
}
```



#### ET和LT

LT：Level Trigger，水平触发；默认的工作方式

ET：Edge Trigger，边缘触发；高效工作方式

使用LT模式下，当`epoll_wait()`检测到其上有事件发生将其通知给应用程序，应用程序可以不立即处理这个事件，那么下次调用`epoll_wait()`的时候，会再次向应用程序通知此事件，一直循环往复直到最终这个事件被处理。如果采用的是ET模式，那么将事件通知给应用程序之后必须立即处理该事件，因为后续的`epoll_wait()`将不再向应用程序通知这个事件

#### EPOLLONESHOT

即使是使用ET模式，一个socket上的事件也有可能被触发多次，主要是出现在并发程序的时候。比如一个线程在读取完某个socket的数据之后开始进行处理，处理过程中又有新的数据可读，此时另外一个线程被唤醒读取这些数据，于是就会出现两个线程同时处理一个socket的情况，这不是我们期望的。一个socket在任意时刻都只能被一个线程处理。