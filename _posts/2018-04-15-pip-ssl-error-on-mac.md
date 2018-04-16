---
title : macOS High Sierra python SSL error 
category : python
tags : [ macOS python ]
---

## objective

  I have a macbook which runs High Sierra(10.13.2) and I set a python environment on it for work or 
for my side projects. These include python 2.7.11 and python 3.6.4 which I usually use them with virtualenv
. Besides these I also installed anaconda for nlp or deep learning framework learning.



## the issue
  The company I work for is stilling using python2 and virtualenv for the work flow. 
Today I want to spend my day off to read the mainline production source code to find a 
way to solve some bugs. Unfortunately I met a ssl error when I tried to install the dependency in the
requirement.txt.

- **the error message**

```bash 

    Download error on https://pypi.python.org/simple/setuptools_scm/: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:661) -- Some pa
ckages may not be found!
    Download error on https://pypi.python.org/simple/setuptools-scm/: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:661) -- Some pa
ckages may not be found!
    Couldn't find index page for 'setuptools_scm' (maybe misspelled?)
    Download error on https://pypi.python.org/simple/: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:661) -- Some packages may not
be found!
```


## the cause

After googling for a hour and diving on SO, it turns out that the cause of the issue comes from mac has dropped openSSL as their SSL interface because its unstable
api between the versions.

- (https://news.ycombinator.com/item?id=13539034)
- (https://stackoverflow.com/questions/49768770/not-able-to-install-python-packages-ssl-tlsv1-alert-protocol-version/49769015#49769015)

- (http://pyfound.blogspot.tw/2017/01/time-to-upgrade-your-python-tls-v12.html) 

**these articles indicate that this error will occur with the python shipped with the macos and the user can fix
that can fix it by using homebrew to install a python with the latest TLS.**

## TL;DR

fix this by 

- use homebrew to install a python version with the latest TLS

or 

- (https://stackoverflow.com/questions/49768770/not-able-to-install-python-packages-ssl-tlsv1-alert-protocol-version/49769015#49769015)

update the pip with the get-pip.py 


