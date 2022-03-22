一个 Solidity 源文件可以包含任意数量的合约定义、import指令和pragma指令。

让我们从一个简单的 Solidity 源程序开始。下面是一个 Solidity 源文件的例子：

pragma solidity >=0.4.0 <0.6.0;

contract SimpleStorage {
uint storedData;

function set(uint x) public {
storedData = x;
}

function get() public view returns (uint) {
return storedData;
}
}
复制
Pragma
第一行是pragma指令，它告诉我们源代码是为Solidity version 0.4.0及以上版本编写的，但不包括version 0.6.0及以上版本。

pragma指令只对自己的源文件起作用，如果把文件B导入到文件A，文件B的pragma将不会自动应用于文件A。

pragma solidity ^0.4.0;
复制
上面的pragma指令意思是，源文件不能用低于0.4.0版本的编译器编译，也不能用0.5.0版本及以上版本的编译器编译。

这里第二个条件是用^加上的，表示不超过0.5.0版本，背后的意思是，0.4.0 ~ 0.4.9 之间的小版本改动通常不会有破坏性更改，源代码应该都是兼容的。

Contract/智能合约
智能合约是位于以太坊区块链上特定地址的代码(函数)和数据(状态)的集合。

这行代码：uint storedData;，声明了一个名为storedData的状态变量，类型为uint, set和get函数可用于修改或检索变量的值。

导入文件
上面的例子没有import语句，但是Solidity 支持与JavaScript非常相似的导入语句。

下面的语句从“filename”导入所有全局符号。

import "filename";
复制
下面的示例，创建一个新的全局符号symbolName，它的成员都是来自“filename”的全局符号。

import * as symbolName from "filename";
复制
要从当前目录导入文件x，请使用import "./x"。如果不指定当前路径，可能会在全局“include目录”中引用另一个文件。

保留关键字
下面是 Solidity 语言中的保留关键字：

abstract
after
alias
apply
auto
case
catch
copyof
default
define
final
immutable
implements
in
inline
let
macro
match
mutable
null
of
override
partial
promise
reference
relocatable
sealed
sizeof
static
supports
switch
try
typedef
typeof
unchecked