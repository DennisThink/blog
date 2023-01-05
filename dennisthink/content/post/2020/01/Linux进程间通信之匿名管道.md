---
title: Linux进程间通信之匿名管道
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Linux进程间通信之匿名管道

Linux的进程间可以使用匿名管道的方式进行通信,它的特点是:

1. 匿名管道仅可在父子进程间使用
2. 管道一般是半双工的工作模式

示例代码如下:

```cpp
/**
 * @file AnonymousPipe.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 匿名管道,用于示范父子进程间通信
 * @version 0.1
 * @date 2020-05-30
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

    int anonymousPipe[2]={0};
    char quitBuf[]="quit\n";
    char readBuf[128]={0};
    //创建管道
    if(pipe(anonymousPipe)){
       std::cerr<<"Create AnonymousPipe Failed "<<std::endl;
       return -1;
    }

    switch(fork()){
        case -1:
        {
            std::cout<<"Fork Failed"<<std::endl;
            return -1;
        }break;   
        case 0:
        {   
            // 子进程,读取数据
            std::cout<<"Create Child Process Succeed"<<std::endl;

            close(anonymousPipe[1]);//关闭未使用的写端
            while(true)
            {
                memset(readBuf,0,128);
                read(anonymousPipe[0],readBuf,128);//读取子进程的输出
                std::string strRead(readBuf);
                if(strRead=="quit\n")
                {
                    break; 
                }
                std::cout<<"Child Process Read:"<<strRead<<std::endl;
            }
            std::cout<<"Child Process Exit"<<std::endl;
        }break;    
        default:
        {    
            //父进程
            std::cout<<"Parent Process Succeed"<<std::endl;
            close(anonymousPipe[0]);//关闭未使用的读端
            sleep(5);//等待子进程启动

            std::string strResult;
            for(int i = 0 ; i < 10 ; i++)
            {
                strResult=std::to_string(i)+"\n";
                write(anonymousPipe[1], strResult.c_str(), strResult.length());//给子进程写数据
                sleep(1);
            }

            write(anonymousPipe[1], quitBuf, sizeof(quitBuf));
            wait(nullptr);//等待子进程结束
            std::cout<<"Parent Process Exit"<<std::endl;
        }break;
    }
   
    return 0;
}  
```

程序输出如下:

![AnonymousPipe](https://www.dennisthink.com/wp-content/uploads/2020/05/Linux_Anonymous_Pipe_Example.jpg)