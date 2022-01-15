# VUE.js-黑马

## webpack

> 概念

```tex
webpack是前端项目工程化的具体解决方案
主要功能:提供了良好的前端模块化开发支持，以及代码压缩混淆、处理浏览器端javascript的兼容性、性能优化等强大的功能
```

> 创建列表隔行变色项目

```tex
1.新建项目空白目录，初始化包管理配置文件package.json
2.新建src源代码目录
3.新建src  index.html  和 index.js
4.初始化首页基本的结构
5.运行 npm install jquery -S 命令，安装jquery 			-S是  --save的缩写  下载到dependencies
6.通过ES6模块化的方式导入jQery,实现列表隔行变色
```

> 安装webpack

```tex
npm install webpack@5.42.1 webpack-cli@4.7.2 -D   -D  --save-dev的缩写	 下载到devDependencies
```

> 基本使用

```bash
# 1.在项目根目录中，创建名为webpack.config.js的webpack配置文件，并初始化如下的基本配置:
	module.exports = {
		mode:'development'	# mode用来指定构建模式，可选值有development和production
	}
# 2.在package.json的script节点下，新增dev脚本如下：
	"scripts":{
		"dev":"webpack"	//script 节点下的脚本，可以通过npm run 执行，例如npm run dev
	}
# 在终端运行npm run dev命令，启动webpack进行项目的打包
```

> mode的可选值

```bash
# development
	开发时用development模式，打包速度快
# production
	上线时用production进行打包，体积小
```

> 默认约定

```js
# 在webpack 4.x和5.x的版本中，有如下约定
    默认打包入口文件为src下的index.js
    打包出口文件为dist下的main.js
# 可以在webpack.config.js中修改打包的默认约定
	entry节点指定打包的入口
	output节点指定打包的出口
	
	//导入路径处理模块
    const path = require('path')


    module.exports = {
        mode:'development',  //mode用来指定构建模式，可选值有development和production
        entry: path.join(__dirname,'./src/index.js'),   //设置webpack打包的入口文件路径
        output: {
            //设置webpack打包的出口文件路径
            path: path.join(__dirname,'./dist'),

            //设置webpack打包的出口文件
            filename:'main.js'
        }
    }
```

### web-dev-server插件

> 作用

```tex
每修改了源代码，webpack会自动进行项目的打包和构建
```

> 安装

```tex
npm install webpack-dev-server@3.11.2 -D
```

> 配置webpack-dev-server

```bash
# 修改package.json
	"scripts": {
		"dev": "webpack serve"	//script节点下的脚本，可以通过npm run运行
	}

# 再次运行npm run dev命令，重新进行项目的打包
# 在浏览器中访问http://localhost:8080地址，查看自动打包效果
# webpack-dev-server会启动一个实时打包的http服务器
# 在内存中生成一个main.js ,频繁读写，使用内存快,看不到，但可以访问,index页面引用内存中的main.js,真正上线时，再打包磁盘中的js文件使用
```

### html-webpack-plugin插件

> 作用

```tex
复制src首页到根路径
```

> 安装

```tex
npm install html-webpack-plugin@5.3.2 -D
```

> 配置html-webpack-plugin

```js
webpack配置文件中
# 导入html插件 得到一个构造函数
const HtmlPlugin = require('html-webpack-plugin')

# 创建html插件的实例对象
const htmlPlugin = new HtmlPlugin({
    templete: './src/index.html',	//指定原文件的存放路径
    filename: './index.html'		//指定生成的文件的存放路径
})

module.exports = {
    mode: 'development',
    plugins: [htmlPlugin]		# 挂载到webpack对象plugins节点上，使htmlPlugin生效
}
```

> 解惑

```tex
1.通过HTML插件复制到项目根目录中的index.html页面，也被放到了内存中
2.HTML插件在生成的index.html页面，自动注入打包了的main.js文件，即使原页面取消了引用
```

### devServer节点

> 作用

```tex
npm run dev后自动打开页面
```

> 配置devServer

```js
devServer: {
    open: true, //初次打包完成后，自动打开浏览器
    host: '127.0.0.1',	//实时打包所使用的主机地址
    port: 8080	//实时打包所使用的端口号
}

# 凡是修改了webpack.config.js配置文件和package.json配置文件，必须重启实时打包的服务器，否则最新的配置文件无法生效
```

### loader

> 概述

```tex
	在实际开发过程中，webpack默认只能打包处理以.js后缀名结尾的模块，其他非.js后缀结尾的模块，webpack默认处理不了，需要调用loader加载器才可以正常打包，否则会报错
	loader加载器的作用:协助webpack打包处理特定的文件模块。比如:
		css-loader 可以打包处理.css相关的文件
		less-loader 可以打包处理.less相关的文件
		babel-loader 可以打包处理webpack无法处理的高级JS语法
```

![image-20220113204423353](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220113204423353.png)

> 打包处理css文件

```js
# 安装
npm i style-loader@3.0.0 css-loader@5.2.6 -D
# 在webpack.config.js的module->rules数组中，添加loader规则如下:
	module: {	//所有第三方文件模块的匹配规则
        rules: [	//文件后缀名的匹配规则
            {test: /\.css$/, use: ['style-loader', 'css-loader']}
        ]
    }
	//text表示匹配的文件类型，use表示对应要调用的loader
	#use数组中指定的loader顺序是固定的
    #多个loader的调用顺序是从后往前调用,最后一个loader会将处理结果传给前一个loader,第一个loader处理完，转交给webpack,合并到main.js中
```

> 打包处理less文件

```js
# 安装
npm i less-loader@10.0.1 less@4.1.1 -D
# 在webpack.config.js的module->rule数据中，添加loader规则如下
module: {	//所有第三方文件模块的匹配规则
    rules: [	//文件后缀名的匹配规则
        {test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader']}
    ]
}
```

> 打包处理样式表中与url路径相关的文件

```js
# 安装
npm i url-loader@4.1.1 file-loader@6.2.0 -D
# 在webpack.config.js的module->rules数组中，添加loader规则如下
module: {	//所有第三方文件模块的匹配规则
    rules: [	//文件后缀名的匹配规则
        {test: /\.jpg|png|gif$/, use: 'url-loader?limit=22229'}
    ]
}
	# ?之后是loader的参数项，limit用来指定图片的大小，单位是字节(byte),只有<=limit大小的图片，才会被转为base64格式的图片
    # 转成base64格式后，会将base64格式的字符串，直接写进js文件中，就可以省去图片请求了，同时dist文件夹中，也不会打包出图片了
```

> 打包处理js文件中的高级语法

```js
# 安装
npm i babel-loader@8.2.2 @babel/core@7.14.6 @babel/plugin-proposal-decorators@7.14.5 -D
# 在webpack.config.js的module->rules数组中，添加loader规则如下
	//必须使用exclude指定排除项，因为node_modules目录下的第三方包不需要被打包
{test: /\.js$/, use: 'babel-loader', exclude: /node_modules/}
# 在项目根目录下，创建名为babel.config.js的配置文件，定义Babel的配置项如下
	module.exports = {
        //声明babel可用的插件
        plugins: [['@babel/plugin-proposal-decorators', {legacy: true}]]
    }

	# 参考官网 https://babeljs.io/docs/en/babel-plugin-proposal-decorators
```

![image-20220114125031011](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220114125031011.png)

### 打包发布(build)

```bash
# 在package.json文件的scripts节点下，新增build命令
	"scripts": {
		"dev": "webpack serve",	//开发环境，运行dev命令
		"build": "webpack --mode production"	//项目运行时，运行build命令
	}
	# --mode 是一个参数项,用来指定webpack的运行模式。production代表生产环境，会对打包生成的文件进行代码压缩和性能优化
	
	# 注意 通过 --mode指定的参数项，会覆盖webpack.config.js中的model选项
```

> 设置图片打包路径

```js
# 修改 webpack.config.js中的url-loader配置项，新增outputPath 选项即可指定图片文件输出的路径
	{
        test: /\.jpg|png|gif$/,
        use: {
            loader:'url-loader',
            options: {
                limit: 22228,
                //明确指定把打包生成的图片文件夹，存储到dist目录下的image文件夹中
                    outputPath: 'images'
            }
        }
    }
```

### 自动清理dist目录下的旧文件

```js
# 安装
npm i clean-webpack-plugin@3.0.0 -D
# 导入插件构造函数，创建实例对象
	const { CleanWebpackPlugin } = require('clean-webpack-plugin')
    const cleanPlugin = new CleanWebPackPlugin()
    
  	//把创建的实例对象，挂载到plugins节点中
    plugins: [cleanPlugin]
```

### Source Map

```tex
Source Map是一个信息文件，里面存放着代码位置信息，webpack运行后，复制的js文件与原文件的行号不一致，
```

```js
# 只需要在webpack.config.js中添加以下配置
	module.exports = {
        mode: 'development',
        //此选项生成的运行时报错行数与源代码行数保持一致，不建议在生产模式下使用,防止源码泄露
        devtool: 'eval-source-map'		#  可选值 nosource-source-map 只显示行号,不暴露源码  source-map直接暴露源码,不推荐
    }
```

### @

```js
# 从src源目录向下找资源
	'@/*.js'
# 配置@,在webpack.config.js中配置
	resolve: {
        alias: { //别名
            '@': path.join(__dirname,'./src/')
        }
    }
	
```

## axios

> axios是一个专注于网络请求的库