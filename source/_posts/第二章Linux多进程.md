---
title: Linux多进程
date: 2023-02-9 00:00:00
tags: 多进程
categoreies: 牛客C++服务器项目
---

# 2.Linux多进程开发

---



## 2.1 进程概述

**程序和进程**

> **程序**
>
> 程序是包含一系列信息的文件，这些文件描述了如何在运行时创建一个进程。
>
> 包含内容：
>
> - 二进制格式标识：每个程序文件都包含用于描述可执行文件格式的元信息。内核利用此信息来解释文件中的其他信息。（ELF可执行链接格式）
> - 机器语言指令：对程序算法进行编码。
> - 程序入口地址：标识程序开始执行时的起始指令位置。
> - 数据：程序文件包含的变量初始值和程序使用的字面量值（如字符串）。
> - 符号表及重定位表：描述程序中函数和变量的位置及名称。这些表格有多重用途，其中包括调试和运行时的符号解析（动态链接）。
> - 共享库和动态链接信息：程序文件所包含的一些字段，列出了程序运行时需要使用的共享库，以及加载共享库的动态链接器的路径名。
> - 其他信息：程序文件还包含其他信息，用来描述如何创建进程。

> **进程**
>
> - 进程是正在运行的程序的实例。
>   - 是一个具有一定独立功能的程序关于某个数据集合的一次运行活动。
>   - 是操作系统动态执行的基本单元，在传统的操作系统中，进程既是基本的分配单元，也是基本的执行单元。
> - 可以用一个程序来创建多个进程，进程是由内核定义的抽象实体，并为该实体分配用以执行程序的各项系统资源。
>   - 从内核的角度看，进程由用户内存空间和一些列的内核数据结构组成，其中用户内存空间包含了程序代码及代码所使用的变量，而内核数据结构则用于维护进程状态信息。
>   - 记录在内核数据结构中的信息包括许多与进程相关的标识号（IDs）、虚拟内存表、打开文件的描述符表，信号传递及处理的有关信息、进程资源使用及限制、当前工作目录和大量的其他信息。



**单道、多道程序设计**

单道程序：在计算机内存中只允许一个的程序运行。

多道程序：计算机内存中同时存放多道相互独立的程序使它们在管理程序控制下，相互穿插运行，两个或两个以上程序在计算机系统中处于开始到结束之间的状态，这些车程序共享计算机资源。（提高CPU的利用率）

- 对于单CPU系统而言，宏观上可以使多个程序像是同时运行，实际上任意时刻CPU上运行的程序只有一个。
- 在多道程序设计模型中，多个程序轮流使用CPU。



**时间片**

时间片 timeslice 又称为“量子 quantum”或“处理器片processor slice”，是操作系统分配给每个正在运行的进程微观上的一段CPU时间。

时间片由操作系统内核的调度程序分配给每个进程。



**并行和并发**

并行（parallel）：指在同一时刻，有多条指令在多个处理器上同时执行。

并发（concurrency）：指在同一时刻只能有一条指令执行，但多个进程指令被快速的轮换执行，使得宏观上具有多个进程同时执行的效果，但在微观上并不是同时执行的，只是把时间分成若干段，使多个进程快速交替的执行。



![image-20230204223514971](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230204223514971.png)

![image-20230204223803946](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230204223803946.png)

 {% asset_img image-20230204223803946.jpg example %}

**进程控制块 PCB**

内核为每个进程分配以一个 PCB 进程控制块，维护进程相关的信息，Linux内核的进程控制块是 task_struct 结构体。

在 /usr/src/linux-headers-xxx/include/linux/sched.h 文件中可以查看 struct task_struct 结构体定义，其部分内部成员如下：

- 进程id：系统中每个进程有唯一的 id ，用 pid_t 类型表示，是一个非负整数
- 进程的状态：就绪、运行、挂起、停止等状态
- 进程切换时需要保存和恢复的一些CPU寄存器信息
- 描述虚拟地址空间的信息
- 描述控制终端的信息
- 当前工作目录
- umask掩码
- 文件描述符表，包含很多执行file结构体的指针
- 和信号相关的信息
- 用户id和组id
- 会话（session）和进程组
- 进程可以使用的资源上限（可用ulimit指令查看系统内核资源上限）



## 2.2 进程状态转换

### 进程的状态

进程状态反映进程执行过程的变化。

`三态模型`中，进程状态分为三个基本状态 —— 就绪态、运行态、阻塞态。

> **运行态**：进程占有处理器正在运行。
>
> **就绪态**：进程具备运行条件，等待系统分配处理器以便运行。当进程分配到除CPU外的所有必要资源后，只要再获得CPU，便可立即执行。多个就绪进程排成队列，称为就绪队列。
>
> **阻塞态**：又称为 等待态(wait) 或 睡眠态(sleep)，指进程不具备运行条件，正在等待某个事件的完成。

![image-20230204225943684](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230204225943684.png)



`五态模型`中，进程状态分为 ——  新建态、就绪态、运行态、阻塞态、终止态。

> **新建态**：进程刚被创建时的状态，尚未进入就绪队列
>
> **终止态**：进程完成任务达到正常结束点，或出现无法克服的错误而异常终止，或被操作系统及有终止权的进程所终止时所处的状态。进入终止态的进程不再执行，但依然保留再操作系统中等待善后。一旦其他进程完成了对终止态进程的信息抽取之后，操作系统将删除该进程。

![image-20230204230502376](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230204230502376.png)



### 进程相关指令

**查看进程**

`ps aux/ajx`

a：显示终端上的所有进程，包括其他用户的进程

u：显示进程的详细信息

x：显示没有控制终端的进程

j：列出与作业控制相关的进程



> stat标识
>
> ![image-20230204232608821](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230204232608821.png)



**实时显示进程状态**

`top`

可以在 top 命令后加 -d 来指定显示信息更新的时间间隔

在 top 命令执行后，可以按一下按键对显示结果进行排序

- M 根据内存使用量
- P 根据CPU占有率
- T 根据进程运行时间长短
- U 根据用户名筛选
- K 输入指定的 PID 杀死进程



**杀死进程**

`kill [-signal] pid`

`kill -l`列出所有signal信号(信号宏)

比如：

- `kill -SIGKILL 进程ID`
- `kill -9 进程ID` 

- `killall name` 根据进程名杀死进程



**进程号和相关函数**

- 每个进程都由**进程号**来表示，其类型为 pid_t（整型），进程号范围：0~32767。

- 任何进程（除init进程）都是由另一个进程创建，即父进程， PPID 为父进程号

- **进程组**是一个或多个进程的集合。他们相互关联，进程组可以接收同一终端的各种信号，关联的进程有一个进程组号 PGID 。默认情况下，当前的进程号会当做当前的进程组号。

相关函数：

- `pid_t getpid(void);`
- `pid_t getppid(void);`
- `pid_t getpgid(pid_t pid);`





## 2.3 进程创建与调试

系统允许一个进程创建新进程，即紫禁城，子进程还可以创建新的子进程，形成树结构模型。

### fork函数

```c
#include <sys/types.h>
#include <unistd.h>

//创建子进程
pid_t fork(void);
/*
	返回值：
		- 成功：子进程中返回0，父进程中返回子进程 ID 
		- 失败：子进程不被创建，父进程中返回-1，并设置errno
		失败的两个主要原因：
		1. 当前系统的进程数已经达到了系统规定的上限，这时 errno 的值被设置为 EAGAIN
		2. 系统内存不足，这是 errno 的值被设置为 ENOMEM

*/
```



用法

```c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main(){
    //创建子进程
    pid_t pid = fork();
    
    if(pid > 0){
        //父进程要执行的内容
        ...
    } else if(pid == 0){
        //子进程要执行的内容
    	...
    }
    
    //都会执行的内容
    ...
        
    return 0;
}

```



### 父、子进程虚拟地址空间情况

![image-20230205003125065](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205003125065.png)

进程的实际pid在内核空间

变量pid的值在栈空间



> fork() 是通过 写时拷贝（copy-on-write） 实现。
>
> 写时拷贝时一钟可以推迟甚至避免拷贝数据的技术。内核此时并不复制整个进程的地址空间，而是让父子进程共享一个地址空间。
>
> 只有在需要写入的时候才会复制地址空间，从而使得各个进程拥有各自的地址空间，在此之前只有以只读方式共享。
>
> 注意：fork后父子进程共享文件
>
> ![image-20230205011611597](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205011611597.png)
>
> ![image-20230205011543783](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205011543783.png)



> 不同的进程访问同样的逻辑地址（虚拟地址）而对应的物理地址不同，是由于各自页表的不同。
>
> linux系统下每个进程都拥有自己的页表，父进程fork出新的子进程时，子进程拷贝一份父进程的页表，且父子进程将页表状态修改为写保护。当父进程或子进程发生写操作时将会发生缺页异常，缺页异常处理函数将会为子进程分配新的物理地址。





### 父子进程关系

**不同点**

1. fork()函数的返回值不同
   - 父进程中：>0 返回子进程的ID
   - 子进程中：=0
2. PCB中的一些数据
   - 当前进程的id pid
   - 当前进程的父进程id ppid
   - 信号集



**共同点**

某些状态下：子进程刚被创建出来，还没有执行任何的写数据的操作

	- 用户区的数据
	- 文件描述符表

父子进程对变量是不是共享的？

- 刚开始是共享的，如果数据发生修改则不共享
- 读时共享，写时拷贝



### GDB多进程调试

GDB默认只能跟踪一个进程，可以在fork函数调用前，通过指令设置 GDB 调试工具跟踪父进程或者是子进程，默认跟踪父进程。



- 设置调试父进程或子进程：

`set follow-fork-mod [parent(default) | child]`

- 设置调试模式：

`set detach-on-fork [on(default) | off]`

​	on：调试当前进程时，其他进程继续运行

​	off：调试当前进程时，其他进程被 GDB 挂起

- 查看调试的进程：

`info inferiors`

- 切换当前调试的进程：

`inferior 编号`（若切换的进程被挂起，切换后先 c ）

- 使进程脱离 GDB 调试：

`detach inferiors id`







## 2.4 exec函数族

c++有函数重载，c语言没有；

函数族即一系列功能相似的函数。



- exec 函数族的作用是根据指定文件名找到可执行文件，并用它来取代调用进程的内容，换句话说，就是在调用进程内部执行一个可执行文件。（实际应用时一般在子进程中执行 exec函数族）
- exec 函数族的函数执行成功后不会返回，因为调用进程的实体，包括代码段、数据段和堆栈等都已经被新的内容取代，只留下进程ID等一些表面上的信息仍保持原样。只有调用失败了，才会返回－１，从原程序的调用点接着往下执行。

![image-20230205022237610](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205022237610.png)



**exec函数族**

>exec后的字母表示传参方式和搜索规则
>
>- l(list)	参数地址列表，以空指针结尾
>
>- v(vector)  存有个参数地址的指针数组的地址
>
>- p(path)	按 PATH 环境变量指定的目录搜索可执行文件
>
>- e(environment) 存有环境变量字符串地址的指针数组的地址

```c
#include <unistd.h>

int execl(const char *path, const char *arg, .../* (char *) NULL */)；
/*
	参数：
		- path：需要指定的执行文件的路径或名称（相对路径或绝对路径）（推荐使用绝对路径）
		- arg：可执行文件的参数列表（第一个参数一般没什么作用，为了方便，一般写的是执行程序的名称，从第二个参数开始往后，就是程序执行所需的参数列表，并以 NULL 结尾（哨兵））
	
	返回值：
		只有调用失败，才有返回值-1，并设置errno。
		调用成功没有返回值。

*/
    
int execlp(const char *file, const char *arg, .../* (char *) NULL */)；
/*
	会到环境变量中查找指定的可执行文件，如果找到了就执行，找不到就执行原程序
	参数：
		- file：需要执行的可执行文件的文件名
		- arg：可执行文件的参数列表（第一个参数一般没什么作用，为了方便，一般写的是执行程序的名称，从第二个参数开始往后，就是程序执行所需的参数列表，并以 NULL 结尾（哨兵））
	
	返回值：
		只有调用失败，才有返回值-1，并设置errno。
		调用成功没有返回值。
*/
    
int execle(const char *path, const char *arg, .../*, (char *) NULL, char *const envp[] */)；

int execv(const char *path, char *const argv[])；
/*
用例：
	char * argv[] = {"ps", "aux", NULL};
	execv("/bin/ps", arg);
*/
    
int execvp(const char *file, char *const argv[])；
    
int execvpe(const char *file, char *const argv[], char *const envp[])；
    
int execve(const char *filename, char *const argv[], char *const envp[])；
/*
用例：
	char * argv[] = {"ps", "aux", NULL};
	char * envp[] = {"/home/nowcoder", "/home/aaa", "/home/bbb"};
	
	execve("ps", arg, envp);
*/
    


```





## 2.5 进程控制

### 进程退出

**exit函数**

```c
#incldue <stdlib.h>
void exit(int status);

#include <unistd.h>
void _exit(int status);

/*
	参数：
		- status：进程退出时的一个状态信息。父进程回收子进程资源的时候可以获取到。
*/

/*
	exit是标准C库的函数，比_exit多做了
	1.调用退出处理函数
	2.刷新I/O缓存
	3.关闭文件描述符
*/

```

![image-20230205024702546](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205024702546.png)





### 孤儿进程

父进程运行结束，但子进程还在运行，这样的子进程就成为孤儿进程（Orphan Process）



每当出现一个孤儿进程时，内核就把孤儿进程的父进程设置为init进程，而init进程会循环地 wait() 它的已经退出的子进程。（init进程会回收运行结束的孤儿进程）



没什么危害



### 僵尸进程

每个进程结束之后，会释放自己地址空间中的用户区数据，内核去的PCB则需要父进程去释放

子进程终止后，父进程尚未将其回收，子进程残留资源（PCB）存放于内核中，变成僵尸进程

- 僵尸进程不能被 kill -9 杀死
- 如果父进程不调用 wait() 或 waitpid() ，那么保留的信息就不会释放，进程号会一直被占用
- 危害：系统的进程号有限，如果出现大量僵尸进程，会导致系统无法产生新的进程



### 进程回收

在每个进程退出的时候，内核释放该进程所有的资源、包括打开的文件、占用的内存等。但是仍为其保留一定的信息，这些信息主要指 PCB 的信息（包括进程号、退出状态、运行时间等）

父进程可以通过 wait() 或 waitpid()得到子进程的退出状态，并彻底清除掉这个进程

- wait() 或 waitpid() 功能一样，区别在于： 	wait() 会阻塞；
  	waitpid() 可以设置不阻塞，且可以指定等待哪个子进程结束；
- 一次wait或waitpid调用只能清理一个子进程，清理多个子进程应该使用循环



#### wait函数

```c
#include <sys/types.h>
#include <sys/wait.h>

pid_t wait(int *wstatus);
/*
	功能：等待任意一个子进程结束，如果任意一个子进程结束了，此函数会回收子进程的资源。
	参数：int *wstatus
		进程退出时的状态信息，传入的是一个int指针，是一个传出参数
	返回值：
		成功：返回被回收的子进程的id
		失败：返回-1（所有的子进程都结束了，调用函数失败）

	调用wait函数的进程会被挂起（阻塞），知道它的一个子进程退出或者收到一个不能被忽略的信号时才被唤醒（相当于继续往下执行）
	如果没有子进程了/子进程都结束了，函数立刻返回，返回-1
*/

```



**退出信息相关宏函数**

- ​	函数					返回值

  ---

- WIFEXITED(status)	非0，进程正常退出

- WEXITSTATUS(status)  如果上宏为真，获取进程退出的状态（exit的参数）

- WIFSIGNALED(status)  非0，进程异常终止

- WTERMSIG(status)     如果上宏为真，获取使进程终止的信号编号

- WIFSTOPPED(status)   非0，进程处于暂停状态

- WSTOPSIG(status)     如果上宏为真，获取使进程暂停的信号编号

- WIFCONTINUED(status) 非0，进程暂停后已经继续运行





#### waitpid函数

```c
#include <sys/types.h>
#include <sys/wait.h>

pid_t waitpid(pid_t pid, int *wstatus, int options);
/*
	功能：回收指定进程号的子进程；可以设置是否阻塞。
	wait(&wstatus); 等价于 waitpid(-1, &wstatus, 0);
	参数：
		- pid：指定子进程的id
			> 0  ：某个子进程的id
			  0  ：回收当前进程组的所有子进程
			  -1 ：回收所有的子进程	（最常用）
			< -1 ：某个进程组组id的绝对值，回收指定进程组中的子进程
		- wstatus：进程退出时的状态信息，传入的是一个int指针，是一个传出参数
		- options：设置阻塞或非阻塞
			0    ：阻塞
			宏值  ：WNOHANG 非阻塞（没有子进程退出的话立即返回）
				   WUNTRACED
				   WCONTINUED
	返回值：
		> 0 ：返回被回收的子进程的id
		= 0 ：options=WNOHANG时，表示还有子进程没有退出
		= -1：错误，或者没有子进程了

	调用wait函数的进程会被挂起（阻塞），知道它的一个子进程退出或者收到一个不能被忽略的信号时才被唤醒（相当于继续往下执行）
	如果没有子进程了/子进程都结束了，函数立刻返回，返回-1
*/

```







## 2.6 进程间通信

### 2.6.1 进程间通信简介

不同的进程需要进行信息的交互和状态的传递等，因此需要进程间通信（IPC：Inter Processes Communication

**进程通信目的：**

- 数据传输：一个进程需要将它的数据发送给另一个进程
- 通知事件：一个进程需要向另一个或一组进程发送信息，通知它（它们）发生了某种事件（如进程终止时要通知父进程）
- 资源共享：多个进程之间共享同样的资源。（需要用到内核提供的互斥和同步机制）
- 进程控制：有些进程希望完全控制另一个进程的运行（如 Debug 进程gdb），此时控制进程希望能够拦截另一个进程的所有陷入和异常，并能够及时知道其状态改变



**Linux进程间通信的方式：**

![image-20230205232903586](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205232903586.png)







### 2.6.2 匿名管道

#### 概述

管道 也叫匿名管道。是 UNIX 系统 IPC 的最古老形式，所有的 UNIX 系统都支持这种通信机制。



**案例解析：**

​	统计一个目录中文件的数目命令：`ls | wc -l`	（管道符 | ）

为了执行该命令，shell 创建了两个进程分别执行 ls 和 wc

![image-20230205233552602](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230205233552602.png)

ls进程的标准输出 stdout 指向管道写入端

wc进程的标准输入 stdin 指向管道读取端



**特点**

- 管道 其实是一个在`内核内存中`维护的缓冲器，这个缓冲器的存储能力是有限的，不同操作系统中可能不同
- 管道拥有文件的特质：读操作、写操作，<u>匿名管道没有文件实体，有名管道有文件实体，但不存储数据。</u>可以按照操作文件的方式操作管道
- 一个管道是一个`字节流`，使用管道时不存在消息或者消息边界的概念，从管道读取数据的进程可以读取任意大小的数据块，而不管写入进程写入管道的数据库的大小是多少
- 通过管道传递的数据是顺序的
- 在管道中的数据的传递方向是单向的，一端写入，一端读取，是半双工的（有两个传递方向，同一时刻内只能通一个方向）
- 从管道读取数据是一次性操作，数据一旦被读走，就从管道中被抛弃，释放空间以便写更多的数据，在管道中无法使用lseek()来随机访问数据
- 匿名管道只能在具有公共祖先的进程之间使用（父子进程共享文件描述符表，有相同的管道）

![image-20230206013639551](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230206013639551.png)



**管道的数据结构**

环形队列（重复利用节点空间）

![image-20230206014318131](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230206014318131.png)



#### 管道的使用

- 创建匿名管道

  ```c
  #include <unistd.h>
  
  int pipe(int pipefd[2]);
  /*
  	创建一个匿名管道，用于进程间通信
  	参数： int pipefd[2]：传出参数
  			pipefd[0] 管道的读端
  			pipefd[1] 管道的写端
  	返回值：
  		成功 0
  		失败 -1
  	
  	管道默认是阻塞的：
  		如果管道中没有数据，read阻塞；
  		如果管道满了，write阻塞；
  	
  	注意：匿名管道只能用于具有关系的进程之间的通信（父子进程，兄弟进程...）
  */
  
  #include <sys/types.h>
  #include <stdio.h>
  #include <stdlib.h>
  #include <string.h>
  
  // 实现：子进程发送数据给父进程，父进程读取到数据输出
  int main(){
      
      // 在fork之前创建管道
      int pipefd[2];
      int ret = pipe(pipefd);
      if(ret == -1){
          perror("pipe");
          exit(0);
      }
      
      // 创建子进程
      pid_t pid = fork();
      if(pid > 0){
          // 父进程
          // 从管道的读取端读取数据
          close(pipefd[1]);
          
      	char buf[1024] = {0};
          int len = read(pipefd[0], buf, sizeof(buf));
          printf("parent recv : %s, pid: %d\n", buf, getpid());
      } else if(pid == 0){
          // 子进程
          close(pipefd[0]);
          
          char *str = "hello, i am child";
          write(pipefd[1], str, strlen(str));
      }
      
      
      return 0;
  }
  ```

- 查看管道缓冲大小命令:`ulimit -a`

- 查看管道缓冲大小函数

  ```c
  #include <unistd.h>
  
  long fpathconf(int fd, int name);
  /*
  	获取文件的配置信息值
  	参数 name：宏值，表明要查看的内容
  		_PC_PIPE_BUF
  		更多参数参考文档
  */
  ```

  

> 规定：
>
> - 使用匿名管道实际开发时，父子进程不相互收发数据，而是规定好一个进程读一个进程写
> - 读的进程关闭写端；写的进程关闭读端；
>
> ![image-20230206022417540](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230206022417540.png)



#### 案例

实现` ps aux | grep xxx `

父子进程间通信

- 子进程：ps aux，结束后将数据发送给父进程

- 父进程：获取数据，并过滤

  pipe()

  execlp()

  dup2() 子进程将标准输出 stdout_fileno 重定向到管道的写端

```c
#include <unistd.h>
#include <sys/types.h>
#include <stdio.h>
#include <stdlib.h>
#include <wait.h>

int main(){
    
    // 创建管道
    int fd[2];
    int ret = pipe(fd);
    
    if(ret == -1){
        perror("pipe");
        exit(0);
    }
    
    // 创建子进程
    pid_t pid = fork();
    if(pid > 0){
        // 父进程
        // 关闭写端
        close(fd[1]);
        
        // 从管道中读取
        char buf[1024] = {0};
        int len = -1;
        while((len = read(fd[0], buf, sizeof(buf)-1)) > 0){
			// 过滤数据并输出  过滤功能还没写          
        	printf("%s", buf); 
            memset(buf, 0, 1024);
        }
        
        wait(NULL);
    } else if(pid == 0){
		// 子进程
        // 关闭读端
        close(fd[0]);
        
        // 文件描述符的重定向 stdout_fileno -> fd[1]
        dup2(fd[1], STDOUT_FILENO);
        
        // 执行 ps aux
        execlp("ps", "ps", "aux", NULL);
        perror("execlp");
        exit(0);
    } else {
        perror("fork");
        exit(0);
    }

   	return 0；
}

```





#### 管道读写特点和非阻塞的设置

**读写特点（阻塞I/O下）**

1. 如果所有的指向 管道写端 的文件描述符都关闭了`（写端引用计数为0）`，有进程从管道的读端读数据，`那么管道中剩余的数据被读取以后，再次read会返回 0`，就像读到文件末尾一样。
2. 如果有指向 管道写端 的文件描述符没有关闭`（写端引用计数大于0）`，而持有管道写端的进程也`没有往管道中写数据`，这时有进程从管道的读端读数据，`那么管道中剩余的数据被读取以后，再次read会阻塞`，直到管道中有数据可以读了才读取数据并返回。
3. 如果所有的指向 管道读端 的文件描述符都关闭了`（读端引用计数为0）`，这个时候向管道中写数据，那么该进程会收到一个信号 SIGPIPE ，通常会导致进程异常终止。
4. 如果有指向 管道读端 的文件描述符没有关闭`（读端引用计数大于0）`，而持有管道读端的进程也`没有往管道中读数据`，这时有进程从管道的写端写数据，`那么管道被写满的时候，再次调用write会阻塞`，直到管道中有空位置才能再次写入数据并返回。

> **总结：**
>
> ​	读管道：
>
> ​		管道中有数据，read读取数据，返回实际读到的字节数。
>
> ​		管道中无数据：
>
> ​			写端被全部关闭，read返回0（相当于读到文件的末尾）
> ​			写端没有完全关闭，read阻塞等待
>
> ​	
>
> ​	写管道：
>
> ​		管道读端全部被关闭，进程异常终止（进程收到 SIGPIPE 信号）
>
> ​		管道读端没有全部关闭：
>
> ​			管道已满，write阻塞
> ​			管道未满，write将数据写入，返回实际写入的字节数





**非阻塞设置**

fcntl函数

```c
int flags = fcntl(fd[0], F_GETFL); // 获取原来的flag
flags |= O_NONBLOCK;			 // 修改flag
fcntl(fd[0], F_SETFL, flags)；	// 设置新的flag

```





### 2.6.3 有名管道

#### 概述

- 匿名管道由于无名，只能用于亲缘关系的进程间通信。为了扩大应用，提出了 有名管道 （FIFO），也叫命名管道，FIFO文件。
- 有名管道（FIFO）不同于匿名管道之处，<u>在于它提供了一个路径名与之关联</u>，以FIFO的文件形式存在于文件系统中，并且打开方式与普通文件相同。只要可以访问该路径，就能彼此通过有名管道相互通信，使得不相关的进程也能通信。



**与匿名管道的不同之处：**

1. FIFO 在文件系统中作为一个特殊文件存在，但 FIFO 中的内容却存放在内核区的内存中。
2. 当使用 FIFO 的进程推出后，FIFO 文件将保存在文件系统中以便以后调用。
3. FIFO 有名，不相关的进程可以通过打开有名管道进行通信。



#### 有名管道的使用

- 通过命令创建：`mkfifo name`

- 通过函数创建

  ```c
  #include <sys.types.h>
  #include <sys.stat.h>
  
  int mkfifo(const char *pathname, mode_t mode);
  /*
  	参数：
  		- pathname：管道名称的路径
  		- mode：文件权限，和 open 的 mode 一样。
  	
  	返回值：
  		成功 0，失败 -1
  */
  
  ```

- 一旦使用 mkfifo 创建了一个 FIFO ，就可以使用 open 打开，以及使用常见的 I/O 函数，如：read，write，unlink，close...

- FIFO 严格遵循先进先出，读操作 总是从开始处返回数据，写操作 则把数据添加到末尾。所以不支持 lseek()等文件定位操作。



#### 案例

**基本使用**

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

// 向管道中写数据
int main(){
    
    // 判断文件是否存在
    int ret = access("test", F_OK);
    if(ret == -1){
        // 管道还未创建
        ret = mkfifo("test", 0664);
        if(ret == -1){
            perror("mkfifo");
            exit(0);
        }
    }
    
    // 打开管道（只写方式）
    int fd = open)"test", O_WRONLY);
    if(fd == -1){
        perror("open");
        exit(0);
    }
    
    // 写数据
    for(int i = 0; i < 100; i++) {
        char buf[1024];
        sprintf(buf,"hello %d\n", i);
        printf("write data: %s\n", buf);
        write(fd, buf, strlen(buf));
        sleep(1);
    }
    
    close(fd);
    
    return 0;
}




// 从管道中读取数据
int main1() {
    
    // 打开管道文件
    int fd = open("test", O_RDONLY);
    int(fd == -1){
        perror("open");
		exit(0);        
    }
    
    // 读取数据
    while(1) {
        char buf[1024] = {0};
        int len = read(fd, buf, sizeof(buf));
        if(len == 0) {
            // 写端断开连接了
            break;
        }
        printf("recv buf: %s\n",buf);
    }
    
    close(fd);
    
    return 0;
}
```



> open 注意事项：
>
> 1. 一个为只读而打开一个管道的进程会阻塞，直到另外一个进程为只写打开管道
> 2. 一个为只写而打开一个管道的进程会阻塞，直到另外一个进程为只读打开管道

> **读管道：**
>
> ​	管道中有数据，read发怒hi实际督导的字节数
>
> ​	管道中无数据：
> ​		管道写端被全部关闭，read返回0 （相当于读到文件末尾）
> ​		管道写端没有全部关闭，read阻塞等待
>
> 
>
> **写管道：**
>
> ​	管道读端被全部关闭，进程异常终止（收到 SIGPIPE 信号）
>
> ​	管道读端没有全部关闭：
> ​		管道已满，write会阻塞
> ​		管道未满，write写入数据，返回实际写入的字节数





**有名管道实现简单聊天**

只能交替地接收和发送

![image-20230206212504676](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230206212504676.png)



进程A

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    
    // 1.判断有名管道文件是否存在
    int ret = access("fifo1", F_OK);
    if(ret == -1){
        // 文件不存在则创建
        ret = mkfifo("fifo1", 0664);
    	if(ret == -1){
			perror("mkfifo");
            exit(0);
    	}
    }
	ret = access("fifo2", F_OK);
    if(ret == -1){
        // 文件不存在则创建
        ret = mkfifo("fifo2", 0664);
    	if(ret == -1){
			perror("mkfifo");
            exit(0);
    	}
    }
    
    // 以只写方式打开管道 fifo1
    int fdw = open("fifo1", O_WRONLY);
    if(fdw == -1) {
        perror("open");
    	exit(0);
    }
    printf("打开管道fifo1成功，等待写入...\n")；
    
    // 以只读方式打开管道 fifo2
    int fdr = open("fifo2", O_RDONLY);
    if(fdr == -1) {
        perror("open");
    	exit(0);
    }
    printf("打开管道fifo2成功，等待读取...\n")；
    
	// 循环写读数据
    char buf[128];
    
    while(1) {
        memset(buf, 0, 128);
    	// 获取标准输入的数据    
        fgets(buf, 128, stdin);
        // 发送信息，写入数据
        ret = write(fdw, buf, strlen(buf));
        if(ret == -1){
            perror("write");
            exit(0);
        }
        
        // 接收信息，读取数据
        memset(buf, 0, 128);
        ret = read(fdr, buf, 128);
        if(ret <= 0){
            perror("read");
           	break;
        }
        printf("buf: %s\n", buf);
    }
        
    close(fdr);
    close(fdw);
        
    return 0;
}


```



进程B 

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    
    // 1.判断有名管道文件是否存在
    int ret = access("fifo1", F_OK);
    if(ret == -1){
        // 文件不存在则创建
        ret = mkfifo("fifo1", 0664);
    	if(ret == -1){
			perror("mkfifo");
            exit(0);
    	}
    }
	ret = access("fifo2", F_OK);
    if(ret == -1){
        // 文件不存在则创建
        ret = mkfifo("fifo2", 0664);
    	if(ret == -1){
			perror("mkfifo");
            exit(0);
    	}
    }
    
    // 以只读方式打开管道 fifo1
    int fdw = open("fifo1", O_RDONLY);
    if(fdw == -1) {
        perror("open");
    	exit(0);
    }
    printf("打开管道fifo1成功，等待读取...\n")；
    
    // 以只写方式打开管道 fifo2
    int fdr = open("fifo2", O_WRONLY);
    if(fdr == -1) {
        perror("open");
    	exit(0);
    }
    printf("打开管道fifo2成功，等待写入...\n")；
    
	// 循环读写数据
    char buf[128];
    
    while(1) {
        
        // 接收信息，读取数据
        memset(buf, 0, 128);
        ret = read(fdr, buf, 128);
        if(ret <= 0){
            perror("read");
           	break;
        }
        printf("buf: %s\n", buf);
        
        
        memset(buf, 0, 128);
    	// 获取标准输入的数据    
        fgets(buf, 128, stdin);
        // 发送信息，写入数据
        ret = write(fdw, buf, strlen(buf));
        if(ret == -1){
            perror("write");
            exit(0);
            
        }
    }
        
    close(fdr);
    close(fdw);
        
    return 0;
}
```



### 2.6.4 内存映射

内存映射 Memory-mapped I/O ,是将磁盘文件的数据映射到内存，用户通过修改内存就能修改磁盘文件。

映射的是进程的虚拟地址空间，映射位置是共享库的位置

![image-20230207021339909](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207021339909.png)



**相关系统调用**

```c
#include <sys/mman.h>

void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
/*
	将一个文件或设备的数据映射到内存当中
	
	参数：
		- addr：传入NULL，由内核决定后续处理
		- length：要映射的数据长度，不能为0.建议使用文件的长度	（使用 stat 或 lseek 可以获取文件长度）
		- prot：对申请的内存映射区的操作权限（不能与打开的文件有权限冲突）
			PROT_NONE 无权限
			PROT_EXEC 可执行
			PROT_READ 可读
			PROT_WRITE 可写
			（要操作映射内存，必须有读权限）
		- flags：
			MAP_SHARED	映射区的数据会自动和磁盘文件进行同步，进程间通信，必须设置该选项
			MAP_PRIVATE	不同步，内存映射区的数据改变不会修改源文件，而会创建一个新的文件 （copy on write）
		- fd：需要映射的文件的文件描述符，通过 open 得到的。 注意：文件大小要大于0，且 open 指定的权限不能和 prot 冲突。
		- offset：偏移量，文件数据的起始位置偏移，一般不使用（必须是4k的整数倍，0表示不偏移）
	
	返回值：
		成功，返回映射到的内存首地址（虚拟地址）
		失败，返回 MAP_FAILED 即（void *) -1
*/


int munmap(void *addr, size_t length);
/*
	释放内存映射
	
	参数：
		- addr：要释放的内存的首地址
		- length：要释放的内存大小，要和mmap函数中的length参数值一致
*/

```



**实现进程间通信**

父子进程之间

> - 在还没有子进程的时候
>   - 通过唯一的父进程，先创建内存映射区
> - 有了内存映射区后，创建子进程
> - 父子进程共享创建的内存映射区

```c
#include <stdio.h>
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <wait.h>

int main() {
    
    // 打开一个文件
    int fd = open("test.txt", O_RDWR);
    if(fd == -1) {
        perror("open");
        exit(0);
    }
    
    // 获取文件大小
    int size = lseek(fd, 0, SEEK_END);
    
    // 创建内存映射区
    void *ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if(ptr == MAP_FAILED) {
        perror("mmap");
        exit(0);
    }
    
    // 创建子进程
    pid_t pid = fork();
    if(pid > 0){
        // 父进程
        wait(NULL);
        char buf[64];
        strcpy(buf, (char *)ptr);
        printf("read data:%s\n", buf);
    } else if(pid == 0){
        // 子进程
        strcpy((char *)ptr, "123456");
    }
    
    //关闭内存映射区
    munmap(ptr, size);
    
    return 0;
}


```



无关系的进程之间

>- 准备一个大小不是0的磁盘文件
>- 进程1 通过磁盘文件创建内存映射区
> - 得到一个操作这块内存的指针
>- 进程2 通过磁盘文件创建内存映射区
> - 到到一个操作这块内存的指针
>- 使用内存映射区通信

```c
#include <stdio.h>
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <wait.h>

int main1() {
    
    // 打开一个文件
    int fd = open("test.txt", O_RDWR);
    if(fd == -1) {
        perror("open");
        exit(0);
    }
    
    // 获取文件大小
    int size = lseek(fd, 0, SEEK_END);
    
    // 创建内存映射区
    void *ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if(ptr == MAP_FAILED) {
        perror("mmap");
        exit(0);
    }
    
    // 通过获取文件数据
    char buf[64];
	strcpy(buf, (char *)ptr);
	printf("read data:%s\n", buf);

    //关闭内存映射区
    munmap(ptr, size);
    
    return 0;
}

int main2() {
    
    // 打开一个文件
    int fd = open("test.txt", O_RDWR);
    if(fd == -1) {
        perror("open");
        exit(0);
    }
    
    // 获取文件大小
    int size = lseek(fd, 0, SEEK_END);
    
    // 创建内存映射区
    void *ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if(ptr == MAP_FAILED) {
        perror("mmap");
        exit(0);
    }
    
    // 写入内存，修改文件
    strcpy((char *)ptr, "123456");
    
    //关闭内存映射区
    munmap(ptr, size);
    
    return 0;
}

```





**思考问题**

1. 如果对mmap的返回值 (ptr) 做++操作 (ptr++) ，munmap能否成功

> 可以 ptr++; 但 munmap 会失败，只能通过首地址释放（提前保存）

2. 如果 open 时 O_RDONLY ，mmap 时 prot 参数指定 PROT_READ | PROT_WRITE 会怎样

> 错误，会返回MAP_FAILED。
>
> open 函数中的权限要满足 mmap，即 prot <= open 的权限参数

3. 如果文件偏移量为1000会怎样

>偏移量必须是 4k 的整数倍，否则会返回 MAP_FAILED

4. mmap什么情况下会调用失败

>- 第二个参数：length = 0
>- 第三个参数：prot
> - 没有指定读权限
> - 参数 fd 在 open 时的权限不满足当前 prot

5. 可以oepn的时候O_CREAT一个新文件来创建映射区吗

> 可以，但是创建的文件大小如果为 0 ，则不行
>
> 可以对新的文件进行拓展
>
> 	- lseek()
> 	- truncate()

6. mmap后关闭文件描述符，对mmap映射有没有影响

> ​	int fd = open("xxx");
>
> ​	mmap(..., fd, ...);
>
> ​	close(fd);
>
> 映射区还存在，创建的映射区的fd被关闭，没有任何影响

7. 对ptr越界操作会怎样

> ​	void * ptr = mmap(NULL, 100, ...);
>
> 越界操作会引发段错误





除了进程间通信，还可以用来实现 **文件复制**

1. 对原始的文件进行内存映射
2. 创建新文件（并拓展
3. 把新文件的数据映射到内存中
4. 通过内存拷贝将第一个文件的数据拷贝到新的文件内存中
5. 释放资源

```c
#include <stdio.h>
#include <sys/mmap.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>

int main() {
    
    // 对原始的文件进行内存映射
    int fd = open("ori.txt", O_RDWR);
    if(fd == -1) {
        perror("open");
        exit(0);
    }
    // 获取原始文件大小
    int len = lseek(fd, 0, SEEK_END);
    
    // 创建一个新的文件
	int fd1 = open("cpy.txt", O_RDWR | O_CREAT, 0664);
    if(fd1 == -1) {
        perror("open");
        exit(0);
    }
    
    // 对新创建的文件进行拓展
    truncate("cpy.txt", len);
    write(fd1, " ", 1);
    
    // 分别做内存映射
    void *ptr = mmap(NULL,len, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    void *ptr1 = mmap(NULL,len, PROT_READ | PROT_WRITE, MAP_SHARED, fd1, 0);
    if(ptr == MAP_FAILED || ptr1 == MAP_FAILED) {
        perror("mmap");
        exit(0);
    }
    
    // 内存拷贝
    memcpy(ptr1, ptr, len);
    
    // 释放资源	（习惯上，先打开的后释放）
    munmap(ptr1, len);
    munmap(ptr, len);    
    
    close(fd1);
    close(fd);
    
    return 0;
}


```





**匿名映射**(用于通信)

不需要文件实体进行内存映射，只能用于有关系的进程之间。

mmap 需要打开 `MAP_ANONYMOUS` 选项

```c
#include <stdio.h>
#include <sys/mmap.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <sys/wait.h>

int main() {
    
    // 创建匿名内存映射区
    int len = 4096;
    void *ptr = mmap(NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS，-1， 0)；
    if(ptr == MAP_FAILED) {
        perror("mmap");
        exit(0);
    }
    
    // 父子进程通信
    pid_t pid = fork();
    
    if(pid > 0) {
        // 父进程
        strcpy((char *)ptr, "hello");
        wait(NULL);
    } else if(pid == 0) {
        // 子进程
		sleep(1);
        printf("%s\n", (char *)ptr);
    }
    
    // 释放内存
    int ret = munmap(ptr, len);
    if(ret == -1) {
        perror("munmap");
        exit(0);
    }
    
    return 0;
}

```



>与直接修改文件相比，在内存修改速度更快 



### 2.6.5 信号

#### 信号概述

信号是 Linux 进程通信最古老的方式之一，是事件发生时对进程的通知机制，有时又称为 `软件中断` ，它是在软件层次上对中断机制的一种模拟，是一种 `异步` 通信的方式。

信号可以导致一个正在运行的进程被另一个正在运行的异步进程中断，转而处理某一个突发事件。



发往进程的诸多信号，通常都是源于`内核`。引发内核为进程产生信号的各类事件如下：

- 对于前台进程，用户可以通过输入特殊的终端字符来发送信号。比如 Ctrl+C 通常会给进程发送一个中断信号。
- 硬件发生异常，即硬件检测到一个错误条件并通知内核，随机再由内核发送相应信号给相关进程。比如执行一条异常的机器语言指令，如除以0，或者引用了无法访问的内存区域。
- 系统状态的变化，比如 alarm 定时器到期将引起 SIGALRM 信号、进程执行的CPU时间超限、或者该进程的某个子进程退出。
- 运行 kill 命令或调用 kill 函数。



**使用信号的两个目的**

- 让进程知道已经发生了一个特定的事情
- 强迫进程执行它自己代码中的信号处理程序



**特点**：

- 简单
- 不能携带大量信息
- 满足某个特定条件才发送
- 优先级比较高



查看系统定义的信号列表：`kill -l`

> 前31个信号为常规信号，其余为实时信号



查看信号的详细信息：`man 7 signal`

![image-20230207074413475](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207074413475.png)



![image-20230207074659817](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207074659817.png)



![image-20230207074814071](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207074814071.png)



![image-20230207074951533](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207074951533.png)



进程的5个**默认处理动作**

- Term 终止进程

- Ign 当前进程忽略掉这个信号

- Core 终止进程，并生成一个Core文件（保存进程异常退出的错误信息）

  > 通过 unlimit -c xxx 设置 core 文件生成
  >
  > 通过 gdb 打开生成的 core 文件

- Stop 暂停当前进程

- Cont 继续执行当前被暂停的进程



信号的3种**状态**：产生、未决、递达



>SIGKILL 和 SIGSTOP 信号不能被捕捉、阻塞或者忽略，只能执行默认动作





#### kill、raise、abort 函数

```c
#include <sys/types.h>
#include <signal.h>
int kill(pid_t pid, int sig);
/*
	给某个进程pid，发送某个信号sig
	参数：
		- pid ：目标进程的 id
			> 0 : 将信号发送给指定进程
			= 0 : 将信号发送给当前进程的进程组
			= -1 : 将信号发生给每一个有权限接收这个信号的进程
			< -1 : 这个pid=某个进程组的gid取反
		- sig ：需要发送的信号编号或宏值，0表示不发送任何信号
*/

#include <signal.h>
int raise(int sig);
/*
	给当前进程发送某个信号sig
	参数：
		- sig ：需要发送的信号编号或宏值，0表示不发送任何信号
		返回值：成功 0 ，失败 非 0
*/


void abort(void);
/*
	发送 SIGABRT 信号给当前进程
*/

```



#### alarm 函数

```c
#include <unistd.h>

unsigned int alarm(unsigner int seconds);
/*
	设置定时器（闹钟）。函数调用后开始倒计时，当计时为0时，函数会给当前进程发送一个 SIGALARM 信号。
	
	参数：
		- seconds：倒计时的时常，单位秒。若为0，则定时器无效（可以用 alarm(0); 取消定时器）
	
	返回值：
		- 之前没有定时器，返回0
		- 之前有定时器，返回定时器剩余的时间
	
	SIGALARM ： 默认终止当前的进程。
	
	每一个进程都有且只有唯一的一个定时器。
	
*/

```



> 程序运行的实际时间 = 内核时间 + 用户时间 + 切换、文件IO等消耗的时间

> 定时器与进程的状态无关。（采用自然定时法，无论进程出于什么状态，alarm都会计时）



#### setitimer 定时器函数

周期性的定时

```c
#include <sys/time.h>

// 定时器值结构体
struct itimerval {
    struct timeval it_interval;	// 每个间隔的时间
    struct timeval it_value;	// 延迟多长时间启动定时器
}
// 时间值结构体
struct timeval {
    time_t tv_sec;		// seconds s
    suseconds_t tv_usec;	// microseconds us
}


int setitimer(int which, const struct itimerval *new_val, struct itimerval *old_value);
/*
	设置定时器（闹钟），替代 alarm 函数。精度微妙us，周期性计时。

	参数：
		- which : 定时器以什么时间计时
			ITIMER_REAL : 真实时间，时间到则发送SIGALARM ，常用
			ITIMER_VIRTUAL : 用户时间，时间到则发送 SIGVTALRM
			ITIMER_PROF : 以该进程在用户态和内核态下所消耗的时间来计算，时间到则发送 SIGPROF
			
		- new_val : 设置定时器的属性
		- old_value : 记录上一次的定时的时间参数，一般不使用，指定 NULL
		
	返回值：
		成功 0 ，失败 -1

*/

```



#### signal 信号捕捉函数

```c
#include <signal.h>

typedef void (*sighandler_t)(int);

sighandler_t signal(int signum, sighandler_t handler);
/*
	设置某个信号的捕捉行为
	
	参数：
		- signum ： 要捕捉的信号
		- handler ： 捕捉到信号后的处理
			可以是：
			SIG_IGN : 忽略
			SIG_DEL : 使用信号默认的行为
			回调函数 ： 由内核调用，程序员负责实现其功能
		回调函数：
			- 由程序员实现，提前准备好的，函数的类型根据实际需求，和函数指针的定义
			- 不是程序员调用，而是当信号产生，由内核调用
			- 函数指针是实现回调的手段，函数实现之后，将函数名放到函数指针的位置即可
			

	返回值：
		成功，返回上一次注册的信号处理函数的地址，若是第一次则返回 NULL
		失败，返回 SIG_ERR ，并设置errno

*/

```



> SIGKILL 和 SIGSTOP 信号不能被捕捉、阻塞或者忽略，只能执行默认动作



**案例**

```c
#include <sys/time.h>
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>

void myalarm(int num) {
    printf("sss\n");
}


//延迟3秒后启动间隔为2秒的定时器,启动时也会发送信号
int main() {
    
    // 注册信号捕捉
    // signal(SIGALARM, SIG_IGN);
    // signal(SIGALARM, SIG_DEL);
    // void (* sighandler_t)(int); 函数指针
    sighandler_t ret1 = signal(SIGALARM, myalarm);
    if(ret1 == SIG_ERR) {
        perror("signal");
        exit(0)；
    }
    
    struct itimerval new_val;
    // 间隔时间
    new_val.it_interval.tv_sec = 2;
    new_val.it_interval.tv_usec = 0;
    // 延迟时间
    new_val.it_value.tv_sec = 3;
    new_val.it.value.tv_usec = 0;
    
    int ret2 = setitimer(ITIMER_REAL, &new_value, NULL);		// 非阻塞的
    if(ret2 == -1) {
        perror("setitimer");
        exit(0);
    }
    
    while(1) {
        sleep(1);
    }
    
    return 0;
}

```



#### 信号集及相关函数

**信号集**

- 许多信号相关的系统调用都需要能表示一组不同的信号，多个信号可使用一个称之为信号集的数据结构来表示，其系统数据类型为 sigset_t
- 在 PCB 中有两个非常重要的信号集。一个称之为“阻塞信号集”，另一个称之为“未决信号集”。这两个信号集都是内核使用位图机制来实现的，但操作系统不允许我们直接对这两个信号集进行位操作，而需自定义另外一个集合，借助信号集操作函数来对 PCB 中的两个信号集进行修改。
- ”未决“，是一种状态，指的是<u>从信号的产生到信号被处理前</u>的这一段时间。
- ”阻塞“，是一个开关动作，指的是阻止信号被处理，但不是阻止信号产生。  
- 信号的阻塞就是让系统暂时保留信号，留待以后发送。由于另外有办法让系统忽略信号，所以一般情况下信号的阻塞只是暂时的，只是为了防止信号打断敏感的操作。



**默认**

![image-20230207122515067](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207122515067.png)



**操作信号集的函数**

```c
#include <signal.h>
//以下信号集相关的函数都是对自定义的信号集（参数set）进行操作

int sigemptyset(sigset_t *set);
/*
	清空信号集中的数据，将所有标志位置 0
	参数：	set 要操作的信号集
	返回值：成功0，失败-1
*/

int sigfillset(sigset_t *set);
/*
	将信号集中的所有标志位置 1
	参数：	set 要操作的信号集
	返回值：成功0，失败-1
*/

int sigaddset(sigset_t *set, int signum);
/*
	设置信号集中的某一个信号对应得标志位为 1 ，表示阻塞这个信号
	参数：	
		- set 要操作的信号集
		- signum 需要设置的信号
	返回值：成功0，失败-1
*/

int sigdelset(sigset_t *set, int signum);
/*
	设置信号集中的某一个信号对应得标志位为 0 ，表示不阻塞这个信号
	参数：	
		- set 要操作的信号集
		- signum 需要设置的信号
	返回值：成功0，失败-1
*/

int sigismember(const sigset_t *set, int signum);
/*
	判断某个信号是否阻塞
	参数：	
		- set 要操作的信号集
		- signum 需要判断的信号
	返回值：
		- 1 ，signum被阻塞
		- 0 ，signum不阻塞
		- -1，调用失败
*/

```



#### sigprocmask 函数

将自己设置好的信号集应用到内核信号集中

```c

int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
/*
	将自定义信号集中的数据设置到内核中（设置阻塞，解除阻塞，替换）
	参数：
		- how  如何对内和阻塞信号集进行处理
			SIG_BLOCK：将用户设置的阻塞信号集添加到内核中，内核中原来的数据不变	（假设内核中默认的阻塞信号集是mask，则令 mask | set ）
			SIG_UNBLOCK：根据用户设置的数据，对内核中的数据进行解除阻塞  （ mask &= ~set ）
			SIG_SETMASK: 覆盖内核中原来的值
		- set  已经初始化的用户自定义的信号集
		- oldset  保存设置之前的内核中的阻塞信号集的状态，可以是NULL
	
	返回值：
		成功 0
		失败 -1
			设置错误号：EFAULT、EINVAL
*/

int sigpending(sigset_t *set);
/*
	获取内核中的未决信号集

	参数 : set  传出参数，保存的是内存中的未决信号集的信息

	返回值：成功0，失败-1
*/

```



#### sigaction 信号捕捉函数

跟 signal 函数作用类似

```c
#include <signal.h>

struct sigaction {
    void (*sa_handler)(int);	// 函数指针，指向的函数就是回调函数
    void (*sa_sigaction)(int, siginfo_t *, void *);		// 不常用，需要就看看查文档
    sigset_t sa_mask;	// 临时阻塞信号集，在信号捕捉函数执行过程中，临时阻塞某些信号
    int sa_flags;	// 决定使用哪一个信号处理函数（ 0 表示使用 sa_handler，SA_SIGINFO 表示使用sa_sigaction
    void (*sa_restorer)(void);	// 废弃
}



int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
/*
	检查或改变信号的处理。信号捕捉
	
	参数：
		- signum  指定的信号（ 除了SIGKILL 和 SIGSTOP ）
		- act  捕捉到信号后相应的处理动作
		- oldact  上一次对信号捕捉设置的处理动作，一般不使用，传递 NULL


	返回值：
		成功 0
		失败 -1

*/


```



>由于编程标准原因，尽量使用 sigaction



如果在回调函数执行过程中又收到信号，则会阻塞回调函数优先处理信号（解决方案：设置临时阻塞信号集）



**内核实现信号捕捉的过程**

![image-20230207223806887](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230207223806887.png)

> 内核态的 do_signal() 和 sys_sigreturn()





#### SIGCHLD 信号

产生条件：

- 子进程终止时
- 子进程接收到 SIGSTOP 信号并停止时
- 子进程处在停止态，接收到 SIGCONT 信号唤醒时

以上情况会由内核给父进程发送 SIGCHLD 信号，父进程默认忽略。



**解决僵尸进程问题**

- 利用子进程终止时发送 SIGCHLD 信号
- 创建子进程前，先阻塞 SIGCHLD ，等回调函数注册完之后再解除阻塞



```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <signal.h>
#include <sys/wait.h>


// 子进程资源回收 回调函数
void myFun(int num) {
    printf("捕捉信号 %d\n", num);
    
    while(1){
        int ret = waitpid(-1, NULL, WNOHANG);
        if(ret > 0) {
            printf("child die %d\n", ret);
        } else if(ret == 0) {
            // 还有子进程存活
            break;
        } else (ret == -1) {
            // 没有子进程了，回收完毕
            break;
        }
    }
}


int main() {
    
    // 打开阻塞
    sigset_t set;
    sigemptyset(&set);
    sigaddset(&set. SIGCHLD);
    sigprocmask(SIG_BLOCK, &set, NULL);
    
    pid_t pid;
    for(int i = 0; i < 20; i++) {
        pid = fork();
        if(pid == 0){
            break;
        }
    }
    
    if(pid > 0) {
        
        struct sigaction act;
        act.sa_flags = 0;
        act.sa_handler = myFun;
        sigemptyset(&act.sa_mask);
        sigaction(SIGCHLD, &act, NULL);
        
        // 解除阻塞
        sigprocmask(SIG_UNBLOCK, &set, NULL);
        
        while(1) {
            sleep(1);
        }
    } else if(pid == 0) {
        printf("child %d\n", getpid());
        
    }
    
    
    return 0;
}





```



### 2.6.6 共享内存

效率最高的进程间通信方式

（内存映射需要用到文件）



- 共享内存允许两个或多个进程共享`物理内存`的同一块区域（通常称为 段 )。由于一个共享内存段会被称为一个进程用户空间的一部分，因此这种 IPC 机制无需内核介入（实际上还是会用到系统调用，但相对较少）。所有需要做的就是让一个进程将数据复制进共享内存中，并且这部分数据会对其他所有共享同一个段的进程可用。
- 与管道等要求 发送进程 将数据从用户空间的缓冲区复制进内核内存 和 接收进程 将数据从内核内存复制进用户空间的缓冲区 的做法相比，共享内存方式速度更快。



**使用步骤**

1. 调用 shmget() 创建一个新共享内存段或区的一个既有共享内存段的标识符（即由其他进程创建的共享内存段）。这个调用将返回后续调用中需要用到的共享内存标识符。
2. 使用 shmat() 来附上共享内存段，也就是使该段成为调用进程的虚拟内存的一部分。
3. 此刻在程序中可以像对待其他可用内存那样对待这个共享内存段。为引用这块共享内存，程序需要使用由 shmat() 调用返回的 addr 值，它是一个指向进程的虚拟地址空间中该共享内存段的起点的指针
4. 调用 shmdt() 来分离共享内存段。调用后，进程就无法再引用这块共享内存。（这一步是可选的，并且在进程终止时会自动完成）
5. 调用 shmctl() 来删除共享内存段。只有当当前所有附加内存段的进程都与之分离之后内存段才会销毁。（只有一个进程需要执行这一步）





**函数接口**

```c
#include <sys/ipc.h>
#include <sys/shm.h>


int shmget(key_t key, size_t size, int shmflg);
/*
	创建一个新的共享内存段，或者获取一个既有的共享内存段的标识
	新创建的内存段中的数据都会被初始化为0
	
	参数：
		- key	key_t时整型，通过这个参数找到或者创建一个共享内存。一般使用16进制表示，非0值
		- size	共享内存的大小（实际按页划分）
		- shmflg 属性
			- 访问权限
			- 附加属性：创建/判断共享内存是否存在
				- 创建：IPC_CREAT
				- 判断共享内存是否存在：IPC_EXCL，需要和IPC_CREAT一起使用
				如 IPC_CREAT | IPC_EXCL | 0664
	
	返回值：
		失败 -1，设置错误号
		成功 >0,返回共享内存的引用 ID ，后面操作共享内存即通过这个值
*/

void *shmat(int shmid, consst void *shmaddr, int shmflg);
/*
	和当前的进程进行关联
	参数：
		- shmid：共享内存的标识id，由shmget返回值获取
		- shmaddr：申请的共享内存的起始地址，指定NULL，由内核决定
		- shmflg：对共享内存的操作
			读：SHM_RDONLY，必须要有读权限
			读写：0
	返回值：
		成功 返回共享内存的首地址
		失败 （void*)-1
*/

int shmdt(const void *shmaddr);
/*
	解除当前进程和共享内存的关联
	参数：
		- shmaddr：共享内存的首地址
	返回值：成功0，失败-1
*/

int shmctl(int shmid, int cmd, struct shmid_ds *buf);
/*
	对共享内存进行操作（共享内存需要删除才会消失，创建共享内存的进程被销毁了对共享内存是没有影响的）
	
	参数：
		- shmid：共享内存id
		- cmd：要做的操作
			IPC_STAT 获取共享内存的当前状态
			IPC_SET 设置共享内存的状态
			IPC_RMID 标记共享内存被销毁
		- buf：需要设置或者获取的共享内存的属性信息
			IPC_STAT：buf存储数据，传出参数
			IPC_SET：buf中需要初始化数据，设置到内核中，传入参数
			IPC_RMID：没有用，NULL

*/

```



> shmid_ds结构体
>
> ![image-20230208120249528](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230208120249528.png)



**案例**

write.c

```c
#include <sys/ipc.h>
#include <sys/shm.h>
#include <stdio.h>
#include <string.h>

int main() {
    
    // 创建一个共享内存
    int shmid = shmget(100, 0, IPC_CREAT);
    
    // 和当前进程进行关联
    void *ptr = shmat(shmid, NULL, 0);
    
    // 写数据
    char *str = "hello";
    memcpy(ptr, str, strlen(str)+1);
    // 暂停一下
    getchar();
    
    // 解除关联
    shmdt(ptr);
    
    // 删除共享内存
    shmctl(shmid, IPC_RMID, NULL);
    
    return 0;
}


```



read.c

```c
#include <sys/ipc.h>
#include <sys/shm.h>
#include <stdio.h>
#include <string.h>

int main() {
    
    // 获取一个共享内存
    int shmid = shmget(100, 4096, IPC_CREAT | 0664);
    
    // 和当前进程进行关联
    void *ptr = shmat(shmid, NULL, 0);
    
    // 读数据
    printf("%s",(char *)ptr);
    // 暂停一下
    getchar();
    
    // 解除关联
    shmdt(ptr);
    
    // 删除共享内存
    shmctl(shmid, IPC_RMID, NULL);
    
    return 0;
}


```



> **删除机制**
>
> shmctl(shmid, IPC_RMID, NULL);
>
>
> 当关联进程数 >= 1 时，调用上面的函数删除共享内存并不会直接删除，而是把 key 值置为0，标记删除；
>
> 当关联进程数 = 0 时，共享内存才会真正删除。





**ftok函数**

```c
#include <sys/types.h>
#include <sys/ipc.h>

key_t ftok(const char *pathname, int proj_id);
/*
	根据指定的路径名和 int 值，生成一个共享内存的 key ，可以作为shmget函数的参数
    参数：
		- pathname：指定一个存在的路径
		- proj_id：int类型的值（4B），但这个系统调用指挥使用其中1个字节
				   范围：0~255，一般指定一个字符 'a'
	
*/
```



**问题**

1. 系统如何知道一块共享内存被多少个进程关联？
   - 共享内存维护了一个 shmid_ds 结构体，该结构体的成员 shm_nattach 记录了关联进程数
2. 可不可以对共享内存进行多次删除
   - 可以，当还有关联进程的时候，共享内存只是标记删除。而直到没有关联进程才会被真正删除。
   - key 为 0 ：被标记删除，不再能被关联
3. 共享内存与内存映射的区别
   - 共享内存可以直接创建，内存映射需要磁盘文件（匿名映射除外）
   - 共享内存效率更高
   - **内存：**
     共享内存，所有进程操作的是同一块共享内存（物理）；
     内存映射，每个进程再自己的虚拟地址空间中有一个独立的内存。
   - **数据安全：**
     - 进程突然退出
       共享内存还存在，内存映射区消失。
     - 电脑突然宕机
       共享内存中的数据消失，内存映射区的数据在磁盘文件中仍存在。
   - **生命周期：**
     - 共享内存：进程退出，共享内存仍存在，需要手动删除或关机（进程退出会自动取消关联）
     - 内存映射区：进程退出，映射区销毁



**一些操作命令**（System V）

`ipcs`

![image-20230208122145744](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230208122145744.png)

`ipcrm`

![image-20230208122153825](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230208122153825.png)



### 补充：2.6.7 消息队列





























## 2.7 守护进程

### 终端、进程组、会话

**终端**

- 在 UNIX 系统中，用户通过 终端 登陆到系统后得到一个 shell 进程，这个终端进程为 shell 进程的 控制终端 （Controlling Termianl) ，进程中，控制终端是保存在 PCB 中的信息，而 fork() 会复制 PCB 中的信息，因此由 shell 进程启动的其他进程的控制终端也是这个终端。
- 默认情况下（没有重定向），每个进程的标准输入、标准输出和标准错误输出（文件描述符）都指向控制终端，进程从标准输入读也就是读用户的键盘输入，进程往标准输出或标准错误输出写也就是输出到显示器上。
- 在控制终端输入一些特殊的控制键可以给前台进程发送信号，例如 Ctrl+C 会产生 SIGINT 信号，Ctrl+\ 会产生 SIGQUIT 信号。



**进程组**

- 进程组和会话在进程之间形成了一种两级层次关系：进程组是一组相关进程的集合，会话是一组相关进程组的集合。
- 进程组和会话是为支持 <u>shell作业控制</u> 而定义的抽象概念，用户通过 shell 能够交互式地在前台或后台运行命令。



- 进程组由一个或多个共享同一进程组标识符（PGID）的进程组成。一个进程组拥有一个 进程组首进程 （组长），该进程是创建该组的进程，<u>其进程 ID 为该进程组的 ID</u> ，新进程会继承父进程所属的进程组 ID 。
- 进程组有一个生命周期，起开始时间为首进程创建组的时刻，结束时间为最后一个成员进程退出的时刻。一个进程可能会因为终止而退出进程组，也可能会因为加入另一个进程组而退出进程组。进程组首进程无需是最后一个离开的成员。





**会话**

- 会话是一组进程组的集合。会话首进程是创建该新会话的进程，其进程ID会成为会话ID。新进程会继承其父进程的会话ID。
- 一个会话中的所有进程共享单个控制终端。控制终端会在会话首进程首次打开一个终端设备时被建立。一个终端最多可能成为一个会话的控制终端。
- 在任意时刻，会话中的其中一个进程组会成为终端的前台进程组，其他进程会成为后台进程组。只有前台进程组中的进程才能从控制终端中读取输入。当用户在控制终端中输入终端字符生成信号后，该信号会被发送到前台进程组中的所有成员。
- 当控制终端的连接建立起来之后，会话首进程会成为该终端的控制进程。



**三者关系图**

指令 

`fing / 2 > /dev/null | wc -l &`( & 后台运行指令 )

`sort < longlist | uniq -c`

![image-20230208194637948](C:\Users\86191\Desktop\myblog\mang959595.github.io\source\_posts\第二章Linux多进程\image-20230208194637948.png)

> 来自评论区：
>
> 为什么两条命令产生了两个新的进程组，并且不同于bach进程组:
>
> 关于find和wc为什么父进程是bash(400)，但是进程组ID确是658，而不是复制父进程的组ID。其实fork函数产生的子进程才是复制父进程的组ID。那通过这种命令产生的子进程，组ID是怎么确立的？在《Linux/UNIX系统编程手册》2.13 中提到，“shell执行的每个程序都会在一个新进程内发起”，这句话解释了为什么find、wc、sort、uniq这四个都是一个单独的进程。“除了Bourne shell以外，几乎所有主流shell都提供了一种交互式特性，名为任务控制。该特性允许用户同时执行并操纵多条命令或管道。在支持任务控制的shell中，会将管道内所有进程置于一个新进程组或任务中。如果情况很简单，shell命令行只包含一条命令，那么就会创建一个只包含单个进程的新进程组。进程组中每个进程都具有相同的进程组标识符，其实就是进程组组长的ID”，这段话可以解释为什么两条命令产生了两个新的进程组，并且不同于bach进程组。这种shell命令创建子进程一定要和fork函数区分开来。





**相关函数**

```c
// 获取当前进程的进程组
pid_t getpgrp(void);
// 获取指定进程的进程组
pid_t getpgid(pid_t pid);
// 设置指定进程的组id
int setpgid(pid_t pid, pid_t pgid);
// 获取指定进程的会话id
pid_t getsid(pid_t pid);
// 设置当前进程的会话id
pid_t setsid(void);


```





### 守护进程

守护进程( Daemon Process )，也就是通常说的 Daemon 进程（精灵进程），是Linux中的后台服务进程。其生存周期较长，通常独立于控制终端并且周期性地执行某种任务或等待处理某些发生地事件。进程名一般以 d 结尾。



**特征**

- 生命周期长，守护进程会在系统启动的时候被创建并一直运行直至系统被关闭。
- 在后台运行并且不拥有控制终端。没有控制终端确保了内核永远不会为守护进程自动生成任何控制信号以及终端相关的信号（如 SIGINT、SIGQUIT）



> Linux的大多数服务器就是用守护进程实现的，比如，Internet服务器 inetd ，Web服务器 httpd 等



**创建步骤**

- *执行 fork()，然后父进程退出，子进程继续执行
  - 假设从命令行启动进程，父进程在退出后会使终端出现shell提示符；提前退出就不会出现了
  - 子进程能确保自己不是进程组的首进程
- *子进程调用 setsid() 开启一个新会话（
  - 用setsid新建会话的首进程不能是进程组的首进程，否则导致组id重复
  - 新建会话是为了控制终端，setsid() 新建的会话在终端连接建立之前默认无控制终端
- （非必须）清除进程的 umask 以确保当守护进程创建文件和目录时拥有所需的权限
- （非必须）修改进程的当前工作目录，通常改为根目录（ / ）
- 关闭守护进程从其父进程继承而来的所有打开着的文件描述符
  - 守护进程脱离了控制终端，但是没有脱离终端
  - 避免守护进程通过标准输出等到终端读写数据
  - 以及关闭其他继承过来的文件描述符，解除文件占用
- 在关闭了文件描述符0、1、2之后，守护进程通常会打开 /dev/null 并使用 dup2() 使所有这些描述符指向这个设备
  - 某些系统调用会使用到0、1、2文件描述符，所以不能关闭
  - 重定向后相当于丢弃掉数据
- *核心业务逻辑



**案例**

写一个守护进程，每个2s获取一次系统时间，并将时间写入到磁盘文件中

```c
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/time.h>
#include <signal.h>
#include <time.h>
#include <stdlib.h>
#include <string.h>

void work(int num) {
    // 捕捉到信号之后，获取系统时间并写入磁盘
    time_t ret = time(NULL);
    struct tm * loc = localtime(&ret);
    // 转换格式
    // char buf[1024];
    // sprintf(buf, "%d:%d:%d\n",loc->tm_hour,loc->tm_min,loc->tm_sec);
    // printf("%s\n",buf);

    char *str = asctime(loc);
    int fd = open("time.txt", O_RDWR | O_CREAT | O_APPEND, 0664);
    write(fd, str, strlen(str));
    write(fd, "\n", strlen("\n"));    
    close(fd);
}


int main() {
    
    // 创建子进程，退出父进程
    pid_t pid = fork();

    if(pid > 0) {
        exit(0);
    }

    // 子进程重新创建一个会话
    setsid();
    // 设置掩码
    umask(022);
    // 更改工作目录
    chdir("/root/user");
    // 关闭、重定向文件描述符
    int fd = open("/dev/null",O_RDWR);
    dup2(fd,STDIN_FILENO);
    dup2(fd,STDOUT_FILENO);
    dup2(fd,STDERR_FILENO);


    // 业务逻辑

    // 注册信号捕捉
    struct sigaction act;
    act.sa_flags = 0;
    act.sa_handler = work;
    sigemptyset(&act.sa_mask);
    sigaction(SIGALRM, &act, NULL);

    // 创建定时器
    struct itimerval val;
    val.it_value.tv_sec = 2;
    val.it_value.tv_usec = 0;
    val.it_interval.tv_sec = 2;
    val.it_interval.tv_usec = 0;

    setitimer(ITIMER_REAL, &val, NULL);

    while(1){
        sleep(10);
    }
    return 0;
}

```







第二章完结！
