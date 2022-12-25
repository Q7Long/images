### Provide 和 inject

*App.vue*

```js
// 因为 vue 中组件间传数据需要遵守单向数据流的原则，所以我们在用父组件传值的时候最好是将数据设置成 readonly 这样组件使用 inject 接收的时候就不能对数据进行修改
 
// 我们在用 Provide 传值的时候，需要用 ref 定义好数据再传值，这样传递的值才是响应式数据，并且我们在设置响应式数据的时候最好是使用 ref，尤大推荐，如果是有关联的数据我们再使用 reactive，使用 ref 可以后期方便抽离
```

```javascript
<template>
  <div class="app">
    <div>AppContent:{{ name }}</div>
    <!-- 共享的数据是响应式的数据，父组件中修改name，子组件中name也会修改 -->
    <button @click="name = 'james'">app btn</button>
    <show-info></show-info>
  </div>
</template>

<script>
// 2. 引入
import { provide, ref } from "vue";
//3. 引入子组件
import ShowInfo from "./ShowInfo.vue";
export default {
  components: {
    ShowInfo,
  },
  setup() {
    const name = ref("zql");
    const age = ref("18");
    // 如果在这里需要给子组件传递值的话，这里最好共享的是响应式数据
    provide("name", readonly(name));
    // 父组件用 provide 子组件用 inject 接收传过去的值
    provide("age", readonly(age));
    return {
      name,
    };
  },
};
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped></style>

```

*ShowInfo.vue*

```javascript
<template>
  <div>ShowInfo:{{name}}--{{age}}</div>
</template>

<script>
// 4. 对父组件传入的值进行一个使用
import { inject } from 'vue';
// setup里面的 inject 不需要手动解包 但是在options api里面的 inject需要手动解包，否则就会报警告
// injected property "name" is a ref and will be auto-unwrapped and no longer needs `.value` in the next minor release
export default {
  // inject的 options api注入，那么就需要手动来解包name.value age.value
  // inject: ["name", "age"],
  setup () {
    const name = inject("name")
    const age = inject("age")
		// inject指定默认值
		const height = inject("height",1.8)
    return {
      name,
      age
    }
  }
}
</script>

<style scoped>
</style>
```