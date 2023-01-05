---
title: Linux进程间通信之消息队列
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Linux进程间通信之消息队列

在Linux进程间可以使用消息队列进行通信,消息队列有以下特点:

1. 消息的发送和接收可以异步进行。
2. 在进程退出以后,消息队列仍然可以存在。


公用的头文件
```cpp
/**
 * @brief MsgQueueHeader.h
 * 
 */
#ifndef _MSG_QUEUE_HEADER_H_
#define _MSG_QUEUE_HEADER_H_
const static int MSG_KEY = 1990;
const static long DENNIS_MSG_TYPE=2;
struct MsgQueueElement 
{
    long msgtype;
    char msgData[256];
};
#endif
```

消息队列的发送端代码:
```cpp
/**
 * @file MsgQueueSend.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux消息队列发送端代码示例
 * @version 0.1
 * @date 2020-05-31
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>
#include <sys/msg.h>
#include <sys/ipc.h>
#include <sys/types.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include "MsgQueueHeader.h"



int main(int argc, char *argv[])
{
    int msgId = msgget(MSG_KEY,IPC_EXCL);//检查消息队列是否存在
    if(msgId < 0) //不存在消息队列
    {
        msgId = msgget(MSG_KEY,IPC_CREAT|0666);//创建消息队列
        if(msgId < 0)
        {
            std::cerr<<"Create Msg Queue Failed "<<std::endl;
            return -1;
        }
    }
    struct MsgQueueElement myMsg;
    myMsg.msgtype = DENNIS_MSG_TYPE;
    int i = 0;
    while(i < 50)
    {
        i++;
        std::string strMsg="www.dennisthink.com "+std::to_string(i);
        strcpy(myMsg.msgData,strMsg.c_str());
        int nRet = msgsnd(msgId,&myMsg,sizeof(myMsg),IPC_NOWAIT);//将数据放入消息队列
        if(nRet < 0)
        {
            std::cerr<<"msgsnd failed "<<errno<<strerror(errno)<<std::endl;
            break;
        }
    }

    sleep(10);
    //msgctl(msgId,IPC_RMID,0); //删除消息队列
    return 0;
}



```


消息队列的接收端代码:
```cpp
/**
 * @file MsgQueueRecv.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux消息队列接收方示例代码
 * @version 0.1
 * @date 2020-05-31
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>
#include <sys/msg.h>
#include <sys/ipc.h>
#include <sys/types.h>
#include <errno.h>
#include <string.h>
#include "MsgQueueHeader.h"

int main(int argc, char* argv[])
{

    int msgId = msgget(MSG_KEY,IPC_EXCL);//作为接收端,直接判断消息队列是否存在
    if(msgId < 0)
    {
        std::cerr<<"Msg Queue Not Exist "<<std::endl;
        return -1;
    }

    struct MsgQueueElement myMsg;
    while(true)
    {
        memset(&myMsg, 0, sizeof(myMsg));
        int nRet = msgrcv(msgId,&myMsg,sizeof(myMsg),0,0);//从消息队列中读取数据,如果队列被删除,此处会引发崩溃
        if(nRet >= 0)
        {
            std::cout<<"Recv From Queue: "<<myMsg.msgData<<std::endl;
        }
        else
        {
            break;
        }
    }
    return 0;
}
```

消息队列的效果演示:

![](https://www.dennisthink.com/wp-content/uploads/2020/05/Linux_Msg_Queue_Demo.jpg)