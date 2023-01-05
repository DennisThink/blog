---
title: Linux线程间通信之信号量
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Linux线程间通信之信号量

本程序通过用四个线程输出从1~100的方式来展示在进行多线程编程的时候,对于信号量和互斥量的使用。


程序代码:
```cpp
/**
 * @file LinuxMultiThread.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief Linux多线程演示代码,使用4个线程打印从1~100
 * @version 0.1
 * @date 2020-06-02
 * 
 * @copyright Copyright (c) 2020
 * 
 */

#include <stdio.h> 
#include <stdlib.h> 
#include <pthread.h>
#include <errno.h>
#include <unistd.h>
#include <iostream>

typedef void * (*pThreadFun)(void *);

static pthread_cond_t cond1=PTHREAD_COND_INITIALIZER;
static pthread_cond_t cond2=PTHREAD_COND_INITIALIZER;
static pthread_cond_t cond3=PTHREAD_COND_INITIALIZER;
static pthread_cond_t cond4=PTHREAD_COND_INITIALIZER;
static pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER;
static pthread_mutex_t mutex2 = PTHREAD_MUTEX_INITIALIZER;
static pthread_mutex_t mutex3 = PTHREAD_MUTEX_INITIALIZER;
static pthread_mutex_t mutex4 = PTHREAD_MUTEX_INITIALIZER;
void * Thread1(void *)
{
    static int nCount = 1;
    
    while(nCount <= 100)
    {
        pthread_mutex_lock(&mutex1);
        pthread_cond_wait(&cond1,&mutex1);
        std::cout<<"Thread Id "<<pthread_self()<<"  Value: "<<nCount<<std::flush<<std::endl;
        nCount +=4;
        pthread_mutex_unlock(&mutex1);
        pthread_cond_signal(&cond2);
    }
}

void * Thread2(void *)
{
    static int nCount = 2;
    
    while(nCount <= 100)
    {
        pthread_mutex_lock(&mutex2);
        pthread_cond_wait(&cond2,&mutex2);
        std::cout<<"Thread Id "<<pthread_self()<<"  Value: "<<nCount<<std::flush<<std::endl;
        nCount +=4;
        pthread_mutex_unlock(&mutex2);
        pthread_cond_signal(&cond3);
    }
}

void * Thread3(void *)
{
    static int nCount = 3;
    
    while(nCount <= 100)
    {
        pthread_mutex_lock(&mutex3);
        pthread_cond_wait(&cond3,&mutex3);
        std::cout<<"Thread Id "<<pthread_self()<<"  Value: "<<nCount<<std::flush<<std::endl;
        nCount +=4;
        pthread_mutex_unlock(&mutex3);
        pthread_cond_signal(&cond4);
    }
}

void * Thread4(void *)
{
    static int nCount = 4;
    while(nCount <= 100)
    {
        pthread_mutex_lock(&mutex4);
        pthread_cond_wait(&cond4,&mutex4);
        std::cout<<"Thread Id "<<pthread_self()<<"  Value: "<<nCount<<std::flush<<std::endl;
        nCount +=4;
        pthread_mutex_unlock(&mutex4);
        pthread_cond_signal(&cond1);
    }
}

int main(int argc, char* argv[])
{
    {
        pthread_cond_init(&cond1, NULL);
        pthread_cond_init(&cond2, NULL);
        pthread_cond_init(&cond3, NULL);
        pthread_cond_init(&cond4, NULL);
    }
    {
        pthread_mutex_init(&mutex1,NULL);
        pthread_mutex_init(&mutex2,NULL);
        pthread_mutex_init(&mutex3,NULL);
        pthread_mutex_init(&mutex4,NULL);
    }
    pthread_t threadId1;
    int nRC1 = pthread_create(&threadId1, NULL,Thread1,NULL);
    if(nRC1 == 0)
    {
        std::cout<<"Create Thread1 Succeed"<<std::endl;
    }
    else
    {
        exit(1);
    }
    
    pthread_t threadId2;
    int nRC2 = pthread_create(&threadId2, NULL,Thread2,NULL);
    if(nRC2 == 0)
    {
        std::cout<<"Create Thread2 Succeed"<<std::endl;
    }
    else
    {
        exit(1);
    }
    
    pthread_t threadId3;
    int nRC3 = pthread_create(&threadId3, NULL,Thread3,NULL);
    if(nRC3 == 0)
    {
        std::cout<<"Create Thread3 Succeed"<<std::endl;
    }
    else
    {
        exit(1);
    }

    pthread_t threadId4;
    int nRC4 = pthread_create(&threadId4, NULL,Thread4,NULL);
    if(nRC4 == 0)
    {
        std::cout<<"Create Thread4 Succeed"<<std::endl;
    }
    else
    {
        exit(1);
    }
    sleep(2);
    pthread_cond_signal(&cond1);
    sleep(20);
    return 0;
}
```

效果演示:
![](https://www.dennisthink.com/wp-content/uploads/2020/06/Linux_Multi_Thread_Demo.jpg)


参考资料:
[Linux多线程编程](https://www.cnblogs.com/skynet/archive/2010/10/30/1865267.html)