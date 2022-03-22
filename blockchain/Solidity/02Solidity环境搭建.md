本章介绍如何搭建 Solidity 开发环境。

在线开发环境Remix(推荐)
学习Solidity推荐使用在线开发环境Remix，本教程的例子将使用Remix开发运行。

安装本地编译器
安装 nodejs / npm
node官方网站下载node，推荐LTS版本，按提示完成安装，npm会同时装上。

验证Node版本：

Kevin@QIKEGU G:\
> node -v
v10.16.3

Kevin@QIKEGU G:\
> npm -v
6.11.3
复制
安装 Solidity 编译器 solc
一旦安装了Node.js包管理器，就可以按照下面的步骤安装 Solidity 编译器

$ npm install -g solc
复制
上面的命令将安装solcjs程序，并使其在整个系统中都可用。

验证solc安装

$ solcjs --version
复制
如果一切顺利，这将打印如下内容

0.5.11+commit.c082d0b4.Emscripten.clang
复制
现在，可以使用solcjs了，它比标准的solidity编译器少很多特性，但对于学习来说足够了。