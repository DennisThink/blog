---
title: "Windows用C++实现控制台的输入输出"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

## 1.背景介绍    

在公司用C++编写了操作短信猫的程序，每次操作短信猫都需要在控制台进行输入和输出。在进行回归测试的时候，每次都要输入输出，工作量很大。通过编写测试程序，调用短信猫操作程序，来实现对应的功能，可以减轻测试的工作量，防止重测和漏测。

如果你想在Linux下完成同样的工作,可以参考这篇文章[](https://www.dennisthink.com/2020/05/29/788/)Linux下用C++实现子进程控制台的输入和输出。

## 2. 被测试程序    

被测试程序不需要进行任何的修改，以下的程序为一个示例。

```cpp {linenos=table}
/**
 * @file RecvPoint.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 
 * @version 0.1
 * @date 2019-05-12
 * 
 * @copyright Copyright (c) 2019
 * 
 */

#include<iostream>
#include<string>
int main()
{
    std::cout    "Please Input you Char:";
    std::string strInc;
    int index = 0;
    while (true)
    {
        std::cin >> strInc;
        std::cout    "Your Input Is:   "    strInc   " RecvPoint "   index   std::endl;
        strInc.clear();
        index++;
    }
    return 0;
}
```

## 3. 测试程序    

我们需要重点关注的是测试程序，以下是测试程序的一个简单的示例，与上面的程序相对应。

```cpp {linenos=table}
/**
 * @file SendPoint.cpp
 * @author DennisMi (https://www.dennisthink.com/)
 * @brief 
 * @version 0.1
 * @date 2019-05-12
 * 
 * @copyright Copyright (c) 2019
 * 
 */
#include<iostream>
#include<Windows.h>
int main()
{
    //子进程的输入句柄，绑定到子进程的std::cin
    HANDLE hRead=INVALID_HANDLE_VALUE; 

    //子进程的输出句柄，绑定到子进程的std::cout
    HANDLE hWrite=INVALID_HANDLE_VALUE; 

    //父进程的读取句柄和子进程的输出句柄绑定到一个管道
    //绑定 hRead
    HANDLE hParentRead=INVALID_HANDLE_VALUE;

    //父进程的写入句柄和子进程的输入句柄绑定到一个管道
    //绑定 hParentWrite
    HANDLE hParentWrite=INVALID_HANDLE_VALUE;

    STARTUPINFO           stiConsole;

    SECURITY_ATTRIBUTES   segConsole;

    PROCESS_INFORMATION   priConsole;

    segConsole.nLength = sizeof(segConsole);
    segConsole.lpSecurityDescriptor = NULL;
    segConsole.bInheritHandle = TRUE;


    //构建输入输出句柄的关联
    BOOL bFirst = CreatePipe(&hRead, &hParentWrite, &segConsole, 0);
    BOOL bSecond = CreatePipe(&hParentRead, &hWrite, &segConsole, 0);

    if (bFirst && bSecond)
    {
        FillMemory(&stiConsole, sizeof(stiConsole), 0);
        stiConsole.cb = sizeof(stiConsole);
        stiConsole.hStdInput = hRead;
        stiConsole.hStdOutput = hWrite;
        stiConsole.hStdError = hWrite;
        stiConsole.dwFlags = STARTF_USESHOWWINDOW | STARTF_USESTDHANDLES;
        stiConsole.wShowWindow = SW_SHOW; // execute hide 


        DWORD nWriteLen = 0;
        char buff[512] = { 0 };
        DWORD nReadLean = 0;
        DWORD nReadAval = 0;
        std::string strBuf = "Hello\r\n";

        //创建子进程
        if (CreateProcess("RecvPoint.exe",
            NULL, 
            NULL,
            NULL, 
            TRUE,
            CREATE_NEW_CONSOLE,
            NULL,
            NULL, &stiConsole, &priConsole) == TRUE)
        {
            //等待子进程启动
            Sleep(5000);

            for(int i = 0 ; i   100 ; i++)
            {
                //检查可读管道内是否有数据
                if (!PeekNamedPipe(hParentRead, NULL, NULL, &nReadLean, &nReadAval, NULL) || nReadAval  = 0)//PeekNamePipe用来预览一个管道中的数据，用来判断管道中是否为空
                {
                    std::cout<<    "Break  "   <<  __LINE__  <<  std::endl;
                    break;
                }
                memset(buff, 0, 512);
                //读取子进程的输出
                if (ReadFile(hParentRead, buff, 512, &nReadLean, NULL))//这里是读管道，即便已经没有数据，仍然会等待接收数据，因为，子进程会认为父进程仍有数据要发送，只是暂时没法送，
                {                                                        //所以，会“卡”在这里。所以才需要PeekNamePipe
                    if (nReadLean == 0)
                    {
                        std::cout   <<  "Break  "   <<  __LINE__  <<   std::endl;
                        break;
                    }

                    std::cout  <<   "Read : " <<    buff   <<  "  "    nReadLean  <<   std::endl;
                }

                //将数据写入子进程
                WriteFile(hParentWrite, strBuf.c_str(), strBuf.length(), &nWriteLen, 0);

                Sleep(1000);
            }
        }
    }

    //关闭相应的句柄
    std::cout   <<  TerminateProcess(priConsole.hProcess, 0)  <<   std::endl;;
    std::cout  <<  CloseHandle(priConsole.hProcess);
    std::cout  <<   CloseHandle(priConsole.hThread);
    std::cout  <<   CloseHandle(hRead);
    std::cout   <<  CloseHandle(hWrite);
    std::cout  <<   CloseHandle(hParentRead);
    std::cout  <<   CloseHandle(hParentWrite);

}
```

## 4. 运行效果    

测试结果如下图所示:

![](https://www.dennisthink.com/image/2019/05/Test_Demo.png)