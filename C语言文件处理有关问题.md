# C语言文件处理有关的问题

> Author : huziang </br>
>
> Date : 2018年3月22日 星期四

## 判断文件是否存在

~~~c
#include <unistd.h>

#define R_OK 4 /* Test for read permission. */
#define W_OK 2 /* Test for write permission. */
#define X_OK 1 /* Test for execute permission. */
#define F_OK 0 /* Test for existence. */
int access(const char *filenpath, int mode); 
//或者
int _access( const char *path, int mode);
~~~

filepath : 文件路径

mode : 以上四种情况

return int : -1代表不满足，0代表满足

## 文件流

在文件流中，如果打开文件后没有关闭，并且意外退出，如下：

~~~c++
FILE *fp = fopen("123.txt", "a");
fprintf(fp, "OK?");
while(1) {}  // (ctrl + c) exit
fclose(fp);
~~~

会出现一种情况：文件并没有写入。

原因如下：

文件输入的时候会存在一个缓冲区，直到文件结束的时候，才会清空缓冲区，将内容输入文件，如果文件没有正常关闭(ctrl + c终止)，内容将不会输入进去，导致错误。

正确做法：

使用fflush(fp)清空缓冲区。