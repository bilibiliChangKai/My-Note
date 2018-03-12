# CTP-API项目发生的问题

解决问题：char\[100\]\[7\]和char**指针的转换问题

> Author：huziang </br>
>
> Date：18-3-12

## 关于char\[x\]\[y\]和char\*\*(char\\[\]\[\])

char\[x\]\[y\]虽然也是char\*\*指针，但是实际上，和真正的char\*\*指针完全不同，具体如下：

写一个简单的cpp文件：

~~~c++
#include <iostream>
using namespace std;

int main() 
{
    char table1[5][5];
    char **table2 = new char*[5];
    for (int i = 0; i < 5; i++) {
        table2[i] = new char[5];
    }
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            table1[i][j] = table2[i][j] = j * 5 + i + 'a';
        }
    }
    
    cout << "n1:" << table1 + 1 << endl;
    cout << "n2:" << table2 + 1 << endl;
    
    cout << "table1:\n";
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            cout << table1[i][j] << " ";
        }
        cout << endl;
    }

    cout << "table2:\n";
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            cout << table2[i][j] << " ";
        }
        cout << endl;
    }

    cout << "end\n";

    while(1) {}
}
~~~

运行，得到结果。可知，使用[]操作符时，两者并没有区别。

~~~bash
(gdb) r
Starting program: C:\Users\Administrator\Desktop/test.exe
[New Thread 2568.0xa80]
n1:0x28fefc
n2:0x661e7c
table1:
a f k p u
b g l q v
c h m r w
d i n s x
e j o t y
table2:
a f k p u
b g l q v
c h m r w
d i n s x
e j o t y

~~~

通过GDB查看char\[5\]\[5\]的内存结构：

~~~bash
(gdb) p table1
$3 = {"afkpu", "bglqv", "chmrw", "dinsx", "ejoty"}
(gdb) p &table1
$3 = (char (*)[5][5]) 0x28fef7
PS:内存地址如下
	table1 = 0x28fef7
	table1 + 1 = x28fefc = 0x28fef7 + 5
~~~

通过GDB查看char**的内存结构：

~~~bash
(gdb) p table2
$2 = (char **) 0x661e78
PS:内存地址如下
	table2 = 0x661e78
	table1 + 1 = 0x661e7c = 0x661e78 + 4
~~~

发现两者的内存结构有很大不同。

char\[5\]\[5\]其实仍然相当于一维数组，只是[]操作符进行重载，使得每次+1相当于+(sizeof(char) \* 5)，在一般的系统中，对应5字节。因此char\[5\]\[5\]必须要使每列大小相同。

char\*\*是真正的二维数组，其中储存一定数量的char\*指针，每个char\*指针都对应一个char数组。因此，char\*\*数组的每一行的列数可以完全不相同，每一列也可以不连续。而char\*\*中的+1相当于+sizeof(char*)，在一般的系统中，对应4或8字节。

因此，将char\[5\]\[5\]强制转换成char**，就会产生错误。

**PS：char\[5\]\[5\]与其说是指针，更不如说是一种数据类型，这种类型也是c数组处理快的基础。**