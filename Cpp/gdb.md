# gdb

[Debugging with GDB](https://sourceware.org/gdb/current/onlinedocs/gdb/)

## 1. 信息查看

> **`info`**: 用于描述程序的状态. 例如获取函数参数, 断点, 寄存器等信息.
>
> **`show`**: 描述GDB本身的状态 , 

### 1.符号

- 查看函数名称: `info functions`
- 查看全局和静态变量: `info variables`
- 查看当前frame的局部变量: `info locals`
- 查看当前frame的参数: `info args`

### 2.线程和栈

- 查看所有线程: `info thread`
- 查看线程栈: `bt`

### 3.打印变量和表达式

> `print`: 简写为p, 打印表达式的值, 表达式可以是任何当前被测程序frame的有效表达式.

- `print <f> <expr>`:
    - `x`: 十六进制, `p /x i`
    - `d`: 十进制, `p /d i`
    - `t`: 二进制
    - `c`: 字符
- `print *array@len`: 查看连续空间的值.

- `whatis 变量|函数`: 查看类型.
- `display 表达式`: 单步运行后, 紧接着输出表达式的值. 

### 4.其他信息

- `info sharedlibrary`: 查看共享库.

- `info signals`: 查看信号处理.

- ```
    (gdb) i signals 
    Signal        Stop      Print   Pass to program Description
    (信号)        (gbd)      (gdb)  (发送给程序)       (描述)
    SIGHUP        Yes       Yes     Yes             Hangup
    SIGINT        Yes       Yes     No              Interrupt
    SIGQUIT       Yes       Yes     Yes             Quit
    ......
    SIGALRM       No        No      Yes             Alarm clock
    ```

- 

## 2.调试控制

### 1.启动

- `gdb <program>`
- `gdb -p pid`
- `gdb attach pid`
- `gdb <program> core.xxx`: 调试core文件.

### 2.断点

#### 1.断点

- `break <function>`: 对函数名添加断点
- `break [file:]linenum`: 在文件某行打断点.

#### 2.事件断点

- `catch syscall xxx`: 监听系统调用.
- `catch exec/fork/vfork`: 监听`exec/fork/vfork`
- `catch throw/catch`: 监听异常的抛出/捕获
- `catch load/unload`: 监听共享库的载入/卸载

#### 3.断点删除, 启用, 禁用

- `delete [断点编号][-断点编号]`: 删除断点

- `disable [断点编号]`
- `enable [断点编号]`

### 3.运行

- `run | r`: 运行, 遇到断点停止.
- `continue | c`: 继续执行.
- `next | n`: 单步跟踪(不进入函数).
- `step | s`: 单步(进入函数).

### 4.信号处理

- `handle [signal] stop/nostop`: 收到`signal`时, gdb是否暂停程序执行.
    - `handle SIGPIPE stop`
- `handle [signal] print/noprint`: 是否打印信号信息.
- `handle [signal] pass/nopass`: 是否将信号发给程序.
- `signal signal_name`: 发送信号给程序.
    - `signal SIGPIPE `

## 3.多线程

- `info thread`: 查询所有线程信息.
- `thread thread-id`: 切换到指定线程.
- `thread apply thread-id|all cmd`: 同时操作多个线程.
- `set scheduler-locking off|on|step`:
    - `off` 不锁定任何线程，也就是所有线程都执行，这是默认值
    - `on` 只有当前被调试程序会执行
    - `step` 在单步的时候，除了next过一个函数的情况(熟悉情况的人可能知道，这其实是一个设置断点然后continue的行为)以外，只有当前线程会执行

- `backtrace | bt`: 调用栈
- `frame n`: 选择帧.
    - `up x`: 在栈中向上移动n个帧.
    - `down x`: 在栈中向下移动n个帧.
    - 可以结合`info args, info locals`查看变量信息.
- 