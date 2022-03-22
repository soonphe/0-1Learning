### Solidity – 智能合约

Solidity中，合约类似于c++中的类。合约包含以下部分：

构造函数 – 使用constructor关键字声明的特殊函数，每个合约执行一次，在创建合约时调用。
状态变量 – 用于存储合约状态的变量。
函数 – 智能合约中的函数，可以修改状态变量来改变合约的状态。
可见性
与以其他语言的类一样，合约中的函数和变量也有可见性：

external − 外部函数由其他合约调用，要在合约内部调用外部函数，使用this.function_name()的方式。状态变量不能标记为外部变量。
public − 公共函数/变量可以在外部和内部直接使用。对于公共状态变量，Solidity为其自动创建一个getter函数。
internal − 内部函数/变量只能在内部或派生合约中使用。
private − 私有函数/变量只能在内部使用，派生合约中不能使用。
示例

pragma solidity ^0.5.0;

contract C {
//private state variable
uint private data;

//public state variable
uint public info;

//constructor
constructor() public {
info = 10;
}
//private function
function increment(uint a) private pure returns(uint) { return a + 1; }

//public function
function updateData(uint a) public { data = a; }
function getData() public view returns(uint) { return data; }
function compute(uint a, uint b) internal pure returns (uint) { return a + b; }
}

//External Contract
contract D {
function readData() public returns(uint) {
C c = new C();
c.updateData(7);         
return c.getData();
}
}

//Derived Contract
contract E is C {
uint private result;
C private c;

constructor() public {
c = new C();
}  
function getComputedResult() public {      
result = compute(3, 5);
}
function getResult() public view returns(uint) { return result; }
function getData() public view returns(uint) { return c.info(); }
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

执行各种合约方法，例如，先执行E.getComputedResult()，再执行E.getResult()：

输出

0: uint256: 8

### Solidity – 合约继承
就像Java、C++中，类的继承一样，Solidity中，合约继承是扩展合约功能的一种方式。Solidity支持单继承和多继承。Solidity中，合约继承的重要特点：

派生合约可以访问父合约的所有非私有成员，包括内部方法和状态变量。但是不允许使用this。
如果函数签名保持不变，则允许函数重写。如果输出参数不同，编译将失败。
可以使用super关键字或父合同名称调用父合同的函数。
在多重继承的情况下，使用super的父合约函数调用，优先选择被最多继承的合约。
示例

pragma solidity ^0.5.0;

contract C {
//private state variable
uint private data;

//public state variable
uint public info;

//constructor
constructor() public {
info = 10;
}
//private function
function increment(uint a) private pure returns(uint) { return a + 1; }

//public function
function updateData(uint a) public { data = a; }
function getData() public view returns(uint) { return data; }
function compute(uint a, uint b) internal pure returns (uint) { return a + b; }
}

//Derived Contract
contract E is C {

uint private result;
C private c;

constructor() public {
c = new C();
}  
function getComputedResult() public {      
result = compute(3, 5);
}
function getResult() public view returns(uint) { return result; }
function getData() public view returns(uint) { return c.info(); }
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

执行各种合约方法，例如，先执行E.getComputedResult()，再执行E.getResult()：

输出

0: uint256: 8

### Solidity – 构造函数
构造函数是使用construct关键字声明的特殊函数，用于初始化合约的状态变量。合约中构造函数是可选的，可以省略。

构造函数有以下重要特性：

一个合约只能有一个构造函数。
构造函数在创建合约时执行一次，用于初始化合约状态。
在执行构造函数之后，合约最终代码被部署到区块链。合约最终代码包括公共函数和可通过公共函数访问的代码。构造函数代码或仅由构造函数使用的任何内部方法不包括在最终代码中。
构造函数可以是公共的，也可以是内部的。
内部构造函数将合约标记为抽象合约。
如果没有定义构造函数，则使用默认构造函数。
pragma solidity ^0.5.0;

contract Test {
constructor() public {}
}
复制
如果基合约具有带参数的构造函数，则每个派生/继承的合约也都必须包含参数。
可以使用下面的方法直接初始化基构造函数
pragma solidity ^0.5.0;

contract Base {
uint data;
constructor(uint _data) public {
data = _data;   
}
}
contract Derived is Base (5) {
constructor() public {}
}
复制
可以使用以下方法间接初始化基构造函数
pragma solidity ^0.5.0;

contract Base {
uint data;
constructor(uint _data) public {
data = _data;   
}
}
contract Derived is Base {
constructor(uint _info) Base(_info * _info) public {}
}
复制
不允许直接或间接地初始化基合约构造函数。
如果派生合约没有将参数传递给基合约构造函数，则派生合约将成为抽象合约。

### Solidity – 抽象合约

类似java中的抽象类，抽象合约至少包含一个没有实现的函数（抽象函数）。通常，抽象合约作为父合约，被用来继承，在继承合约中实现抽象函数，抽象合约也可以包含有实现的函数。

如果派生合约没有实现抽象函数，则该派生合约也将被标记为抽象合约。

示例

尝试下面的代码，来理解抽象合约是如何工作的。

pragma solidity ^0.5.0;

contract Calculator {
function getResult() public view returns(uint);
}

contract Test is Calculator {
function getResult() public view returns(uint) {
uint a = 1;
uint b = 2;
uint result = a + b;
return result;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: uint256: 3

### Solidity – 接口
接口类似于抽象合约，使用interface关键字创建，接口只能包含抽象函数，不能包含函数实现。以下是接口的关键特性：

接口的函数只能是外部类型。
接口不能有构造函数。
接口不能有状态变量。
接口可以包含enum、struct定义，可以使用interface_name.访问它们。
示例

尝试下面的代码来理解这个接口是如何可靠地工作的。

pragma solidity ^0.5.0;

interface Calculator {
function getResult() external view returns(uint);
}

contract Test is Calculator {
constructor() public {}
function getResult() external view returns(uint){
uint a = 1;
uint b = 2;
uint result = a + b;
return result;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

在单击deploy按钮之前，从下拉菜单中选择Test。

输出

0: uint256: 3

### Solidity – 库

库类似于合约，但主要作用是代码重用。库中包含了可以被合约调用的函数。

Solidity中，对库的使用有一定的限制。以下是库的主要特征。

如果库函数不修改状态，则可以直接调用它们。这意味着纯函数或视图函数只能从库外部调用。
库不能被销毁，因为它被认为是无状态的。
库不能有状态变量。
库不能继承任何其他元素。
库不能被继承。
示例

尝试下面的代码，来理解库是如何工作的。

pragma solidity ^0.5.0;

library Search {
function indexOf(uint[] storage self, uint value) public view returns (uint) {
for (uint i = 0; i < self.length; i++) if (self[i] == value) return i;
return uint(-1);
}
}
contract Test {
uint[] data;
constructor() public {
data.push(1);
data.push(2);
data.push(3);
data.push(4);
data.push(5);
}
function isValuePresent() external view returns(uint){
uint value = 4;

      // 使用库函数搜索数组中是否存在值
      uint index = Search.indexOf(data, value);
      return index;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

在单击deploy按钮之前，从下拉菜单中选择Test。

输出

0: uint256: 3
复制
Using For
using A for B指令，可用于将库A的函数附加到给定类型B。这些函数将把调用者类型作为第一个参数(使用self标识)。

示例

尝试下面的代码，来理解Using For是怎么工作的。

pragma solidity ^0.5.0;

library Search {
function indexOf(uint[] storage self, uint value) public view returns (uint) {
for (uint i = 0; i < self.length; i++)if (self[i] == value) return i;
return uint(-1);
}
}
contract Test {
using Search for uint[];
uint[] data;
constructor() public {
data.push(1);
data.push(2);
data.push(3);
data.push(4);
data.push(5);
}
function isValuePresent() external view returns(uint){
uint value = 4;

      // data 表示库
      uint index = data.indexOf(value);
      return index;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

在单击deploy按钮之前，从下拉菜单中选择Test。

输出

0: uint256: 3

### Solidity – 使用汇编(Assembly)代码
与c/c++类似，solability程序中，可以使用EVM汇编语言。

内联汇编
使用内联汇编，可以在Solidity源程序中嵌入汇编代码，对EVM有更细粒度的控制，在编写库函数时很有用。

汇编代码嵌入使用：

assembly { ... }
复制
示例

尝试下面的代码，来理解汇编是怎么使用的。

pragma solidity ^0.5.0;

library Sum {   
function sumUsingInlineAssembly(uint[] memory _data) public pure returns (uint o_sum) {
for (uint i = 0; i < _data.length; ++i) {
assembly {
o_sum := add(o_sum, mload(add(add(_data, 0x20), mul(i, 0x20))))
}
}
}
}
contract Test {
uint[] data;

constructor() public {
data.push(1);
data.push(2);
data.push(3);
data.push(4);
data.push(5);
}
function sum() external view returns(uint){      
return Sum.sumUsingInlineAssembly(data);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

在单击deploy按钮之前，从下拉菜单中选择Test。

输出

0: uint256: 15

### Solidity – 事件(Event)
事件是智能合约发出的信号。智能合约的前端UI，例如，DApps、web.js，或者任何与Ethereum JSON-RPC API连接的东西，都可以侦听这些事件。事件可以被索引，以便以后可以搜索事件记录。

事件在区块链中的存储

区块链是一个区块链表，这些块的内容基本上是交易记录。每个交易都有一个附加的交易日志，事件结果存放在交易日志里。合约发出的事件，可以使用合约地址访问。

Solidity 事件
Solidity中，要定义事件，可以使用event关键字(在用法上类似于function关键字)。然后可以在函数中使用emit关键字触发事件。

// 声明一个事件
event Deposit(address indexed _from, bytes32 indexed _id, uint _value);

// 触发事件
emit Deposit(msg.sender, _id, msg.value);
复制
示例

创建合约并发出一个事件。

pragma solidity ^0.5.0;

contract Counter {
uint256 public count = 0;

    event Increment(address who);   // 声明事件

    function increment() public {
        emit Increment(msg.sender); // 触发事件
        count += 1;
    }
}
复制
上面的代码中,

event Increment(address who) 声明一个合约级事件，该事件接受一个address类型的参数，该参数是执行increment操作的账户地址。
emit Increment(msg.sender) 触发事件，事件会记入区块链中。
按照惯例，事件名称以大写字母开头，以区别于函数。

用JavaScript监听事件
下面的JavaScript代码侦听Increment事件，并更新UI。

counter = web3.eth.contract(abi).at(address);

counter.Increment(function (err, result) {
if (err) {
return error(err);
}

log("Count was incremented by address: " + result.args.who);
getCount();
});

getCount();
复制
contract.Increment(...) 开始侦听递增事件，并使用回调函数对其进行参数化。
getCount() 是一个获取最新计数并更新UI的函数。
索引(indexed)参数
一个事件最多有3个参数可以标记为索引。可以使用索引参数有效地过滤事件。下面的代码增强了前面的示例，来跟踪多个计数器，每个计数器由一个数字ID标识:

pragma solidity ^0.4.21;

contract Multicounter {
mapping (uint256 => uint256) public counts;

    event Increment(uint256 indexed which, address who);

    function increment(uint256 which) public {
        emit Increment(which, msg.sender);
        counts[which] += 1;
    }
}
复制
counts替换count，counts是一个map。
event Increment(uint256 indexed which, address who) 添加一个索引参数，该参数表示哪个计数器。
emit Increment(which, msg.sender) 用2个参数记录事件。
在Javascript中，可以使用索引访问计数器：

...

counter.Increment({ which: counterId }, function (err, result) {
if (err) {
return error(err);
}

log("Counter " + result.args.which + " was incremented by address: "
+ result.args.who);
getCount();
});

...
复制
事件的局限
事件构建在Ethereum中，底层的日志接口之上。虽然您通常不会直接处理日志消息，但是了解它们的限制非常重要。

日志结构最多有4个“主题”和一个“数据”字段。第一个主题用于存储事件签名的哈希值，这样就只剩下三个主题用于索引参数。主题需要32字节长，因此，如果使用数组作为索引参数(包括类型string和bytes)，那么首先将哈希值转换为32字节。非索引参数存储在数据字段中，没有大小限制。

日志，包括记录在日志中的事件，不能从Ethereum虚拟机(EVM)中访问。这意味着合约不能读取自己的或其他合约的日志及事件。

总结
Solidity 提供了一种记录交易期间事件的方法。
智能合约前端(DApp)可以监听这些事件。
索引(indexed)参数为过滤事件提供了一种高效的方法。
事件受其构建基础日志机制的限制。

### Solidity – 错误处理
Solidity 提供了很多错误检查和错误处理的方法。通常，检查是为了防止未经授权的代码访问，当发生错误时，状态会恢复到初始状态。

下面是错误处理中，使用的一些重要方法：

assert(bool condition) − 如果不满足条件，此方法调用将导致一个无效的操作码，对状态所做的任何更改将被还原。这个方法是用来处理内部错误的。
require(bool condition) − 如果不满足条件，此方法调用将恢复到原始状态。此方法用于检查输入或外部组件的错误。

require(bool condition, string memory message) − 如果不满足条件，此方法调用将恢复到原始状态。此方法用于检查输入或外部组件的错误。它提供了一个提供自定义消息的选项。

revert() − 此方法将中止执行并将所做的更改还原为执行前状态。

revert(string memory reason) − 此方法将中止执行并将所做的更改还原为执行前状态。它提供了一个提供自定义消息的选项。

示例

尝试下面的代码，来理解Solidity中的错误处理。

pragma solidity ^0.5.0;

contract Vendor {
address public seller;
modifier onlySeller() {
require(
msg.sender == seller,
"Only seller can call this."
);
_;
}
function sell(uint amount) public payable onlySeller {
if (amount > msg.value / 2 ether)
revert("Not enough Ether provided.");
// 执行销售操作
}

复制
当调用revert时，它将返回十六进制数据，如下所示。

0x08c379a0                     // 错误函数选择器(字符串)
0x0000000000000000000000000000000000000000000000000000000000000020 // 数据偏移/Data offset
0x000000000000000000000000000000000000000000000000000000000000001a // 字符串长度/String length
0x4e6f7420656e6f7567682045746865722070726f76696465642e000000000000 // 字符串数据/String data




