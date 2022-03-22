
### Solidity – 第一个程序

为简单起见，我们使用在线Solidity开发工具Remix IDE编译和运行Solidity程序。

第1步 – 在File explorers选项卡下，新建一个test1.sol文件，代码如下：

示例

pragma solidity ^0.5.0;
contract SolidityTest {
constructor() public{
}
function getResult() public view returns(uint){
uint a = 1;
uint b = 2;
uint result = a + b;
return result;
}
}
复制
第2步 – 在Compiler选项卡下，单击 Compile 按钮，开始编译
第3步 – 在Run选项卡下，单击 Deploy 按钮进行部署
第4步 – 在Run选项卡下，选择 SolidityTest at 0x… 下拉
第5步 – 单击 getResult 按钮显示结果。

输出

0: uint256: 3


### Solidity – 代码注释

Solidity 支持c风格和c++风格的注释。

//之后到行尾的文本，都被看作注释，编译器忽略此内容
/* 与 */ 之间的文本被看作注释， 编译器忽略此内容
示例

注释示例。

function getResult() public view returns(uint){
// 这是一行注释，类似于c++中的注释

/*
* 这是多行注释
* 类似于c语言中的注释
  */
  uint a = 1;
  uint b = 2;
  uint result = a + b;
  return result;
  }

### Solidity – 数据类型

在用任何语言编写程序时，都需要使用变量来存储各种信息。变量是内存空间的名称，变量有不同类型，例如整型、字符串类型等等。操作系统根据变量的数据类型分配内存。

Solidity中，变量类型有以下几大类：

值类型
地址类型
引用类型
值类型
类型	保留字	取值
布尔型	bool	true/false
整型	int/uint	有符号整数/无符号整数。
整型	int8 to int256	8位到256位的带符号整型数。int256与int相同。
整型	uint8 to uint256	8位到256位的无符号整型。uint256和uint是一样的。
定长浮点型	fixed/unfixed	有符号和无符号的定长浮点型
定长浮点型	fixedMxN	带符号的定长浮点型，其中M表示按类型取的位数，N表示小数点。M应该能被8整除，从8到256。N可以是0到80。fixed与fixed128x18相同。
定长浮点型	ufixedMxN	无符号的定长浮点型，其中M表示按类型取的位数，N表示小数点。M应该能被8整除，从8到256。N可以是0到80。fixed与fixed128x18相同。
地址类型
地址类型表示以太坊地址，长度为20字节。地址可以使用.balance方法获得余额，也可以使用.transfer方法将余额转到另一个地址。

address x = 0x212;
address myAddress = this;

if (x.balance < 10 && myAddress.balance >= 10)
x.transfer(10);
复制
引用类型/复合数据类型
Solidity中，有一些数据类型由值类型组合而成，相比于简单的值类型，这些类型通常通过名称引用，被称为引用类型。

引用类型包括：

数组 (字符串与bytes是特殊的数组，所以也是引用类型)
struct (结构体)
map (映射)

### Solidity – 变量
Solidity 支持三种类型的变量：

状态变量 – 变量值永久保存在合约存储空间中的变量。
局部变量 – 变量值仅在函数执行过程中有效的变量，函数退出后，变量无效。
全局变量 – 保存在全局命名空间，用于获取区块链相关信息的特殊变量。
Solidity 是一种静态类型语言，这意味着需要在声明期间指定变量类型。每个变量声明时，都有一个基于其类型的默认值。没有undefined或null的概念。

状态变量
变量值永久保存在合约存储空间中的变量。

pragma solidity ^0.5.0;
contract SolidityTest {
uint storedData;      // 状态变量
constructor() public {
storedData = 10;   // 使用状态变量
}
}
复制
局部变量
变量值仅在函数执行过程中有效的变量，函数退出后，变量无效。函数参数是局部变量。

pragma solidity ^0.5.0;
contract SolidityTest {
uint storedData; // 状态变量
constructor() public {
storedData = 10;   
}
function getResult() public view returns(uint){
uint a = 1; // 局部变量
uint b = 2;
uint result = a + b;
return result; // 访问局部变量
}
}
复制
示例

pragma solidity ^0.5.0;
contract SolidityTest {
uint storedData; // 状态变量
constructor() public {
storedData = 10;   
}
function getResult() public view returns(uint){
uint a = 1; // 局部变量
uint b = 2;
uint result = a + b;
return storedData; // 访问状态变量
}
}
复制
可以使用Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: uint256: 10
复制
全局变量
这些是全局工作区中存在的特殊变量，提供有关区块链和交易属性的信息。

名称	返回
blockhash(uint blockNumber) returns (bytes32)	给定区块的哈希值 – 只适用于256最近区块, 不包含当前区块。
block.coinbase (address payable)	当前区块矿工的地址
block.difficulty (uint)	当前区块的难度
block.gaslimit (uint)	当前区块的gaslimit
block.number (uint)	当前区块的number
block.timestamp (uint)	当前区块的时间戳，为unix纪元以来的秒
gasleft() returns (uint256)	剩余 gas
msg.data (bytes calldata)	完成 calldata
msg.sender (address payable)	消息发送者 (当前 caller)
msg.sig (bytes4)	calldata的前四个字节 (function identifier)
msg.value (uint)	当前消息的wei值
now (uint)	当前块的时间戳
tx.gasprice (uint)	交易的gas价格
tx.origin (address payable)	交易的发送方
Solidity 变量名
在为变量命名时，请记住以下规则。

不应使用 Solidity 保留关键字作为变量名。例如，break或boolean变量名无效。
不应以数字(0-9)开头，必须以字母或下划线开头。例如，123test是一个无效的变量名，但是_123test是一个有效的变量名。
变量名区分大小写。例如，Name和name是两个不同的变量。


### Solidity – 变量作用域
局部变量的作用域仅限于定义它们的函数，但是状态变量可以有三种作用域类型。

Public – 公共状态变量可以在内部访问，也可以通过消息访问。对于公共状态变量，将生成一个自动getter函数。
Internal – 内部状态变量只能从当前合约或其派生合约内访问。
Private – 私有状态变量只能从当前合约内部访问，派生合约内不能访问。
示例

pragma solidity ^0.5.0;
contract C {
uint public data = 30;
uint internal iData= 10;

function x() public returns (uint) {
data = 3; // 内部访问
return data;
}
}
contract Caller {
C c = new C();
function f() public view returns (uint) {
return c.data(); // 外部访问
}
}
contract D is C {
uint storedData; // 状态变量

function y() public returns (uint) {
iData = 3; // 派生合约内部访问
return iData;
}
function getResult() public view returns(uint){
uint a = 1; // 局部变量
uint b = 2;
uint result = a + b;
return storedData; // 访问状态变量
}
}

### 





