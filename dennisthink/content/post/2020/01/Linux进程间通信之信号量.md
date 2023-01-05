---
title: Linux进程间通信之信号量
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
tags: ["linux","ipc"]
---

# Linux进程间通信之信号量

在Linux进程间,可以使用信号量进行通信,使用Linux信号量进行通信的特点有:

1. 对进程间的关系没有要求。
2. 可以进行多个进程间的通信。

示例代码:
```cpp
/**
 * @file ProcessSemaphore.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux进行进程间通信的示例代码
 * @version 0.1
 * @date 2020-06-01
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/sem.h>

#include <errno.h>
#include <iostream>
#include <unistd.h>
#include <sys/types.h>

const int G_SEMAPHORE_KEY=1990;
union semun
{
	int val;
	struct semid_ds *buf;
	unsigned short *arry;
};


void Semaphore_P(const int semaId)
{
    struct sembuf sem_b;
	sem_b.sem_num = 0;
	sem_b.sem_op = -1;//P()
	sem_b.sem_flg = SEM_UNDO;
	if(semop(semaId, &sem_b, 1) == -1)
	{
		std::cerr<<"semaphore_p failed"<<std::endl;
		return ;
	}
    
	return;
}

void Semaphore_V(const int semaId)
{
    struct sembuf sem_b;
	sem_b.sem_num = 0;
	sem_b.sem_op = 1;//V()
	sem_b.sem_flg = SEM_UNDO;
	if(semop(semaId, &sem_b, 1) == -1)
	{
		std::cerr<< "semaphore_v failed"<<std::endl;
		return ;
	}
}

int SonProcess()
{
    std::cout<<"Process Id: "<<getpid()<<" is running"<<std::endl;
    int semaId = semget(G_SEMAPHORE_KEY,1,IPC_CREAT|0666);
    if(semaId < 0)
    {
        std::cerr<<" create semaphore failed"<<std::endl;
        return -1;
    }

    for(int i = 0 ; i < 5; i++)
    {
        Semaphore_V(semaId);
        std::cout<<"Process Id: "<<getpid()<<" is running"<<std::endl;
        Semaphore_P(semaId);
        sleep(rand()%10);
    }
    union semun sem_union;
    if(semctl(semaId,0,IPC_RMID,sem_union) == -1)
    {
        std::cout<<"Process Id: "<<getpid()<<" remove semaphore failed"<<std::endl;
    }
    return 0;
}

int main(int argc, char *argv[])
{
    pid_t fid= fork();
    if(fid < 0)
    {
        return 0;
    }
    
    if(fid == 0)
    {
        SonProcess();
    }
    else
    {
        SonProcess();
    }
    return 0;
}
```

进程间使用信号量进行通信的效果演示:
![](https://www.dennisthink.com/wp-content/uploads/2020/06/Linux_Process_Semaphore_demo.jpg)