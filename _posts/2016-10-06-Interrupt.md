---
title : Interrupt
category : C
tags: [UTAustinX UT.RTBN.12.01x Realtime Bluetooth Networks]
---

## About
  
  This is all about ***interrupt*** I learned in [UTAustinX UT.RTBN.12.01x Realtime Bluetooth Networks](https://courses.edx.org/courses/course-v1:UTAustinX+UT.RTBN.12.01x+3T2016/courseware/2db03080d42d4968b355638ab4a252b8/c6f77ba0b24f40f6b8f22f39310d4000/?child=first).

## interruupt
  A **software** action performed in response t  a **event(hardware or software)**.
  
### trigger type

#### software
* SVC (Trap)
* PendSV

#### timer
* systick(lab2)
* timer(lab3)

#### I/O(edge-triggered)

## how interrupt work

#### interrupt vector table


#### initialization for one time

* set common mechanism : mailbox, fifo
* ARM/Enable the device that causes the interrupt
* set the priority. in this coure, it is 3-bits means from 0 to 7.

#### ISR , interrupt service routine  software action

* acknowledge the interrupt : tell the system the interrupt should be considered handled.
  the interrupt will not run again and again.

* perform the real action and return

#### interrupt from a ***hardware*** viewpoint

#### conditions for an interrupt to have effect

| | stack |
| ---| ---|
|0x0000 1018| MOV ----(Int occur)|
| |...|

***condition***
* trigger(event)
* I=0
* NVIC(nested vector interrupt controller) flags congigures/(arm coterx M , this course)
* priority must be higher than the current machine.

and then

***saving state of current state***

current state  == the registers

* push registers 8 of them
 R0-R3,R12, PC,LR,PSR

| | stack |
| ---| ---|
||R0,R1-R3|
| |R12|
||LR==R14|
||R15== PC|
||PSR, contains NVZC|

* look up the interrupt vector table
look up the interrupt vector table and pick up the corresponding entry,
**write the interrupt of a  interrupt number to the PC**
then we go to the **ISR**

| | stack |
| ---| ---|
|0x0000 1018| MOV ----(Int occur) ---> go to ISR|   
| |...|

**write INT number to  IPSR**


**set LR to magic number 0xffff fff9**
In  **ISR**, when **BX LR** happen, 
Normally **BX LR** go back whatever LR holding,
with LR hodling magic number, we tell processor to pop all these guys and go back,

| | stack |
|---| ---|
||R0,R1-R3|
| |R12|
||LR==R14|
||R15== PC|
||PSR, contains NVZC|

***remember the PC is the address of the next instruction before we jump to ISR***
***like AAPCS for a function, if the ISR want to use R4-R6, it must save them by theirself, in this point , it is like a function.***
