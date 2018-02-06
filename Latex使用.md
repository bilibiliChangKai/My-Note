# Latex使用

解决问题：Latex编写

> Author：huziang </br>
>
> Date：18-2-5

## 矩阵

$$
\begin{gather*}
\begin{matrix} 0 & 1 \\ 1 & 0 \end{matrix}\quad
\begin{pmatrix} 0 & -i \\ i & 0 \end{pmatrix}\\
\begin{bmatrix} 0 & -1 \\ 1 & 0 \end{bmatrix}\quad
\begin{Bmatrix} 1 & 0 \\ 0 & -1 \end{Bmatrix}\\
\begin{vmatrix} a & b \\ c & d \end{vmatrix}\quad
\begin{Vmatrix} i & 0 \\ 0 & -i \end{Vmatrix}
\end{gather*}
$$

##  动态规划方程式：

$$
f(x) =
\begin{cases}
	0 & \text{x=0}\\
	1 & \text{x<=0} \\
	2 & \text{x>=0}
\end{cases}
$$

## 方程式对齐：

$$
\begin{align*}
f(x) &= 1 + 2 + 3 \\
       &= 3 + 3  \\
       &= 6
\end{align*}
$$

## 求和：


$$
\sum_{i=1}^{l-1}k_{i}+1, \sum_{i=1}^{l-1}k_{i}+2, \cdots , \sum_{i=1}^{l-1}k_{i}+k_{l}
$$

$$
\sum\limits_{i=1}^{l-1}k_{i}+1, \sum\limits_{i=1}^{l-1}k_{i}+2, \cdots , \sum\limits_{i=1}^{l-1}k_{i}+k_{l}
$$

