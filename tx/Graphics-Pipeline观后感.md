# Graphics-Pipeline观后感

> Author: huzi(moustache)<br>
> Date: 18-7-26 20:37

## Part1

DX11(directx11.0)编程渲染过程：

 1. 应用程序调用API
 2. API交给UMD（user-mode graphics driver）进行编译，优化。
    - UMD实际上是一个DDL。
    - 对于DX11，先转换成HLSL代码，最终编译成D3D字节码。
 3. 代码交给图形调度程序，等待调度。
 4. 进入KMD（kernel-mode driver），输入到主命令缓冲区。
 5. 通过bus。
 6. GPU读取命令，处理命令。

OpenGL与D3D不同的是，着色器的编译完全由驱动程序执行，而不是只有HLSL，因此会有很多版本。

## Part2

CPU和GPU区别：

![](photo/cpugpu.jpg)

GPU中DRAM(Dynamic Random Access Memory)使用格网，**会同时访问定行中的所有列**。

