### 前端路由
```js 
// 前端映射关系   前端路由
路径 -> 组件
/home -> Home.vue
/about -> About.vue
/category -> Category

http://baidu.com/home
http://baidu.com/about

// 但是这样做，输入一个地址的时候，浏览器会去静态服务器请求静态资源，但是我们想要做到的就是，在我们改变路径的时候不要随便去静态服务器请求静态资源，如果去请求静态资源，那么所有的资源都会重新加载一遍，但我们不需要这么做
// 两种方式
// 1. 后面的路径写成 hash值， http://baidu.com#home 当我们这样改变的时候，不会去服务器请求静态资源的
// 2. H5中的 history 模式 
```

![image-20221222161115592](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C01_%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%94%B1.assets%5Cimage-20221222161115592.png)

```js
// Hash模式的底层实现代码
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
	<style>
		*{
			margin: 0;
			padding: 0;
		}
		a,.content{
			font-size: 23px;
			margin-left: 20px;
		}
	</style>
</head>
<body>
	<div id="app">
	<a href="#/home">Home</a>
	<a href="#/about">About</a>

	<div class="content">
		default
	</div>
</div>

	<script>
		const contentEl = document.querySelector('.content');
		// 监听浏览器的 hash 值的改变
		window.addEventListener("hashchange",()=>{
			console.log("hashchange")
			// location.hash 可以拿到 hash 值
			switch(location.hash){
				case "#/home":
				contentEl.innerHTML = "Home"
				break;
				case "#/about":
				contentEl.innerHTML = "about"
				break;
				default:
				contentEl.innerHTML = "default"
			}
		})
	</script>
</body>
</html>
```

![image-20221222164743182](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C01_%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%94%B1.assets%5Cimage-20221222164743182.png)

![image-20221222172752596](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C01_%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%94%B1.assets%5Cimage-20221222172752596.png)

```js
// History的底层实现代码
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>history原理</title>
</head>
<body>
	<div id="app">
		<a href="#/home">Home</a>
		<a href="#/about">About</a>
	
		<div class="content">
			default
		</div>
	</div>
	<script>
		const contentEl = document.querySelector('.content');

		// 封装重复代码
		const changeContent = ()=>{
			switch(location.pathname){
					case "/home":
						contentEl.innerHTML = "Home";
						break
					case "/about":
						contentEl.innerHTML = "About";
						break
					default:
						contentEl.innerHTML = "default";
				} 
		}
		
		const aEls = document.getElementsByTagName("a");
		for(let aEl of aEls){
			aEl.addEventListener("click",e=>{
				// preventDefault() 方法将阻止a标签上面的链接打开跟随打开 URL。
				e.preventDefault();
				
				const href = aEl.getAttribute("href");
				
				// push操作 使用新的路径
				history.pushState({},"",href)
				

				// 在这里与 hash中的 location.hash 不同这里是 location.pathname
				console.log(location.pathname)
				
				// 直接调用封装的函数
				changeContent()
			})
		}

		// 路径的回退
		window.addEventListener("popstate",changeContent)

	</script>
</body>
</html>
```

