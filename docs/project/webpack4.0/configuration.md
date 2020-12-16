> 配置项

+ 什么是

- diy你的webpack打包

+ 怎么用

- 创建一个webpack.config.js , 在里面配置

  module.exports = {
  	你的配置
  }

+ 其他

### entry和output

	- 什么是

		- 指定打包的入口和出口

	- 怎么用

		- 配置

		  module.exports = {
		  entry: {
		          main: './src/index.js',
		      },
		  output: {
		          filename: 'bundle.js',
		          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
		      },
		  }

	- 其他

		- 1、解决打包多个入口文件

		  module.exports = {
		  
		  entry: {
		  
		          main: './src/index.js',
		  
		          qq:'./src/index.js'
		  
		      },
		  
		  
		  
		  output: {
		  
		          filename: '[name].js',//[name]表示entry里面配置的属性名字
		  
		          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
		  
		      },
		  
		      },
		  
		  
		  
		  }

		- 2、让你引入js文件时用的绝对路径
本来：./js
现在：https:***/**/js

		  output: {
		          publicPath:'https://www.baidu.com',
		          filename: '[name].js',//[name]表示entry里面配置的属性名字
		          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
		      },

### fileLoader  文件打包

	- 什么是

		- 处理文件类型

	- 怎么用

		- 使用file-loader
打包图片类型文件
配置webpack.config.js里的module

		  const path = require('path')//NodeJs的一个核心模块
		  
		  module.exports = {
		      mode: 'development',//production时，会把打包的文件压缩，设置成development时，不压缩
		      entry: {
		          main: './src/index.js',
		      },
		      module: {
		          rules: [
		              {
		                  test: /\.(jpg|png|gif)$/,
		                  use: {
		                      loader: 'file-loader',
		                      options: {
		                          name: '[name]_[hash].[ext]',//这个叫做占位符，placeholder,可以去官网查看配置项
		                          outputPath:'images/'
		                      }
		                  }
		              }
		          ]
		      },
		      output: {
		          filename: 'bundle.js',
		          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
		      }
		  }

		- 图片加载

			- 使用url-loader，它包含了file-loader的打包图片功能，并且可以把图片转换成base64编码的字符串形式
打包图片类型

			  const path = require('path')//NodeJs的一个核心模块
			  
			  module.exports = {
			      mode: 'development',//production时，会把打包的文件压缩，设置成development时，不压缩
			      entry: {
			          main: './src/index.js',
			      },
			      module: {
			          rules: [
			              {
			                  test: /\.(jpg|png|gif)$/,
			                  use: {
			                      loader: 'file-loader',
			                      options: {
			                          name: '[name]_[hash].[ext]',//这个叫做占位符，placeholder,可以去官网查看配置项
			                          outputPath:'images/',
			                          limit: 2048,//表示图片小于2048字节，就转换成base64;;不然就会在dist中生成图片
			                      }
			                  }
			              }
			          ]
			      },
			      output: {
			          filename: 'bundle.js',
			          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
			      }
			  }

### cssLoader

	- 什么是

		- 打包css啊

	- 怎么用

		- 打包css

		  {
		                  test: /\.css$/,
		                  use: ['style-loader','css-loader']
		              }

			- 打包scss

			  {
			                  test: /\.scss$/,
			                  use: ['style-loader','css-loader','sass-loader']//loader都是从右到左一次执行的
			              }

				- use: ['style-loader','css-loader','sass-loader']
//loader都是从右到左一次执行的

			- 添加css前面的厂商前缀

				- 1、下载包，看官网指令
				- 2、下载plugins

					- cnpm i autoprefixer -D

				- 3、新建postcss.config.js

				  module.exports = {
				      plugins: [
				          require('autoprefixer')
				      ]
				    }

				- 4、使用

				  {
				                  test: /\.scss$/,
				                  use: ['style-loader','css-loader','sass-loader','postcss-loader']//loader都是从右到左一次执行的
				              }

			- cssloader的一些配置
模块中引入css，不会作用到其他模块

			  {
			                  test: /\.scss$/,
			                  use: ['style-loader',{
			                      loader:'css-loader',
			                      options:{
			                          importLoaders : 2,//表示一个scss文件引入其他scss文件时，其他scss也同样经过下面这两个loader
			                          modules: true//表示在一个模块引入的css，不会作用到其他模块
			                      }
			                  },'sass-loader','postcss-loader']//loader都是从右到左一次执行的
			              }

### sourceMap

	- 什么是

		- 浏览器输出错误只会显示你打包的js文件的第几行有错误
		- socuseMap能映射错误到你打包之前的哪个js文件出错

	- 怎么用

	  module.exports = {
	  		devtool:'cheap-module-eval-source-map',
	  }

	- 其他

		- 生产环境

			- devtool:'none'

				- 不使用socuseMap

			- devtool:'cheap-module-source-map'

				- 打包后生成一个单独的sourceMap文件

		- 开发环境

			- 综合常用配置：
cheap-module-eval-source-map

				- cheap-source-map

					- 表示只显示报错的行数，不显示列数
					- 提高打包效率

				- eval-source-map

					- 只是提高打包效率

				- inline-source-map

					- 不生成sourceMap文件，打包后嵌入仅生成的main.js文件里面

				- module

					- 表示引用的模块的报错也会显示出来

### devServer

	- 什么是

		- 启动一个服务，并且监听你的文件改动，实时变化

	- 怎么用

		- 1、安装webpack-dev-server

			- cnpm i webpack-dev-server -D

		- 2、webpack.config.js中配置

		  devServer:{
		          contentBase:'./dist',
		  	open:true,//自动打开
		      },

		- 3、在package.json中配置命令

		  "scripts": {
		      "start": "webpack-dev-server"
		    },

		- 4、运行npm run start

	- 其他

		- 还有两种方式，可以实现自动编译代码
减少每次手动刷新
官网也是提供了这三种方式
watch
devServer
complier

			- 1、简单的监听：通过指令
webpack --watch

				- 什么是

					- 你文件变动就会刷新你的打包文件

				- 怎么用

					- 在package.json中配置

					  "scripts": {
					      "watch":"webpack --watch"
					    },

				- 其他

					- 我用的时候碰到一个问题，我使用的CleanWebpackPlugin插件会删除我打包的dist目录下的html文件，所以需要配置这个插件

						- new CleanWebpackPlugin({cleanStaleWebpackAssets:false})

			- 2、手撸一个简单的服务器，
放置webpack

				- 什么是

					- 以前webpack没有devServer，出来之后，配置项也没这么多，以前的框架的作者都会自己手写
					- 这里的例子，帮助大家开阔眼界

				- 怎么用

					- 1、安装express
安装webpack-dev-middleware

						- 你也可以用其他的，这里作者熟悉这个，就用这个了
						- npm install express webpack-dev-middleware -D

					- 2.创建server.js

					  const express = require('express');
					  const webpack = require('webpack');
					  const webpackDevMiddleware = require('webpack-dev-middleware');//一个中间件
					  const config = require('./webpack.config.js');
					  const complier = webpack(config);//complier相当于编译器，使用一次就相当于执行一次webpack打包
					  
					  const app = express();//开启一个express服务器
					  app.use(webpackDevMiddleware(complier))//use是express使用中间件的写法，中间件指服务器接收请求，先使用提交给中间件
					  
					  app.listen(3000, () => {//监听3000端口
					      console.log('sercer is running')
					  })

					- 3.配置指令

						- 1、package.json中配置

						  "scripts": {
						        "server":"node server.js"
						    },

						- 2、npm run server

					- 4、打开localhost:3000

						- 这里的例子，不能做到，你修改一个文件，就能实时地改变网页的内容，你需要手动刷新浏览器
						- 如要实现上面这个功能，就自行百度了，还需要其他的配置项

### hot  module  replacement
模块热替换

	- 什么是

		- 只修改你修改的内容，不刷新其他的内容

	- 怎么用

		- 在webpack.config.js中配置

		  const webpack = require('webpack')
		  module.exports = {
		  devServer: {
		          hot:true,
		  //热更新
		          hotOnly:true,//不自动刷新页面
		   },
		  plugins: [
		          new webpack.HotModuleReplacementPlugin()
		      ],
		  }

	- 其他

		- 1、开启热更新之后，还要你自己写个，类似于监听的代码，才能实现你修改代码，端口内的内容就变化的功能

			- import bb from './bb'
bb();
if(module.hot){
//如果bb.js文件有更新的话，就执行bb()
    module.hot.accept('./bb',()=>{
        bb()
    })
}

		- 2、css-loader封装了上面这个代码，所以修改css的时候，就会更新
		- 3、babel也封装了，所以js文件修改，也会有实时更新

### Tree shaking

	- 什么是

		- 你引入一个js文件，只使用了js文件里的a方法，但是打包后的文件里，有js文件里的所有方法，你希望不打包不用的代码，就使用tree shaking

	- 怎么用

		- 只支持ES引入方式export和import
		- 1、在webpack.config.js中配置

		  optimization:{
		          usedExports: true//配置了这个，在package。json文件中就可以使用sideEffects了
		      },

			- 在开发环境下打包，还是会引入文件的所有方法
只是多了提示
			- 提示例如：

				- /*! exports provided: add, minus */
/*! exports used: add */
				- 你只用了add方法，但是引入了add和minus两个方法

		- 1.1、当变成生成环境下打包，上面第一步的配置都不用加，打包后直接是去除掉不用的引入的

		  mode: 'production',//生产环境下

			- 艹蛋

		- 2、package.json中配置

		  "sideEffects":[
		        "*.css"
		    ],

			- 这个配置项表示，你引入的css不回去检测分割
			- 不然你在js里引人css文件，但是没有使用，就会给你在打包的时候去除掉

### 浏览器缓存

	- 什么是

		- 你第一次访问网页，网页会把js等文件缓存，你打包更新到正式服，网页并不会重新请求js文件，因为你的js文件名称没变，浏览器用的还是缓存中的js文件，所以你更新的内容，用户看不到
		- 原因：js文件名并没有改变，网页不会自动重新请求

	- 怎么用

		- 在ouput中配置

		  output:{
		          filename: '[name].[contenthash]js',//contenthash表示根据内容生成的hash值
		          chunkFilename:'[name].[contenthash]js',
		      }

		- 配置中的contentcash会根据内容不同生成不同的hash值，网页识别后，重新请求文件

### Shimming  
+ 什么是
```js
    1. 这是a.js的内容：
    import $ from 'jquery' 
    import { ui } from './b.js'
    2. 这是b.js的内容：
    export function ui(){
        $('body').css('background','green')
    }
    3. 然后在a.js下面，执行   ui()
    4. 会报错：$不存在，因为每个js之间作用域不相通
    5. shimming用来解决这个问题
```		

+ 怎么用
```js
    1、在webpack。config.js中配置
    const webpack = require('webpack')
    plugins: [
        new webpack.ProvidePlugin({//表示任意js文件中使用到了$，就自动在文件中import $ from 'jquery'
            $:'jquery'
        })
    ],
```

+ 其他
  1. imports-loader解决js文件中一些变量的指向问题

```js
-存在的问题：
    一个js文件中
    console.log(this === window);
    输出为false，为了让每个js文件的js指向window，需要使用插件imports-loader
    具体配置看webpack官网吧，官网讲的很详细
-cnpm i imports-loader -D
```