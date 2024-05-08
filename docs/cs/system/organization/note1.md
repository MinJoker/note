# RISC-V&thinsp;指令

## 指令集

RISC-V 指令集包括基础指令集和扩展指令集。基础指令集具有 32-bit 固定长度，扩展指令集支持 16-bit 任意整数倍的可变长度。

RISC-V 基础指令集具有 R、I、S、SB、U、UJ 六种结构，如图自上而下。

![](/assets/images/cs/organization/riscv_isa_structure.png)

RISC-V 基础指令集中有些细节需要注意：

- 对于 I 型指令，立即数移位 `slli` `srli` `srai` 会把指令 [31:26] 用作额外的操作码，因为移位立即数不会超过 64
- 对于 SB 和 UJ 型指令，执行 PC 相对寻址，立即数低位自动补 `0`，相对寻址步长为 16-bit，以适应指令集的最小长度
- 对于 U 型指令，`lui` `auipc` 把立即数置入寄存器的 [31:12] 位，低位自动补 `0`，通常配合 `addi` 补上低位，从而构造大立即数

### 指令助记卡

=== "计组课堂"

    ![](/assets/images/cs/organization/riscvcard_lecture.png)

    > source: [咸鱼暄的代码空间](https://github.com/xuan-insr/xuan-insr.github.io/blob/main/docs/assets/1654864713202-23520b16-be27-484e-8f08-39aa863679ba.png)

=== "教材附录"

    <embed src="/assets/images/cs/organization/riscvcard.pdf" style="width:100%; height:80vh;" type="application/pdf">

## 寄存器组

RISC-V 架构提供 32 个整数寄存器，每个寄存器是 64-bit 的。当实现浮点扩展时，还提供 32 个浮点寄存器，计组课程不涉及。

寄存器访问速度快于内存，编译器优先调用寄存器空间。当寄存器空间不足时，通过把部分寄存器数据压入内存栈来释放寄存器空间。

![](/assets/images/cs/organization/register.png)

> source: [咸鱼暄的代码空间](https://github.com/xuan-insr/xuan-insr.github.io/blob/main/docs/assets/1654054605190-66992a62-3995-4285-8002-c28a0a8e9073.png)

## 内存与寻址

RISC-V 架构下内存地址是 64-bit、小端序、非对齐的。注意我们称 32-bit 为一个 word，64-bit 为一个 doubleword。

RISC-V 是一种 load-store 架构，即除了 load 和 store 系指令可以直接访问内存，其余指令仅可访问寄存器。RISC-V 架构具有立即数寻址、寄存器寻址、基址寻址、PC 相对寻址四种寻址方式，如图自上而下。

![](/assets/images/cs/organization/address_mode.png)

## 程序调用与内存管理