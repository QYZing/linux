# **linux**

**所见都是文件**

[TOC]

## 根目录：

- ​	bin:二进制可执行文件

- ​	boot: 开机启动程序

- ​	dev:存放设备文件

- ​	home:存放用户

- ​	etc:用户信息和系统配置文件

- ​	lib:库文件

- ​	root:管理员宿主目录（家目录）

- ​	usr:用户资源管理目录


## linux系统文件类型：

- ​	普通文件：-

- ​	目录文件：d

- ​	字符设备文件：c

- ​	块设备文件：b（block）

- ​	软连接：l

- ​	管道文件：p

- ​	套接字：s

- ​	未知文件

## linux 命令:

##### **软连接  硬链接：**

```shell
	软连接  ln -s 文件路径（绝对路径） 创建的文件名  （快捷方式）

​	硬链接 ln  文件名 创建的文件名 （.hard） 类似引用 （共同id）
```

##### **find  目录 (找文件) : **

```shell
	-type 按文件类型搜索  d/p/s/c/b/l f:文件

​		-name 按文件名搜索

​					find ./ -name '*file.jpg'

​		-maxdepth  指定深度

​				find ./ -maxdepth 1 -name '\*file\*.jpg'

​		-size 按文件大小  k ,M ,G 

​				find /home size  +20M -size -50M

​		-atime mtime dtime 

​		-exec 将find 搜索结果执行一个命令		
			$ find /usr/ -name "*tmp*" -exec ls -l{}\;
​		-xargs  将find 搜索结果执行一个命 结果大可以分片映射
			find /usr/local/DyingDemo1/ -type f |xargs ls -l
				有空格时 -print0
		   find /usr// -type f  -print0 |xargs -print0 ls -l
```

##### **grep （找文件内容）:**

```shell
	grep -r（递归） 'copy' ./ n(显示行号)
​	ps -a(all) u(user) x(没有控制终端的) |grep 进程内容名 （会显示grep搜索进程）
```

##### **创建用户：**

```shell
	sudo adduser 用户名
```

##### **修改文件所属用户** 

```shell
	sudo chown 新用户名 待修改文件
```

##### **删除用户：**

```shell
	sudo deluser 用户名
```

##### **创建用户组:**

```shell
	sudo addgroup 新组名
```

##### **压缩： **

```shell
tar -z(gzip)c(create)v(显示)f（file） 要生成的压缩文件 材料
	tar zcvf text.tar.gz file1 file2
```

```shell
zip -r 压缩包名（带.zip后缀） 材料
	zip -r text.zip file1 file2
```

##### **解压：** 

```shell
tar -z(gzip)xv(显示)f  解压文件名
	tar zxvf text.tar.gz 
```

```shell
unzip  压缩包名（带.zip后缀） 
	unzip  text.zip 
```

# C（GCC）

## 编译4步骤

​		预处理  编译  汇编  链接

​		**gcc -E 预处理 -> gcc -S  编译-> gcc -c 汇编-> 无参数  链接 -> 可执行文件 **

1. ​		预处理： 展开宏头文件 替换条件编译 删除注释，空行，空白
2. ​		编译： 检查语法规范（时间比较长）
3. ​		汇编： 将汇编指令转换成二进制指令
4. ​		链接：数据段合并地址回填

```c
gcc 编译
	-I 指定头文件所在目录位置
	-c 只做预处理 编译 汇编 得到二进制文件
	-g 编译时添加调试语句 之后可以被gdb调试
	-Wall 显示所有警告信息、
	-D 动态注册一个宏（常用于调试语句输出）
```

## 静态库：

```shell
#先把文件生成.o 文件  
gcc -c file -o file.o
#然后用ar rcs  libXX.a  .o文件名  生成libXX.a 静态库
ar rcs libfile.a file.o
#链接时源码在前 库在后  使用静态库要包含声明头文件
gcc text.c libasd.a -o a.out -Wall
#库头文件
	#ifndef _IFNDEF_
	#define _IFDNEF_
	....
	#endif
```

## 动态库：

```shell
#将 .c 文件生成 .o文件 （生成于位置无关代码-fPIC)
	gcc -c file.c -o(起名用的) file.o -fPIC
#使用 gcc -shared 制作动态库
	gcc -shared lib库名.so （二进制源文件）
#使用动态库 编译可执行程序时 指定使用动态库 -l 库名 -L 路径
	gcc text.c -o a.out -l 库名 -L./lib
#运行 a.out 会出错！！！
	#原因：   链接器：工作于链接阶段，工作时需要-l -L
    #动态链接器：工作与程序运行阶段，工作时需要提供动态库所在目录位置
    #【1】通过环境变量改变export LD_LIBRARY_PATH=./（路径（临时）
    ./a.out #建议使用绝对路径
    #【2】可通过修改bash环境变量 把上述写入  vi ~/ .bashrc 
    # . .bashrc/  source .bashrc / 重启
    #ldd 程序名  可以查看动态库链接情况
    #【3】拷贝到 /lib64  （标准库里) 不推荐
    #【4】配置文件
    	#（1) sudo vi /etc/ld.so.conf
    	# (2) 写入动态库绝对路径
    	# (3) sudo ldconfig -v 刷新配置文件
    	# (4) ./a.out 成功  ldd 查看
```

## GDB:

```shell
#调试时加 -g 生成调试文件  逻辑错误
#gdb a.out
#list : l  n  显示第n行以后
#break/b （if i=5) :设置断点 (if条件)
#run/r :运行程序  可以之间跳到段错误位置
#n/next :下一行  跳过函数内
#s/step :下一条指令（进入函数）
#p/print: 打印目标的值
#continue: 继续执行断点后的数据
#quit：退出gdb
#finish: 跳出当前函数
#set args:设置main函数的命令参数（在start和run前设置）
#run 参数 参数 同上功能
#info b: 查看断点信息表
#ptype: 查看变量类型
#bt: 列出当前程序正在存活栈帧
#frame: 根据栈帧编号切换栈帧
#display: 设置跟踪变量
#undisplay: 取消跟踪变量 （编号）
```



## makefile:（命名makefile or Makefile)

##### **1 个规则：**

目标：依赖条件 

​				（一个tap缩进）命令

​	1 目标时间必须晚于依赖时间 否则更新目录

​	2 依赖条件如果不存在  去寻找新的规则创造依赖条件

​	ALL: 指定makefile最终目标文件  （懒汉模式）

##### **2 个函数：**

​	src = $(wildcard *.c) 匹配当前工作目录下所有.c 文件 将文件名组成列表 赋值给 src。src= *.c

​	obj = $(patsubst %.c  %.o , $(src)) 将参数3中包含参数1中部分 替换成参数2

​	clean: (没有依赖) make clean -n(务必检查一下删除对没对) 

​		-rm -rf $(obj)  a.out   前面的-是不管有没有文件都删不报错

##### **3 个自动变量：**

​	**$@  :**在规则命令中，表示规则中的目标。

​	**$^ ：**在规则命令中，表示所有依赖条件。

​	**$< ：**在规则命令中，表示第一个依赖条件。模式规则可依次执行

#####     4. **模式规则 ：**

​		**%.o:%.c**

​				gcc -c $< -o $@

​		**静态模式规则：**

​				**$(obj):%.p:%.c**

​						gcc -c $< -o $@

​		伪目标： **.PHONY**

 

## 系统函数（系统调用）



##### open

​	

```c
int open(const char * pathname ,int flags);
int open(const char * pathname ,int flags,mode_t mode);
int close(int fd);

//常用参数  O_RDONLY O_WRONLY O_RDWR O_APPEND O_CREAT O_EXCL O_TRUNC O_NONBLOC
//创建文件时需要指定文件权限，权限同时受umask影响结论为  umask取反小的优先 最大umask取反
#include<fcntl.h>
open 常见错误 -1
	1 打开文件不存在
	2 以写方式打开文件（没有对应权限）
	3 以只写方式打开文件
```

##### close：

```c
int close(int fd); 创建文件描述符之后用来关闭
```

##### read:

```c
ssize_t read(int fd,void *buf,size_t count);
//参数：fd文件描述符  buf：存储数据缓冲区  count：缓冲区大小
//返回值 0读到文件结尾  成功读到的字节数 失败 -1设置error
//如果返回-1 且error= EAGIN 或EWOULDBLOCK 说明 在以非阻塞情况读一个设备（网络）空文件
```

##### write:

```c
ssize_t write(int fd,const void *buf,size_t count);
//参数：fd文件描述符  buf：存储数据缓冲区  count：写出内容的大小
//返回值 尾  成功写到的字节数 失败 -1设置error
```

##### 错误处理函数：

```c
printf("xxx error:%d\n",errno); //#include<errno>
char *strerror(int errnum);
	printf("xxx error : %s\n",strerror(errno));
perror("xxx error");  //#include<string>
```



##### fcntl:更改查看文件状态

```c
int fcntl(int fd,int cmd, ...)
int flags = fcntl(fd,F_GETFL);
flags |= O_NONBLOCK
fcntl(fd,F_SETFL);
获取文件状态： F_GETFL
设置文件状态： F_SETFL
```



##### lseek:文件操作

```c
off_t lseek(int fd,off_t offset , int whence);
参数：  fd 文件描述符  offset 偏移量  whence 起始偏移量： SEEK_SET/SEEK_CUR/SEEK_END
返回值： 成功较启示位置偏移量  失败 -1 error
应用场景：  文件读写 在同一偏移位置
		  使用lseek获取文件大小
		  使用lseek拓展文件大小，要想使文件大小真正扩展必须引起I/O 操作 使用truncate 直接扩展文件
```



## 文件描述符:



pcb进程控制块，本质：结构体

成员 ： 文件描述符表

文件描述符：0...1023

0   -  STDIN_FILENO

1 - STDOUT_FILENO

2 - STDERR_FLIENO

## 阻塞/非阻塞：（设备/网络 文件的属性）

产生阻塞的场景，读设备文件，读网络文件（常规文件无阻塞属性）

/dev/tty --（终端文件）

open("/dev/tty",O_RDWR|O_NONBLOCK)  --设置/dev/tty 非阻塞状态  （默认阻塞）

//重新打开时改变文件属性

------



## 传入传出参数：



##### 传入参数：

1. ​				使用指针作为函数参数
2. ​				同常有const关键字修饰
3. ​				指针指向有效区域，在函数内部做读操作

##### 传出参数：

1. ​				指针作为函数参数
2. ​                在函数调用之前，指针指向的空间可以无意义，但必须有效
3. ​                 在函数内部，做写操作
4. ​                 函数调用结束后，充当函数返回值。           

##### 传入传出参数

1. ​				指针作为函数参数
2. ​                在函数调用之前，指针指向的空间有实际意义
3. ​                在函数内部，先做读操作，后做写操作
4. ​                在函数调用结束后，充当函数返回值                

## 文件/目录（操作）

##### stat/lstat:

```c
int stat(const char *path,struct stat *buf);
参数： 文件路径 传出参数
返回值 成功 0 失败 -1 errno
获取文件大小：buf.st_size
获取文件类型：buf_st_mode
获取文件权限：buf_st_mode
符号穿透 ：stat 会/stat 不会  （对于软（硬）链接 是否会访问本身还是链接所对应的事物）
```

##### 目录操作函数

```c
DIR *opendir(char name)
int closerdir(DIR *dp);
struct dirent *readdir(DIR *dp);
struct dirent{
	inode
	char dname[256];
}
```



##### dup/dup2:

```c
int dup(int oldfd);
oldfd:已有文件描述符
返回新的描述符
int dup(int oldfd,int newfd);
newfd合法就返回不合法返回合法的描述符

fcntl实现dup
int fcntl(int fd,int cmd,...);
cmd:F_DUPFD
参3： 被占用的，返回最小可用（同上）
```

##  进程：

程序：死的，只占用磁盘空间  

进程： 活的，运行起来的程序占用内存、cpu等系统资源

##### pcb进程控制块：（本质结构体）

​	进程id

​	文件描述符

​	进程状态： 初始态，就绪态，运行态，挂起态，终止态

​	进程工作目录位置

​	*umask掩码

​	信号相关信息资源

​	用户id和组id

##### fork

```c
	pid_t fork(void);
	创建子进程，父子进程各自返回，父进程返回子进程pid，子进程返回0
	getpid（） getpid（）
	循环创建N个子进程，每个子进程都有自己标识身份
```

##### 父子进程相同点：

刚fork后 ，data段，text段 堆，栈，环境变量，全局变量，宿主目录位置，进程工作目录位置，信号处理方式

##### 父子进程不同点：

进程id，返回值，各自的父进程，进程创建时间，闹钟，未决信号集

##### 父子进程共享：

​	读时共享，写时复制，--------------全局变量

​	1 文件描述符，2mmap映射区

##### gdb调式：

​	设置父进程调试路径 ：set follow-fork-mode parent

​	设置子进程调试路径： set follow-fork-mode child

##### exec函数族

使进程执行某一程序，成功无返回值，失败返回-1

```c
int execlp(const char *file,const char *arg,...);//借助PATH 环境变量寻找可执行程序
//参1：程序名 参2 argv[0] 参3 argv[1]...argv[n]  哨兵 NULL(结束符)
int execl(const char *path,const char *arg,...);//自己指定执行程序路径
```



##### 孤儿进程：

父进程先于子进程终止，子进程沦为孤儿进程，会被init进程收养

##### 僵尸进程：

子进程终止，父进程尚未对子进程进行回收，在此期间，子进程沦为僵尸进程 kill无效

##### wait函数  （回收子进程退出资源）

```c
pid_t wait(int *status);
//参数 （传出）回收状态
//返回值：成功 回收的pid 失败 -1 errno
```

函数作用1：	阻塞等待子进程退出

函数作用2：	清理子进程残留在内核的pcb资源

函数作用3：	通过传出参数，得到子进程结束状态

获取子进程正常终止值：

```c
WIFEXITED(status) --> 为真 -->调用 WEXITSTATUS(status) 得到子进程退出值
```

 获取导致子进程异常终止信号：

```c
WIFSIGNALED(status) --> 为真 -->调用 WTERMSIG(satus) 得到导致子进程异常终止信号编号
```

##### waitpid函数：指定一个进程回收，可以设置非阻塞

```c
pid_t waitpid(pid_t,int *status,int options);
waitpid(-1,&status,0) ==wait(&status);
//参数： pid_t 指定回收子进程pid >0 待回收id -1 任意进程 0 同组进程
//		status 传出 回收进程状态
//		options： WNOHANG 指定回收方式为：非阻塞

//返回值： >0 表示成功回收子进程id
//		  0：函数调用时，参3指定了WNOHANG 并且没有子进程结束
//		  -1 失败 errno
```

​										**wait,waitpid 一次调用，回收一个子进程，回收多个用循环**

## 进程间通信方式：

##### 常用通信方式：

​		管道：简单

​		信号： 开销小

​		mmap映射：非血缘关系进程间

​		socket（本地套接字）：稳定

##### 管道：

​	实现原理：内部借助环形队列机制，使用内核缓冲区实现

​	特质： 1 伪文件

​				 2 管道中的数据只能读取一次

​				 3 数据在管道中，只能单向流动

​	局限性: 1 自己写，不能自己读

​				  2 数据不可以反复读取

​				  3 半双工通信

​				  4 血缘关系进程间可用

##### pipe函数： （创建并打开管道）

用于有血缘关系的进程同通信（父子，兄弟）

```c
int pipe(int fd[2]);用于初始化 传出参数
参数： fd[0] 读端
	  fd[1] 写端
返回值： 成功 0
		失败 -1 errno
```

管道的读写行为

​	读管道：

​					1： 管道有数据，read返回实际读取到的字节数

​					2： 管道无数据：1） 无写端，read返回0（类似读到文件尾）

​												  2）有写端，read 阻塞等待

​	写管道：

​					1：无读端，异常终止（SIGPIPE导致的）

​					2：有读端：         1）管道已满，阻塞等待

​												   2）管道未满，返回写出字节数

##### fifo管道：可以用于无血缘关系进程通信

​	命名管道：mkfifo

​	无血缘关系进程通信

​			读端： open fifo O_RDONLY

​			写端:	open fifo O_WRONLY

##### 文件实现进程间通信：

​		打开文件是内核中的一块缓冲区，多个无血缘关系的进程可以同时访问该文件

##### mmap函数

```c
void *mmap(void *addr,size_t length ,int prot,int flags, int fd,off_t offset)
参数:
	addr：指定映射区的首地址，通常是NULL 表示让系统自动分配
	length：共享内存的映射区的大小（<=文件实际大小）
	prot：共享内存的读写属性 PROT_READ、PROT_WRITE、PROT_READ|PROT_WRITE
	flasgs：共享内存属性 MAP_SHARED MAP_PRIVATE
	fd:用于创建共享内存内存映射区的那个文件，文件描述符
	offset:默认0 表示文件全部 否则4k整数倍
返回值：
	成功：映射区的首地址
	失败：MAP_FAILED,errno
```

搭配munmap释放映射区

```c
int munmap(void *addr,size_t length);
addr:mmap返回值
length：大小
```

###### 使用注意事项

```
1 用于创建映射区的文件大小为0，实际指定非0大小创建映射区，出“总线错误”
2 用于创建映射区的文件大小为0，实际指定大小0创建映射区 出“无效参数”
3 用于创建映射区的文件属性为 只读 映射区属性 为 读写 出 “无效参数"
4 创建映射区，需要read权限，当访问权限指定为共享MAP_SHARED mmap的读写权限 应该小于等于文件open权限
5 文件描述符fd，在mmap创建映射区完成即可关闭，后续访问文件用地址访问
6 offset 必须是4k整数倍（mmu映射最小单元4k）
7 对申请的映射区内存不能越界访问
8 munmap用于释放地址必须是mmap返回的地址
9 映射区的访问权限为私有MAP_PRIVATE对内存做的所有修改只在内存有效磁盘无效
10映射区访问权限为私有MAP_PRIVATE 只需要open文件 读权限 用于创建映射区即可 
```

mmap保险调用方式：

```c
1 fd=open("文件名"，O_RDWR)
2 mmap(NULL,有效文件大小，PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
```

##### 父子进程mmap通信

​	父进程先创建映射区 open(O_RDWR)   mmap(MAP_SHARED)

​	指定MAP_SHARED权限

​	fork（）创建子进程

​	一个进程读 一个进程写

##### 无血缘关系进程间mmap通信

​	俩个进程 打开同一个文件 创建映射区

​	指定flags 为 MAP_SHARED

​	一个进程写入 ，另外一个进程读出

​	【注意】：无血缘关系进程通信 mmap 数据可以重复读取

​						fifo：数据只能读一次

##### 匿名映射：只能用于有血缘关系进程通信

```c
void * p = mmap(NULL,10,PROT_READ|PROT_WRITE,MAP_SHARED|MAP_ANONYMOUS,-1,0);
```

## 信号：

##### 信号共性

​		简单，不能携带大量信息，满足条件才能发送

##### 信号的特质

```
产生信号：
	1 按键产生
	2 系统调用产生
	3 软件条件产生
	4 硬件异常产生
	5 命令产生
概念：
	未决：产生与递达之间的状态
	递达：产生并且送达到进程，直接被内核处理掉
	信号处理方式：执行默认处理动作，忽略，捕捉（自定义）
	阻塞信号集（信号屏蔽字）：本质--位图，用来记录信号的屏蔽状态，一旦被屏蔽信号，在解除屏蔽前一直处于未决
	未决信号集：本质--位图，用来记录信号的处理状态，该信号集中的信号表示产生未处理
```

##### 信号4要素：

​	信号使用之前，应确定其4要素，而后使用

​		编号、名称、对应事件、默认处理动作

##### kill 命令和kill 函数

```c
int kill(pid_t pid , int signum);
参数：
	pid：>0 发送信号给指定进程
		=0 发送数据给跟调用kill函数的那个进程处于同一进程组的进程
		<-1取绝对值，发送信号给该绝对值所对应的进程组的所有成员
		=-1发送信号给有权限发送的所有进程
	signum：待发送信号
返回值
	成功 0
	else -1 errno
```

##### alarm函数，使用自然记时法

```c
定时发送SIGALARM给当时进程
unsigned int alarm(unsigned int seconds);
	seconds:定时秒数
	返回值： 上次剩余的时间
			无错误现象
	alarm（0） 取消计时
time命令：查看程序执行时间 实际时间 = 用户时间 + 内核时间 + 等待时间 --》优化瓶颈 I/O 【time /a.out】
```

##### setitimer函数：

```c
int setitimer(int which,const struct itimrval*new_value , itimerval *old_value);
自己百度太多了
```

##### 其他几个发信号函数

```c
int raise(int sig);
void abort(void);
```

##### 信号集操作函数

```c
sigset_set;自定义信号集
sigemptyset(sigset_t *set); 清空信号集
sigfillset(sigset_t *set);全布置
sigaddset(sigset *set ,int signum); 增加到信号集
sigdelset(sigset *set,int signum);移除信号从信号集中
sigismember(const sigset *set ,int signum);判断信号是否处于信号集中 在1 else 0
```

##### 设置信号屏蔽字解除信号屏蔽字

```c
int sigprocmask(int how,const sigset_t *set ,sigset_t *oldset);
	how: SIG_BLOCK 设置阻塞
		 SIG_UNBLOCK 取消阻塞
		 SIG_SETMASK 自定义set替换mask
	set:自定义set
	oldset：保留旧的set
```

##### 查看未决信号集

```
int sigpending(sigset_t *set);
	set 传出的 未决信号集
```

##### 信号捕捉函数（DEMO：day07 sigaction）

###### signal

```c
替换信号处理函数
signal(int signum,void XXX(int signum));
```

###### sigaction！！！

```c
   	struct sigaction act, oldact;
	act.sa_handler = sig_cath; //设置回调函数
    sigempgtyset(&act.sa_mask);//初始化清空 只在sig_catch 有效
    act.sa_flags = 0;//设置默认状态

    int ret = sigaction(SIGINT, &act, &oldact);//注册信号捕捉
```

特性：

```
	1 捕捉函数执行期间，信号屏蔽字 由马mask--》sa——mask 捕捉函数执行结束恢复mask (防止执行一半右返回去执行)
	2 捕捉函数执行期间，本信号自动屏蔽（sa_flags=0)
	3 捕捉函数执行期间，被屏蔽信号多次发送，解除屏蔽后只处理一次
```

可借助信号回收进程

## 守护进程：

##### 会话：

脱离控制台终端不与用户直接交互

```c
 pid=setsid(); //create the new session
 子进程使用，父进程不能弄
```

##### 守护进程：

​	daemon进程，通常运行于操作系统后台，脱离控制终端，不与用户直接交互，周期性的等待某个事件发生或者周期性执行某一个动作，不受用户登录注销影响，通常以d结尾命名

守护进程创建步骤

```c
	1 fork子进程，终止父进程
	2 调用setsid（）创建新会话
	3 通常根据需要设置工作目录位置chdir（）
	4 通常根据需要设置umask （影响新文件创建权限）
	5 通常根据需要设置 关闭/重定向 文件描述符
	6 守护进程 业务逻辑 while（）
```

## 线程：

##### 概念：

```
进程：有独立的进程地址空间 独立pcb 分配资源最小单元
线程：有独立的pcb，没有独立的进程地址空间
ps-lf进程id--》线程号 lwp--》cpu执行最小单位
```

##### 线程共享和线程独享

```
	独享栈空间（内核栈，用户栈）
	共享 ./text/data ./rodataa ./bss heap  ->(全局变量)       (errno)不共享
```

##### 线程创建（day01）

```c
pthread_self()//获取当前线程id
pthread_create(&pthread,NULL,thread_proc,&pthread);//创建
//参数1 传出参数 ，表示创建子线程id
//参数2 线程属性 NULL表示默认属性
//参数3 子线程回调函数，创建成功，pthread_create函数返回时该函数自动调用
//参数4 参数3的参数
//返回值 成功 0  失败 errno
pthread_join(pthread,(void **)&m);//添加阻塞 等待加入线程执行完运行
封装lei（day01 newthread）
```

##### pthread_exit(void *retval) 退出当前线程

```c
int pthread_exit(void * retval);
retval 退出值，无退出值NULL
exit（）退出当前线程
return 返回到调用者
```

##### pthread_join

```c
int pthread_join(pthread_t thread,void **retval);
thread：待回收的线程id
retval：传出参数，回收那个线程返回值
返回值 成功0 失败 errno
```

##### pthread_detach

```c
int pthread_detach(pthread_t thread);
thread ：待分离线程id
返回值 ：成功0 失败errno
```

##### pthread_cancel

```c
int pthread_cancel(pthread_t thread);
thread:带杀死线程id
返回值 ：成功0 失败errno
如果子线程没有到达取消点，那么该函数无效 （取消点指的是进入内核）
pthread_testcancel();手动取消点
成功被杀死的用pthread_join() 回收
```

##### 线程 and  进程 控制原语

```c
线程控制原语										进程控制原语
pthread_create()								fork()
pthread_self()									getpid()
pthread_exit()									exit()
pthread_join()									wait()/waitpid()
pthread_cancel()								kill()
pthread_detach()
```

##### 线程属性

设置分离属性

```c
pthread_attr_t attr ; //创建一个线程属性结构体变量
pthread_attr_init(&attr);// 初始化线程属性
pthread_attr_setdetachstate(&attr.PTHREAD_DETACHED);//设置线程属性  分离太
pthread_create(&tid,&attr,fun,NULL)// 借助修改后的设置线程属性 创建为分离太的新线程
pthread_attr_destroy(&attr) //销毁线程属性
```












