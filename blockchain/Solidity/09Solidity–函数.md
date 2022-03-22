
### Solidity – 函数
函数是一组可重用代码的包装，接受输入，返回输出。

函数定义
Solidity中， 定义函数的语法如下：

语法
function function-name(parameter-list) scope returns() {
//语句
}
复制
函数由关键字function声明，后面跟函数名、参数、可见性、返回值的定义。

示例
下面的例子，定义了一个名为getResult的函数，该函数不接受任何参数：

pragma solidity ^0.5.0;

contract Test {
function getResult() public view returns(uint){
uint a = 1; // 局部变量
uint b = 2;
uint result = a + b;
return result;
}
}
复制
函数调用与函数参数
要调用函数，只需使用函数名，并传入参数即可。

pragma solidity ^0.5.0;

contract SolidityTest {   
constructor() public{       
}
function getResult() public view returns(string memory){
uint a = 1;
uint b = 2;
uint result = a + b;
return integerToString(result); // 调用函数
}
function integerToString(uint _i) internal pure
returns (string memory) {

      if (_i == 0) {
         return "0";
      }
      uint j = _i;
      uint len;

      while (j != 0) {
         len++;
         j /= 10;
      }
      bytes memory bstr = new bytes(len);
      uint k = len - 1;

      while (_i != 0) {
         bstr[k--] = byte(uint8(48 + _i % 10));
         _i /= 10;
      }
      return string(bstr);// 访问局部变量
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 3
复制
return 语句
Solidity中， 函数可以返回多个值。

pragma solidity ^0.5.0;

contract Test {
function getResult() public view returns(uint product, uint sum){
uint a = 1; // 局部变量
uint b = 2;
product = a * b; // 使用返回参数返回值
sum = a + b; // 使用返回参数返回值

      // 也可以使用return返回多个值
      // return(a*b, a+b);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: uint256: product 2
1: uint256: sum 3

### Solidity – 函数修饰符
函数修饰符用于修改函数的行为。例如，向函数添加条件限制。

创建带参数修饰符和不带参数修饰符，如下所示：

contract Owner {

// 定义修饰符 onlyOwner 不带参数
modifier onlyOwner {
require(msg.sender == owner);
_;
}

// 定义修饰符 costs 带参数
modifier costs(uint price) {
if (msg.value >= price) {
_;
}
}
}
复制
修饰符定义中出现特殊符号_的地方，用于插入函数体。如果在调用此函数时，满足了修饰符的条件，则执行该函数，否则将抛出异常。

pragma solidity ^0.5.0;

contract Owner {
address owner;

constructor() public {
owner = msg.sender;
}

// 定义修饰符 onlyOwner 不带参数
modifier onlyOwner {
require(msg.sender == owner);
_;
}

// 定义修饰符 costs 带参数
modifier costs(uint price) {
if (msg.value >= price) {
_;
}
}
}

contract Register is Owner {
mapping (address => bool) registeredAddresses;
uint price;

constructor(uint initialPrice) public { price = initialPrice; }

// 使用修饰符 costs
function register() public payable costs(price) {
registeredAddresses[msg.sender] = true;
}

// 使用修饰符 onlyOwner
function changePrice(uint _price) public onlyOwner {
price = _price;
}
}


### Solidity – View(视图)函数
View(视图)函数不会修改状态。如果函数中存在以下语句，则被视为修改状态，编译器将抛出警告。

修改状态变量。
触发事件。
创建合约。
使用selfdestruct。
发送以太。
调用任何不是视图函数或纯函数的函数
使用底层调用
使用包含某些操作码的内联程序集。
Getter方法是默认的视图函数。声明视图函数，可以在函数声明里，添加view关键字。

示例

pragma solidity ^0.5.0;

contract Test {
function getResult() public view returns(uint product, uint sum){
uint a = 1; // 局部变量
uint b = 2;
product = a * b;
sum = a + b;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: uint256: product 2
1: uint256: sum 3

### Solidity – Pure(纯)函数
Pure(纯)函数不读取或修改状态。如果函数中存在以下语句，则被视为读取状态，编译器将抛出警告。

读取状态变量。
访问 address(this).balance 或 <address>.balance
访问任何区块、交易、msg等特殊变量(msg.sig 与 msg.data 允许读取)。
调用任何不是纯函数的函数。
使用包含特定操作码的内联程序集。
如果发生错误，纯函数可以使用revert()和require()函数来还原潜在的状态更改。

声明纯函数，可以在函数声明里，添加pure关键字。

示例

pragma solidity ^0.5.0;

contract Test {
function getResult() public pure returns(uint product, uint sum){
uint a = 1;
uint b = 2;
product = a * b;
sum = a + b;
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: uint256: product 2
1: uint256: sum 3


### Solidity – fallback(回退) 函数
fallback(回退) 函数是合约中的特殊函数。它有以下特点

当合约中不存在的函数被调用时，将调用fallback函数。
被标记为外部函数。
它没有名字。
它没有参数。
它不能返回任何东西。
每个合约定义一个fallback函数。
如果没有被标记为payable，则当合约收到无数据的以太币转账时，将抛出异常。
语法

// 没有名字，没有参数，不返回，标记为external，可以标记为payable
function() external {
// statements
}
复制
下面的示例展示了合约中的回退函数概念。

示例

pragma solidity ^0.5.0;

contract Test {
uint public x ;
function() external { x = 1; }    
}
contract Sink {
function() external payable { }
}
contract Caller {

function callTest(Test test) public returns (bool) {
(bool success,) = address(test).call(abi.encodeWithSignature("nonExistingFunction()"));
require(success);
// test.x 是 1

      address payable testPayable = address(uint160(address(test)));

      // 发送以太测试合同,
      // 转账将失败，也就是说，这里返回false。
      return (testPayable.send(2 ether));
}

function callSink(Sink sink) public returns (bool) {
address payable sinkPayable = address(sink);
return (sinkPayable.send(2 ether));
}
}

### Solidity – 函数重载
同一个作用域内，相同函数名可以定义多个函数。这些函数的参数(参数类型或参数数量)必须不一样。仅仅是返回值不一样不被允许。

下面的例子展示了Solidity中的函数重载概念。

示例

pragma solidity ^0.5.0;

contract Test {
function getSum(uint a, uint b) public pure returns(uint){      
return a + b;
}
function getSum(uint a, uint b, uint c) public pure returns(uint){      
return a + b + c;
}
function callSumWithTwoArguments() public pure returns(uint){
return getSum(1,2);
}
function callSumWithThreeArguments() public pure returns(uint){
return getSum(1,2,3);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

首先单击callSumWithTwoArguments按钮，然后单击callSumWithThreeArguments按钮查看结果。

输出

0: uint256: 3
0: uint256: 6

### Solidity – 数学函数
Solidity 也提供了内置的数学函数。下面是常用的数学函数：

addmod(uint x, uint y, uint k) returns (uint) 计算(x + y) % k，计算中，以任意精度执行加法，且不限于2^256大小。
mulmod(uint x, uint y, uint k) returns (uint) 计算(x * y) % k，计算中，以任意精度执行乘法，且不限于2^256大小。
下面的例子说明了数学函数的用法。

示例

pragma solidity ^0.5.0;

contract Test {   
function callAddMod() public pure returns(uint){
return addmod(4, 5, 3);
}
function callMulMod() public pure returns(uint){
return mulmod(4, 5, 3);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

首先单击callAddMod按钮，然后单击callMulMod按钮查看结果。

输出

0: uint256: 0
0: uint256: 2

### Solidity – 加密函数

Solidity 提供了常用的加密函数。以下是一些重要函数：

keccak256(bytes memory) returns (bytes32) 计算输入的Keccak-256散列。
sha256(bytes memory) returns (bytes32) 计算输入的SHA-256散列。
ripemd160(bytes memory) returns (bytes20) 计算输入的RIPEMD-160散列。
ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address) 从椭圆曲线签名中恢复与公钥相关的地址，或在出错时返回零。函数参数对应于签名的ECDSA值: r – 签名的前32字节; s: 签名的第二个32字节; v: 签名的最后一个字节。这个方法返回一个地址。
下面的例子说明了加密函数的用法。

示例

pragma solidity ^0.5.0;

contract Test {   
function callKeccak256() public pure returns(bytes32 result){
return keccak256("ABC");
}  
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: bytes32: result 0xe1629b9dda060bb30c7908346f6af189c16773fa148d3366701fbaa35d54f3c8




