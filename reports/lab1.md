# lab1
## 实验总结
为了实现获取当前任务信息，我修改了TaskControlBlokc属性，以此能够存储需要的信息，包括任务第一次被调用的时间和调用系统调用的次数，此外，我还修改并添加了TaskManager的方法，从而能够访问其私有变量，并对其进行特定的修改。
## 问答题
1. 本人使用的sbi版本未知，为内置的bootloader/rustsbi-qemu.bin
- ch2b_bad_address
    - 出错行为
    > [1]    32760 segmentation fault (core dumped)
    - 出错原因：访问非法内存地址 0x0
- ch2b_bad_instructions
    - 出错行为
    > [1]    41574 illegal hardware instruction (core dumped)
    - 出错原因：在U态使用S态才能使用的sret指令
- ch2b_bad_register
    - 出错行为
    > [1]    42443 illegal hardware instruction (core dumped)
    - 出错原因：在U态访问S态寄存器sstatus

2. (1) 刚进入__restore时，a0的值为内核栈的栈顶。_restore两种使用情景：
- 在trap_handler返回后从保存在内核栈上的trap上下文恢复寄存器
- 使寄存器达到启动应用程序所需要的上下文状态
(2) 特殊处理了sstatus、sepc、sscratch这些寄存器
- sstatus保存当前状态信息，确保可以恢复之前的状态
- sepc 记录 Trap 发生之前执行的最后一条指令的地址，包正程序能够在回到用户态后继续执行
- sscratch 存储临时数据，此处存储了指向用户栈栈顶的值，便于结束restore时恢复sp
(3)因为在trap时，同样跳过了x2和x4，自然不用恢复。其中x2为sp，此时指向内核栈，不应存储，而应存储内核栈栈顶，即scratch。x4则是因为应用程序不会使用，故也不存储。
(4)sp为用户栈栈顶，sscratch为内核栈栈顶
(5)L61 sret。因为sret 指令会读取 sepc的值，确定在用户态中恢复执行的位置。同时，它会根据 sstatus 寄存器的状态恢复用户态的特权级和中断使能等信息
(6)sp为内核栈栈顶，sscratch为用户栈栈顶
(7)ecall

## 荣誉准则
1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 以下各位 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：
- 无
2. 此外，我也参考了 以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：
- [rCore-Tutorial-Book-v3](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter3/6answer.html) 
参考了修改TaskControlBlock的做法

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。