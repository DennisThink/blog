---
title: Linux下用C++实现子进程控制台的输入和输出
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Linux下用C++实现子进程控制台的输入和输出

[原文链接](https://www.dennisthink.com/2020/05/29/788/)


在linux下对编写的控制台程序进行测试的话,就需要对子进程的控制台输入和输出进行控制。在测试进程中,完成子进程控制台的输入和输出操作,我们通用的做法是通过父子进程建立管道,通过dup2完成管道和标准输入、输出的绑定。在父进程中通过read和write函数对管道进行读写,以下就是示例代码。

如果你想在Windows平台实现这样的功能,可以参考[Windows用C++实现控制台的输入输出](https://www.dennisthink.com/2019/05/12/405/)

## 1、父进程示例代码

```cpp
/**
 * @file SendString.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 调用RecvString的函数,在测试的时候作为主进程
 * @version 0.1
 * @date 2020-05-29
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <string>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <iostream>
int main(int argc, char *argv[]) {
    int writePipe[2]={0};//Parent--->child
    int readPipe[2]={0};//Child---->Parent
    char buf[] = "HELLO\n";
    char quitBuf[]="quit\n";
    char readBuf[128]={0};
    //创建管道
    if(pipe(writePipe)){
       std::cerr<<"Create Write Pipe Failed "<<std::endl;
       return -1;
    }

    //创建管道
    if(pipe(readPipe)){
        std::cerr<<"Create Read Pipe Failed "<<std::endl;
        return -1;
    }

    switch(fork()){
        case -1:
        {
            perror("fork");
            return -1;
        }break;   
        case 0:
        {   
            // 子进程
            std::cout<<"Create Child Process Succeed"<<std::endl;

            close(writePipe[1]);//关闭未使用的写端
            close(readPipe[0]);//关闭未使用的读端
            dup2(writePipe[0], STDIN_FILENO);//映射子进程的标准输入
            dup2(readPipe[1],STDOUT_FILENO);//映射子进程的标准输出

            int nResult = execl("/home/dennis/Code/RecvString", //子进程路径
            "RecvString",//子进程名称
            (char*)(0));//参数
            std::cout<<"Start Child "<<nResult<<std::endl;//如果启动失败,则此信息输出
        }break;    
        default:
        {    
            //父进程
            std::cout<<"Parent Succeed"<<std::endl;
            close(writePipe[0]);//关闭未使用的读端
            close(readPipe[1]);//关闭未使用的写端
            sleep(5);//等待子进程启动
            // parent
    
            // 业务代码开始
            //处理和子进程的交互,此处属于业务逻辑代码,需要和具体的业务结合
            std::string strResult;
            for(int i = 0 ; i < 100 ; i++)
            {
                strResult = std::to_string(i)+buf;
                memset(readBuf,0,128);
                read(readPipe[0],readBuf,128);//读取子进程的输出
                std::cout<<"Read From Child: "<<readBuf<<std::endl;
                write(writePipe[1], strResult.c_str(), strResult.length());//给子进程写数据
            }

            write(writePipe[1], quitBuf, sizeof(quitBuf));
            memset(readBuf,0,128);
            read(readPipe[0],readBuf,128);
            std::cout<<"Read From Child: "<<readBuf<<std::endl;
            //业务代码结束
    

            wait(nullptr);//等待子进程结束
        }break;
    }
    std::cout<<"Parent Process Exit"<<std::endl;
    close(readPipe[0]);
    close(readPipe[1]);
    close(writePipe[0]);
    close(writePipe[1]);
    return 0;
}  
```

## 2、子进程示例代码

```cpp
/**
 * @file RecvString.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 被测试的程序,输出用户输入的字符串,输入quit退出
 * @version 0.1
 * @date 2020-05-29
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>
#include <string>

int main(int argc,char * argv[])
{
    std::string strInput;
    while(true)
    {
        std::cout<<"Please Input Your String:"<<std::endl;
        std::cin>>strInput;
        std::cout<<"Your Input is:  " <<strInput<<std::endl;
        if("quit"==strInput)
        {
            std::cout<<"Exit Program"<<std::endl;
            break;
        }
    }
    return 0;
}
```

## 3、程序演示

单独调用子程序
![单独调用子程序](https://www.dennisthink.com/wp-content/uploads/2020/05/Linux_RecvString.jpg)

通过父进程调用子程序
![通过父进程调用子程序](https://www.dennisthink.com/wp-content/uploads/2020/05/Linux_SendString.jpg)
