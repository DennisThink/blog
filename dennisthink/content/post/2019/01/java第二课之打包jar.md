---
title: java第二课之打包jar"
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---

# java第二课之打包jar

### 1.单文件打包jar

单文件生成Jar的命令


>  jar -cef ${MAINCLASS} ${JARNAME} ${CLASS_NAME}


其中${MAINCLASS}代表主类，${JARNAME}代表生成的jar的名称，${CLASS_NAME}表示需要打包的class文件。


>  SingleJar.java


```java {linenos=table}
public class SingleJar{
    public static void main(String[] args){
        System.out.println("Single jar Demo");
    }
}
```

执行的编译命令为:

>
  javac SingleJar.java
  jar -cef SingleJar SingleJar.jar SingleJar.class
  java -jar SingleJar.jar
>

执行的结果为:
![](https://www.dennisthink.com/image/2019/01/one_singleJar.png)

### 2. 单文件使用Manifest

Manifest文件需要注意的地方:
*冒号之后要有空格，最后一行以后要有空行*

Manifest.mf

```{linenos=table}
Manifest-Version: 1.0
Create-By: DennisMi
Class-Path: .
Main-Class: UseManifest

```

java源代码

```java {linenos=table}
public class UseManifest{
    public static void main(String[] args){
        System.out.println("Use Manifest");
    }
}
```

执行的命令:


>  javac UseManifest.java
  jar -cvfm UseManifest.jar    Manifest.mf UseManifest.class
  java -jar UseManifest.jar


执行的结果:
![](https://www.dennisthink.com/image/2019/01/use_manifest.png)

### 3.使用Manifest带Package

Manifest.mf

``` {linenos=table}
Manifest-Version: 1.0
Created-By: DennisMi
Class-Path: .
Main-Class: Cal.Main

```

Add.java

```java {linenos=table}
package Cal;
public class Add{
    public void PrintName(){
        System.out.println("Add");
    }
}
```

Main.java

```java {linenos=table}
package Cal;
public class Main{
    public static void main(String[] args){
        Add add = new Add();
        add.PrintName();
        System.out.println("Main");
    }
}
```

执行的命令:

>
  javac Cal/ em>.java            
  jar cfm three.jar ./Cal/Manifest.mf ./Cal/ /em>.class            
  java -jar three.jar


执行的结果:
![](https://www.dennisthink.com/image/2019/01/three_use_manifest.png)

### 4.带Package的编译

Manifest.mf

```{linenos=table}
Manifest-Version: 1.0
Created-By: DennisMi
ClassPath: Cal
Main-Class: Main

```

Add.java

```java {linenos=table}
package Cal;
public class Add{
    public void PrintName(){
        System.out.println("Add Print Name");
    }
}
```

Main.java

```java {linenos=table}
import Cal.*;
public class Main{
    public static void main(String[] args){
        Add a = new Add();
        a.PrintName();
        System.out.println("Four main");
    }
}
```

执行的命令:

> javac ./Cal/Add.java             
  javac Main.java                    
  jar -cvfm four.jar Manifest.mf Main.class ./Cal/Add.class                
  java -jar four.jar                 

执行的结果:
![](https://www.dennisthink.com/image/2019/01/four_result.png)

### 5.使用Manifest打包jar

Manifest.mf

```{linenos=table}
Manifest-Version: 1.0
Created-By: DennisMi
Class-Path: ../Add.jar
Main-Class: Main

```

Add.java

```java {linenos=table}
package Cal;
public class Add{
    public void PrintName(){
        System.out.println("Add Print Name");
    }
}
```

Main.java

```java {linenos=table}
import Cal.*;
public class Main{
    public static void main(String[] args){
        Add a = new Add();
        a.PrintName();
        System.out.println("Main");
    }
}
```

执行的命令:

> javac ./Cal/Add.java            
  jar -cef Cal.Add Add.jar  ./Cal/Add.class            
  cd src            
  javac Main.java -cp ../Add.jar:$CLASSPATH            
  java -cp ../Add.jar:$CLASSPATH Main      
  jar -cfm five.jar Manifest.mf  ../Add.jar ./Main.class            
  java -jar five.jar

执行的结果: