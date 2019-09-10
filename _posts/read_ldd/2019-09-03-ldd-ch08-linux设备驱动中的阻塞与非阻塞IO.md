---
layout:  post
title:  "[LDD] 8. 设备驱动中的阻塞与非阻塞I/O"
date:    2019-09-03
author:  Abodu
catalog: true
tags: [ Kernel, LinuxDriver, 设备驱动, 笔记 ]
---

## 8.1 阻塞与非阻塞`I/O`

**`阻塞操作`**是指在执行设备操作时，若不能获得资源，则挂起进程直到满足可操作的条件后再进行操作。被挂起的进程进入睡眠状态，被从调度器的运行队列移走，直到等待的条件被满足。而**`非阻塞操作`**的进程在不能进行设备操作时，并不挂起，它要么放弃，要么不停地查询，直至可以进行操作为止。

驱动程序通常需要提供这样的能力：当应用程序进行read（）、write（）等系统调用时，若设备的资源不能获取，而用户又希望以阻塞的方式访问设备，驱动程序应在设备驱动的xxx_read（）、xxx_write（）等操作中将进程阻塞直到资源可以获取，此后，应用程序的read（）、write（）等调用才返回，整个过程仍然进行了正确的设备访问，用户并没有感知到；若用户以非阻塞的方式访问设备文件，则当设备资源不可获取时，设备驱动的xxx_read（）、xxx_write（）等操作应立即返回，read（）、write（）等系统调用也随即被返回，应用程序收到-EAGAIN返回值。

如图8.1所示，在阻塞访问时，不能获取资源的进程将进入休眠，它将CPU资源“礼让”给其他进程。因为阻塞的进程会进入休眠状态，所以必须确保有一个地方能够唤醒休眠的进程，否则，进程就真的“寿终正寝”了。唤醒进程的地方最大可能发生在中断里面，因为在硬件资源获得的同时往往伴随着一个中断。而非阻塞的进程则不断尝试，直到可以进行I/O。

![1568020677387](../../img/in-post/wlan_fitm/1568020677387.png)

下面的两段分别演示了以阻塞和非阻塞方式读取串口一个字符的代码。

```c
/// 阻塞地读串口一个字符
char buf;
fd = open("/dev/ttyS1", O_RDWR);
...
res = read(fd,&buf,1); /* 当串口上有输入时才返回 */
if(res==1)
printf("%c\n", buf);
```

```c
/// 非阻塞地读串口一个字符
char buf;
fd = open("/dev/ttyS1", O_RDWR| O_NONBLOCK); 
...
while(read(fd,&buf,1)!=1)
continue; /* 串口上无输入也返回，所以要循环尝试读取串口 */
printf("%c\n", buf);
```

除了在打开文件时可以指定阻塞还是非阻塞方式以外，在文件打开后，也可以通过ioctl()和fcntl()改变读写的方式，如从阻塞变更为非阻塞或者从非阻塞变更为阻塞。例如，调用fcntl(fd,F_SETFL，O_NONBLOCK)可以设置fd对应的I/O为非阻塞。

### 8.1.1 等待队列(`Wait Queue`)

在Linux驱动程序中，可以使用等待队列（Wait Queue）来实现阻塞进程的唤醒。等待队列很早就作为一个基本的功能单位出现在Linux内核里了，它以队列为基础数据结构，与进程调度机制紧密结合，可以用来同步对系统资源的访问，第7章中所讲述的信号量在内核中也依赖等待队列来实现。

Linux内核提供了如下关于等待队列的操作:

```c
// 1.定义等待队列头部
wait_queue_head_t my_queue;

// 2. 初始化等待队列头部
init_waitqueue_head(&my_queue);

//下面的DECLARE_WAIT_QUEUE_HEAD()宏可以作为定义并初始化等待队列头部的“快捷方式”
DECLARE_WAIT_QUEUE_HEAD(queue_name);

// 3. 定义等待队列元素
DECLARE_WAITQUEUE(name, tsk); //该宏用于定义并初始化一个名为name的等待队列元素

// 4. 添加/移除等待队列元素
void add_wait_queue(wait_queue_head_t *q, wait_queue_t *wait);
void remove_wait_queue(wait_queue_head_t *q, wait_queue_t *wait);
```

