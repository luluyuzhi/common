# Linux 调试 Dump 文件



没有太多能说的基本上都是操作，之前使用 Windws 来调试 Dump 文件有 Vs 这个全球第一的 IDE 加成, 整个操作还是比较简单的。



​	

1. **查看core文件存储位置**

```shell
   cat /proc/sys/kernel/core_pattern |/usr/share/apport/apport %p %s %c %P
```

   

2. 修改 core 文件大小

```shell
ulimit -c
//详细信息
ulimit -a

如果结果是0，我们需要修改其大小

//当前有效的修改
ulimit -c [size]  //这里size一般修改为unlimited,或者是其他数字：2048
```

例如：

```shell
muduo@DESKTOP-L2ICAQT:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 15710
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 15710
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```



```c++
#include <iostream>
#include <stdio.h>
#include <stdlib.h>

void DumpCrash()
{
    char *pStr = "test_conntent";
    free(pStr);
}

int main()
{
    DumpCrash();
    return 0;
}
```





