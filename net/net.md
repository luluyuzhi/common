## API函数

1.socket

函数的作用：建立一个新的socket套接字

函数的原型：`int socket(int domain,int type,int protocol)`

函数的参数：

> domain:表示使用何种地址类型，
>
> AF_INET：IPV4;  AF_INET6：IPV6

> type: SOCK_STREAM：TCP面向数据流的
>
> SOCK_DGRAM： UDP使用不连续不可信赖的数据包连接
>
> SOCK_RAM：提供原始网络协议
>
> protocol:指定socket传输协议编号，设为0即可
>

返 回 值：成功返回socket套接字描述符，失败-1

头 文 件：#include <sys/socket.h>

 

2.bind

函数的作用：绑定IP地址

函数的原型：`int bind(int sockfd,struct sockaddr * my_addr,int addrlen)`

函数的参数:

sockfd：套接字描述符

my_addr：主机地址

addrlen：sockaddr的地址长度

```cpp
struct sockaddr
{
    unsigned short int sa_family;
    char sa_data[14];
}；
```


​            

```cpp
struct sockaddr_in

{
    unsigned short sin_family;
    uint16_t sin_port;   端口号
    struct in_addr sin_addr;   IP地址
    unsigned char sin_zero[8];
};
```

头 文 件：

#include <sys/types.h>
#include <sys/socket.h>

返 回 值：成功0；出错-1

 

3.connect

函数的作用：建立socket连接的通常客户端连接服务器使用

函数的原型：int connect(int sockfd,struct sockaddr * serv_addr,int addrlen);

函数的参数：

serv_addr：表示要连接的服务器IP地址

addrlen：struct sockaddr的长度

返 回 值：成功0；出错-1

 

4.listen

函数的作用：聆听网络，等待连接

函数的原型：int listen(int sockfd,int backlog)

函数的参数：

sockfd：描述符

backlog：允许接入的客户端数目

注意：**listen并没有连接，只是设置socket的listen模式而已，真正连接的是accept!**

返 回 值：成功0，出错-1

 

5.accept

函数的作用：接收网络的连接，客户端连接，三次握手

函数的原型：int accept(int sockfd,struct sockaddr *addr,int *addrlen);

函数的参数：

addr:连接成功填充远端客户机地址

addrlen:struct sockaddr的长度

返 回 值：成功返回新的fd,出错-1

 

6.send

函数的作用：经过socket传送数据，向对方发送数据

函数的原型：int send(int sock_fd,const void * msg,int len,unsigned int flags)

函数的参数：sock_fd：accept建立起来的socket连接描述符，连接远方的IP地址

> msg：发送的数据
> len：数据长度
> flags：设为0

返 回 值：成功是实际传递出去的字节数，出错-1

 

7.recv

函数的作用：经过socket接收数据

函数的原型：int recv(int socket_fd,void *buf,int len,unsigned int flag)

函数的参数：

sock_fd：accept以后的socket套接字描述符
buf：存放地址
len：接收数据的最大长度

返 回 值：成功：接收的字节数,出错：-1

 

8.大端模式：低字节存放在低地址，高字节存放在高地址

 小端模式：低字节存放在高地址，高字节存放在低地址

 

字节序的转化函数：

头文件：#include <apra/inet.h>

从主机发送到网络：

uint32_t  htonl(uint32_t,hostin32);  //32位数据传送，从主机到网络

uint16_t  htonl(uint16_t,hostin16);  //16位数据传送，从主机到网络

从网络到主机：

uint32_t ntohs(uint32_t netint32);   //32位的数据接收，从网络到主机

uint16_t ntohs(uint16_t netint16);   //16位的数据接收，从网络到主机

 

9.地址格式的转化

十进制点分形式转化成二进制形式：

函数的原型：unsigned long int inet_addr(const char *cp);

返 回 值: 成功则返回对应的二进制的数字，失败-1

函数的参数：

cp：放置如:”192.168.1.100”的点分IP地址

 

函数的原型：int inet_pton(int af,const char * src,void *dst)

函数的参数：

af：AF_INET,AF_INET6

src：点分的要转化的IP地址

返 回 值：成功1，格式无效0，出错-1

 

## 协议



**以太网协议格式**

 **![img](https://img-blog.csdn.net/20161105233147573?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)**

**IP协议格式**

 **![img](https://img-blog.csdn.net/20161105233156999?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)**

**TCP协议格式**

 **![img](https://img-blog.csdn.net/20161105233205433?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)**

**UDP协议格式**

 **![img](https://img-blog.csdn.net/20161105233239933?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)**



## 多路复用



谈一下自己的理解， 在没有出现 `epoll select pool` 之前， 通过 `accept` 监听 `socket` 线程是被阻塞，当有新的消息, 通过创建一个新的线程来处理新的消息,当有 n 个 客户端连接 ,也就创建 n 个 线程来处理消息.像这样:

```cpp
ClientSocket s = accept(ServerSocket);  // 阻塞
new Thread(s,function(){
    while( have data) {
        s.read(); // 阻塞
        s.send('xxxx');
    }
    s.close();
});
```

不能通过新建一个线程来处理客户端连接, 是因为如果是一个线程来处理客户端连接会造成监听线程与事件处理线程进行同步，使得新的客户端的 socket 同步到 事件线程,导致监听线程阻塞。



![img](https://pic3.zhimg.com/80/bf52854bd1dc678de998b77aebaa2311_1440w.jpg?source=1940ef5c)



**IO多路复用**的概念。多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如线程池



![img](https://pic1.zhimg.com/80/9155e2307879cd7ce515e7a997b9d532_1440w.jpg?source=1940ef5c)





## 什么是可写可读事件



以select和tcp socket为例，所谓可读事件，具体的说是指以下事件：
1 socket内核接收缓冲区中的可用字节数大于或等于其低水位SO_RCVLOWAT;
2 socket通信的对方关闭了连接，这个时候在缓冲区里有个文件结束符EOF，此时读操作将返回0；
3 监听socket的`backlog`队列有已经完成三次握手的连接请求，可以调用accept；
4 socket上有未处理的错误，此时可以用getsockopt来读取和清除该错误。

所谓可写事件，则是指：
1 socket的内核发送缓冲区的可用字节数大于或等于其低水位`SO_SNDLOWAIT`；
2 socket的写端被关闭，继续写会收到`SIGPIPE`信号；
3 非阻塞模式下，connect返回之后，发起连接成功或失败；
4  socket上有未处理的错误，此时可以用`getsockopt`来读取和清除该错误。

I/O多路复用技术（multiplexing）是什么？ - 晨随的回答 - 知乎 https://www.zhihu.com/question/28594409/answer/52763082



## select Api

```c
#include <sys/select.h>

int select(int nfds, fd_set *readfds, fd_set *writefds,
                fd_set *exceptfds, struct timeval *timeout);
```



> int nfds--->是一个整数值，是指集合中所有文件描述符的范围，即所有文件描述符的最大值加1
>
> fd_set *readfds用来检查一组可读性的文件描述符。
>
> fd_set *writefds
>
> 用来检查一组可写性的文件描述符。
>
> fd_set *exceptfds
>
> 用来检查文件文件描述符是否异常
>
> sreuct timeval *timeout 
>
> 最多等待时间，对阻塞操作则为NULL

> select函数的返回值
> 负值：select错误
> 正值：表示某些文件可读或可写
> 0：等待超时，没有可读写或错误的文件



```c
void FD_ZERO(fd_set *set);//清空一个文件描述符的集合
void FD_SET(int fd, fd_set *set);//将一个文件描述符添加到一个指定的文件描述符集合中
void FD_CLR(int fd, fd_set *set);//将一个指定的文件描述符从集合中清除；
int  FD_ISSET(int fd, fd_set *set);//检查集合中指定的文件描述符是否可以读写
```

```c
struct timeval {
    time_t         tv_sec;     /* seconds */
    suseconds_t    tv_usec;    /* microseconds */
};
```

```c
# define __FD_SETSIZE 1024
```



### 运行机制

Select会将全量fd_set从用户空间拷贝到内核空间，并注册回调函数， 在内核态空间来判断每个请求是否准备好数据 。select在没有查询到有文件描述符就绪的情况下，将一直阻塞(I/O多路服用中提过：select是一个阻塞函数)。如果有一个或者多个描述符就绪，那么select将就绪的文件描述符置位，然后select返回。返回后，由程序遍历查看哪个请求有数据。

### Select的缺陷

- 每次调用select，都需要把fd集合从用户态拷贝到内核态，fd越多开销则越大；
- 每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大
- select支持的文件描述符数量有限，默认是1024。参见/usr/include/linux/posix_types.h中的定义：



### select 例子

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, cahr **argv)
{
    struct timeval timeout;
    int ret = 0;
    fd_set rfds;
 
    FD_ZERO(&rfds);//清空描述符集合 
    FD_SET(0, &rfds);//将标准输入（stdin）添加到集合中
    FD_SET(sockfd, &rfds);//将我们的套接字描述符添加到集合中
    /*设置超时时间*/
    timeout.tv_sec = 1;   
    timeout.tv_usec = 0;

    /*监听套接字是否为可读*/
    ret = select(sockfd+1, &rfds, NULL, NULL, &timeout);
    if(ret == -1) {//select 连接失败
        perror("select failure\n");
        return 1;
    }   
    else if(ret == 0) {//超时（timeout）
        return 1;
    }   

    if(FD_ISSET(pesockfd, &rfds)) {//如果可读
                    
            //recv or recvfrom
            ..................
    }
    
   return 0; 
}
```

## poll api

```c
#include <poll.h>

int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```



## tcp 与 http 中 keepalive 的区别？

TCP的保活机制就是用来解决此类问题，这个机制我们也可以称作：keepalive。保活机制默认是关闭的，TCP连接的任何一方都可打开此功能。有三个主要配置参数用来控制保活功能。

如果在一段时间（**保活时间：tcp_keepalive_time**）内此连接都不活跃，开启保活功能的一端会向对端发送一个保活探测报文。

- 若对端正常存活，且连接有效，对端必然能收到探测报文并进行响应。此时，发送端收到响应报文则证明TCP连接正常，重置保活时间计数器即可。
- 若由于网络原因或其他原因导致，发送端无法正常收到保活探测报文的响应。那么在一定**探测时间间隔（tcp_keepalive_intvl）**后，将继续发送保活探测报文。直到收到对端的响应，或者达到配置的**探测循环次数上限（tcp_keepalive_probes）**都没有收到对端响应，这时对端会被认为不可达，TCP连接随存在但已失效，需要将连接做中断处理。

在探测过程中，对端主机会处于以下四种状态之一：

![img](C:\Users\pwj\Documents\md\mysql\source\tcp-keepalive)

[HTTP协议简介](https://zhuanlan.zhihu.com/p/224595048/edit#commonResp)中提到http协议是一个运行在TCP协议之上的无状态的应用层协议。它的特点是：客户端的每一次请求都要和服务端创建TCP连接，服务器响应后，断开TCP连接。下次客户端再有请求，则重新建立连接。

在早期的http1.0中，默认就是上述介绍的这种“请求-应答”模式。这种方式频繁的创建连接和销毁连接无疑是有一定性能损耗的。

所以引入了**keep-alive**机制。http1.0默认是关闭的，通过http请求头设置“connection: keep-alive”进行开启；http1.1中默认开启，通过http请求头设置“connection: close”关闭。

**keep-alive**机制：若开启后，在一次http请求中，服务器进行响应后，不再直接断开TCP连接，而是将TCP连接维持一段时间。在这段时间内，如果同一客户端再次向服务端发起http请求，便可以复用此TCP连接，向服务端发起请求，并重置timeout时间计数器，在接下来一段时间内还可以继续复用。这样无疑省略了反复创建和销毁TCP连接的损耗。

### **HTTP和TCP的长连接有何区别？HTTP中的keep-alive和TCP中keepalive又有什么区别？**

1、TCP连接往往就是我们广义理解上的长连接，因为它具备双端连续收发报文的能力；开启了keep-alive的HTTP连接，也是一种长连接，但是它由于协议本身的限制，服务端无法主动发起应用报文。

2、TCP中的keepalive是用来保鲜、保活的；HTTP中的keep-alive机制主要为了让支撑它的TCP连接活的的更久，所以通常又叫做：HTTP persistent connection（持久连接） 和 HTTP connection reuse（连接重用）。



1. [HTTP keep-alive和TCP keepalive的区别，你了解吗？](https://zhuanlan.zhihu.com/p/224595048)

给笔者更多的认为没有什么可比性，纯粹是将两个完全不同的东西混为一谈。


