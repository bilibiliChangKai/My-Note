# Golang 协程时出现的问题

解决问题：Docker输入输出通讯

> Author：huziang </br>
>
> Date：18-2-6

## 关于Channel

- 管道传值函数的时候是指针传值，可以直接传。


- 外部变量被匿名函数调用的时候，会拷贝复制，放心使用。

~~~go
package main

import "fmt"

func test() {
	i := 1
	go func() {
		for ; i < 100; i++ {
			fmt.Println(i)
		}
	}()
}

func main() {
	test()
	fmt.Println("out func!")
	for {
	}
}

~~~

