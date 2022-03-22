
### Solidity – 条件语句

Solidity支持条件语句，让程序可以根据条件执行不同的操作。条件语句包括：

if
if...else
if...else if

### Solidity – if语句
语法
if (条件表达式) {
被执行语句(如果条件为真)
}
复制
示例
展示if语句用法。

pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public {
storedData = 10;   
}
function getResult() public view returns(string memory){
uint a = 1;
uint b = 2;
uint result = a + b;
return integerToString(result);
}
function integerToString(uint _i) internal pure
returns (string memory) {
if (_i == 0) {   // if 语句
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

### Solidity – if…else语句
语法
if (条件表达式) {
被执行语句(如果条件为真)
} else {
被执行语句(如果条件为假)
}
复制
示例
展示if...else语句用法。

pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
storedData = 10;   
}
function getResult() public view returns(string memory){
uint a = 1;
uint b = 2;
uint result
if( a > b) {   // if else 语句
result = a;
} else {
result = b;
}       
return integerToString(result);
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

0: string: 2

### Solidity – if…else if…语句
语法
if (条件表达式 1) {
被执行语句(如果条件 1 为真)
} else if (条件表达式 2) {
被执行语句(如果条件 2 为真)
} else if (条件表达式 3) {
被执行语句(如果条件 3 为真)
} else {
被执行语句(如果所有条件为假)
}


复制
示例
展示if...else if...语句用法。

pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData; // State variable
constructor() public {
storedData = 10;   
}
function getResult() public view returns(string memory) {
uint a = 1;
uint b = 2;
uint c = 3;
uint result

      if( a > b && a > c) {   // if else if 语句
         result = a;
      } else if( b > a && b > c ){
         result = b;
      } else {
         result = c;
      }       
      return integerToString(result); 
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