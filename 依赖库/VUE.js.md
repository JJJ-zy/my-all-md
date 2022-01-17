# VUE.js

## 简介

```tex
构建用户界面的渐进式的js框架
渐进式：可以根据用户需求选择使用库的复杂性
```

## 特点

```tex
1.采用组件化模式，提高代码复用率，提高效率，更好维护
2.声明式编码，让编码人员不需要操作dom，实现功能
3.采用虚拟dom+优秀的Diff算法，尽量复用Dom节点
```

## 模板语法

### 插值语法

```tex
用于解析标签体内容
{{表达式}}
```

### 指令语法

```tex
用于解析标签（包括属性，内容，事件等）
v-bind:href="表达式"   v-bind:  简写---> :  
```

## 数据绑定

```tex
v-bind单向数据绑定，前端数据不能改变控制台数据
v-model双向数据绑定，前端数据可以改变控制台数据，只能用于表单类元素   简写 v-model:value -->v-model
```

## el和data的两种写法

### el

```tex
1.new vue实例的时候作为参数
2.使用实例.$mount(el),更灵活，比如定时器
```

### data

```tex
1.data:{}
2.data:function(){
	return:{
		name:''
	}                      //组件中必须使用函数式
}
简写
data(){
	return:{
		name:''
	}
}
```

## MVVM

```tex
model-view-viewmodel
数据，视图
数据和视图的绑定
```

## 数据代理

### object.defineproperty

```javascript
let person = {
    name:'张三',
    sex:'男'
}
object.defineproperty(person,'age',{
    value:'18', //age的值
    enumerable:true, //控制添加的属性是否可以被遍历出来，默认是false不可以，下面三个操作不会取到该属性。
                            /**
                           	* for..in循环
                            * Object.keys方法
                            * JSON.stringify方法
                            */
    writable:true,  //控制属性值是否可以被修改，默认false不可以
    configurable:true  //控制属性是否可以被删除，默认false不可以
    
    //高级
    get:function(){  //简写get(){}
    	return 变量名      //get的作用:将变量绑定到key上，key调用值时就会调用变量，普通对象中的key放置的变量只会读取一次
	}
})
```

### 定义

```javascript
通过一个对象代理另一个对象中属性的操作(读写)
通过get和set方法完成对对象属性的代理
```

### vue中的数据代理

```javascript
创建vue实例对象vm，vue通过数据代理将data对象中的所有属性交给vm中的_data,并且为_data中的每一个属性添加getter和setter方法
```



## 事件处理

### 基本事件

```tex
v-on:+事件  例如v-on:click   简写  @click
事件方法要放到vue实例中的methods中
```

### 传参函数

```javascript
1.函数名($event,num)  $event是占位符，防止event对象消失
2.使用data-参数名   使用event.target.dataset.参数名获取参数
```

### 事件修饰符

```tex
可以使用多个，用.连续写
```



#### 阻止默认行为

```javascript
1.js中event.preventDefault
2.vue中标签属性@click.prevent=""
```

#### 阻止事件冒泡

```javascript
@click.stop=""   例如div中的button有相同点击事件，点击button会有两次弹窗，阻止弹窗冒泡，谁需要弹，在谁身上阻止下次冒泡
```

#### 事件只触发一次

```javascript
@click.once=""    事件只能用一次
```

#### 使用事件的捕获模式

```javascript
@click.capture=""    从外向内捕获，从内向外冒泡，可以设置事件在捕获阶段执行
```

#### 只有操作event和target时触发

```javascript
@click.self=""   点谁谁触发
```

#### 事件的默认行为立即执行，无需等待回调函数执行完毕

```javascript
@click.passive=""  直接执行事件默认行为，不需要等待函数执行完毕
```

### 键盘事件

```tex
@keyup.enter   抬起enter执行函数
```

#### 常用按键别名

```tex
回车	enter
删除	delete   删除和退格键
退出	esc
空格	space
换行	tab  适合keydown     keyup之前焦点已经切走，函数触发不了
上	up
下	down
左	left
右	right

未提供别名的按键可以通过获取key值去绑定   多个单词组成的key值注意转为两个单词拼接短横线命名kebab-case
```

#### 特殊按键

```tex
ctrl,alt,shift,win
配合keyup使用时，按住其中一个键，再按其他组合键，组合键松开触发事件
配合keydown使用时，正常触发事件

自定义键名 Vue.config.keyCodes.键名 = keyCode
```



## 计算属性

### computed计算的

```tex
vue属性computed
computed:{
	fullname:{
		get(){
			return 
		},
		set(value){
		
		}
	}
}

优点: 缓存机制，依赖的数据不变就调用缓存，依赖数据变了，计算属性get方法重新调用

简写
computed:{
	//get方式
	fullname(){
		return 
	}
}
```

## 监视属性

### watch

```javascript
watch:{
	需要监视的属性:{
		//默认为false,为true时，初始化页面时直接调用handler
		immediate:false
		//监视执行函数，属性改变时调用
		handler(newValue,oldValue){
			function body
		}
	}
}

也可以在vm外部
使用vm.$watch('需要监视的属性'，{
	//默认为false,为true时，初始化页面时直接调用handler
	immediate:false
    //vue中的watch监视多级数据改变，默认监视一层，默认值false
    deep:true
	//监视执行函数，属性改变时调用
	handler(newValue,oldValue){
		function body
	}
})
```

### 简写

```javascript
watch:{  //当不需要handler之外的属性时
	需要监视的属性(newValue,oldValue){
		function body
	}
}

vm.$watch('监视属性',function(newValue,oldValue){
	function body
})
```

## 计算属性和监视属性的区别

```tex
相同:计算属性能完成的功能，watch也可以完成
不同:watch可以做异步操作(延时)，但同样可以实现的功能，计算属性更简便
	
	普通函数和箭头函数的使用时机
	所有被vue直接管理的函数，最好都写成普通函数,这样this是vm
	vue中不被vue管理的函数最好写成箭头函数()=>{}  （定时器的回调函数，ajax的回调函数）,这样this找不到对象，会向外找到vm函数
```

## 绑定样式

### 绑定class

```tex
1.字符串写法，单个样式，或多个样式个数确定，  :class=""    会和class自动结合
2.数组写法，个数不确定，名称不确定        vm中设置数组data  arr:['','','']
3.对象写法，个数确定，名称确定，不确定用不用    vm中设置对象data   obj:{'':true/false}
```

### 绑定style

```tex
:style="对象属性"
对象中的key:value中的key为正常css属性的名称，连字符用驼峰命名替换
例子:
:style="{'fontsize':data属性+'px'}"
:style="obj data属性对象"
:style="对象属性数组"
```

## 条件渲染

### v-show

```tex
组件隐藏
v-show="false" ==> display:none
v-show="true" ==> display:block
```

### v-if

```tex
条件渲染
v-if="false"  ==>组件不存在
v-if="true" ==> 组件添加进来

v-else-if="false/true/表达式"

v-else
```

### template

```tex
template标签包裹的内容，template不会对内容造成影响，页面渲染时会自动删除template
但是template只能和v-if一起使用
```

## 列表渲染(循环)

### v-for

```javascript
//数组遍历
v-for = "形参(v,index) in/of 数组对象"  :key="唯一标识(key) 形参.id/(index)"
//对象遍历
v-for = "(v,k) in/of 对象" :key="k"
//字符串遍历
v-for="(v,index) in/of 字符串" :key="index"
//次数遍历
v-for="(v,index) in/of 5" :key="index"
```

### key

![image-20210809094859807](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210809094859807.png)

```tex
当新数据破坏原有数据的顺序时，index会造成虚拟dom对比不一样，导致页面数据不一样
按顺序可以，因为index每次都是从0开始

当对象id作为key时，每次都从对象中拿id,id和对象是绑定的，不管怎么换都不变
```

![image-20210809095226244](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210809095226244.png)

```tex
id可以提高效率,提高dom的复用，减少dom的创建，最好使用key
```

## 列表过滤filter

```javascript
filter((p)=>{
    return 判断条件为真返回  indexof()包含关系
})
```

## 列表排序sort

```javascript
//自定义sort规则，传两个参数，返回值为正的，交换位置，为负的位置不变
sort((a,b)=>{
    return a-b 升序  b-a 降序
})
```

## Vue底层监测

### 对象监测Vue.set() 和 vm.$set()

```tex
程序固定后，正常添加的是写死的属性
通过以上两个方法添加的是动态的
但是不能直接添加到vm实例上
方法属性:target目标(不能是vm),key,value
```

### 数组监测

```tex
Vue包装了七个数组方法，只有调用数组方法的时候才会动态改变数据
push,pop,shift,unshift,splice,sort,reverse
因为Vue不会对数组中的每个属性创建set和get方法
也可以通过filter过滤之后重新赋值给数组，直接改变整个数组
```

## 收集表单数据

```tex
普通输入框表单，收集value值
单选框收集value值,用法  v-model="属性名"  value="值"
多选框收集value值,用法  v-model="属性名"  value="值"   data中字符串收集checked boolean值，数组收集value值
```

### 修饰符

```tex
number,将收集到的数据转为数字
lazy,失去焦点时收集数据
trim,去除首尾空格
```

## 过滤器

```tex
格式化数据
```

### 书写格式

```javascript
{{time | 过滤器名称}}
{{time | 过滤器名称('自定义参数')}}   过滤器不管写不写time参数，都会传  自定义参数例如时间格式
{{time | 过滤器名称 | 过滤器名称}}   过滤器可以嵌套使用，但是只能按顺序将返回值交给下个过滤器
v-bind="time | 过滤器"

vue({
    filters:{
        过滤器名称(value){ //value为time
            return dayJs().format()
        }
    }
})
```

### 时间格式化js

```javascript
dayjs()
  .startOf('month')
  .add(1, 'day')
  .set('year', 2018)
  .format('YYYY-MM-DD HH:mm:ss')
```

### 全局过滤器

```tex
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})
```

## 内置指令

```tex
v-bind;v-model;v-for;v-on;v-if;v-if-else;v-else;v-show;v-text;v-html;v-slot;v-pre;v-cloak;v-once
```

### v-text

```tex
相当于{{}}
但是写的文本不会被解析成标签，会全部当成文本
```

### v-html

```tex
可以解析文本中的标签，但是会有xss攻击危险，盗取用户信息
```

### v-cloak

```tex
vue介入的一瞬间就会删除
配合css属性选择器可以实现vue之前的样式，vue一介入就删除
[v-cloak]:css属性选择器
```

### v-once

```tex
v-once指令会初始化动态渲染一次就结束
例如
<h1 v-once>初始化{{n}}</h1>    n初始化后就不变了
<h1>变化{{n}}</h1>
```

### v-pre

```tex
跳过vue对节点的编译过程
用处:可以跳过没有用到vue动态渲染地方，加快编译
```

## 自定义指令

### directives

```javascript
vue实例中的属性directives
```

### 使用方式一(函数)

```javascript
directives:{
    //调用时机
    //1.当元素初次和指令绑定时
    //2.当指令所在整个模板重新解析时，即模板发生变化时
    big(element,binding){
        //element  dom元素    binding绑定信息
        element.innerText = binding.value     //this为window对象
    }
}
```

### 使用方式二(对象)

```javascript
directives:{
    fbind:{
        //元素与指令初次绑定时触发
        bind(element,binding){
            
        },
        //元素插入到页面时触发
        inserted(element,binding){
            
        },
        //指令所在模板重新解析时触发
        update(element,binding){
            
        }
        
    }
}
```

### 全局自定义指令

#### Vue.directive

```javascript
Vue.directive('',{
    
})
```

## 生命周期函数(钩子)

### 挂载mounted

```tex
当vue将dom解析完毕后放入页面的时候调用
vue({
	mounted(){
	
	}
})
```

### 更新updated

```tex

```

### 销毁destoryed

```tex

```

![生命周期](D:\软件\开发工具\资料\资料（含课件）\02_原理图\生命周期.png)

# Vue组件化编程

## 组件定义

```tex
实现应用中局部功能代码和资源的集合
```

## 组件使用

```javascript
//1.创建组件
const school = Vue.extend({
    没有el
    template:`
		<html标签>
	`
    data(){
		return {
            schoolName:''
            schoolAddress:''
        }    
	}
})

//2.注册组件(局部注册)
new Vue({
    el:'',
    components:{
        school:school  ==>school
    }
})
//3.使用
<school> </school>
    
    
    全局组件
    Vue.component('',school)
```

### 注意点

![image-20210810144303339](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210810144303339.png)

## 组件嵌套

### app

```javascript
const student = Vue.extend({
    
})
const school = Vue.extend({
    template:`
		<student></student>
	`,
    data(){
        return {}
    },
    components:{
        student
    }
})
const app = Vue.extend({
    template:`
		<school></school>
	`,
    data(){
        return {}
    },
    components:{
        school
    }
})
new Vue({
    template:`
		<app></app>
	`,
    data:{},
    components:{
        app
    }
})
```

## VueComponent构造函数

```tex
每一个组件本质都是一个VueComponent构造函数
Vue解析时会自动new VueComponent去调用，即自动创建VueComponent构造函数的实例对象
每一次调用Vue.extend都会创建一个新的VueComponent实例对象

组件中的data函数，methods,watch,computed中的this都指向VueComponent实例对象

简称vc
```

## 内置关系

```tex
VueComponent.protoType.__proto__ = Vue.protoType
```

# Vue脚手架cli

```tex
vue官方提供的标准化开发工具
vue-cli最新版本是4.x
cli command line interface 命令行接口工具
```

## 安装

```bash
1.全局安装@vue/cli
	npm install -g @vue/cli
2.切换到你==要创建项目的目录==，然后使用命令常见项目
	vue create xxxx
3.启动项目
	npm run serve
```

![image-20210811154144369](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210811154144369.png)

## render函数

```bash
#import vue from vue  是在vue文件夹下引入的残缺版vue,没有模板解析器，vue/package.json文件中
#module:'dist/vue.runtime.esm.js'
#文件都在node_modules文件夹下
#vue.runtime.esm.js中没有模板解析器，比原vue文件小，可以使用render函数做模板解析
#render函数只在main.js中的vm实例中用一次
render(createElement){  #需要有返回值
	return createElement('h1','helloworld')
}

#es6语法   箭头函数之一个参数，一句方法体
render : h => h('h1','helloWorld')

#.vue文件中的template模板会被模板解析器解析  全局package.json中有配置
```

## 配置文件

> webpack配置文件

```tex
webpack配置文件被隐藏了
想查看需要使用命令
vue inspect > output.js   不用改
```

> vue.config.js

```bash
vue-cli提供了通过自定义配置文件，加载配置
配置项在官网可查

#更改入口
	pages:{
		
	}
#关闭语法检查
	lintOnSave : false
```

![image-20210823104354723](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210823104354723.png)

## Vue标签ref属性

```html
<!--vue组件文件中的所有元素都会放到VueComponent对象身上。可以在vc身上拿到ref中的元素，就是原生dom元素,this指向vc-->
<template>
	<h1 ref="title"></h1>
    <!--this.$refs.sch是school的组件实例对象    子组件对象-->
    <School ref="sch"></School>
</template>
```

## 组件实例属性props

```bash
#props接收组件标签传输的数据,直接渲染到模板中
	#写法一，数组,类型不影响操作
		props:['name','age','sex']
	#写法二，对象 可以设置类型限制
		props:{name:String,age:Number,sex:String}
	#写法三，嵌套对象
		props:{
			name:{
				type:String,	#类型限制
				required:true	#是否必要
				default:aa		#默认值，一般不和required一起写，需要必要时，默认值没用
			}
		}
#	: = v-bind        表示执行引号中的表达式   正常情况下传值为字符串，v-bind可以传number
	<school name="" sex="" :age="" />   
#修改数据，在data中创建变量接收props
	data(){
		return{
			newName:this.name
		}
	}
```

## mixin混入

> 相当于java中的include

```bash
#将组件中的组件对象中的公共部分拿出来放到一个js文件中
	export const methods = {  #分别暴露
		methods:{
			showName(){
				console.log("aaa")
			}
		}
	}
	
#组件中使用
	import {methods} from './mixin.js'
	
	export default{
		mixin:[methods]
	}
#组件中有属性，就会混合，不会替换

#全局混合
main.js中使用
import {methods} from './mixin.js'
Vue.mixin(methods)  #所有的vc和vm都混合methods
	
```

## 插件

```tex
#创建plugins.js，使用对象，对象中必须有install方法
	export default{
		install(Vue){	#vue原型对象,可以有多个参数来接收使用者传递的信息,第一个为Vue原型对象
			#全局过滤器
			Vue.filter()
			
			#定义全局指令
			Vue.directive()
			
			#定义混入
			Vue.mixin()
			
			#给原型添加属性或方法
			Vue.protoType.hello = () => alert('123')
			
		}
	}

#使用
main.js中vue实例对象之前
import plugin from './plugins.js'
Vue.use(plugin)

```

## scoped style标签属性

```bash
#scoped防止class名称重复，因为每个组件的样式会汇总到一起，scoped意味着该style为局部css,标注scoped之后，模板解析时模板会自动生成一个属性来使用样式  <div xxxx>   形成 demo[xxxx]{}
<style scoped> 
    .demo{
        background-color: yellow;
        text-align: center;
        border: 1px solid black;
    }
</style>

#style lang
lang="css" / lang="less" 来告诉vue,样式使用什么写的
```

```bash
#npm下载less加载器,因为vue-cli依赖的webpack是4版本的，而直接下载less都是最新版,最新版的less只兼容webpack5，所以下载7-版本
	npm i less-loader@7
	
#less可以嵌套写样式
```

## 案例

![image-20210823181951365](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210823181951365.png)

## 浏览器本地存储

### localStorage

```bash
#存储到本地
	window方法 window.xx == xx
	localStorage.setItem("k","v")   #对象数据json转换
#获取localStorage数据
	localStorage.getItem("k")
#删除localStorage数据
	localStorage.removeItem("k")
#删除所有localStorage数据
	localStorage.clear()
```

## 浏览器会话存储

### sessionStorage

```bash
#存储到会话
	window方法 window.xx == xx
	sessionStorage.setItem("k","v")   #对象数据json转换
#获取sessionStorage数据
	sessionStorage.getItem("k")
#删除sessionStorage数据
	sessionStorage.removeItem("k")
#删除所有sessionStorage数据
	sessionStorage.clear()
```

# 组件自定义事件

## 组件实现调用父组件事件的方式

> 第一种

```bash
#父组件使用v-bind向子组件传递函数，子组件使用props接收
	#父组件
	<Student :function="function" />
	methods:{function(){}}
	#子组件
	<button @click="methods"></button>
	props:['function']
		#使用方式
		this.trans()
```

> 第二种 自定义事件一

```bash
#父组件使用v-on为子组件绑定自定义事件传递方法，事件会定义到子组件实例vc身上的$emit上 调用$emit('函数名')
	#父组件
	<Student v-on:mytrans="function" />      # v-on == @   //自定义事件名mytrans
	methods:{function(){}}
	#子组件
	<button @click="methods"></button>
	methods:{
		method(){
			#调用事件
			this.$emit('mytrans',参数)  #参数可传可不传  
		}
	}
```

> 第三种 自定义事件二

```bash
#父组件使用ref标记子组件，使用ref获取组件实例对象添加自定义事件,使用生命周期钩子，挂载完毕时添加事件
	<Student ref="student" />
	mounted(){
		this.$refs.student.$on('mytrans',this.function)      #mytrans自定义事件名，function 绑定的函数
		this.$refs.student.$once() #一次事件
	}
#子组件
	<button @click="methods"></button>
	methods:{
		method(){
			#调用事件
			this.$emit('mytrans',参数)  #参数可传可不传  
		}
	}
```

### 解绑自定义事件 

```bash
#解绑自定义事件(从被绑定组件的内部解绑)
	this.$off('mytrans')  #只解绑一个事件
	this.$off(['mytrans','mytrans2']) #解绑多个事件
	this.$off()  #没参数解绑所有事件
```

# 全局事件总线

```bash
任意组件间通信
设置一个所有组件外的  全局人  因为vc对象是Vue.extend返回的   
# 因为vc对象的原型的原型是Vue的原型，所以使用Vue的原型作为全局对象
```

![image-20220116115419317](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220116115419317.png)

```js
# 设置全局事件总线
	new Vue({
      render: h => h(App),
      beforeCreate(){
        //在页面创建之前将vue实例对象传递给vue原型对象
        Vue.prototype.$bus = this    //设置全局事件总线
      }
    }).$mount('#app')

# 全局事件总线可以实现组件间通信
# 在组件消失时，解绑事件
	  destroyed(){
          this.$bus.$off('getStudent')    // 当前组件没了，但是总线身上的事件不会自动消失
      }

# 父给子用props  使用场景，多层嵌套，例如孙子给爷爷数据
```

# 消息订阅与发布

```js
# 订阅和发布
	需要消息的订阅
    发送消息的发布
# js 库
 pubsub-js
# 订阅消息
    methods:{
        //订阅消息，第一个返回值时消息名  ，如果要得到  发布的数据 就需要设置第二个参数,或更多
        getStudentName(msgName,data){
            console.log(`school发现${msgName}发布了${data}消息`);
        }
    },
    mounted(){ 
        //订阅消息  以'hello'作为消息名    本质和全局事件总线差不多  
        this.pubId = pubsub.subscribe('hello', this.getStudentName)
    },
    beforeDestroy(){
        //取消订阅   获取订阅时的id
        pubsub.unsubscript(this.pubId)
    }
# 发布消息
    methods: {
        // 发布消息
        setStudentName(){
            pubsub.publish('hello',this.studentName)
        }
    },
```

# $nextTick()

```js
# 语法
	this.$nextTick(回调函数)
# 作用
	在下一次DOM更新结束后执行其指定的回调
# 什么时候用
	当改变数据后，要基于更新后的Dom进行某些操作时，要在nextTick所指定的回调函数中执行
```

# 过度和动画

```js
# 动画 css
# <transtion>标签包裹住的标签  会自动在style里面寻找v-enter-active 和 v-leave-active  css选择器

    <div>
        <button @click="isShow = !isShow">显示/隐藏</button>
    <transition name="hello" appear>   # name值决定 css中 v-enter-active的v   |  appear决定动画是否上来就展示  :appear="true" 或 appear
        								# name 用来区分 多个动画效果
        <h1 v-show="isShow">你好啊!!!</h1>
    </transition>
  </div>

    .v-enter-active{
        animation: atguigu 1s;
    }

    .v-leave-active{
        animation: atguigu 1s reverse;
    }

    @keyframes atguigu {
        from{
            transform: translateX(-100%);
        }
        to{
            transform: translateX(0px);
        }
    }
```

```js
# 过渡 css
	
	/*  进入的起点  离开的终点 */ 
    .h-enter,.h-leave-to{
        transform: translateX(-100%);
    }
    .h-enter-active,.h-leave-active{
        transition: 0.5s linear;
    }
    /* 进入的终点 离开的起点*/
    .h-enter-to,.h-leave{
        transform: translateX(0);
    }
```

> 多个元素过度

```js
# 一个<transation> 中只能使用一个元素
# 多个元素 在同一个transation中要用 transation-group 并且每个元素都要指定key
```

## 第三方动画库

```js
# 安装
npm i animate.css
```

# 配置代理(跨域问题)

```js
同域访问  同协议、同主机ip、同端口号
http localhost 8080    http localhost 8080

跨域 
http localhost 8080    http localhost 5000
```

> 解决方法

```js
# cors
	写服务端的人 ， 写一些特殊的响应头
    但是会 被任何请求都能请求到
# jsonp      借助 script的src
	用的少  只能解决get请求跨域   面试问的多
# 代理服务器
	http localhost 8080  -->  http localhost 8080 -->  http localhost 5000
	
	代理服务器没有浏览器，服务器访问服务器 采用传统的http协议 ，没有同源协议 ,同源协议是ajax这种才有

	// nginx  或者  ** vue-cli **
# vue-cli 代理
	# https://cli.vuejs.org/zh/config/#devserver-proxy

	如果你的前端应用和后端 API 服务器没有运行在同一个主机上，你需要在开发环境下将 API 请求代理到 API 服务器。这个问题可以通过 vue.config.js 中的 		devServer.proxy 选项来配置。
    module.exports = {
        devServer: {
            proxy: 'http://localhost:4000'
        }
    }
	//ajax请求本机代理   如果public文件夹下有同名的文件，就不会使用代理，直接返回当前有的文件
	axios.get('http://localhost:8080/students')

	# 如果你想要更多的代理控制行为，也可以使用一个 path: options 成对的对象
    module.exports = {
      devServer: {
        proxy: {
          '/api': {
            target: '<url>',
            pathRewrite:{'^/api':''} 	//将路径转换
            ws: true,		//用于支持websocket
            changeOrigin: true		//是否以真实地址访问  false使用当前host  true使用访问api的host 即操控host的值   
          },
          '/foo': {
            target: '<other_url>'
          }
        }
      }
    }
```

# vue-resource

```js
# vue-resource 是插件库
//引入vue-resource
    import vueResource from 'vue-resource'

    Vue.use(vueResource)  //直接应用在vm原型对象上


	this.$http.get(url).then()
```

# slot插槽

```tex
http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4
https://s3.ax1x.com/2021/01/16/srJlq0.jpg

```

> 默认插槽

```js
# 在组件中使用<slot></slot>标签做标识
        <slot>
            <ul>
                <li v-for="data in listData" :key="data.index">{{data}}</li>
            </ul>
        </slot>
# 在使用组件时，用双闭合标签，并且不要再传数据，否则数据还会渲染
		<Category title="美食">
            <img src="https://s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
        </Category>
```

> 具名插槽

```js
# 具有名字的插槽
	<template>
        <div class="category">
            <h3>{{title}}分类</h3>
            <slot name="center">
                <ul>
                    <li v-for="data in listData" :key="data.index">{{data}}</li>
                </ul>
            </slot>
            <slot name="footer"></slot>
        </div>
	</template>
# 使用组件
    <Category title="美食">
        <img slot="center" src="https://s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
            <a slot="footer" href="#">更多美食</a>
	</Category>


# 多个标签使用插槽 用template标签包裹，template标签会被去除
	# 在template中有使用slot的新写法
    	<template v-slot:center>
        </template>
```

> 作用域插槽

```js
# 子组件向父组件传递数据  子组件的具体结构由父组件决定  数据由子组件管理
	<template>
        <div class="category">
            <h3>{{title}}分类</h3>
            <slot :games="games"></slot>
        </div>
    </template>

    <script>
        export default {
            name: 'Category',
            props:['title'],
            data() {
                return {
                    games:['红色警戒','穿越火线','劲舞团','超级玛丽'],
                }
            },
        }
    </script>

# 父组件
	<template>
        <div class="container">
            <Category title="游戏">
                <template scope="atguigu">      <!-- 或者解构赋值 {games} 因为可能传递多个数据-->
                    <ul>
                        <li v-for="(g,index) in atguigu.games" :key="index">{{g}}</li>   
                    </ul>
                </template>

            </Category>
            <Category title="游戏">
                <template slot-scope="{ games }">      <!-- 或者解构赋值 {games} 因为可能传递多个数据-->
                    <ul>
                        <li v-for="(g,index) in games" :key="index">{{g}}</li>   
                    </ul>
                </template>
            </Category>
        </div>
    </template>
```

# Vuex

> 概念

```tex
专门在Vue中实现集中式状态(数据)管理的一个Vue插件,对vue应用中多个组件的共享状态进行集中式的管理(读/写),也是一种组件间通信的方式，且适用于任意组件间通信
```

> 什么时候使用

```tex
1.多个组件依赖于同一状态
2.来自不同组件的行为需要变更同一状态
```

```js
# 操控数据的读写方便
```

![image-20220117110452963](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220117110452963.png)

## vuex工作原理

```js
#  https://vuex.vuejs.org/zh/#%E4%BB%80%E4%B9%88%E6%98%AF-%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86%E6%A8%A1%E5%BC%8F
```

## 搭建vuex环境

```js
# 安装
npm i vuex
# 创建 store
	import { Store } from "vuex";   //vuex.Store({})
    // 引入vuex
    import vuex from 'vuex'
    import Vue from 'vue'
    Vue.use(vuex)

    //该文件用于创建Vuex中最核心的store
    export default new Store({

        //准备actions 用于响应组件中的动作
        actions: {

        },
        //准备mutations 用于操作数据(state)
        mutations: {

        },
        //准备state  用于存储数据
        state: {

        }
    })
# 引入 store
    //引入vue-resource    //http插件
    import vueResource from 'vue-resource'

    import vuex from 'vuex'
    Vue.use(vuex)
    //引入store
    import store from './store' //默认拿index   //es6 import 只会在加载js文件时，先import
    // const store = require("./store")     // node方式，可以在任意位置随时随意import

    Vue.config.productionTip = false

    Vue.use(vueResource)

    const vm = new Vue({
      render: h => h(App),
      //只有引入了vuex才能定义store
      store,  // store:store.default,
      beforeCreate(){
        Vue.prototype.$bus = this
      }
    }).$mount('#app')
    console.log(vm);

# store其他里面常用的配置项
	getters		中的方法有一个state参数  , 必须有返回值
    
#### 代码生成函数
	mapState       mapGetters     mapActions          mapMutations

# 自动生成计算属性
// 对象写法
    	...mapState({'sum':'sum','school':'school','course':'course'}),    //生成state中的属性  
        ...mapGetters({'getDouble':'getDouble'})							//生成getters中的属性

//简写  数组写法  放计算属性名和数据名一致时
            ...mapState(['sum','school','course']),
            ...mapGetters(['getDouble'])

# 自动生成 actions对象中对应的methods
	...mapActions({'incrementOdd':'jiaOdd','incrementWait':'jiaWait'})
 		//但是调用时，要传递参数，否则不会自动传参
		<button @click="incrementOdd(n)">当前求和为奇数再加</button>
	//数组写法  简写
	...mapActions(['jiaOdd','jiaWait'])
# 自动生成 mutations对象中对应的methods
	...mapMutations({'increment':'JIA','decrement':'JIAN'}),
        //但是调用时，要传递参数，否则不会自动传参
        <button @click="increment(n)">+</button>
	
	//数组写法  简写
	...mapMutations(['JIA','JIAN']),
```

## vuex模块化 namespace

```js
# 一下看不明白  设置好后输出 this.$store

# 可以使用多个文件

# 对不同组件的操作，放在不同的子store中,使用modules引入

	const a = {
        namespaced:true,			//namespaced标记的子store,才可以被自动生成代码的方法识别到name
        actions:{},
        mutations:{},
        state:{},
        getters:{}
    }
    const b = {
        namespaced:true,			//namespaced标记的子store,才可以被自动生成代码的方法识别到name
        actions:{},
        mutations:{},
        state:{},
        getters:{}
    }
    
    new vuex.Store({
        modules:{
            a,
            b
        }
    })

# 组件中使用自动生成代码 使用子store     第一个参数放子store的名  
    ...mapState('a',{'sum':'sum','school':'school','course':'course'}), 
        
        
        computed:{
            //自动生成计算属性
            ...mapState('CountOption',['sum','school','course']),
            ...mapState('PersonOption',['personList']),
            ...mapGetters('CountOption',['getDouble'])
        },
        methods: {
            ...mapMutations('CountOption',{'increment':'JIA','decrement':'JIAN'}),

            ...mapActions('CountOption',{'incrementOdd':'jiaOdd','incrementWait':'jiaWait'})
        
# 组件中不使用自动生成代码
	 computed:{
            personList(){
                return this.$store.state.PersonOption.personList
            },
            sum(){
                return this.$store.state.CountOption.sum
            },
            sumDouble(){
                return this.$store.getters['CountOption/getDouble']
            }
        },
        methods:{
            add(){
                if(this.name != ''){
                    const personObj = {id:nanoid(),name:this.name}
                    this.$store.commit('PersonOption/ADD_PERSON',personObj)  //子store做commit  在参数前面加子store名路径
                    this.name = ''
                }else {
                    alert('不能为空')
                }
                
            }
        }
```

# 路由vue-router

```js
专门用来实现spa(single page web application)单页面应用
# 通过监视路径变化，匹配组件
	一个路由就是一组映射关系
    key为路径，value为function函数(后端路由)或component组件
```

> 基本使用

```js
# 创建router文件夹 下 创建 index.js
    // 该文件专门用于创建整个应用的路由器
    import VueRouter from "vue-router";
    //引入组件
    import About from '../components/About.vue'
    import Home from '../components/Home.vue'

    //创建一个路由器
    export default new VueRouter({

        routes:[
            {
                path: '/about',
                component: About
            },
            {
                path: '/home',
                component: Home
            }
        ]

    })
# main.js
    //使用vue-router插件
    import VueRouter from 'vue-router'

    //引入路由器对象
    import router from './router/index'

    Vue.use(VueRouter)

    const vm = new Vue({
      render: h => h(App),
      router: router
    }).$mount('#app')
    console.log(vm);
# app.vue
    // 原始跳转使用a标签 
    /* <a class="list-group-item active" href="./about.html">About</a>
    	<a class="list-group-item" href="./home.html">Home</a> */

    // router-link标签实现路由的切换   active-class指定被选中之后的样式
        <router-link class="list-group-item" active-class="active" to="/about">About</router-link>
    	<router-link class="list-group-item" active-class="active" to="/home">Home</router-link>


 	// 指定组件的呈现位置-->
            <router-view></router-view>
```

> 注意点

```tex
1.路由组件一般放在pages文件夹，一般组件通常存放在components文件夹
2.通过切换，“隐藏”了的路由组件会被销毁
3.每个组件都有自己的$route属性，存储自己的路由信息
4.一个应用只有一个$router属性，可以通过组件的$router属性获取到
```

## 嵌套(多级)路由

```js
# children
//创建一个路由器
export default new VueRouter({

    routes:[
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home,
            children: [
                {
                    path: 'news',
                    component: News
                },
                {
                    path: 'message',
                    component: Message
                }
            ]
        }
    ]

})
# template
<template>
    <div>
        <h2>我是Home的内容</h2>
        <div>
            <ul class="nav nav-tabs">
                <li>
                <!-- <a class="list-group-item active" href="./home-news.html">News</a> -->
                    <router-link class="list-group-item" active-class="active" to="/home/news">News</router-link>
                </li>
                <li>
                <!-- <a class="list-group-item " href="./home-message.html">Message</a> -->
                    <router-link class="list-group-item" active-class="active" to="/home/message">Message</router-link>
                </li>
            </ul>
            <router-view></router-view>
        </div>
    </div>
    
</template>

```

## 路由的query传参

```js
# 直接在路径后使用问号传参
	// query传参 问号方法 
    // <router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">{{m.title}}</router-link> 
	// query传参 对象写法 
    <router-link :to="{
        path: '/home/message/detail',
            query: {
                id: m.id,
                title: m.title
            }
        }">
        {{m.title}}
     </router-link>
```

## name 命名路由

```js
# 在 路由js中给路由命名
	{
        name:'xiangqing',
        path: 'detail',
        component: Detail
    }

# 在组件中使用  使用name替换path ,简化跳转路径，相当于别名
	<router-link :to="{
    	name: 'xiangqing',
        query: {
            id: m.id,
            title: m.title
        }
    }">
    	{{m.title}}
    </router-link>
```

## params参数传参

```js
# params参数 需要先在路由js中声明
    {
        //用占位符告诉路由器参数
        name:'xiangqing',
        path: 'detail/:id/:title',
        component: Detail
    }
# 在组件中使用
    <!-- params传参 -->
        <!-- <router-link :to="`/home/message/detail/${m.id}/${m.title}`">{{m.title}}</router-link> -->
    <!-- params传参 对象方法 -->
    <router-link :to="{
    	name: 'xiangqing',  // 使用params参数 不能使用 path参数
        params: {
            id: m.id,
                title: m.title
        }
    }">
    {{m.title}}
    </router-link>
```

## 路由的props参数

```js
# 在路由js中配置
{
    //用占位符告诉路由器参数
    name:'xiangqing',
    path: 'detail/:id/:title',
    component: Detail,
    //props的第一种写法，值为对象，该对象中所有的key-value都会以props的形式传给Detail组件
   	// props:{a:1,b:'hello'}

    //props的第二种写法，值为布尔值，若布尔值为真，就会把该路由组件收到的所有  params  参数，以props的形式传给Detail组件
    // props: true

    //props的第三种写法，值为函数 , 可以返回params和query
    props($route){
         return {id:$route.params.id,title:$route.params.title}
    }
}

# 数据传到哪个组件,就在哪个组件路径下定义props属性
<template>
    <ul>
        <li>消息编号: {{id}}</li>
        <li>消息标题: {{title}}</li>
    </ul>
</template>

<script>
    export default {
        name: 'Detail',
        props:['id','title']
        // mounted() {
        //     console.log(this.$route);
        // },
    }
</script>

# 作用 简化$route.params这种写法
```

## router-link 的 replace属性

```html
<!-- 
	1.作用: 控制器由跳转时操作浏览器历史记录的模式
	2.浏览器的历史记录有两种写入方式，分别为push和replace,push是追加历史记录，replace是替换当前记录。路由跳转时候默认为push
	3.如何开启replace模式:<router-link replace ......>News</router-link>
-->
	<router-link :replace="true" class="list-group-item" active-class="active" to="/about">About</router-link>
	<router-link replace class="list-group-item" active-class="active" to="/home">Home</router-link>
```

## 编程式路由导航

```js
# 使用应用唯一的 $router对象
	this.$router.back()	//前进
	this.$router.forward()	//后退
	this.$router.go(num)	//按参数决定前进或后退几步

	this.$router.push({	//进入到哪个路径
        name: 'xiangqing',  // 使用params参数 不能使用 path参数
        params: {
            id: m.id,
            title: m.title
        }
    })

	this.$router.replace({ 		//进入到哪个路径 替换当前路径
        name: 'xiangqing',  // 使用params参数 不能使用 path参数
        params: {
            id: m.id,
            title: m.title
        }
    })
```

## 缓存路由组件

```js
# 因为 组件切换时会被销毁
<keep-alive include="News">		//include指定的组件名  组件不会被销毁   多个组件用数组 :include="[]"
    <router-view></router-view>
</keep-alive>
```

## 两个生命周期

```js
# 因为使用缓存路由组件时，组件不会被销毁，所以销毁生命周期无法使用
# 所以有两个新生命周期使用，当做setInterval时，使用
        activated(){           //激活
            this.timer = setInterval(() => {
                console.log('@');
                this.opacity -= 0.01
                if(this.opacity <= 0) this.opacity = 1
            }, 16);
        },
        deactivated(){          //关闭激活
            clearInterval(this.timer) 
        }
```

## 路由守卫

> 全局前置

```js
//全局前置路由守卫--初始化的时候被调用，每次路由切换前被调用，在router对象暴露前配置
router.beforeEach((to,from,next) => {	//to是要去的路径from是被切换的路径  next放行
    const school = localStorage.getItem('school')
    console.log(school == "尚硅谷");
    console.log(to.name,from,next);
    if(to.name === "xiaoxi" || to.name === "xiangqing"){
        if(school == "尚硅谷"){
            next()
        }else{
            alert("学校名不对")
        }
    }else{
        next()
    }
    
})

export default router
```

> meta属性

```js
# meta 用来设置哪个路由需要被拦截
	meta:{isAuth:true}


router.beforeEach((to,from,next) => {	//to是要去的路径from是被切换的路径  next放行
    const school = localStorage.getItem('school')
    console.log(school == "尚硅谷");
    console.log(to.name,from,next);
    if(to.meta.isAuth){			//isAuth为true的都放行
        if(school == "尚硅谷"){
            next()
        }else{
            alert("学校名不对")
        }
    }else{
        next()
    }
    
})
```

> 后置守卫

```js
//全局前后置路由守卫--初始化的时候被调用，每次路由切换后被调用，在router对象暴露前配置
router.afterEach((to,from) => {
    document.title = to.meta.title || '硅谷系统'
})

```

> 独享路由守卫

```js
# 单个路由单独享用的守卫
	{
        name:'xiaoxi',
        path: 'news',
        component: News,
        meta:{isAuth: true,title:'新闻'},
        beforeEnter: (to, from, next) => {			//独享路由守卫
             const school = localStorage.getItem('school')
             if(to.meta.isAuth){
                  if(school == "尚硅谷"){
                       next()
                  }else{
                       alert("学校名不对")
                  }
              }else{
                  next()
              }
         }
    },
```

> 组件内路由守卫

```js
//通过路由规则，进入该组件前被调用
beforeRouteEnter (to, from, next) {
    // ...
},
//通过路由规则，离开该组件时调用
beforeRouteLeave (to, from, next) {
    // ...
}
```

## history模式和hash模式

```js
# 默认开启hash模式
	hash模式就是 在路径上加  /#/
    /#/后面的路径不会传到服务器，作为hash值，在前端使用
# 切换模式	在router对象中添加属性
	mode:'history'

# 区别
	1.样式
	hash /#/
    history /
        
    2.兼容性
    	hash比history好
    3.不带# 
    	影响发送后端请求
        
# history解决办法
	后端解决 辨别后端路径和前端路径
```

# UI

![image-20220117214928574](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220117214928574.png)

> 按需引入配置

```js
# babel.config.js		先安装@babel/preset-env -D
	presets: [
    	'@vue/cli-plugin-babel/preset',
    	["@babel/preset-env", { "modules": false }]
	],
    plugins: [
        [
            "component",
            {
                "libraryName": "element-ui",
                "styleLibraryName": "theme-chalk"
            }
        ]
    ]


# https://element.eleme.cn/#/zh-CN/component/installation
```

