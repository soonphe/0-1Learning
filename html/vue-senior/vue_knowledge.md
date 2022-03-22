# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## vue进阶知识


### vue定时任务
参考：https://www.cnblogs.com/jin-zhe/p/10001236.html

js中定时器有两种，一个是循环执行setInterval，另一个是定时执行setTimeout

一、 循环执行（setInterval）
顾名思义，循环执行就是设置一个时间间隔，每过一段时间都会执行一次这个方法,直到这个定时器被销毁掉

用法是setInterval（“方法名或方法”，“延时”）， 第一个参数为方法名或者方法，注意为方法名的时候不要加括号,第二个参数为时间间隔

```
<template>
  <section>
    <h1>hello world~</h1>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        timer: '',
        value: 0
      };
    },
    methods: {
      get() {
        this.value ++;
        console.log(this.value);
      }
    },
    mounted() {
      this.timer = setInterval(this.get, 1000);
    },
    beforeDestroy() {
      clearInterval(this.timer);
    }
  };
</script>
```

二、 定时执行 （setTimeout）

定时执行setTimeout是设置一个时间，等待时间到达的时候只执行一次，但是执行完以后定时器还在，只是没有运行

用法是setTimeout(“方法名或方法”, “延时”); 第一个参数为方法名或者方法，注意为方法名的时候不要加括号,第二个参数为时间间隔

```
<template>
  <section>
    <h1>hello world~</h1>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        timer: '',
        value: 0
      };
    },
    methods: {
      get() {
        this.value ++;
        console.log(this.value);
      }
    },
    mounted() {
      this.timer = setTimeout(this.get, 1000);
    },
    beforeDestroy() {
      clearTimeout(this.timer);
    }
  };
</script>
```

### promise 和 async/await 区别
promise的用法
Promise,简单来说就是一个容器，里面保存着某个未来才会结束的时间(通常是一个异步操作的结果)
```
 let p = new Promise((resolve,reject) => {
        //...
        resolve('success')
    });
    
    p.then(result => {
        console.log(result);//success
    });
```
promise共有三个状态
pending（执行中）、success（成功）、rejected（失败）

链式调用
错误捕获
Promise.prototype.catch用于指定Promise状态变为rejected时的回调函数，可以认为是.then的简写形势，返回值跟.then一样
```
let p = new Promise((resolve,reject) => {
  reject('error');
});

p.catch(result => {
  console.log(result);
})
```

async、await
简洁：异步编程的最高境界就是不关心它是否是异步。async、await很好的解决了这一点，将异步强行转换为同步处理。
async/await与promise不存在谁代替谁的说法，因为async/await是寄生于Promise，Generater的语法糖。

用法
async用于申明一个function是异步的，而await可以认为是async wait的简写，等待一个异步方法执行完成。
规则：
1 async和await是配对使用的，await存在于async的内部。否则会报错
2 await表示在这里等待一个promise返回，再接下来执行
3 await后面跟着的应该是一个promise对象，（也可以不是，如果不是接下来也没什么意义了…）

写法：
```
async function demo() {undefined
  let result01 = await sleep(100);
  //上一个await执行之后才会执行下一句
  let result02 = await sleep(result01 + 100);
  let result03 = await sleep(result02 + 100);
  // console.log(result03);
  return result03;
}

demo().then(result => {
  console.log(result);
});
```

错误捕获
如果是reject状态，可以用try-catch捕捉
```
let p = new Promise((resolve,reject) => {
  setTimeout(() => {
    reject('error');
  },1000);
});

async function demo(params) {
  try {
    let result = await p;
  }catch(e) {
    console.log(e);
  }
}

demo();
```
区别：
1. promise是ES6，async/await是ES7
2. async/await相对于promise来讲，写法更加优雅
3. reject状态：
    1）promise错误可以通过catch来捕捉，建议尾部捕获错误，
    2）async/await既可以用.then又可以用try-catch捕捉





