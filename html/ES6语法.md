# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ES6语法

TypeScript和JavaScript的关系：
TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript


### let和const（）
let是块级变量，声明的变量仅在块级作用域内有效
const：声明一个只读的常量。一旦声明，就必须立即初始化常量的值就不能改变

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

### 数组

1、数组在ES6与ES5用法的异同

let arrayLike = {
'0' : 'T',
'1' : 'h',
'2' : 'i',
'3' : 'n',
'4' : 'k',
length: 5
};

// ES5的写法
const array1 = [].slice.call(arrayLike);
// ES6的写法
const array2 = Array.from(arrayLike);
console.log(array1);
console.log(array2);
输出：

[ 'T', 'h', 'i', 'n', 'k' ]
[ 'T', 'h', 'i', 'n', 'k' ]
只要是部署了Iterator接口的数据结构，Array.from都能将其转为数组。

2、新语法

for(let index of array2.keys()){
console.log(index);
}
for(let [index,elem] of array2.entries()){
console.log(index + ' - ' + elem);
}
keys()跟entries()是ES6提供的新方法，用于遍历数组。

### 函数

1、默认值

es6之前，js不能直接为函数的参数指定默认值。es6赋给默认值的例子如下：

'use strict';
function billboard(author='Linkin Park', song = 'numb'){
console.log(author + ' --- ' + song);
}
billboard();
billboard('Taylor swift', 'shake it off');
通常默认值为函数的未参数。

2、rest参数

ES6引入了rest参数，用于获取函数的多余参数，这样就不需要使用arguments对象了。

function album(author, ...songs){
console.log('author: ' + author);
for(let song of songs){
console.log(song);
}
}
album('Eason', '放', '收心操', '海胆');
3、替换apply方法

扩展运算符(…)可以展开数组，所以不再需要apply方法将数组转为函数的参数。

function Mayday(a, b ,c ,d, e){
console.log(a + ', '+ b + ', '+ c + ', '+ d + ', '+ e + ', ')
}
const args = ['Monster', 'Ashin', 'Stone', 'Matthew Tsai', 'Lau Ming'];

// es5写法
Mayday.apply(null,args);

// es6写法
Mayday(...args);
4、箭头函数

ES6允许使用箭头定义函数。

let sum = (male, female) => {
console.log('新浪瘫痪');
};
sum('deer', 'Traey Miley’);


### Symbol

1、概述

ES6引入了一种新的原始数据类型Symbol，表示独一无二的值。只要属性名是Symbol类型，可以保证不会与其他属性名产生冲突。

let Mayday = Symbol();
let SHE = Symbol();
let JayChou = Symbol('中国风');
let Beyond = Symbol('永远的beyond');

let JJ_Lin = Symbol.for('Singer');
let Leehom_Wang = Symbol.for('Singer');
console.log(JayChou === Beyond);  // false
console.log(Mayday === SHE);  // false
console.log(JJ_Lin === Leehom_Wang); // true
每一个symbol都不一样所以Mayday、SHE、JayChou、Beyond都是独一无二的。而用Symbol.for()可以做到使用同一个Symbol值，所以JJ_Lin和Leehom_Wang是一样的普通歌手。

2、消除魔法字符串

这个很像java中的Enum类型的用法。

const singerAttr = {
maestro: Symbol('change'),
only: Symbol('name'),
outstanding: Symbol(),
};
function getName(singer){
switch(singer){
case singerAttr.maestro:
console.log('Michael');
break;
case singerAttr.only:
console.log('JayChou');
break;
case singerAttr.outstanding:
console.log('JJ_Lin');
break;
}
}
getName(singerAttr.maestro);
getName(singerAttr.only);
getName(singerAttr.outstanding);
3、Symbol方法和内置的Symbol值

ES6可以定义自己使用的Symbol值，还提供了11个内置的Symbol值。

class Music {
[Symbol.sing](string){
return 'name : '+string;
}
}
const music = new Music();
console.log(music[Symbol.sing]('JJ_lin'));
console.log('a = ' + ‘JayChou'.search('a'));
Symbol.sing是自定的，search是内置的属性。

let obj = {
[Symbol.toPrimitive](singer){
switch(singer){
case 'number':
return 2;
case 'string':
return 'JayChou';
case 'default':
return 'JJ_Lin'
}
}
};
console.log(5 * obj);
重写对象的Symbol.toPrimitive属性。