# ECMAScript6

## 相关名词介绍

> ECMA

```tex
ECMA(European Computer Manufacturers Association)欧洲计算机制造商协会，这个组织的目标是评估、开发和认可电信和计算机标准，1994奶奶改名为ECMA国际
```

> ECMAScript

```tex
是由ecms国际通过ecma-262标准化的脚本程序设计语言
```

> ECMA-262

![image-20220101222425823](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220101222425823.png)

> ES6兼容性

![image-20220101222746502](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220101222746502.png)

## 变量声明

> let

```tex
块级作用域，局部变量，不会上升到全局变量
不存在变量提升，在变量声明前访问，会报错ReferenceError
不影响作用域链，在同层函数中可以使用
```

> const

```tex
必须要赋初始值
变量不允许被修改，直接声明为常量
常量名一般大写，潜规则
块级作用域，不会放入window对象中
对于数组中的数据进行修改，不算是对常量修改，不会报错    对象也一样
```

## 变量的解构赋值

```js
//ES6允许按照一定的模式从数组和对象中提取值，对变量进行赋值   新变量需要按照数组或对象的格式  []  {}
//1.数组的解构
        const F4 = ['小沈阳','刘能','赵四','宋小宝'];
        let [xiao,liu,zhao,song] = F4;
        console.log(xiao)
        
//2.对象的解构
        const shan = {
            name:'赵本山',
            age:60,
            xiaopin:function(){
                console.log('我要演小品')
            }
        }
        let {name,age,xiaopin} = shan;
        // let {xiaopin} = shan;
        xiaopin()
```

## 模板字符串(新的字符串声明方式)

```js
//声明   ``
let a = `123456789`

//可以换行
let a = `<th>
        <td></td>
        </th>`

//变量拼接
let a = `123`
let b = `${a}456`
```

## 简化对象写法

```js
ES6允许在对象中直接写入变量和函数，作为对象的属性和方法，将定义好的变量和函数的 赋值名称放进去即可
let name = '尚硅谷';
let change = function(){
    console.log('我们可以改变你!!');
}

const school = {
    name,
    change,
    improve(){
        console.log('将冒号和function去掉也可以哦')
    }
}

console.log(school)
```

## 箭头函数

```tex
1.箭头函数中的this是静态的，始终指向   函数声明时   所在作用域下的this的值

2.箭头函数中的this不能构造实例化对象
    let Person = (name,age) => {
        this.name = name;
        this.age = age
    }
3.不能使用dom中的arguments保存数据
4.箭头函数缩写
    1)省略小括号
    	let a = n => {return n}

    2)省略大括号,语句执行结果就是return返回值
    	let b = n => n;
```

## 参数默认值

```js
ES6允许给函数中的形参赋值，被赋值的形参一般位置在最后，相当于默认值
    function add(a,b,c=3){
        return a+b+c;
    }
    let result = add(1,2);
    console.log(result)
可以与解构赋值相结合
function connect({host='127.0.0.1',username,password,port}){
    console.log(host)
}

connect({
    host: 'localhost',
    username: 'root',
    password: 'root',
    port: 80
})
```

## rest参数(...args)

```js
rest参数用来替代arguments
//ES5 获取实参的方式
function date(){
    console.log(arguments);  //arguments是对象
}
date(1,2,3,4)

//ES6 rest参数   如果有多个参数 rest参数放到最后(必须，否则报错)
function msg(a,b,...args){
    console.log(args)	//args是数组
}
msg(1,2,3,4)
```

## 扩展运算符

```js
将数组转换为逗号分隔的序列

 //声明一个数组
const tfboys = ['易烊千玺','王源','王俊凯'];

//声明一个函数
function chunwan(){
    console.log(arguments)
}

chunwan(...tfboys)  // chunwan('易烊千玺','王源','王俊凯')  与rest区别，扩展运算符在实参

//数组合并
const a = [1,2]
const b = [3,4]
const c = [...a,...b]

//数组克隆  同理

//将伪数组转换为数组，即将获取到的dom对象数组转换为数组
const divs = document.querySelectorAll('div');
const divArr = [...divs];
console.log(divArr)
```

## Symbol基本使用

```js
//创建方式
Symbol()		//可以加字符串  Symbol('aaa')
Symbol.for()   //可以加字符串  Symbol.for('aaa')

/*
     Symbol特点:
     1.Symbol的值是唯一的，用来解决命名冲突的问题
     2.Symbol值不能与其他数据进行运算
     3.Symbol定义的对象属性不能使用for in 循环遍历，但是可以使用Reflect.ownKeys()来获取对象的所有键名
*/

let s2 = Symbol('尚硅谷');
let s3 = Symbol('尚硅谷');
console.log(s2 === s3)     //false

let s4 = Symbol.for();
let s5 = Symbol.for();
console.log(s4 === s5);  //true

//向对象中添加方法一
let game = {
    up:function(){},
    down:function(){}
}

let methods = {
    up:Symbol(),
    down:Symbol()
}

game[methods.up] = function(){
    console.log('上')
}

game[methods.down] = function(){
    console.log('下')
}

//向对象中添加方法二
let s = Symbol('say')
let youxi = {
    name:'狼人杀',
    s:function(){
        console.log('...')
    }
}
//调用方法
youxi[s]();
```

