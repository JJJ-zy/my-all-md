# Node.js

> 为什么js可以在浏览器中被执行

![image-20220112114034637](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112114034637.png)

![image-20220112114218188](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112114218188.png)

![image-20220112114614922](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112114614922.png)

## 简介

> 概念

```tex
Node.js是一个基于Chrome V8引擎的javaScript运行环境
```

> 运行环境

![image-20220112115633065](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112115633065.png)

> node.js可以做什么

![image-20220112115920990](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112115920990.png)

> 怎么学

![image-20220112120127188](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112120127188.png)

## fs文件系统模块

> 简介

```tex
fs模块是node.js官方提供的、用来操作文件的模块。它提供了一系列的方法和属性，用来满足用户对文件的操作需求
例如
	fs.readFile()方法，用来读取指定文件中的内容
	fs.writeFile()方法，用来向指定的文件中写入内容
```

> 使用

```bash
# 在代码中导入fs模块
	const fs = require('fs')
	
# fs.readFile() 语法格式
	fs.readFile(path,[options],callback)
	参数：
		1.必选参数，字符串，表示文件的路径
		2.可选参数，表示以什么编码格式来读取文件
		3.必选参数，文件读取完成后，通过回调函数拿到读取结果
		
	示例代码：
		const fs = require('fs')
		fs.readFile('./111.txt','utf8',function(err,dataStr){
			# 打印失败的结果，成功为null,失败为失败对象
			# 通过判断err的值，判断文件是否读取成功
			console.log(err)
			# 打印成功的数据
			console.log(dataStr)
			return dataStr; #将结果返回
		})
		
# fs.writeFile() 语法格式
	fs.writeFile(file,data,[options],callback)
	参数：
		1.必选参数，需要指定文件路径的字符串
		2.必选参数，表示要写入的内容
		3.可选参数，以什么字符格式写入，默认utf8
		4.必选参数，文件写入后的回调函数
		
	示例代码：
		const fs = require('fs');
		fs.writeFile('./222.txt','22222','utf8',function(err){
			console.log(err)
		})

# fs.readFile() 和 fs.writeFile() 联合使用
	//调用fs模块
    const fs = require('fs');
    //读取文件
    fs.readFile('./score.txt','utf8',function(err,dataStr){
        //判断是否成功
        if(err){
            return console.log("读取文件失败，原因:"+err.message)
        }
        console.log("读取文件成功:"+dataStr)

        //先把成绩的数据，按照空格进行分隔
        const arrOld = dataStr.split(" ")
        //循环将等号替换    
        const arrNew = []
        arrOld.forEach(item => {
            arrNew.push(item.replace('=',':'))
        })
        const newArr = arrNew.join('\r\n')
        console.log(newArr)
        fs.writeFile('./score-new.txt',newArr,'utf8',function(err){
            if(err){
                return console.log(err)
            }
        })
    })


# __dirname表示当前文件所处目录
```

![image-20220112133352993](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112133352993.png)

## path模块

> 简介

```tex
path模块是node.js官方提供的、用来处理路径的模块。它提供了一系列的方法和属性，用来满足用户对路径的处理需求
```

> 常用方法

```bash
# path.join()方法
	用来将多个路径片段拼接成一个完整的路径字符串
# path.basename()
	用来从路径字符串中，将文件名解析出来
# 导入path模块
	const path = require('path')
```

> 方法语法格式

```js
path.join(...paths)
```

![image-20220112141858837](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112141858837.png)

```js
path.basename()
```

![image-20220112143107322](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220112143107322.png)

## fs实例

> 实现步骤

```tex
1.创建两个正则表达式，分别用来匹配<style>和<script>标签
2.使用fs模块，读取需要被处理的文件
3.自定义resolveCSS方法，来写入index.css样式文件
4.自定义resolveJS方法，来写入index.js脚本文件
5.自定义resolveHTML方法，来写入index.html文件
```

![image-20220113091354280](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113091354280.png)

## http模块

> 介绍

```tex
http模块是node.js官方提供的、用来创建web服务器的模块，通过http模块提供的http.createServer()方法，就能方便的把一台普通的电脑，编程一台web服务器，从而对外提供web资源服务,相当于tomcat服务器的作用

const http = require('http')
```

> 相关概念

```bash
# ip地址
	ip地址就是互联网上每台计算机的唯一地址
# 域名 和 域名服务器
	与ip地址意义对应，方便记忆
# 端口号
	相当于门牌号，每个端口号对应一个web服务，客户端发送过来的网络请求，通过端口号，可以准确地交给对应的web服务
```

> 创建http实例

```js
// 导入http模块
const http = require('http');

//创建web服务器实例
const server = http.createServer();

//为服务器实例绑定request事件，监听客户端请求
server.on('request',function(req,res){
    console.log('Someone visit our web server')
})

//启动服务器
server.listen(8080,function(){
    console.log('server running at http://127.0.0.1:8080')
})
```

> req请求对象

```tex
只要服务器接收到了客户端的请求，就会调用server.on()为服务器绑定的request事件处理函数
如果想在事件处理函数中，访问与客户端相关的数据或属性，可以使用以下方式
```

```js
const http = require('http');

const server = http.createServer();

//req是请求对象，包含了与客户端有关的数据和属性
server.on('request',(req,res)=>{
    //req.url 是客户端请求的url地址
    const url = req.url;

    //req.method 是客户端请求的method类型

    const method = req.method;

    const str = `Your request url is ${url},and request method is ${method}`

    console.log(str)

})
server.listen(80,()=>{
    console.log('server running at http://127.0.0.1')
})
```

> res响应对象

```tex
在服务器的request事件处理函数中，如果想访问与服务器相关的数据或属性，可以使用如下的方式
```

```js
const http = require('http');

const server = http.createServer();

//req是请求对象，包含了与客户端有关的数据和属性
server.on('request',(req,res)=>{
    //req.url 是客户端请求的url地址
    const url = req.url;

    //req.method 是客户端请求的method类型

    const method = req.method;

    const str = `Your request url is ${url},and request method is ${method}`

    //调用res.end()方法 ，向客户端响应一些内容,并结束这次请求
    res.end(str)

})
server.listen(80,()=>{
    console.log('server running at http://127.0.0.1')
})
```

> res.end()中文乱码问题

```tex
设置响应头
```

```js
//中文乱码
res.setHeader('Content-type','text/html;charset=utf-8')
res.end('张三')
```

> 根据不同的url响应不同的html内容

```tex
实现步骤
	1.获取请求的url地址
	2.设置默认的响应内容为 404 not found
	3.判断用户请求是否为/或/index.html
	4.判断用户请求的是否为about.html
	5.设置Content-type响应头,防止中文乱码
	6.使用res.end()把内容响应给客户端
```

### 时钟web服务器案例

![image-20220113112300350](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113112300350.png)

> 实现步骤

```bash
# 导入需要的模块
fs
path
http
# 创建服务器
# 将资源的请求url地址映射为文件的存放路径
```

## 模块化

> 分类

```bash
# 内置模块
	node.js官方提供  例如 fs,path,http
# 自定义模块
	用户创建的每个.js文件，都是自定义模块
# 第三方模块
	由第三方开发出来的模块，使用前需下载
```

> 加载模块

```bash 
# 使用强大的require()方法，可以加载需要的内置模块、用户自定义模块、第三方模块进行使用
	加载自定义模块需要加路径
	使用require()方法加载其他模块时，会执行被加载模块中的代码
```

![image-20220113114458366](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113114458366.png)

> 模块作用域

```tex
自定义模块中定义的变量、方法，只能在当前模块内被访问

优点：
	防止全局变量的污染
```

### Module对象

> import 和 module区别

```tex
require/exports 输出的是值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。

import/export 模块输出的是值的引用。JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。

若文件引用的模块值改变，require 引入的模块值不会改变，而 import 引入的模块值会改变。

require/exports 是运行时动态加载，import/export 是静态编译
```

> 介绍

```tex
在每个.js自定义模块中都有一个module对象，它里面存储了和当前模块有关的信息
console.log(module)
```

#### module.exports对象

> 简介

```tex
在自定义模块中，可以使用module.exports对象，将模块内的成员共享出去，供外界使用。
外界用require()方法导入自定义模块时，得到的就是module.exports所指向的对象
```

> 使用

```js
//在一个自定义模块中，默认情况下，module.exports = {}

//向 module.exports 对象上挂载 username 属性
module.exports.username = 'zs'

module.exports.sayHello = () => {
        console.log('Hello!')
}
```

> 注意

```tex
使用require()方法导入模块时，导入的结果，永远以module.exports指向的对象为准
```

#### exports对象

```tex
为了简化代码，node提供了exports对象，默认情况下，exports 和 module.exports指向同一个对象。最终共享的结果，还是以module.exports指向的对象为准
```

> exports 和 module.exports使用误区

```tex
require()模块时，得到的永远是 module.exports指向的对象
如果exports的引用对象变了，module.exports的对象还是不变；module.exports的指向对象变了，结果就变为了module.exports指向的对象
如果只是添加属性的话，exports和module，exports还是指向同一个对象
```

![image-20220113133027549](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113133027549.png)

### 模块化规范

```tex
node.js遵循了CommonJS模块化规范，CommonJS规定了模块的特性和各模块之间如何相互依赖

CommonJS规定:
	1.每个模块内部，module变量代表当前模块
	2.module变量是一个对象，他的exports属性是对外的接口
	3.加载某个模块，其实是加载该模块的module.exports属性，require()方法用于加载模块
```

## npm与包

> 什么是包

```tex
node.js中的第三方模块又叫做包，由第三方团队或个人开发的，供所有人使用
```

> 为什么需要包

```tex
由于node.js内置模块仅提供了一些底层的API，导致在基于内置模块进行项目开发时，效率很低。包是基于内置模块封装出来的，提供了更高级、更方便的API，极大的提高了开发效率
包和内置模块之间的关系，相当于jquery和浏览器内置API之间的关系
```

> 从哪里下载包

```tex
国外npm,lnc公司旗下的https://www.npmjs.com,是全球最大的包共享中心，可以  搜索  任何想用的包
https://registry.npmjs.org的服务器，来对外共享所有的包，我们可以从这个服务器上  下载  自己所需要的包
```

> 如何下载包

```tex
npm(node package manager) ,包管理工具，伴随node.js安装到用户的电脑
安装多个包 空格隔开
```

### 初体验(时间格式化)

> 传统做法

![image-20220113134730933](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113134730933.png)

```js
//定义格式化时间的方法
function dataFormat(dtStr){
    const dt = new Date(dtStr)

    const y = dt.getFullYear();
    const m = padZero(dt.getMonth());
    const d = padZero(dt.getDate());

    const hh = padZero(dt.getHours());
    const mm = padZero(dt.getMinutes());
    const ss = padZero(dt.getSeconds());

    return `${y}-${m}-${d} ${hh}:${mm}:${ss}`

}

//定义补零的函数
function padZero(n){
    return n > 9 ? n : '0' + n;
}

//暴露模块
module.exports = {
    dataFormat
}
```

> 第三方npm包做法

```tex
1.npm下载包 moment
2.require导入包
3.参考moment官网，使用对应方法
```

```JS
const moment = require('moment')

// const dt = new Date();

// const newDT = moment(dt).format('YYYY-MM-DD hh-mm-ss')

const newDT = moment().format('YYYY-MM-DD hh-mm-ss')

console.log(newDT)
```

> 注意点

```tex
初次在项目中安装包，会多一个node_modules的文件夹和package-lock.json配置文件
node_modules存放下载的包资源
package-lock.json用来记录包的下载信息
```

> 安装指定版本的包

```bash
# 通过@符号指定具体的版本
	npm i moment@2.22.2  # 不需要删除之前的包，会覆盖
```

> 包的语义化规范

```tex
包的版本号是以"点分十进制"形式进行定义的，总共有三位数字，例如:2.24.0
其中每位数字所代表的含义如下:
	第一位:大版本
	第二位:功能版本
	第三位:Bug修复版本
	只要前面的版本号升级了，后面的版本号归零
```

### 包管理配置文件

```tex
npm规定，在项目根目录中，必须提供一个叫做package.json的包管理配置文件，用来记录与项目有关的一些配置信息
例如: 
	项目的名称，版本号，描述
	项目中都用到了哪些包
	哪些包只在开发期间会用到
	哪些包在开发和部署时都需要用到
```

> 多人协作问题

![image-20220113150106692](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113150106692.png)

```tex
在项目根目录中，创建一个package.json包管理配置文件，即可用来记录项目中安装了哪些包，从而方便剔除node_modules目录后，在团队成员之间共享项目的源代码

注意: 今后在项目开发中，一定要把node_modules文件夹，添加到.gitignore忽略文件中
```

> 创建

```bash
# npm包管理工具提供了一个快捷命令，可以在执行命令时所处的目录中，快速创建package.json这个包管理配置文件
npm init -y
# 上述命令只能在英文的目录下成功运行，所以项目文件夹的名称一定要用英文名，不能使用中文，不能出现空格
# 运行npm install命令安装包的时候，npm包管理工具会自动把包名称和版本号，记录到package.json中
```

```bash
# dependencies属性
	项目install的包
# 一次性安装所有的包
	当我们拿到一个剔除了node_module文件夹的项目时，如何一次性下载所有依赖包
	npm install   /   npm i
	执行npm install 命令，会直接读取package.json配置文件中dependencies中的包名称
	
# devDependencies节点
	如果某些包只在项目开发阶段会用到，在项目上线之后不会用到，则建议把这些包记录到devDependencies节点中
	如果开发和上线阶段都用得到，就放在dependencies节点中
	可以在npm搜索官网查看  应该放在哪
	
	移动方式
	npm i 包名 --save-dev     / npm i 包名 -D(简写)
```

### 卸载包

```bash
# 运行npm uninstall 命令  ， 会自动在dependencies中移除包的信息
	npm uninstall 包名
```

### 国内npm资源配置

> 淘宝npm镜像

```tex
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

```bash
# 检查当下的包镜像源
 npm config get registry
# 设置淘宝镜像源
 npm config set registry=https://registry.npm.taobao.org        	# 切换npm下载源
 npm install -g cnpm --registry=https://registry.npm.taobao.org		# 下载下载源cnpm   推荐 ，因为有些资源只有npm里面有
# npm config get registry
```

> nrm

```bash
# 为了更方便的切换下载包的镜像源，我们可以安装crm这个小工具，利用nrm提供的终端命令，可以快速查看和切换下包的镜像源
npm i nrm -g  # 全局安装
nrm ls # 查看所有可用的镜像源
nrm use taobao # 将下包的镜像源切换为 taobao镜像
```

### 包分类

#### 项目包

```tex
那些被安装到项目的 node_modules目录中的包，都是项目包

项目包又分为两类，分别是
	开发依赖包(devDependencies)
	核心依赖包(dependencies)
```

#### 全局包

```tex
在执行npm install命令时，如果提供了 -g 参数 ，则会把安装包安装为全局包
```

> i5ting_toc

```bash
# 可以把一个md文档转为html页面的小工具
	npm install -g i5ting_toc
	i5ting_toc -f 要转换的md文件路径 -o
```

> 规范的包结构

```tex
必须符合以下三点
	1.必须以单独的目录存在
	2.包的顶级目录下必须要包含package.json这个包管理配置文件
	3.package.json中必须包含name,version,main 这三个属性，分别代表包的名字、版本号、包的入口
```

### 开发自己的包

> 初始化包的基本结构

```bash
# 1.新建itheima-tools文件夹，作为包的根目录
# 2.新建三个文件: 
	package.json(包管理配置文件)
	index.js(包的入口文件)
	README.md(使用说明)
# 3.初始化package.json
	{
      "name": "itheima-tools",
      "version": "1.0.0",
      "description": "提供了格式化时间,HTMLEscape的功能",
      "main": "index.js",
      "keywords": ["itheima","dataFormat","eacape"],
      "author": "",
      "license": "ISC"
    }
```

> 功能拆分

```tex
拆分多个文件，使用module.exports暴露出去，然后在主js文件中导入这些文件，使用扩展运算符将这些拆分的文件再次暴露
```

> md中包含的内容

```tex
安装方式、导入方式、格式化时间、转义html中的特殊字符、还原html中的特殊字符、开源协议
npm install itheima-tools

require('itheima-tools')
```

> 发布

```tex
1.注册npm账号
	https://www.npmjs.com
2.在终端执行 npm login 命令，依次输入用户名、密码、邮箱     注意： 在运行npm login之前，需要将下包服务器切回npm官方服务器

3.发布包，将终端切换到包的根目录之后，运行npm publish命令,即可将包发布到npm上  (注意:包名不能雷同)
4.删除已发布的包
	npm unpublish 包名 --force  即可从npm删除已发布的包			
	只能删除发布72小时以内发布的包，超过72小时就永久不能删除，删除后24小时不能重复提交
```

## 模块的加载机制

```bash
# 优先从缓存中加载
	任何模块在第一次被加载后会被缓存，这也意味着  同时多次  调用require()不会导致模块的代码被执行多次
# 内置模块的加载机制
	内置模块的加载优先级最高
# 加载自定义模块必须加  ./  或 ../
	在使用require()加载自定义模块时，如果省略了文件的扩展名，则node.js就会按照以下顺序尝试加载文件
		1.按照确切的文件名加载
		2.补全.js扩展名
		3.补全,json扩展名
		4.补全.node扩展名
		5.加载失败，终端报错
# 第三方模块的加载机制
	从当前模块的父目录开始，尝试从/node_modules文件夹中加载第三方模块
	如果没有找到，则移动到上一层父目录进行加载，直到文件系统的根目录
	
# 目录加载机制
	当把目录作为模块标识符，传递给require()进行加载的时候，有三种加载方式
		1.在被加载的目录下查找一个叫做package.json的文件，并寻找main属性，作为require()加载的入口
		2.如果目录里没有package.json文件，或者main入口不存在或无法解析，则node.js将会试图加载目录下的lndex,js文件
		3.如果都失败了，node.js会在终端打印错误信息，报告模块的丢失
```

