---
title: java学习第三课之静态代码块"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# java学习第三课之静态代码块

java的静态块在整个运行过程中只执行一次，所有的情况如下所示。
代码的目录结构如下所示。
![]("https://www.dennisthink.com/image/2019/01/file_struct.png)

## 1.基础代码
Parent.java

```java {linenos=table}
package staticLearn;
public class Parent{
    public static String Name="Parent";

    //Normal Block
    {
        System.out.println("Parent Normal Block1");
    }

    //Static Block 
    static {
        System.out.println("Parent Static Block1");
    }

    public Parent() {
        System.out.println("Parent Constructor");
    }

    public void PrintName() {
        System.out.println("PrintName");
        {
            System.out.println("Block In Function");
        }
    }

    public static void PrintParentName(){
        System.out.println("Static Method PrintParentName");
    }
    //Static Block 
    static {
        System.out.println("Parent Static Block2");
    }

    {
        System.out.println("Parent Normal Block2");
    }
}
``` 

Child.java

```java {linenos=table}
package staticLearn;
public class Child extends Parent{
    public static String childName = "Child Name";

    //Normal Block
    {
        System.out.println("Child Normal Block 1");
    }

    //Static Block 1
    static {
        System.out.println("Child Static Block 1");
    }

    {
        System.out.println("Child Normal Block 2");
    }

    static {
        System.out.println("Child Static Block 2");
    }

    public Child() {
        System.out.println("Child Constructor");
    }

    public static void PrintChildName(){
        System.out.println("Static Method Print ChildName");
    }
}
``` 

执行编译过程:


>  javac staticLearn/*.java


### 2. 生成类的实例执行静态代码

One.java

```java {linenos=table}
import staticLearn.*;
public class One {
    public static void main(String[] args) {
        System.out.println("Main Start");
        Child ch = new Child();
        ch.PrintName();
        System.out.println("Main -----------");
        Child ch2 = new Child();
        ch2.PrintName();
        System.out.println("Main End");
    }
}
``` 

执行的命令:

>javac One.java   
  java One

程序的输出:
![](https://www.dennisthink.com/image/2019/01/one_result.png)

分析:
1. 从输出的情况可以看出，对于静态代码块，仅执行一次。
2. 先执行超类的静态代码块，后执行子类的静态代码块。
3. 静态代码块，按照顺序从前到后执行。
4. 对于非静态代码块，每生成一个实例，就执行一次。
5. 非静态代码块，也是按照顺序执行。

## 2 调用静态函数执行静态代码块    

Two.java

```java {linenos=table}
import staticLearn.*;
public class Two{
    public static void main(String[] args){
        System.out.println("Main Start");
        Parent.PrintParentName();
        System.out.println("Main ---------");
        Child.PrintChildName();
        System.out.println("Main End");
    }
}

``` 

执行的命令:

> javac Two.java   
  java Two


程序输出:![](https://www.dennisthink.com/image/2019/01/two_result.png)

Three.java

```java {linenos=table}
import staticLearn.*;
public class Three{
    public static void main(String[] args){
        System.out.println("Main Begin");
        Child.PrintChildName();
        System.out.println("Main End");
    }
}
``` 

执行的命令:


  javac Three.java
  java Three

程序输出:![](https://www.dennisthink.com/image/2019/01/three_result.png)

Four.java

```java {linenos=table}
import staticLearn.*;
public class Four{
    public static void main(String[] args){
        System.out.println("Main Begin");
        Child.PrintParentName();
        System.out.println("Main End");
    }
}
``` 

执行的命令:

> javac Four.java
  java Four

程序输出:![](https://www.dennisthink.com/image/2019/01/four_result-1.png)

结果分析:
1. 调用类的静态方法，会执行类的静态代码块。
2. 调用子类的静态方面，会先执行超类的静态代码块，再执行子类的静态代码块。
3. 用子类的名称，调用超类的静态方法，只执行超类的静态代码块。

### 3.引用静态成员变量，调用静态代码块

示例1
Five.java

```java {linenos=table}
import staticLearn.*;
public class Five{
    public static void main(String[]args){
        System.out.println("Main Begin");
        System.out.println(Child.childName);
        System.out.println("Main End");
    }
}
``` 

执行的命令:


>  javac Five.java   
java Five


程序的输出:![](https://www.dennisthink.com/image/2019/01/five_result.png)

示例2
Six.java

```java {linenos=table}
import staticLearn.*;
public class Six{
    public static void main(String[] args){
        System.out.println("Main begin");
        System.out.println(Child.Name);
        System.out.println("main End");
    }
}
``` 

执行的命令:

>javac Six.java          
java Six


程序的输出:![](https://www.dennisthink.com/image/2019/01/six_result.png)

结果分析:
1. 引用类的静态变量，会执行类的静态代码块。
2. 引用子类的静态变量，会先执行超类的静态代码块，再执行子类的静态代码块。

### 5 执行反射代码，调用类的静态代码块

反射子类
Seven.java

```java {linenos=table}
public class Seven{
    public static void main(String[] args){
        try{
            Class.forName("staticLearn.Child");
        } catch (ClassNotFoundException e){
            e.printStackTrace();
        }
    }
}
``` 

执行的命令:


>  javac Seven.java      
   java Seven


执行的结果:![](https://www.dennisthink.com/image/2019/01/seven_result.png)

反射超类
Eight.java

```java 
public class Eight{
    public static void main(String[] args){
        try {
            Class.forName("staticLearn.Parent");
        } catch (Exception e) {
            //TODO: handle exception
            e.printStackTrace();
        }
    }
}
``` 

执行的命令:

> javac Eight.java      
  java Eight

执行的结果:![](https://www.dennisthink.com/image/2019/01/eight_result.png)
结果分析:
1. 反射某个类，会执行该类的静态代码块。
2. 反射子类，会先执行超类的静态代码块，后执行子类的静态代码块。