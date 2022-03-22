### Solidity – 循环语句
与其他语言类似，Solidity语言支持循环结构，Solidity提供以下循环语句。

while
do ... while
for
循环控制语句：break、continue。

### Solidity – while循环
语法
Solidity 中， while循环的语法如下：

while (表达式) {
被执行语句(如果表示为真)
}
复制
示例
pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
storedData = 10;   
}
function getResult() public view returns(string memory){
uint a = 10;
uint b = 2;
uint result = a + b;
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

      while (_i != 0) { // while 循环
         bstr[k--] = byte(uint8(48 + _i % 10));
         _i /= 10;
      }
      return string(bstr);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 12

### Solidity – do…while循环
语法
Solidity 中， do…while循环的语法如下：

do {
被执行语句(如果表示为真)
} while (表达式);
复制
注意: 不要漏掉do后面的分号。

示例
pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
storedData = 10;   
}
function getResult() public view returns(string memory){
uint a = 10;
uint b = 2;
uint result = a + b;
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

      do {                   // do while 循环 
         bstr[k--] = byte(uint8(48 + _i % 10));
         _i /= 10;
      }
      while (_i != 0);
      return string(bstr);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 12

### Solidity – for循环
语法
Solidity 中， for循环的语法如下：

for (初始化; 测试条件; 迭代语句) {
被执行语句(如果表示为真)
}
复制
示例
pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
storedData = 10;   
}

function getResult() public view returns(string memory){
uint a = 10;
uint b = 2;
uint result = a + b;
return integerToString(result);
}

function integerToString(uint _i) internal pure
returns (string memory) {
if (_i == 0) {
return "0";
}
uint j=0;
uint len;
for (j = _i; j != 0; j /= 10) {  //for循环的例子
len++;         
}
bytes memory bstr = new bytes(len);
uint k = len - 1;
while (_i != 0) {
bstr[k--] = byte(uint8(48 + _i % 10));
_i /= 10;
}
return string(bstr);//访问局部变量
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 12

### Solidity – break与continue
continue – 跳出本次循环，继续执行接下来的循环
break – 跳出循环(或跳出代码块)
break 示例
pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
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

      if (_i == 0) {
         return "0";
      }
      uint j = _i;
      uint len;

      while (true) {
         len++;
         j /= 10;
         if(j==0){
            break;   // break 语句跳出循环
         }
      }
      bytes memory bstr = new bytes(len);
      uint k = len - 1;

      while (_i != 0) {
         bstr[k--] = byte(uint8(48 + _i % 10));
         _i /= 10;
      }
      return string(bstr);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 3
复制
continue 示例
pragma solidity ^0.5.0;

contract SolidityTest {
uint storedData;
constructor() public{
storedData = 10;   
}
function getResult() public view returns(string memory){
uint n = 1;
uint sum = 0;

      while( n < 10){
         n++;
         if(n == 5){
            continue; // 当n的和是5时，跳过n。
         }
         sum = sum + n;
      }
      return integerToString(sum); 
}
function integerToString(uint _i) internal pure
returns (string memory) {

      if (_i == 0) {
         return "0";
      }
      uint j = _i;
      uint len;

      while (true) {
         len++;
         j /= 10;
         if(j==0){
            break;   // break跳出循环
         }
      }
      bytes memory bstr = new bytes(len);
      uint k = len - 1;

      while (_i != 0) {
         bstr[k--] = byte(uint8(48 + _i % 10));
         _i /= 10;
      }
      return string(bstr);
}
}
复制
可以参考Solidity – 第一个程序中的步骤，运行上述程序。

输出

0: string: 49