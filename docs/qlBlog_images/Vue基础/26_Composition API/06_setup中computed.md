### Computed

```vue
<template>
  <div class="app">
    <h2>{{fullName}}</h2>
    <button @click="setFullName">设置fullName</button>
    <h2>{{scoreLevel}}</h2>
  </div>
</template>

<script>
import { reactive, ref, computed } from 'vue'
export default {
  setup () {
    //1. 定义数据
    const names = reactive({
      firstName: 'james',
      lastName: 'Lebron'
    })

    // computed 如果不写set get 那么就不用写成对象，写成函数形式
    // computed: {
    //   fullName(){

    //   }
    // }

    // 1. fullName的第一种方式，直接这样赋值就不是响应式的，可以添加 get 和 set 才行
    // const fullName = computed(() => {
    //   return names.firstName + '' + names.lastName
    // })

    // 2. fullName的第二种方式
    const fullName = computed({
      set: function (newValue) {
        const template = newValue.split("")
        names.firstName = template[0]
        names.lastName = template[1]
      },
      get: function () {
        return names.firstName + " " + names.lastName
      }
    })

    // 2.2 实现按钮方法
    function setFullName () {
      // 不能这样设置值，因为这里是const不能修改
      // fullName = 'zhang qilong'
      // 怎么修改呢？ computed返回的是一个ref，可以使用.value
      fullName.value = "Long King"
    }

    // 2. 定义score
    const score = ref(89)
    const scoreLevel = computed(() => {
      return score.value > 60 ? "及格" : "不及格"
    })

    return {
      names,
      fullName,
      scoreLevel,
      setFullName
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

