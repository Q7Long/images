### Ref Api
1. reactive API对传入的类型是有限制的，它要求我们必须传入的是一个对象或者数组类型
- 如果我们传入了一个 <font color="red">基本数据类型(String、Number、Boolean)</font>会报一个警告
```javascript
value cannot be made reactive:Hello World
```
2. 这个时候Vue3给我们提供了另外一个API:Ref Api
- ref会返回一个 <font color="red">可变的响应式对象</font>，该对象作为一个 <font color="red">响应式的引用</font>维护着它 <font color="red">内部的值</font>，这就是 <font color="red">ref名称的来源</font>
- 它内部的值是 <font color="red">在ref的value属性</font>中被维护的

***这里有两个注意事项***
- 在 <font color="red">模板中引入ref的值</font>时，Vue会 <font color="red">自动帮助我们进行解包</font>操作，所以 <font color="red">并不需要在模板中通过ref.value(message.value)</font>的方式来使用
- 但是在 <font color="red">setup函数内部</font>，它依然是一个 <font color="red">ref引用</font>，所以对其进行操作时，我们依然需要 <font color="red">使用ref.value的方式(message.value)</font>
```javascript
<template>
  <div class="app">
    <!-- 默认情况下在template中使用ref时候，vue会自动对其进行解包(解包就是取出其中的value) -->
    <h2>message:{{message}}</h2>
    <button @click="changeMessage">修改message</button>
    <!-- 4. ref浅层解包 这里现在是可以的，但是在3.1的版本是不可以的-->
    <h2>当前计数：{{info.message}}</h2>
    <!-- 5.在以前的版本中，是需要使用 info.message.value的 -->
    <!-- 6.但是这里就不可以了 
    <button @click="info.message ='不可以被修改'">这里就必须要用value才行</button>
		-->
    <button @click="info.message.value ='不加value不可以修改'">这里就必须要用value才行</button>
  </div>
</template>

<script>
// 导入 ref 函数
import { ref } from 'vue'
export default {
  setup () {
    const message = ref('ref函数定义数据')
    const changeMessage = () => {
      // 这里如果这样修改， message = "ref定义的数据也是响应式的"
      // 那么就会报错TypeError: Assignment to constant variable.
      // 1. 这里是 const 不能直接修改const定义的值
      // 2. 这里返回的不是字符串了，而是一个ref对象，我们是要取到ref里面值的
      message.value = "ref定义的数据也是响应式的"
    }

    //3. ref是浅层解包
    const info = {
      message
    }
    return {
      message,
      changeMessage,
      info
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```
#### 开发中的ref和reactive使用场景总结

![image-20221208111922061](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C26_Composition%20API%5C02_ref%20API.assets%5Cimage-20221208111922061.png)

```javascript
<template>
  <div class="app">
    <form>
      <label for="ipt">
        账号：<input type="text"
               name="ipt"
               v-model="account.username">
        密码：<input type="text"
               v-model="account.password">
      </label>
    </form>
  </div>
</template>

<script>
import { onMounted, reactive, ref } from 'vue'
export default {
  setup () {
    // 定义响应式数据：reactive/ref ref用的是最多的
    const info = ref({
      username: "zql",
      password: "123456"
    })

    // 1. reactive的应用场景
    // 1.1 条件一: reactive应用于本地的数据，自己定义的不是从服务器获取的
    // 比如这里的 username 和 password 一般选择时候使用 reactive 不是必须
    // 1.2 条件二：多个数据之间是有关系/联系的(聚合的数据，将它们放在一起会产生某种意义)
    const account = reactive({
      username: "zhangqilong",
      password: "123456",
      //1.3 组合在一起有意义，这里最好不要出现无关的数据
      // productList: ['apple', 'banana']
    })

    // 2. ref的应用场景，其他的场景基本都用ref(computed)
    // 2.1 定义本地的一些简单数据
    const message = ref("Hello World")
    const counter = ref(0)

    // 2. 定义从网络中获取的数据也是使用ref
    // const musics =

    // 3. 从网络中获取数据
    // 3.2 const musics = reactive([])
    // 3.3 用ref定义一个空数组
    const musics = ref([])
    onMounted(() => {
      const serverMusics = ['海阔天空', '小苹果', 'Mine Mine']
      // 3.1 如果使用reactive定义数据，那么不能打.添加，一种办法for循环，太浪费性能了
      // 3.4 这样就简单了
      musics.value = serverMusics
    })
    return {
      info,
      account,
      message,
      counter
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```