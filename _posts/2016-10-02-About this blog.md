---
title : Past and Future
category : markdown
tags : markdown  projects future
---
***About***
  
Since I worked in an server oem company as a embedded engineer from a year ago, I learned how computer work and programming in C.
My daily job focus on adding new features and debuging on the firmware we release. I also wrote a python package to debug our 
firmware via SG3 utils. I have a lot of fun on writing the python package because this the first time I use an oop interface of the 
programming and I also use multi-threading on many functions of this package. 
  
  I joined a summer workshop held by Jserv on this summer. The talk given by Jserv focus on C, system programming, ARM 
processors and how to make linux to hard real time. There are so many assignments from that talk I have not accomplished.
I also joined a MOOC on EDX recently. The MOOC is  about realtime Bluetooth Networks, which is created by Professors Yerraballi and 
Valvano from the University of Texas at Austin.  

This blog is a record for the topic or project I am insterested and the learning process.



***weekend projects***

*  google drive api downloader(python)

*  google tensoeflow sef-peaced online course

*  EDX : Realtime Bluetooth Networks, RTOS

* The talk and assignments from Jserv


***work***

*  TI TM4C bootloader  application
  
  I used the tm4c launch pad and the bootloader sdk from TI to implement an bootloader via I2C.
On every MCU reset, the bootloader will check a update flag to see if the firmware update is needed,
if yes, the bootloader will wait for an binary uploading via I2c, if not, the sp and pc will jump to where the
applocation locates.

*  seperate a tool configuration to xml 

  There is a tool developed by our team. This tool mainly manage some embedded devices and update the firmwares. For different
productions, the tool need to be build by different "gcc ... -Dxxx ". I seperate this configuration behavior from the macro define 
way to a xml configuration.

*  firmware Debug tool ~ a python package

*  production firmware develope, a malloc and free records(C language)





