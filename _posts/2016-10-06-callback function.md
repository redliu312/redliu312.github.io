---
title : callback function
category : C
tags: [C, UTAustinX UT.RTBN.12.01x Realtime Bluetooth Networks]
---

## About
  In  [UTAustinX: UT.RTBN.12.01x Realtime Bluetooth Networks](https://courses.edx.org/courses/course-v1:UTAustinX+UT.RTBN.12.01x+3T2016/courseware/2db03080d42d4968b355638ab4a252b8/3c9da1eaae9c4a15ae4b17ccd16fb091/),
there is a introduction about the callback function in RTOS. I heard and saw callback function in many codes for a while(maybe for an year), but never figured
out why it has been called "callback" and how to use it in a elegant way.


## function pointer

first write a **user function**
```c
int count;
void Callme(void){
  count++;
} 
```

* declaring  a function pointer
```c
void (*PtrFunc)(void);
```
* assign a value to function pointer

```c
PtrFunc=&callme;//ptrfunc point to callme
```

* run the function
```c
*ptrfunc();// call the function it points to
```

## callback function

There is an example in this class. This example is a BSP function to let user register a task running periodically.
To register the function, it use the **concept of the function pointer**.

How elegant this code is :

* fisrt declare the pointer function **periodictask**
* the user register the task via **BSP_PeriodicTask_Init**
* the task pointed by **periodictask** running in **T32_INT1_IRQHandler**

```c
void (*PeriodicTask)(void);    // user function
void BSP_PeriodicTask_Init(void(*task)(void), uint32_t freq, uint8_t priority){long sr;
  if((freq == 0) || (freq > 10000)){
    return;                        // invalid input
  }
  if(priority > 6){
    priority = 6;
  }
  sr = StartCritical();
  PeriodicTask = task;             // user function
                                   // timer reload value
  TIMER32_LOAD1 = (BSP_Clock_GetFreq()/freq - 1);
  TIMER32_INTCLR1 = 0x00000001;    // clear Timer32 Timer 1 interrupt
  // bits31-8=X...X,   reserved
  // bit7=1,           timer enable
  // bit6=1,           timer in periodic mode
  // bit5=1,           interrupt enable
  // bit4=X,           reserved
  // bits3-2=00,       input clock divider /1
  // bit1=1,           32-bit counter
  // bit0=0,           wrapping mode
  TIMER32_CONTROL1 = 0x000000E2;
// interrupts enabled in the main program after all devices initialized
  NVIC_IPR6 = (NVIC_IPR6&0xFFFF00FF)|(priority<<13);
  NVIC_ISER0 = 0x02000000;         // enable interrupt 25 in NVIC
  EndCritical(sr);
}

void T32_INT1_IRQHandler(void){
  TIMER32_INTCLR1 = 0x00000001;    // acknowledge Timer32 Timer 1 interrupt
  (*PeriodicTask)();               // execute user task
}
```

