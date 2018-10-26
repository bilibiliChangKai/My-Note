# 移动GPU架构分析

> Author : moustache (huzi) <br>
>
> Date : 18-10-16 21:33

参考博客：http://gad.qq.com/article/detail/15884

## 流水线

流水线实际上是GPU中的多个核，每个核负责图像的其中一部分的渲染，其中带有帧同步。

## 移动GPU的三种方式

- IMR：Immediate Mode Rendering

  提交的每个渲染命令都会立即开始执行，并且该渲染命令会在整条流水线中执行完毕后才开始执行下一个渲染命令。

- TBR：Tile Based Rendering

  将画面分成一个矩形区块（4 x 4 或 8 x 4）将顶点运算之后，会组装成一个个小的triangle在固定tile里面绘制。当一个tile里面的所有渲染指令都执行完Vertex Shader生成triangle后，每个tile就会有一个triangle list。基于这个triangle list执行光栅化（raster）和Fragment Shader。

- TBDR：Tile Based Deferred Rendering

  通过HSR减少需要渲染的像素。

  HSR：Hidden Surface Removal

  在光栅化后，执行Pixel Shader之前，对光栅化生成的每个像素都做depth test的比较，剔除被遮挡的像素。