---
title: Linux进程间通信之域套接字
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
tags: ["linux","ipc"]
---
# Linux进程间通信之域套接字

在Linux进程间,可以使用域套接字进行通信,它有以下的特点:

1. 可以进行一对多的进程间通信
2. 比普通的套接字处理的流程更少一些,不用走协议栈
3. 不能跨操作系统进行通信

域套接字的服务器端程序代码:
```cpp
/**
 * @file DomainSocketServer.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux域套接字的服务端程序的示例代码
 * @version 0.1
 * @date 2020-06-01
 * 
 * @copyright Copyright (c) 2020
 * 
 */
#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h> 
#include <sys/un.h>
#include <unistd.h> 
#include <stdlib.h> 
#include <iostream>
#include <string>

const static std::string G_STR_SERVER_SOCK_ADDR="DennisThinkSocket";

int main(int argc, char *argv[])
{
    int nServerSocketFd = 0;
    int nClientSocketFd = 0;

    struct sockaddr_un serverAddr;
    struct sockaddr_un clientAddr;

    unlink(G_STR_SERVER_SOCK_ADDR.c_str());
    nServerSocketFd = socket(AF_UNIX,SOCK_STREAM,0);

    serverAddr.sun_family = AF_UNIX;
    strcpy(serverAddr.sun_path,G_STR_SERVER_SOCK_ADDR.c_str());
    int serverLen = sizeof(serverAddr);

    bind(nServerSocketFd,(struct sockaddr*)(&serverAddr),serverLen);

    listen(nServerSocketFd,5);

    int clientAddrLen = sizeof(clientAddr);
    nClientSocketFd = accept(nServerSocketFd,(struct sockaddr*)(&clientAddr),(socklen_t*)(&clientAddrLen));

    char recvBuf[128]={0};
    for(int i = 0 ; i < 10 ; i++)
    {
        {
            memset(recvBuf,0,128);
            int readBytes = read(nClientSocketFd,recvBuf,128);
            if(readBytes>0)
            {
                std::cout<<"Read From Client: "<<recvBuf<<std::endl;
            }
            sleep(1);
        }

        {
            std::string strSend = std::to_string(i);
            int writeBytes = write(nClientSocketFd,strSend.c_str(),strSend.size());
            if(writeBytes < 0)
            {
                exit(1);
            }
            sleep(1);
        }
    }
    close(nClientSocketFd);
    unlink(G_STR_SERVER_SOCK_ADDR.c_str());

    return 0;
}
```

域套接字的客户端程序代码:
```cpp
/**
 * @file DomainSocketClient.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux域套接字的客户端代码示例
 * @version 0.1
 * @date 2020-06-01
 * 参考 https://blog.csdn.net/qq_22863733/article/details/80629920
 * @copyright Copyright (c) 2020
 * 
 */

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h> 
#include <sys/un.h>
#include <unistd.h> 
#include <stdlib.h> 
#include <iostream>
#include <string>
#include <errno.h>

const std::string G_STR_SERVER_SOCK_ADDR="DennisThinkSocket";
int main(int argc, char *argv[])
{
    int nSockFd = socket(AF_UNIX, SOCK_STREAM,0);
    if(nSockFd < 0)
    {
        std::cerr<<"Create Socket Failed"<<std::endl;
    }
    struct sockaddr_un addr;
    addr.sun_family = AF_UNIX;
    strcpy(addr.sun_path,G_STR_SERVER_SOCK_ADDR.c_str());

    int nResult = connect(nSockFd,(struct sockaddr *)&addr,sizeof(addr));
    if(nResult == -1)
    {
        std::cerr<<"connect to server failed"<<errno<<std::endl;
        return -1;
    }

    for(int i = 0 ; i <  5 ; i++)
    {
        {
            std::string strSend="www.dennisthink.com"+std::to_string(i);
            int nWriteBytes = write(nSockFd,strSend.c_str(),strSend.length());
            if(nWriteBytes < 0)
            {
                break;
            }
        }
        {
            char readbuf[128]={0};
            int nReadBytes = read(nSockFd,readbuf,128);
            if(nReadBytes < 0)
            {
                break;
            }
            else
            {
                std::cout<<" Read From Server: "<<readbuf<<std::endl;
            }
        }
    }
    close(nSockFd);
    return 0;
}
```

代码的演示效果:
![](https://www.dennisthink.com/wp-content/uploads/2020/06/Linux_Domain_Socket_Demo.jpg)