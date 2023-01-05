---
title: Linux 的 function 追踪.md
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# Linux 的 function 追踪    

在学习开源代码或者接触到老项目的时候，对于复杂的函数调用流程，往往会感觉晕头转向。这个时候如果能打印出函数的执行流程，对于我们了解项目会很有帮助，下面介绍一下在Linux下打印函数调用流程的技术。

## 1. 使用gcc和g++的  code>-finstrument-functions ``` 选项    

-finstrument-functions 选项的意思是在函数的入口处自动的插入  code>__cyg_profile_func_enter ``` 在函数的出口自动加入  code>__cyg_profile_func_exit ```，通过自己实现这两个函数，就可以打印出调用流程了。

## 2. function_trace.c    

实现的一个模板如下:

 pre> code class="language-C ">//function_trace.c
#include  stdio.h>
#define __USE_GNU
#include  dlfcn.h>
static FILE *fp_trace=NULL;
static int nDepth = 0;
void  __cyg_profile_func_enter(void *func, void *caller) __attribute__((no_instrument_function));
void  __cyg_profile_func_exit(void *func, void *caller) __attribute__((no_instrument_function));

void __attribute__((constructor)) traceBegin(void) {
  fp_trace = fopen("func_trace.out", "w");
}

void __attribute__((destructor)) traceEnd(void) {
  if (fp_trace != NULL) {
    fclose(fp_trace);
    fp_trace = NULL;
  }
}


void __cyg_profile_func_enter(void *func, void *caller) 
{
    nDepth ++;
    for(int i = 0 ; i   nDepth ; i++)
    {
        if(fp_trace)
        {
            fprintf(fp_trace,"\t");
        }
        printf("\t");
    }
    Dl_info info;
    if (dladdr(func, &info) != 0) 
    {
        if(fp_trace)
        {
            fprintf(fp_trace,"Enter:[%s,%s] \n",info.dli_fname,info.dli_sname);
        }
        printf("Enter:[%s,%s] \n",info.dli_fname,info.dli_sname);
    }
}

void __cyg_profile_func_exit(void *func, void *caller) {

    for(int i = 0 ; i   nDepth ; i++)
    {
        if(fp_trace)
        {
            fprintf(fp_trace,"\t");
        }
        printf("\t");
    }
    nDepth --;

    Dl_info info;
    if (dladdr(func, &info) != 0) 
    {        
        if(fp_trace)
        {
            fprintf(fp_trace,"Enter:[%s,%s] \n",info.dli_fname,info.dli_sname);
        }
        printf("Leave:[%s,%s] \n",info.dli_fname,info.dli_sname);
    }
}
 ``` /pre>

 *编译命令 /*:

```
  gcc function_trace.c -c
 ```


## 3 主文件示例    

functrace_main.cpp

 pre> code class="language-C ">//functrace_main.cpp 
#include  stdio.h>

class CBase
{
public:
    explicit CBase()
    {

    }

    virtual ~CBase()
    {

    }

    virtual void Print()
    {
       printf("%s\n",__PRETTY_FUNCTION__);
    }
};

class CDriveOne:public CBase
{
public:
  CDriveOne()
  {

  } 
  virtual ~CDriveOne()
  {

  } 

  virtual void Print() override 
  {
     printf("%s\n",__PRETTY_FUNCTION__);
  }
};

class CDriveTwo:public CBase
{
public:
  CDriveTwo()
  {

  } 
  virtual ~CDriveTwo()
  {

  } 

  virtual void Print() override 
  {
     printf("%s\n",__PRETTY_FUNCTION__);
  }
};


void Overload()
{
  printf("%s\n",__PRETTY_FUNCTION__);
}

void Overload(int x)
{
  printf("%s\n",__PRETTY_FUNCTION__);
}

void FuncOuter()
{

}

void FuncMedium()
{
    FuncOuter();
}

void FuncInner()
{
    FuncOuter();
    FuncMedium();
}

int main(int argc, char **argv) { 

  {
    printf("----------------------------Drive override Test Begin\n");
    CBase * pFirst = new CDriveOne();
    pFirst->Print();

    CBase * pSecond = new CDriveTwo();
    pSecond->Print();

    delete pFirst;
    delete pSecond;
    printf("----------------------------Drive override Test End\n");
  }

  {
    printf("----------------------------Override  Test Begin\n");
    Overload();
    Overload(2);
    printf("----------------------------Override  Test End\n");
  }  
  {
    printf("----------------------------Simple  Test Begin\n");
    FuncOuter();
    FuncMedium();
    FuncInner();
    printf("----------------------------Simple  Test End\n");
  }
  return 0;
}
 ``` /pre>

 *编译命令 /*:

```
  g++ -g function_trace.o functrace_main.cpp  -ldl -finstrument-functions -export-dynamic
 ```


## 4 程序输出    

```
Enter:[./a.out,main]
----------------------------Drive override Test Begin
                Enter:[./a.out,_ZN9CDriveOneC1Ev]
                        Enter:[./a.out,_ZN5CBaseC1Ev]
                        Leave:[./a.out,_ZN5CBaseC1Ev]
                Leave:[./a.out,_ZN9CDriveOneC1Ev]
                Enter:[./a.out,_ZN9CDriveOne5PrintEv]
virtual void CDriveOne::Print()
                Leave:[./a.out,_ZN9CDriveOne5PrintEv]
                Enter:[./a.out,_ZN9CDriveTwoC2Ev]
                        Enter:[./a.out,_ZN5CBaseC1Ev]
                        Leave:[./a.out,_ZN5CBaseC1Ev]
                Leave:[./a.out,_ZN9CDriveTwoC2Ev]
                Enter:[./a.out,_ZN9CDriveTwo5PrintEv]
virtual void CDriveTwo::Print()
                Leave:[./a.out,_ZN9CDriveTwo5PrintEv]
                Enter:[./a.out,_ZN9CDriveOneD0Ev]
                        Enter:[./a.out,_ZN9CDriveOneD2Ev]
                                Enter:[./a.out,_ZN5CBaseD2Ev]
                                Leave:[./a.out,_ZN5CBaseD2Ev]
                        Leave:[./a.out,_ZN9CDriveOneD2Ev]
                Leave:[./a.out,_ZN9CDriveOneD0Ev]
                Enter:[./a.out,_ZN9CDriveTwoD0Ev]
                        Enter:[./a.out,_ZN9CDriveTwoD1Ev]
                                Enter:[./a.out,_ZN5CBaseD2Ev]
                                Leave:[./a.out,_ZN5CBaseD2Ev]
                        Leave:[./a.out,_ZN9CDriveTwoD1Ev]
                Leave:[./a.out,_ZN9CDriveTwoD0Ev]
----------------------------Drive override Test End
----------------------------Override  Test Begin
                Enter:[./a.out,_Z8Overloadv]
void Overload()
                Leave:[./a.out,_Z8Overloadv]
                Enter:[./a.out,_Z8Overloadi]
void Overload(int)
                Leave:[./a.out,_Z8Overloadi]
----------------------------Override  Test End
----------------------------Simple  Test Begin
                Enter:[./a.out,_Z9FuncOuterv]
                Leave:[./a.out,_Z9FuncOuterv]
                Enter:[./a.out,_Z10FuncMediumv]
                        Enter:[./a.out,_Z9FuncOuterv]
                        Leave:[./a.out,_Z9FuncOuterv]
                Leave:[./a.out,_Z10FuncMediumv]
                Enter:[./a.out,_Z9FuncInnerv]
                        Enter:[./a.out,_Z9FuncOuterv]
                        Leave:[./a.out,_Z9FuncOuterv]
                        Enter:[./a.out,_Z10FuncMediumv]
                                Enter:[./a.out,_Z9FuncOuterv]
                                Leave:[./a.out,_Z9FuncOuterv]
                        Leave:[./a.out,_Z10FuncMediumv]
                Leave:[./a.out,_Z9FuncInnerv]
----------------------------Simple  Test End
        Leave:[./a.out,main]
 ``` /pre>

输出分析:

```
  Enter:[./a.out,_ZN9CDriveOneC1Ev]   //派生类  code>CDriveOne ```的构造函数 br />
  Enter:[./a.out,_ZN5CBaseC1Ev]   //父类  code>CBase ```的构造函数
  Enter:[./a.out,_Z8Overloadv]    //调用  code>void Overload() ``` br />
  Enter:[./a.out,_Z8Overloadi]    //调用  code>void Overload(int) ```
 ```
