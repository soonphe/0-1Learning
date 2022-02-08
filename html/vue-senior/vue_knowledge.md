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