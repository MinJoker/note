# RISC-V&thinsp;处理器

## 单周期

单周期处理器是 RISC-V 处理器的一种简单实现。所谓单周期，指所有指令都在单个时钟周期内完成，即 CPI = 1。

处理器可以分为控制单元和数据通路（下图通过黑色和蓝色作区分），该图表示的处理器支持 `add` `sub` `and` `or` `ld` `sd` `beq` 等指令，不支持 `jal` 指令。

![](/assets/images/cs/organization/processor.png)

### 例：常用指令数据通路

=== "R 型指令"

    ![](/assets/images/cs/organization/processor_r.png)

=== "load 系指令"

    ![](/assets/images/cs/organization/processor_load.png)

=== "`beq` 指令"

    ![](/assets/images/cs/organization/processor_beq.png)

### 控制逻辑

上文图片表示的处理器的控制单元逻辑如下：

=== "Control"

    ![](/assets/images/cs/organization/control.png)

=== "ALU control"

    ![](/assets/images/cs/organization/control_alu.png)


## 流水线