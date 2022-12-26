### Vue中的单向数据流

*App.vue*

```javascript
<template>
  <div class="app">
    <h2>App:{{info}}</h2>
    // 子组件接收数据，父组件在组件中传出
    // 需要注意的是，这里传入的是对象的引用，那么子组件修改的话，就是修改的原对象
    <show-info :info="info"></show-info>
  </div>
</template>

<script>
import ShowInfo from './ShowInfo.vue';
import { reactive } from 'vue'
export default {
  components: {
    ShowInfo
  },
  setup () {
    // 本地定义多个数据，都需要传递给给子组件
    // name/age/height
    const info = reactive({
      name: 'zql',
      age: 18,
      height: 1.88
    })
    return {
      info
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
  <div>
    <h2>ShowInfo:{{ info }}</h2>
    <button @click="changeShowInfo">修改ShowInfo里面的info数据</button>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => ({})
    }
  },
  methods: {
    changeShowInfo () {
      this.info.name = 'james'
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style><template>
  <div>
    <h2>ShowInfo:{{ info }}</h2>
    <button @click="changeShowInfo">修改ShowInfo里面的info数据</button>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => ({})
    }
  },
  methods: {
    changeShowInfo () {
      this.info.name = 'james'
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

*但是有个问题就是，这样组件间传值是传的对象的引用，如果子组件中对数据进行了修改，那么也会影响到父组件*

**弊端**
*虽然这样是可行的，但是却不怎么规范，因为这样就违反了vue中的单项数据流(规范)，子组件拿到数据后只能使用，不能修改*
*如果确实要修改，那么应该将事件传递出去，由父组件来修改数据。比如在开发中，不止有一个子组件，应该是有很多个，那么数据给了子组件之后呢？那么也就意味着每个子组件都是可以对数据进行修改的，如果有一天父组件中数据被修改了，就不知道是被哪个子组件修改了*
*如果是传递事件给父组件，然后父组件拿到@ShowInfoBtnClick就知道是哪个子组件修改了，再对数据进行修改*

*react:react使用的是非常灵活的，但是它有一个重要的原则，任何一个组件都应该像纯函数一样，不能修改传入的props。react里面也是组件化编程，那么也就意味着，react里面也是有类似于App和ShowInfo的组件的，那么当子组件接收到数据的时候，那么只能对数据进行引用，不能修改*

***怎么办***

*App.vue*

```javascript
<template>
  <div class="app">
    <h2>App:{{info}}</h2>
    <!-- 子组件接收数据，父组件在组件中传出
    需要注意的是，这里传入的是对象的引用，那么子组件修改的话，就是修改的原对象 -->
    <show-info :info="info"
               @changeInfo="changeInfoName"></show-info>
  </div>
</template>

<script>
import ShowInfo from './ShowInfo.vue';
import { reactive } from 'vue'
export default {
  components: {
    ShowInfo
  },
  setup () {
    // 本地定义多个数据，都需要传递给给子组件
    // name/age/height
    const info = reactive({
      name: 'zql',
      age: 18,
      height: 1.88
    })
    // 自组件传值过来，这里使用一个函数接收，在这里对数据进行修改
    // 这样虽然麻烦点，但是代码符合规范，知道是哪个组件修改了数据
    function changeInfoName (payload) {
      info.name = payload
    }
    return {
      info,
      changeInfoName
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
  <div>
    <h2>ShowInfo:{{ info }}</h2>
    <button @click="showInfoBtnClick">修改ShowInfo里面的info数据</button>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => ({})
    }
  },
  emits: ['changeInfo'],
  setup (props, context) {
    function showInfoBtnClick () {
      // 但是setup里面是没有this的，那么就需要在setup里面传入两个参数
      // this.$emit("changeInfo", 'kobe')
      context.emit("changeInfo", 'james')
    }
    return {
      showInfoBtnClick
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

#### readonly强制函数

***认识readonly***

- 我们通过 reactive或者ref可以获取到一个响应式的对象，但是在某些情况下，我们传入给其他地方(组件)的这个响应式对象希望在另外一个地方(组件)被使用，但是不能修改，这个时候如何防止这种情况的出现呢？
  - Vue3为我们提供了 <font color="red">readonly的方法</font>
  - <font color="red">readonly会返回原始对象的只读代理</font>(也就是它依然是一个Proxy，这是一个 <font color="red">Proxy的set方法被劫持</font>，并不能对其进行修改)

- 在开发中常见的readonly方法会传入三个类型的参数
  - 类型一：<font color="red">普通对象</font>
  - 类型二：<font color="red">reactive返回的对象</font>
  - 类型三：<font color="red">ref的对象</font>

#### readonly的使用

- 在readonly的使用过程中，有如下规则
  - readonly <font color="red">返回的对象都是不允许修改的</font>
  - 但是经过readonly处理的 <font color="red">原来的对象</font>是允许被修改的
    - 比如 const info = readonly(obj), <font color="red">info对象是不允许被修改的</font>
    - 当 <font color="red">obj被修改时，readonly返回的info对象也会被修改</font>
    - 但是我们 <font color="red">不能去修改readonly返回的对象info</font>
- 其实本质上就是 <font color="red">readonly返回的对象的setter方法</font>被劫持了而已

***如果写法上遵守了单项数据流的情况下，也是可以不写readonly的***

*App.vue*

```javascript
<template>
  <div class="app">
    <h2>App:{{info}}</h2>
    <show-info :info="info"
               :roInfo="roInfo"
               @changeRoInfoName="changeRoInfoName"
               @changeInfo="changeInfoName"></show-info>
  </div>
</template>

<script>
import ShowInfo from './ShowInfo.vue';
// readonly创建出来的数据也是响应式的，但是不允许对 readonly的数据进行修改
import { reactive, readonly } from 'vue'
export default {
  components: {
    ShowInfo
  },
  setup () {
    const info = reactive({
      name: 'zql',
      age: 18,
      height: 1.88
    })
    function changeInfoName (payload) {
      info.name = payload
    }

    //1. 使用readonly包裹info
    const roInfo = readonly(info)
    // 6. 对事件进行监听实现
    function changeRoInfoName (payload) {
      info.name = payload
    }
    return {
      info,
      changeInfoName,
      roInfo,
      changeRoInfoName
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
  <div>
    <h2>ShowInfo:{{ info }}</h2>
    <button @click="showInfoBtnClick">修改ShowInfo里面的info数据</button>
    <hr>
    <h2>展示readonly数据:{{roInfo}}</h2>
    // 2. 代码会无效(报警告) Set operation on key "name" failed: target is readonly. Proxy {name: 'zql', age: 18, height: 1.88}
    <button @click="roInfo.name = 'james'">ShowInfo按钮</button>
    // 3.正确的做法 
    <button @click="roInfoBtnClick">roInfo按钮</button>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => ({})
    },
    // 这次传入的是 readonly的数据
    roInfo: {
      type: Object,
      default: () => ({

      })
    }
  },
  // 5.
  emits: ['changeInfo', 'changeRoInfoName'],
  setup (props, context) {
    function showInfoBtnClick () {
      // 但是setup里面是没有this的，那么就需要在setup里面传入两个参数
      // this.$emit("changeInfo", 'kobe')
      context.emit("changeInfo", 'james')
    }

    //4. 绑定事件
    function roInfoBtnClick () {
      context.emit('changeRoInfoName', 'kobe')
    }
    return {
      showInfoBtnClick,
      roInfoBtnClick
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

#### readonly的作用

![readonly的作用](D:/workspace/QiLongZhang/Vue/Q7Long/LEARN%2520VUEJS/04_learn_composition/src/img/readonly%25E7%259A%2584%25E4%25BD%259C%25E7%2594%25A8.png)

![isProxy](D:/workspace/QiLongZhang/Vue/Q7Long/LEARN%2520VUEJS/04_learn_composition/src/img/isProxy.png)

![isReactive](D:/workspace/QiLongZhang/Vue/Q7Long/LEARN%2520VUEJS/04_learn_composition/src/img/isReactive.png)

![reactive判断的API](D:/workspace/QiLongZhang/Vue/Q7Long/LEARN%2520VUEJS/04_learn_composition/src/img/Reactive%25E5%2588%25A4%25E6%2596%25AD%25E7%259A%2584API.png)