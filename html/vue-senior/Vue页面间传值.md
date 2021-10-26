# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Vue页面间传值和组件通信

### 1.vuex传值——index界面使用mapActions存，add界面使用mapState取值
    methods: {
      ...mapActions([ 'saveAdvert', 'clearAdvert']),
某方法中引用this.saveAdvert(row)

    computed: {	//计算属性基于数据之上的数据（有缓存），不同于watch在数据变化时做一些事情
      ...mapState({
        form: state => state.Advert.advert,
      })
    },


### 2.路由传值——query/params（弊端：不是路由跳转的界面无法使用）
注：实用params去传值的时候，在页面刷新时，参数会消失，用query则不会有这个问题。
this.$router.push({
          /**
           * 页面间传值 ①使用路由带参数 ②使用vuex
           */
          path: '/advertSponsor/add'
          // 由于动态路由也是传递params的，所以在 this.$router.push() 方法中 path不能和params一起使用，否则params将无效。需要用name来指定页面
          // path: ({path: '/advert/add', params: {typeList: this.typeList}}) 错误
          // 通过路由名称跳转，携带参数（已成功）
          // name: 'advertAdd', params: {typeList: this.typeList}
        })


### 3.通过$parent,$chlidren等方法调取用层级关系的组件内的数据和方法（弊端：耦合性太高）
this.$parent.$data.id  //获取父元素data中的id
this.$children.$data.id  //获取父元素data中的id


### 4.使用eventbus
全局定义：window.eventBus = new Vue();
发送数据：eventBus.$emit('eventBusName', id);
用on接受该值（或对象）：eventBus.$on('eventBusName', function(val) { 　　console.log(val)})
关闭eventbus：eventBus.$off('eventBusName');

---

### 父组件向子组件传递数据
在vue中，父组件往子组件传递参数都是通过属性props的形式来传递的

父组件：
<parent>
    <child :child-com="content"></child> 
</parent>

子组件：
第一种方法
props: ['childCom']
第二种方法
props: {
    childCom: String //这里指定了字符串类型，如果类型不一致会警告的哦
}
第三种方法
props: {
    childCom: {
        type: String,
        default: 'sichaoyun' 
    }
}

示例：
```

父组件：<child :inputName="name">
子组件
  props: {
    inputName: { //注意这里用驼峰写法哦
      type: String,
      default: 'chart'
    }
  }
```

### 子组件向父组件传递数据
子组件向父组件传值$emit

子组件
<template>
    <div @click="open"></div>
</template>
methods: {
   open() {
        this.$emit('showbox','the msg'); //触发showbox方法，'the msg'为向父组件传递的数据
    }
}
父组件——这里用v-on:showbox / @showbox都行
<child @showbox="toshow" :msg="msg"></child> //监听子组件触发的showbox事件,然后调用toshow方法
methods: {
    toshow(msg) {
        this.msg = msg;
    }
}

### 兄弟组件通信：
我们可以实例化一个vue实例，相当于一个第三方
**main.js**
let vm = new Vue(); //创建一个新实例/也可以称之为事件中心
组件他哥
<div @click="ge"></div>
methods: {
    ge() {
        vm.$emit('blur','sichaoyun'); //触发事件
    }
}
组件小弟接受大哥命令
<div></div>
created() {
  vm.$on('blur', (arg) => { 
        this.test= arg; // 接收
    });
}