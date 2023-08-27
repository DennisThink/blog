---
title: "C++和Java动态数组比较"
date: 2013-04-07T21:38:52+08:00
lastmod: 2013-04-07T21:41:52+08:00
weight: 50
# you can close something for this content if you open it in config.toml.
tags: ["from-csdn"]
comment: true
mathjax: false
---

在 C++ 和 Java 中经常要动态数组，下面我们看看两个的区别和联系。
我们知道 Java 中的引用和 C++ 的中的指针很像，那么我们将在下面的代码中展示它们。

C++代码
```cpp
#include <vld.h>
#include <iostream>
using namespace std;
 
class UniArray
{
public:
	UniArray(int length)
	{
		cout << "malloc memory" << endl;
		this->length = length;
		elem = new int[length];
	}
 
	virtual ~UniArray()
	{
		delete[] elem;
	}
 
private:
	int length;
	int* elem;
};
 
class TwoDimArray
{
public:
	TwoDimArray(int x, int y)
	{
		xLength = x;
		yLength = y;
		pArray = new int * [xLength];
		for (int i = 0; i < xLength ; i++)
		{
			pArray[i] = new int [yLength];
		}
	}
 
	virtual ~TwoDimArray()
	{
		for (int i = 0 ; i < xLength ; i++)
		{
			delete[] pArray[i];
		}
		delete pArray;
	}
private:
	int xLength;
	int yLength;
	int** pArray;
};
 
int main()
{
	TwoDimArray(5, 5);
	UniArray array(5);
	return 0;
}
```


Java代码

```java
public class ArrayTest {
	public static void main(String[] args)
	{
		for(int i = 0 ; i < 10 ; i++)
		{
			System.out.println(i);
			UniArray uniArray = new UniArray(6);
			uniArray = null;
			System.gc();
		}
		
		for(int i = 0 ; i < 10 ; i++)
		{
			System.out.println(i);
			TwoDiArray twoDiArray = new TwoDiArray(5, 5);
			twoDiArray = null;
			System.gc();
		}
	
		System.gc();
	}
	
	static class UniArray
	{
		public UniArray(int length)
		{
			pArray = new int[length];
		}
		
		protected void finalize()
		{
			System.out.println("UniArray Garbage Collection");
		}
		private int[] pArray; 
	}
	
	static class TwoDiArray
	{
		public TwoDiArray(int x,int y)
		{
			pArray = new int[x][];
			for(int i = 0 ; i < x ; i++)
			{
				pArray[i] = new int[y];
			}
		}
		protected void finalize()
		{
			System.out.println("TwoDiArray Garbage Collection");
		}
		private int[][] pArray;
	}	
}
```


我们经过比较发现：
C++ 中的 int * 语义上等价于 Java 中的 int[]
C++ 中的 int ** 语义上等价于 Java 中的 int[][];
两者的申请过程是非常的相似的。