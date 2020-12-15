# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Vue页面间传值

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