# Linux 调试死锁

```c++
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
 
int g_tickets = 100;
pthread_mutex_t g_mutex = PTHREAD_MUTEX_INITIALIZER;
 
void* thread_proc1(void* arg)
{
        while (1)
        {
                pthread_mutex_lock(&g_mutex);
                if (g_tickets > 0)
                        printf("thread 1 sell tickets:%d\n", g_tickets--);
                else
                {
                        //pthread_mutex_unlock(&g_mutex);
                        pthread_mutex_lock(&g_mutex);
                        break;
                }
                //pthread_mutex_unlock(&g_mutex);
                pthread_mutex_lock(&g_mutex);
        }
 
        return (void*)1;
}
 
void* thread_proc2(void* arg)
{
        while (1)
        {
                pthread_mutex_lock(&g_mutex);
                if (g_tickets > 0)
                        printf("thread 2 sell tickets:%d\n", g_tickets--);
                else
                {
                        pthread_mutex_unlock(&g_mutex);
                        break;
                }
                pthread_mutex_unlock(&g_mutex);
        }
 
        pthread_exit((void*)2);
}
 
int main(int argc, char*argv[])
{
        pthread_t tid1, tid2;
        void *ret1, *ret2;
 
        pthread_create(&tid1, NULL, thread_proc1, NULL);
        pthread_create(&tid2, NULL, thread_proc2, NULL);
 
        pthread_join(tid1, &ret1);
        pthread_join(tid2, &ret2);
 
        printf("ret1:%d\n",(int)ret1);
        printf("ret2:%d\n",(int)ret2);
 
        return 0;
}
```



**编译运行 lock.c**

```bash
[root@localhost ~]# **gcc -g lock.c -pthread**
[root@localhost ~]# ./a.out
timeout we will start dead lock

(程序挂起)
```

**查找进程id**

```bash
[root@localhost ~]# **ps -e | grep a.out**

12826 pts/3   00:00:00 a.out //进程id为12826
```

**启动 gdb attach 进程**

```c++
[root@localhost ~]# **gdb a.out 12826**
GNU gdb (GDB) CentOS (7.0.1-45.el5.centos)
Copyright (C) 2009 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "i386-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /root/a.out...done.
Attaching to program: /root/a.out, process 12826
Reading symbols from /lib/libpthread.so.0...(no debugging symbols found)...done.
[Thread debugging using libthread_db enabled]
[New Thread 0xb7524b90 (LWP 12828)]
[New Thread 0xb7f25b90 (LWP 12827)]
Loaded symbols for /lib/libpthread.so.0
Reading symbols from /lib/libc.so.6...(no debugging symbols found)...done.
Loaded symbols for /lib/libc.so.6
Reading symbols from /lib/ld-linux.so.2...(no debugging symbols found)...done.
Loaded symbols for /lib/ld-linux.so.2
0x00502402 in __kernel_vsyscall ()
(gdb) **info threads //显示所有线程信息**
 3 Thread 0xb7f25b90 (LWP 12827)  0x00502402 in __kernel_vsyscall ()
 2 Thread 0xb7524b90 (LWP 12828)  0x00502402 in __kernel_vsyscall ()
\* 1 Thread 0xb7f266c0 (LWP 12826)  0x00502402 in __kernel_vsyscall ()
(gdb) **thread 2  //跳到第2个线程**
[Switching to thread 2 (Thread 0xb7524b90 (LWP 12828))]#0  0x00502402 in __kernel_vsyscall ()
(gdb) **bt  //查看线程2的堆栈，可以发现该线程堵塞在lock.c第17行**
\#0  0x00502402 in __kernel_vsyscall ()
\#1  0x0072e839 in __lll_lock_wait () from /lib/libpthread.so.0
\#2  0x00729e9f in _L_lock_885 () from /lib/libpthread.so.0
\#3  0x00729d66 in pthread_mutex_lock () from /lib/libpthread.so.0
\#4  0x080485b4 in work_thread (arg=0x0) at lock.c:17
\#5  0x00727912 in start_thread () from /lib/libpthread.so.0
\#6  0x0066660e in clone () from /lib/libc.so.6
(gdb) 
```