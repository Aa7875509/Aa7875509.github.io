# 去掉 console.log

<!-- 运行 -->

## vue-cli2

1.  项目 `build` 下面 `webpack.prod.config.js` 文件中:

```javascript

plugins: [
    new webpack.DefinePlugin({
      	'process.env': env
    }),
    new UglifyJsPlugin({
		uglifyOptions: {
			compress: {
			warnings: false,
			//drop_console  传递true以放弃对控制台的调用。*功能
			drop_console: true,
			// pure_funces 禁用console.log函数
			pure_funcs: ['console.log']
			}
		},
		sourceMap: config.build.productionSourceMap,
		parallel: true
    }),

    ......
]
```

注意：搜索 `plugins` 这个下面，`UglifyJsPlugin` 默认已经安装了,只需要添加 `drop_console: true, pure_funcs: ['console.log']`

代码参照如上即可, 我就是这样添加的

## vue-cli3

1. 只有 `vue.config.js` 里配置.

```javascript
   const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

   configureWebpack: {
     //注释console
		optimization: {
			minimizer: [
				new UglifyJsPlugin({
					uglifyOptions: {
						compress: {
							// warnings: false,
							drop_console: false, //注释console
							drop_debugger: false,
							pure_funcs: ["console.log"], //移除console
						},
					},
				}),
			];
		}
   }
```

2. 需先安装 `uglifyjs-webpack-plugin` 插件

```javascript
   	npm install uglifyjs-webpack-plugin --save-dev
```

# vue 方法 ab 后调用 C

`promise` 是什么？
1、主要用于异步计算
2、可以将异步操作队列化，按照期望的顺序执行，返回符合预期的结果
3、可以在对象之间传递和操作 `promise`，帮助我们处理队列

```javascript
	mounted() {
        Promise.all([this.caa(),this.cbb()]).then((res)=>{
            console.log(res)//输出["caa", "cbb"]
        })
    },
    methods: {
        async caa(){
            return new Promise((resolve, reject) => {
                // logout(state.token).then((response) => {
                    resolve('caa')
                // }).catch(error => {
                //     reject(error)
                // })
            })
        },
         async cbb(){
            return new Promise((resolve, reject) => {
                // logout(state.token).then((response) => {
                    resolve('cbb')
                // }).catch(error => {
                //     reject(error)
                // })
            })
        },
    }
```

# vux

## state

`state`就是`Vuex`中的公共的状态, 我是将`state`看作是所有组件的`data`， 用于保存所有组件的公共数据.

```javascript
const store = new Vuex.Store({
  state: {
    products: [
      { name: "鼠标", price: 20 },
      { name: "键盘", price: 40 },
      { name: "耳机", price: 60 },
      { name: "显示屏", price: 80 },
    ],
  },
});
```

引用

```javascript
	this.$store.state.products;
```

## **Getters**

同步

我将`getters`属性理解为所有组件的`computed`属性, 也就是计算属性. `vuex` 的官方文档也是说到可以将 `getter` 理解为 `store` 的计算属性, `getters` 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

```javascript
//main.js
const store = new Vuex.Store({
	state:{
		products:'',
	},
	getters:{ //添加getters
		saleProducts: (state,val) => {
			state.products = var
		}
	}
})
```

使用

```javascript
this.$store.getters.saleProducts(val);
```

## **Mutations**

异步

我将`mutaions`理解为`store`中的`methods`, 两个参数 第一个参数是`state`, 第二参数是`val`是自定义参数.

```javascript
//main.js
const store = new Vuex.Store({
	state:{
		products:'',
	},
	getters:{ //添加getters
		saleProducts: (state,val) => {
			state.products = var
		}
	},
    mutaions:{ //添加mutations
        minusPrice ({commit,state}, val ) {
			commit('saleProducts',val)//可修改getters.saleProducts
			return new Promise((resolve, reject) => {
				logout(state.token).then(() => {
					resolve()
				}).catch(error => {
					reject(error)
				})
			})
        }
  	}
})
```

使用

```javascript
this.$store.commit("minusPrice", 2);
```
