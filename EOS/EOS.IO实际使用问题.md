# EOS.IO实际使用问题

## EOS关系模型

关系图：

![](photo/Accounts-and-Wallets-Overview.png)

- nodeos：服务端。建立在本机的私有链条，接口为Restful模式，运行后每0.5s生成一个块。通过nodeos可以进行账户和合约操作。

- cleos：客户端。用户输入指令访问keosd和nodeos进程，执行命令。

- keosd：管理wallet的守护进程，wallet包含私钥和公钥，储存在本地保证安全。

  （PS：nodeos重启的时候，wallet会关闭，需要重新打开）

