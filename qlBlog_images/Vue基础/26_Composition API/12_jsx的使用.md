```js
// jsx的代码其实还是通过被 babel 转化成了 h() 函数的代码
```

04_learn_composition\src\15_jsx的使用\App.vue

```vue
<script>
import HelloWorld from "./HelloWorld.vue";
export default {
  data() {
    return {
      counter: 0,
    };
  },
  render() {
    const increment = () => this.counter++;
    const decrement = () => this.counter--;
    // return h("div", null, "Hello World");
    // 第二种方式
    return (
      <div>
        <h2>当前计数：{this.counter}</h2>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <HelloWorld>{{ default: (props) => <button>按钮</button> }}</HelloWorld>
      </div>
    );
  },
};
</script>

<style scoped lang="less"></style>
```

04_learn_composition\src\15_jsx的使用\HelloWorld.vue

```vue
<script>
export default {
  render() {
    return (
      <div>
        <h2>HelloWorld</h2>
        {this.$slots.default ? this.$slots.default() : <span>哈哈哈哈</span>}
      </div>
    );
  },
};
</script>

<style scoped lang="less"></style>
```

页面效果如下

![image-20221209150110627](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5CComposition%20API%5C12_jsx%E7%9A%84%E4%BD%BF%E7%94%A8.assets%5Cimage-20221209150110627.png)