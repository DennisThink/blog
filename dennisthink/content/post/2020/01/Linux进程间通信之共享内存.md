---
title: Linux进程间通信之共享内存
date: 2020-01-20T21:38:52+08:00
lastmod: 2020-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
tags: ["linux","ipc"]
---

# Linux进程间通信之共享内存

在Linux上可以使用共享内存进行进程间通信,他的主要特点如下: 

1. 适用于非父子进程间通信。
2. 通信的效率很高,特别是数据量比较大的时候。

示例代码如下:
```cpp
/**
 * @file ProcessSharedMemory.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux使用共享内存进行通信
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
#include <string>
#include <string.h>
#include <sys/shm.h>


const int G_SEMAPHORE_KEY=1990;
const int G_SHARED_MEM_KEY=1024;

struct SharedMem_St 
{
    pid_t m_pid;
    char  m_msg[128];
};

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

void WriteSharedMem(SharedMem_St* pMem)
{
    pMem->m_pid = getpid();
    memset(pMem->m_msg,0,128);
    std::string strTime = std::to_string(time(NULL));
    strcpy(pMem->m_msg,strTime.c_str());
    std::cout<<"Process Write SharedMem: "<<getpid()<<" "<<strTime<<std::flush<<std::endl;
}

void ReadSharedMem(SharedMem_St* pMem)
{
    std::cout<<"Process Id: "<<getpid()<<"  Read: "<<pMem->m_pid<<"  "<<pMem->m_msg<<std::flush<<std::endl;
}

using CallBackFunc = decltype(ReadSharedMem);

int SharedMemProcess(CallBackFunc& pCallBack)
{
    std::cout<<"Process Id: "<<getpid()<<" is running"<<std::endl;
    int semaId = semget(G_SEMAPHORE_KEY,1,IPC_CREAT|0666);
    if(semaId < 0)
    {
        std::cerr<<" create semaphore failed"<<std::endl;
        return -1;
    }
    SharedMem_St * pMem = nullptr;
    int sharedMemId = shmget(G_SHARED_MEM_KEY,sizeof(SharedMem_St),0666|IPC_CREAT);
    if(sharedMemId < 0)
    {
        if(EEXIST == errno)
        {
            sharedMemId = shmget(G_SEMAPHORE_KEY,sizeof(SharedMem_St),0666);
            pMem = (SharedMem_St*)shmat(sharedMemId,NULL,0);
            std::cout<<pMem->m_pid<<std::endl;
        }
    }
    else
    {
        pMem = (SharedMem_St*)shmat(sharedMemId,NULL,0);
        std::cout<<pMem->m_pid<<std::endl;
    }
   
    

    if(pMem)
    {
        memset(pMem,sizeof(SharedMem_St),0);
        for(int i = 0 ; i < 5; i++)
        {
            Semaphore_V(semaId);
            pCallBack(pMem);
            Semaphore_P(semaId);
            sleep(rand()%10);
        }
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
        SharedMemProcess(WriteSharedMem);
    }
    else
    {
        SharedMemProcess(ReadSharedMem);
    }
    return 0;
}
```

示例效果演示:
![](https://www.dennisthink.com/wp-content/uploads/2020/06/Linux_Process_Shared_Memory.jpg)