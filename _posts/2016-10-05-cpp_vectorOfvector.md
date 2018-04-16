---
title : cpp day 1 - vector of vector
category : C++
tags : C++
---


## About

  I start to learn some alogorithms and data structures form [T-414-ÁFLV: A Competitive Programming Course (2016 edition)](https://algo.is/).
This course uses an online judge system named [kattis](https://open.kattis.com/). This course mainly use c++ to describe the concepts about 
the alogorithms and data structures. Although is is possible for me to use C or python to learn these topics, but maybe it will be better for
for me to learn the pogramming language what this course use. I watched a TED about tring new things in 30 days will make the days more 
mrmorable and develope a new habit. So I decided to write the articles about C++ common usages or the problems I encounter in the course 
in the next 30 days. The tag should be *C++*.

## vector of vector in cpp

This example is from This [video](https://www.youtube.com/watch?v=Da_PPyjOUrE).

### declare

```c++

vector<vector<int> >stuff;

```

### usage

```c++

for (int i = 0; i < 3; ++i)	
{
	vector<int> temp;
	for (int j = 0; j < 3; ++j)
	{
		temp.push_back(i);
	}
	stuff.push_back(temp);
}

```

### access

```c++
for (int i = 0; i < stuff.size(); ++i)
	{
	/* code */
	for (int j = 0; j < stuff[i].size(); ++j)
	{
		/* code */
		cout<<stuff[i][j];
	}
	cout<<endl;
}
```



  
