### Reactive API
*想要让多个数据都变成响应式的话，就可以使用reactive(比如账号，密码...)，reactive可以定义复杂类型的数据，如果想要定义简单类型的数据就可以使用其他方式*
```javascript
<template>
  <div class="app">
    <h2>message:{{message}}</h2>
    <button @click="changeMessage">修改message</button>
    <hr>
    <h2>账号：{{ account.username }}</h2>
    <h2>密码：{{ account.password }}</h2>
    <button @click="changeAccount">修改账号</button>
  </div>
</template>

<script>
// 3. 导入 reactive函数
import { reactive } from 'vue'
export default {
  setup () {
    //1. 定义数据，但是这样定义的数据不是响应式的
    let message = 'Hello World'

    // 2. 定义响应式数据
    // 2.1 reactive函数:定义复杂数据类型的数据，比如账号，密码
    const account = reactive({
      username: 'zql',
      password: "123456"
    })

    // 那么这是什么原因呢？为什么就可以变成响应式数据，普通数据定义是没有办法被劫持的
    // 这是因为我们使用reactive函数处理我们的数据之后，数据再次被使用时就会进行依赖收集
    // 比如之前我们 data中的数据就是会被vue劫持，然后数据一旦发生改变，就会被响应到template上
    const changeAccount = () => {
      account.username = "zhangqilong"
    }

    const changeMessage = () => {
      message = "你好啊"
      console.log(message)
    }
    // 这里定义完数据必须要返回
    return {
      message,
      changeMessage,
      account,
      changeAccount
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```
1. 如果想在setup中定义的数据提供响应式的特性，那么我们可以***使用reactive的函数***
2. 那么这是什么原因呢？为什么就可以变成响应式的呢？
	- 这是因为当我们 <font color="red">使用reactive函数处理我们的数据之后</font>，数据再次被使用时就会进行依赖收集
	- 当 <font color="red">数据发生改变时</font>，所有 <font color="red">收集到的依赖</font>都是 <font color="red">进行对应的响应式操作</font>(比如界面更新)
	- 事实上，我们编写的 <font color="red">data选项</font>	，也是在内部 <font color="red">交给了reactive函数</font>将其变成响应式对象的
