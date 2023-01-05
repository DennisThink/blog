---
title: std::chrono简介    
date: 2019-01-20T21:38:52+08:00
lastmod: 2019-01-28T21:41:52+08:00
# you can close something for this content if you open it in config.toml.
comment: true
mathjax: false
---


# std::chrono简介    

参考资料:            
[博客园](https://www.cnblogs.com/jwk000/p/3560086.html)              
[官方文档](http://www.cplusplus.com/reference/chrono/)

chrono主要包括三个部分.

## 1. clock    

clock主要包括三种 ```system_clock ```  ```steady_clock ```  ```high_resolution_clock ```

* system_clock是系统时钟，在系统时间回退的时候，计时的结果会跟着回退。 /li>
* high_resolution_clock，跟系统时钟一样，有计时回退的问题，更适用于精确的计时。
* steady_clock是单调时钟，不会随着系统时间的回退而改变

示例代码

```cpp {linenos=table}
#include  <chrono>
#include  <iostream>
#include  <thread>
using namespace std::chrono;
int main(int argc,char * argv[])
{
    auto begin = system_clock::now();
    auto highBegin = high_resolution_clock::now();
    auto steadyBegin = steady_clock::now();
    std::this_thread::sleep_for(60s);
    auto end = system_clock::now();
    auto highEnd = high_resolution_clock::now();
    auto steadyEnd = steady_clock::now();
    duration double,std::ratio 1,1>> peridSeconds = end-begin;
    duration double,std::ratio 1,1>> highSeconds = highEnd-highBegin;
    duration double,std::ratio 1,1>> steadySeconds = steadyEnd - steadyBegin;
    std::cout  "System_clock Time :"  peridSeconds.count()  " s"  std::endl;
    std::cout  "High Time :"  highSeconds.count()  " s"  std::endl;
    std::cout  "SteadySeconds Time :"  steadySeconds.count()  " s"  std::endl;

    duration double,std::nano> peridMillSeconds = end-begin;
    duration double,std::nano> highPeridMillSeconds = highEnd - highBegin;
    duration double,std::nano> steadyMillSeconds = steadyEnd - steadyBegin;
    std::cout  "Time :"  peridMillSeconds.count()  " ns"  std::endl;
    std::cout  "High Time :"  highPeridMillSeconds.count()  " ns"  std::endl;
    std::cout  "Steady Time :"  steadyMillSeconds.count()  " ns"  std::endl;
    return 0;
}
```

不修改系统时间的运行结果：
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/normal_result.png" alt="normal_result" />

修改系统时间的运行结果:
 img src="https://www.dennisthink.com/wp-content/uploads/2019/01/back_result.png" alt="back_result" />

## 2.duration    

duration是一个模板，用来表示一段时间，可以使用duration_cast在不同的计时单位之间转换。

示例代码

```cpp {linenos=table}         
#include  <chrono>
#include  <iostream>
using namespace std::chrono;
int main(int argc,char * argv[])
{
    using half_hour_type = duration int,std::ratio 30*60>>;
    using half_day_type = duration int,std::ratio 12*60*60>>;
    using half_week_type = duration int,std::ratio 7*12*60*60>>;

    hours one_hour(1);

    hours day_hours(24);
    hours week_hours(7*24);

    half_hour_type half_hours = duration_cast half_hour_type>(one_hour);
    half_day_type  half_day_hours = duration_cast half_day_type>(day_hours);
    half_week_type half_weak_hours = duration_cast half_week_type>(week_hours);
    half_day_type  half_week_to_half_days = duration_cast half_day_type>(week_hours);
    std::cout  "1 hour have "   half_hours.count()   " half_hours "  std::endl;
    std::cout  "1 day  have "   half_day_hours.count()  " half_days "  std::endl;
    std::cout  "1 week  have "   half_weak_hours.count()  " half_weeks "  std::endl;
    std::cout  "1 week  have "   half_week_to_half_days.count()  " half_days "  std::endl;
    return 0;
}
```

程序输出:
![](https://www.dennisthink.com/image/2019/01/duration_result.png)