### 在setup方法里面使用ref获取元素或者组件

#### 以前的做法

*App.vue*
```javascript
<template>
  <div class="app">
    <h2 ref="title">我是标题</h2>
    <button ref="btn">按钮</button>
  </div>
</template>

<script>
export default {
  //1. 以前写法
  mounted () {
    console.log(this.$refs.title)
    console.log(this.$refs.btn)
  }
}
</script>

<style scoped>
</style>
```

#### 现在的方法

*App.vue*
```javascript
<template>
  <div class="app">
    <!-- 获取元素 -->
    <!-- 2.3这里需要改成titleRef -->
    <h2 ref="titleRef">我是标题</h2>
    <button ref="btnRef">按钮</button>
    <button @click="getElement">先等挂载上再获取元素</button>
    <!-- 4.1 获取组件中的元素 -->
    <show-info ref="showInfoRef"></show-info>
  </div>
</template>

<script>
// 2.2 
// 3.1 引入onMounted
import { ref, onMounted } from 'vue'
import ShowInfo from './ShowInfo.vue'
export default {
  components: {
    ShowInfo
  },
  //1. 以前写法
  // mounted () {
  //   console.log(this.$refs.title)
  //   console.log(this.$refs.btn)
  // }

  //2. 现在写法
  setup () {
    //2.1 setup中没有this，并且这里最好不要使用querySelector
    // console.log(this.$refs)
    const titleRef = ref()
    const btnRef = ref()
    // 4.2 
    const showInfoRef = ref()
    // 2.4 这里还没有挂载到ref上面
    console.log(titleRef.value)
    // 2.5 等到挂载上去再获取元素
    function getElement () {
      console.log(titleRef.value)
    }
    // 3. mounted的生命周期函数
    // 3.2
    onMounted(() => {
      console.log(titleRef.value)
      // 4.3 
      console.log(showInfoRef.value)
      // 4.5 获取组件实例，调用其中的方法
      showInfoRef.value.showInfoFoo()
    })
    return {
      titleRef,
      getElement,
      btnRef,
      showInfoRef
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

*ShowInfo.vue*
```javascript
<template>
  <div>ShowInfo</div>
</template>

<script>
import { setgroups } from 'process';

export default {
  // 4.4获取组件中的方法
  methods: {
    showInfoFoo () {
      console.log("showInfo foo function")
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```
##### 现在需要将组件中的方法也放进 setup里面
```javascript
<template>
  <div>ShowInfo</div>
</template>

<script>
export default {
  // 4.4获取组件中的方法
  // methods: {
  //   showInfoFoo () {
  //     console.log("showInfo foo function")
  //   }
  // }
  // 5. 方法放在 setup里面
  setup () {
    function showInfoFoo () {
      console.log("showInfo foo function")
    }
    return {
      showInfoFoo
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

