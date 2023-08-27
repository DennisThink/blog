---
title: java第一课"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# java第一课

java的```public class```  的名称要和文件的名称保持一致。

所有示例的整体目录结构如下:![](https://www.dennisthink.com/image/2019/01/folder_struct.png)

## 1. 不带package的最简单的示例

>  MainFirst.java

```java 
public class MainFirst
{
    public static void main(String[] args){
        System.out.println("MainFirst");
    }
}
```

在ch0目录下运行的结果![](https://www.dennisthink.com/image/2019/01/One_ch0.png)

## 2. 带package的示例


>  MainSecond.java

```java package Two;
public class MainTwo{
    public static void main(String[] args){
        System.out.println("MainTwo");
    }
}
```

在ch0目录运行
![](https://www.dennisthink.com/image/2019/01/two_ch0.png)

在Two目录运行
![](https://www.dennisthink.com/image/2019/01/Two_Two.png)

## 3. 带package引用同一个package

>  Add.java


```java package Three;
public class Add
{
    public int AddValue(int x,int y){
        return x+y;
    }
}
```


>  MainThree.java

```java package Three;
public class MainThree{
    public static void main(String[]args){
        System.out.println("MainThree");
        Add util = new Add();
        System.out.println(util.AddValue(4, 2));
    }
}
```

在ch0目录下
![](https://www.dennisthink.com/image/2019/01/three_ch0.png)
在three目录下
![](https://www.dennisthink.com/image/2019/01/three_three.png)

## 4. 使用-d参数


>  javac -d ./Four/Dst ./Four/Test/TestAdd.java ./Four/Test/TestMain.java


文件名


>  TestAdd.java

```java package Test;
public class TestAdd{
    public void PrintName(){
        System.out.println("TestAdd");
    }
}
```


>  TestMain.java


```java
package Test;
public class TestMain{
    public static void main(String[] args){
        TestAdd ad = new TestAdd();
        ad.PrintName();
        System.out.println("TestMain");
    }
}
```

在ch0目录运行
![](https://www.dennisthink.com/image/2019/01/Four__Test_ch0.png)

在Four目录运行
![](https://www.dennisthink.com/image/2019/01/Four_four.png)

在Dst目录运行
![](https://www.dennisthink.com/image/2019/01/Four_dst.png)