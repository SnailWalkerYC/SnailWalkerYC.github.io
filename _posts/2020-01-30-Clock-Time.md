---
layout: post
title: Clock Time
date: 2020-01-30
categories: [technology]
tags: [tech]
comments: false


---



有句谚语叫做：如果你有一个时钟，你可以很快说出时间；但是如果你有两个时钟，那么你就很难说出时间。因为需要更高精度的时间，到毫秒级别的时候，不同时钟源就会有不同的表达。首先介绍一下目前的时间：



- TAI(french name): UTC basis; 37 s forward UTC. Used for astronomical calculation and civil time keeping(UTC basis, UTC is our local time source). Due to earth motion, UTC(earth motion slow, cause irregular interval) and TAI have drifted. Represent as calendar.
- UTC: coordinated universal time
- GPS time：not match to earth time  TAI - GPS = 19s，compensation with error 800m. Week seconds.



- system time

在Ubuntu上我们可以使用不同的时间：

1. time(): 获得的是1970年以来的秒数

2. gettimeofday(): 从1970年以来的秒数和毫秒数

3. clock_gettime(): 

   CLOCK_REALTIME（不单调，和网络进utc时间行ntp同步）、CLOCK_MONOTONIC（单调递增，会受ntp影响，系统休眠的时候时间不递增）、CLOCK_PROCESS_CPUTIMEID、CLOCK_THREAD_CPUTIME_ID、CLOCK_MONOTONIC_RAW（单调递增，不受ntp影响）

   

- clock_gettime

```
#include <stdio.h> 
#include <time.h> 
#include <sys/time.h>   
#include <sys/resource.h> 

///////////////////////////////////
// measure elapsed real time using the "clock_gettime" call. 
// This is one of the most accurate ways to measure wallclock time.
///////////////////////////////////

// struct timespec {                                                                                     
//    time_t   tv_sec;        /* seconds */                                                             
//    long     tv_nsec;       /* nanoseconds */                                                         
// };  

void main () { 
    struct timespec start, finish; 
    clock_gettime(CLOCK_REALTIME, &start); 

    // chew up some CPU time
    int i,j; for (i=0,j=0; i<100000000; i++) { j+=i*i; }     

    clock_gettime(CLOCK_REALTIME, &finish); 

    long seconds = finish.tv_sec - start.tv_sec; 
    long ns = finish.tv_nsec - start.tv_nsec; 
    
    if (start.tv_nsec > finish.tv_nsec) { // clock underflow 
	--seconds; 
	ns += 1000000000; 
    } 
    printf("seconds without ns: %ld\n", seconds); 
    printf("nanoseconds: %ld\n", ns); 
    printf("total seconds: %e\n", (double)seconds + (double)ns/(double)1000000000); 
} 

```





> Clock time: https://confluence.qps.nl/qinsy/9.0/en/utc-to-gps-time-correction-32245263.html
>
> Different Time Visualization: http://leapsecond.com/java/gpsclock.htm
>
> Excellent clock_gettime: https://www.cs.rutgers.edu/~pxk/416/notes/c-tutorials/gettime.html 
>
> System time definition: https://en.wikipedia.org/wiki/System_time 
>
> Computer System Clock: https://blog.csdn.net/peterlin666/article/details/32344355 

