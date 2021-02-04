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



# vux简用



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



## actions



`actions` 类似于 `mutations`，不同在于：

- `actions`提交的是`mutations`而不是直接变更状态
- `actions`中可以包含异步操作, `mutations`中绝对不允许出现异步
- `actions`中的回调函数的第一个参数是`context`, 是一个与`store`实例具有相同属性和方法的对象
- 此时,我们在`store`中添加`actions`属性, 其中`minusPriceAsync`采用`setTimeout`来模拟异步操作,延迟2s执行 该方法用于异步改变我们刚才在`mutaions`中定义的`minusPrice

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
        }
  	},
  	actions:{
  	    // 登出
        LogOut({ commit, state },{imgurl, name}) {
            return new Promise((resolve, reject) => {
                logout(state.token).then(() => {
                    commit('SET_TOKEN', '')
                    commit('SET_ROLES', [])
                    removeToken()
                    resolve()
                }).catch(error => {
                    reject(error)
                })
            })
        },
  	}
})
```

使用

```javascript
this.$store.dispatch('LogOut', {1,2}); //分发actions中的LogOut这个异步函数
```

# **JavaScript 代码技巧**



## **1. 多表达式多 if 判断**



我们可以在数组中存储多个值，并且可以使用数组include方法。

```javascript

// 长
if (x === 'abc' || x === 'def' || x === 'ghi' || x ==='jkl') {
  //logic
}
// 短
if (['abc', 'def', 'ghi', 'jkl'].includes(x)) {
  //logic
}
```



## **2. 简写 if else**



如果 if-else 的逻辑比较降低，可以使用下面这种方式镜像简写，当然也可以使用三元运算符来实现。

```javascript

// 长
let test: boolean;
if (x > 100) {
  test = true;
} else {
  test = false;
}
// 短
let test = (x > 10) ? true : false;
// 也可以直接这样
let test = x > 10;
```



## **3. 合并变量声明**



当我们声明多个同类型的变量时，可以像下面这样简写。

```
// 长 
let test1;
let test2 = 1;
// 短 
let test1, test2 = 1;
```



## **4. 合并变量赋值**



当我们处理多个变量并将不同的值分配给不同的变量时，这种方式非常有用。

```javascript
// 长 
let test1, test2, test3;
test1 = 1;
test2 = 2;
test3 = 3;
// 短 
let [test1, test2, test3] = [1, 2, 3];
```



## **5. && 运算符**



如果仅在变量值为 true 的情况下才调用函数，则可以使用 && 运算符。

```javascript
// 长 
if ( test1 ) { 
    callMethod(); 
} 
// 短 
test1 && callMethod();
```



## **6. 箭头函数**



```javascript
// 长  
function add(a, b) {   
  return a + b;
} 
// 短 
const add = (a, b) => a + b;
```



## **7. 短函数调用**



可以使用三元运算符来实现这些功能。

```javascript
const fun1 = () => console.log('fun1');
const fun2 = () => console.log('fun2');
// 长
let test = 1;
if (test == 1) {  
	fun1();
} else{  
	fun2();
}
// 短
(test === 1? fun1:fun2)();
```



## **8. Switch 简记法**



我们可以将条件保存在键值对象中，并可以根据条件使用。

```javascript

// 长
switch (data) {
  case 1:
    test1();
  break;

  case 2:
    test2();
  break;

  case 3:
    test();
  break;
  // And so on...
}

// 短
const data = {
  1: test1,
  2: test2,
  3: test
};

data[something] && data[something]();
```



## **9. 默认参数值**



```javascript

// 长
function add(test1, test2) {
  if (test1 === undefined)
    test1 = 1;
  if (test2 === undefined)
    test2 = 2;
  return test1 + test2;
}
// 短
const add = (test1 = 1, test2 = 2) => (test1 + test2);
```



## **10. 扩展运算符**



```javascript

// 长-合并数组
const data = [1, 2, 3];
const test = [4 ,5 , 6].concat(data);
// 短-合并数组
const data = [1, 2, 3];
const test = [4 ,5 , 6, ...data];


// 长-拷贝数组
const test1 = [1, 2, 3];
const test2 = test1.slice()
// 短-拷贝数组
const test1 = [1, 2, 3];
const test2 = [...test1];
```



## **11. 模版字符串**



```javascript

// 长
const welcome = 'Hi ' + test1 + ' ' + test2 + '.'
// 短
const welcome = `Hi ${test1} ${test2}`;

```



## 12. **简写对象**



```javascript

let test1 = 'a'; 
let test2 = 'b';
// 长 
let obj = {test1: test1, test2: test2}; 
// 短 
let obj = {test1, test2};
```



## **13. 在数组中查找最大值和最小值**



```javascript

const arr = [1, 2, 3]; 
Math.max(…arr); // 3
Math.min(…arr); // 1
```