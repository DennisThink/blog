---
title: "Linux 网络编程之 Select"
date: 2017-11-20T21:38:52+08:00
lastmod: 2017-11-20T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-csdn"]
comment: true
mathjax: false
---

```cpp
/*server*/
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <sys/time.h>
#include <stdlib.h>
#include "common.h"
#include <iostream>
void process_cli(Client *client, char* recvbuf, int len,int count);  //客户请求处理函数
int main(int argc,char * argv[])
{
    int listenFd=0;
    //地址信息结构体大小
    if ((listenFd = socket(AF_INET, SOCK_STREAM, 0)) == -1) {    //调用socket创建用于监听客户端的socket
        std::cout<<"Creating socket failed."<<std::endl;
        exit(1);
    }

    int opt = SO_REUSEADDR;
    setsockopt(listenFd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));    //设置socket属性

    struct sockaddr_in serverAddr;     //服务器地址信息结构体
    bzero(&serverAddr,sizeof(serverAddr));
    serverAddr.sin_family=AF_INET;
    serverAddr.sin_port=htons(PORT);
    serverAddr.sin_addr.s_addr = htonl (INADDR_ANY);
    if (bind(listenFd, (struct sockaddr *)&serverAddr, sizeof(struct sockaddr)) == -1) {    //调用bind绑定地址
        std::cout<<"Bind error."<<std::endl;
        exit(1);
    }

    if(listen(listenFd,BACKLOG) == -1) {
        //调用listen开始监听
        std::cout << "listen() error"<<std::endl;
        exit(1);
    }


 
    Client client[FD_SETSIZE];     //FD_SETSIZE为select函数支持的最大描述符个数
    char recvbuf[MAXDATASIZE];    //缓冲区
    int sin_size=sizeof(struct sockaddr_in);
    //初始化select
    int maxI;
    int maxFd;
    int sockfd;
    int    nReady;
    ssize_t      n;
    fd_set readSet;//可读socket的文件描述符的集合
    fd_set allSet;//所有socket的文件描述符的集合
    for (int i = 0; i < FD_SETSIZE; i++) {
        client[i].fd = -1;
    }
    maxFd= listenFd;
    FD_ZERO(&allSet);        //清空
    FD_SET(listenFd, &allSet);    //将监听socket加入select检测的描述符集合

    int connectFd=0;     //socket文件描述符
    int clientCount=0;
    while(1)
    {
        struct sockaddr_in addr;
        readSet = allSet;
        nReady = select(maxFd+1, &readSet, NULL,NULL, NULL);    //调用select
        if (FD_ISSET(listenFd, &readSet)) {      //检测是否有新客户端请求
            //调用accept，返回服务器与客户端连接的socket描述符
            if ((connectFd = accept(listenFd,(struct sockaddr *)&addr,(socklen_t *)&sin_size))==-1) {
                std::cout<<"accept() error"<<std::endl;
                continue;
            }

            //将新客户端的加入数组
            int i = 0;
            for (int i = 0; i < FD_SETSIZE; i++) {
                if (client[i].fd < 0) {
                    client[i].fd = connectFd;    //保存客户端描述符
                    char addrBuf[32];
                    sprintf(addrBuf,"%s:%d",inet_ntoa(addr.sin_addr),addr.sin_port);
                    clientCount++;
                    client[i].m_addr=std::string(addrBuf);
                    break;
                }
            }
            if (i == FD_SETSIZE)
            {
                std::cout<<"too many clients"<<std::endl;
            }
            if (i > maxI)          //数组最大元素值
            {
                maxI = i;
            }
            FD_SET(connectFd, &allSet);   //将新socket连接放入select监听集合
            if (connectFd > maxFd)
            {
                maxFd = connectFd;   //确认maxfd是最大描述符
            }

            if (--nReady <= 0)
            {
                continue;      //如果没有新客户端连接，继续循环
            }
        }

        for (int i = 0; i <= maxI; i++) 
        {
            if ( (sockfd = client[i].fd) < 0)       //如果客户端描述符小于0，则没有客户端连接，检测下一个
            { 
                continue;
            }
            if (FD_ISSET(sockfd, &readSet)) 
            {        //检测此客户端socket是否有数据
                int nReadLength = recv(sockfd, recvbuf, MAXDATASIZE,0);
                if (nReadLength == 0) 
                { //从客户端socket读数据，等于0表示网络中断
                    close(sockfd);        //关闭socket连接
                    FD_CLR(sockfd, &allSet);    //从监听集合中删除此socket连接
                    client[i].fd = -1;        //数组元素设初始值，表示没客户端连接
                    std::cout<<"Client Addr: "<< client[i].m_addr << " Close "<<std::endl;
                    clientCount--;
                } 
                else
                {
                    process_cli(&client[i], recvbuf, nReadLength,clientCount);    //接收到客户数据，开始处理
                }
                if (--nReady <= 0)
                {
                    break;       //如果没有新客户端有数据，跳出for循环回到while循环
                }  
            }
        }
    }
    close(listenFd);  //关闭服务器监听socket
}

void process_cli(Client *client, char* recvbuf, int len,int count)
{
    char sendbuf[MAXDATASIZE];
    OctArray16 * recvData = (OctArray16*)recvbuf;
    std::cout<<"RecvData From Client:"<< client->m_addr <<"   Data:"<<recvData->toString()<<std::endl;
    sprintf(sendbuf,"%d--%s",count,GetCurrentTime().c_str());
    OctArray16 sendData(sendbuf);
    send(client->fd,&sendData,sendData.size(),0);
}

```


```cpp
/* client */
#include <stdio.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <netdb.h>
#include <unistd.h>
#include <string.h>
#include <arpa/inet.h>
#include <errno.h>
#include <stdlib.h>
#include <iostream>
#include "common.h"


int main(int argc, char *argv[])
{
    char hostname[100];
    gethostname(hostname,sizeof(hostname));
    int sockfd, numbytes;

    struct hostent *he;
    struct sockaddr_in serv_addr;
    if ((he = gethostbyname(defaultHostName)) == NULL)
    {
        std::cout<<"gethostbyname error!"<<std::endl;
        return 0;;
    }
    if ((sockfd = socket(AF_INET, SOCK_STREAM, 0)) == -1)
    {
        std::cout<< "socket error!"<<std::endl;
        return 0;
    }
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(PORT);
    serv_addr.sin_addr = *((struct in_addr *)he->h_addr);
    bzero(&(serv_addr.sin_zero), 8);
    if (connect(sockfd, (struct sockaddr *)&serv_addr, sizeof(struct sockaddr)) == -1)
    {
        std::cout<<"connect error!"<<std::endl;
        return 0;
    }
    pid_t pId= getpid();
    char SendBuf[16];

    srand(time(0));
    char buf[MAXDATASIZE];
    for(int x = 0 ; x < 10 ; x++) {
        sleep(rand()%10);
        sprintf(SendBuf,"%d",pId);
        OctArray16 sendData(SendBuf);
        if ((numbytes = send(sockfd,&sendData,sendData.size(),0)) != -1)
        {
            if ((numbytes = read(sockfd, buf, MAXDATASIZE)) == -1)
            {
                std::cout<<"read error!\n"<<std::endl;
                return 0;
            }
            OctArray16 * RecvData = (OctArray16*)buf;
            std::cout<<"Client  "<< pId <<"  Recv: "<<RecvData->toString()<<std::endl;
        }
    }
    close(sockfd);       
}
```

```cpp
/*common.h*/
#ifndef COMMON_H_
#define COMMON_H_
const int PORT=2248;           //服务器端口
const int BACKLOG=5;           //listen队列中等待的连接数
const int MAXDATASIZE=1024;    //缓冲区大小
const char defaultHostName[] = "127.0.0.1";
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <iostream>
#include <time.h>
#include <string.h>
struct Client {       //客户端结构体
    int fd;        //客户端socket描述符
    std::string m_addr;
};
class OctArray16 {
public:
    uint32_t   m_len;
    char     data[28];
    size_t size(){
        return 32;
    }
    std::string toString(){
        return std::string(data,m_len);
    }
    
    OctArray16(const char *str){
        m_len = strlen(str);
        strcpy(data,str);
    }
};

std::string GetCurrentTime(){
    time_t cutTime=time(0);
    tm * tmT=localtime(&cutTime);
    char buf[12];
    sprintf(buf,"%d:%d:%d",tmT->tm_hour,tmT->tm_min,tmT->tm_sec);
    return std::string(buf);
}
#endif
```

```cmake
cmake_minimum_required(VERSION 2.8)

## Use the variable PROJECT_NAME for changing the target name
set( PROJECT_NAME "SelectLearn" )

## Set our project name
project(${PROJECT_NAME})

## Use all the *.cpp files we found under this folder for the project
SET(CLIENT_SOURCE Client.cpp common.h)
SET(SERVER_SOURCE Server.cpp common.h)
## Define the executable
add_executable(server ${SERVER_SOURCE})
add_executable(client ${CLIENT_SOURCE})
```