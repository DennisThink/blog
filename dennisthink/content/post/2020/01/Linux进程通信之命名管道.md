---
title: Linux进程通信之命名管道
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
tags: ["linux","ipc"]
---

# Linux进程通信之命名管道

Linux进程间通信可以使用命名管道,它的主要特点是:

1. 可以进行非父子进程间的通信
2. 发送进程和接收进程不需要同时启动。

发送端示例代码:

```cpp
/**
 * @file NamedPipeSend.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 命名管道发送数据的示例代码
 * @version 0.1
 * @date 2020-05-30
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h> 
#include <fcntl.h> 
#include <string.h>
#include <string>

const static std::string G_PIPE_NAME="www.dennisthink.com";

int main(int argc, char *argv[])
{
    if( (mkfifo(G_PIPE_NAME.c_str(),S_IRUSR | S_IWUSR) < 0) && 
        (errno != EEXIST) ) 
    {
        std::cerr << "Create Named Pipe failed "<<std::endl;
        return -1;
    }

    int nWriteFd = open(G_PIPE_NAME.c_str(),O_WRONLY,0);
    if(nWriteFd > 0)
    {
        sleep(5);//等待管道读取进程启动
        std::string strWrite="www.dennisthink.com";
        for(int i = 0 ; i < 10 ; i++)
        {
            write(nWriteFd,strWrite.c_str(),strWrite.length());
            sleep(1);
        }
        write(nWriteFd,"quit\n",strlen("quit\n"));
        close(nWriteFd);
    }
    return 0;
}
```

接收端示例代码:

```cpp
/**
 * @file NamedPipeRecv.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 命名管道接收数据的示例代码
 * @version 0.1
 * @date 2020-05-30
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <iostream>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h> 
#include <fcntl.h> 
#include <string.h>
#include <string>

const static std::string G_PIPE_NAME="www.dennisthink.com";

int main(int argc, char *argv[])
{
    int nReadFd=-1;
    char readBuf[128]={0};
    nReadFd = open(G_PIPE_NAME.c_str(), O_RDONLY,0);
    if(nReadFd < 0 )
    {
        std::cerr<<"Open Named Pipe Failed"<<std::endl;
        return -1;
    }

    int nReadLen=0;
    while(true)
    {
        memset(readBuf,0,128);
        nReadLen = read(nReadFd,readBuf,128);
        if(nReadLen > 0)
        {
            std::cout<<"Read From Pipe(www.dennisthink.com): "<<readBuf<<std::endl;
        }
        if(0==strcmp(readBuf,"quit\n"))
        {
            break;
        }
    }
    std::cout<<"Read Pipe Process Quit\n"<<std::endl;
    return 0;
}
```

效果展示:
![Named_Pipe_Recv](https://www.dennisthink.com/wp-content/uploads/2020/05/Linux_Named_Pipe_Recv.jpg)