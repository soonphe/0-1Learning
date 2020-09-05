# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## ES6语法
TypeScript和JavaScript的关系：

TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript

ES6语法{
1.let和const（）
let是块级变量，声明的变量仅在块级作用域内有效
且如果在循环中，设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
不存在变量提升，即不可以在声明变量前使用变量
在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。
不允许重复声明
避免在块级作用域内声明函数
// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }
}
而应该尽量使用函数表达式，
// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}

const：声明一个只读的常量。一旦声明，就必须立即初始化常量的值就不能改变
const的作用域与let命令相同：只在声明所在的块级作用域内有效
const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动
即const可以存放常量，也可以存放对象（这里的对象可以动态添加属性），但是不能修改指向另一个对象












}

