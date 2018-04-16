---
title : python lambda function for sorting key function
category : Python
tags: [python, lambda, sort]
---


## objective

During today's work, I need to write a small code snippet which simpily 
sort a list with dictionaries which values are integer string in it. 

```python

import operator

simon = {    
            "color":"white",
            "@size":"3"}
irene = {   
            "color":"black",
            "@size":"2" 
}

alist = [simon,irene] ## now sort the list
```

I first wrote some redundant codes like this

```python

for i in alist:
	i.["@size"] = int(i.["@size"])
sorted_list = sorted(alist,key=operator.itemgetter(u"@size"))
```

After writing this, I really felt depressed like I am the most stupid person in the world.


## lambda expression , anonymous function


After studying the definiton and the anonymous function again, It comes to me there should be an easy 
and clear way to do this. 

**Recall** 

```
Lambda expressions are shorthand to create anoymous functions.

lambda arguments: expressions   
		
		equal =

def named(arguments):
	expressions
```

## Finally I got it.


```python

sorted_list= sorted(alist,key= lambda x: int(x[u"@size"]))

```



## more readings

[expressions](https://docs.python.org/2/reference/expressions.html)









