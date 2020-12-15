
### prop和model的区别
prop(表单域model字段，即传入form组件的model中的字段)和model(表单数据对象)
```
<el-form ref="form" :model="form" label-width="80px">	//:model引用上层传入的form对象，同时真正被提交的form表单
//$refs 是所有注册过的ref的一个集合，
//ref="form"用来给元素或子组件引用注册信息，引用信息将会注册在父组件的$refs对象上，
//this.$refs.form指的就是这个form表单
  <el-form-item prop="name" label="活动名称">	//prop：在使用 validate、resetFields 方法的情况下，该属性是必填的
    <el-input v-model="form.name"></el-input>	//引用form对象的name字段
  </el-form-item>
```
  
value和 :value
:model和v-model的区别： :model是v-bind:model的缩写,
<child :model="msg"></child>这种只是将父组件的数据传递到了子组件，并没有实现子组件和父组件数据的双向绑定