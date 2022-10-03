# GDB调试过程

为了可以使用GDB调试，在编译的时候需要加入一些参数指令，如下所示：

```C++
gcc -g -Wall program.c -o program
```

`-g`的作用是在可执行文件中加入一些源代码信息，指明我当前执行的可执行文件中的机器指令对应着源代码的第几行代码

`-Wall`的作用显示所有的警告信息

## GDB常用的调试命令

- 启动/退出：`gdb 文件名`、`quit`

- 设置参数/显示参数：`set args var1 var2`、`show args`

- GDB使用帮助：`help`

- 查看当前文件代码：`list`

- 设置断点：`break 行号`、`break 函数名 `

  - 查看断点信息：`info break`
  - 打了断点之后启动GDB调试：`start（程序停在第一行）`、`run（程序停在第一个断点处）`
  - 向下单步调试：`step`，遇到函数体会进入函数体；`next`
  - 打印变量的值/类型：`print 变量名`；`ptype 变量名`

- 多线程调试：

  - 显示所有的线程：`info threads`，显示所有可以调试的线程ID，这个ID是GDB分配的，从1开始，和线程ID不是同一个东西

  - 切换线程：`thread ID`

  - 线程锁：`set scheduler-locking off|on`

    - on：表示锁定其他线程，只有当前线程可以执行
    - off：不锁定任何线程，当程序运行的时候如果有断点，那么所有的线程都会停止下来，如果在当前线程下启动continue，那么会启动所有的线程

  - non-stop模式：某个线程中断在断点处，其他的线程继续运行

    ```C++
    set target-async 1
    set pagination off
    set non-stop on
    ```

    

