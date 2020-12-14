# Webpack4.0

## 创建项目

### 怎么用

- 1创建简单webpack打包

	- npm init -y

		- 初始化项目，生成package.json
		- -y表示直接默认配置创建，
没有-y的时候，会有一堆的询问，
要按好几次回车

	- npm i webpack webpack-cli -D

		- webpack-cli是用来可以在，cmd中使用webpack命令

	- 创建并配置webpack.config.js

	  const path = require('path')//NodeJs的一个核心模块
	  
	  module.exports = {
	      mode: 'production',//production时，会把打包的文件压缩，设置成development时，不压缩
	      entry: {
	          main: './src/index.js',
	      },
	      output: {
	          filename: 'bundle.js',
	          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
	      }
	  }

	- 在package.json文件内配置
打包命令

	  {
	      "name": "webpack-demo",
	      "version": "1.0.0",
	      "description": "",
	      "private": true,
	      "scripts": {
	          "bundle": "webpack"//在命令行中执行npm bundle,就在目录下执行webpack
	      },
	      "author": "syd",
	      "license": "ISC",
	      "devDependencies": {
	          "webpack": "^5.3.2",
	          "webpack-cli": "^4.1.0"
	      }
	  }

### 其他

- 一些指令

	- npx webpack-v  

		- //表示在该目录下执行webpack -v,不会去找全局的webpack

	- npx webpack --config   a.js

		- //a.js就是你的webpack配置文件

- 生产环境和开发环境
打包

	- 什么是

		- 区分开 开发和生产的 webpack配置文件

	- 怎么用

		- 1、下载依赖包webpack-merge

			- 什么是

				- 用于合并两个配置文件

			- cnpm i webpack-merge -D

		- 2、创建webpack.connon.js放      
开发和生产都要用的代码

		  const path = require('path')//NodeJs的一个核心模块
		  const HtmlWebpackPlugin = require('html-webpack-plugin');
		  const { CleanWebpackPlugin } = require('clean-webpack-plugin');
		  
		  module.exports = {
		      entry: {
		          main: './src/index.js',
		      },
		      module: {
		          rules: [
		              { 
		                  test: /\.js$/, 
		                  exclude: /node_modules/, 
		                  loader: "babel-loader" ,
		              },
		              {
		                  test: /\.(eot|svg|ttf|woff)$/,
		                  use: {
		                      loader: 'file-loader',
		                      options: {
		                          name: '[name]_[hash].[ext]',//这个叫做占位符，placeholder,可以去官网查看配置项
		                      }
		                  }
		              },
		              {
		                  test: /\.scss$/,
		                  use: ['style-loader', {
		                      loader: 'css-loader',
		                      options: {
		                          importLoaders: 2,//表示一个scss文件引入其他scss文件时，其他scss也同样经过下面这两个loader
		                      }
		                  }, 'sass-loader', 'postcss-loader']//loader都是从右到左一次执行的
		              },
		              {
		                  test: /\.css$/,
		                  use: [
		                      'style-loader',
		                      'css-loader',
		                      'postcss-loader'
		                  ]
		              }
		          ]
		      },
		      plugins: [
		          new HtmlWebpackPlugin(
		              {
		                  template: 'index.html'
		              }
		          ),
		          new CleanWebpackPlugin({ cleanStaleWebpackAssets: false }),
		      ],
		      output: {
		          filename: '[name].js',//[name]表示entry里面配置的属性名字
		          path: path.resolve(__dirname, 'dist')//path必须为绝对路径，所以用到path模块
		      },
		  }

		- 3、创建webpack.dev.js放开发用的配置

		  const webpack = require('webpack')
		  const { merge } = require('webpack-merge')
		  const commonConfig = require('./webpack.common.js')
		  console.log(merge)
		  const devConfig = {
		      mode: 'development',//production时，会把打包的文件压缩，设置成development时，不压缩
		      devtool: 'cheap-module-eval-source-map',
		      devServer: {
		          contentBase: './dist',
		          open: true,
		          hot: true,
		          hot: true,
		          hotOnly: true,//不自动刷新页面
		      },
		      optimization: {
		          usedExports: true
		      },
		      plugins: [
		          new webpack.HotModuleReplacementPlugin()
		      ],
		  }
		  module.exports = merge(commonConfig, devConfig)

		- 4、创建webpack.prod.js放打包用的配置

		  const { merge } = require('webpack-merge')
		  const commonConfig = require('./webpack.common.js')
		  const prodConfig = {
		      mode: 'production',//production时，会把打包的文件压缩，设置成development时，不压缩
		      devtool: 'cheap-module-source-map',
		  }
		  module.exports = merge(commonConfig, prodConfig)

		- 5、在package文件配置指令

			- 开发

				- "dev": "webpack-dev-server --config webpack.dev.js",
				- 一般是启动一个服务器

			- 打包

				- "build": "webpack --config webpack.prod.js",

- webpack和Code Splitting

	- 什么是Code Splitting

		- Code Splitting字面意思：代码分割
		- 举例说明

			- 老旧的方式

				- 全部打包到一个main.js文件

			- code spliting方式

				- 不同库打包成不同的js文件，最后引入到html中

	- 怎么用

		- webpack中配置，即可实现代码分割
		- 配置项

		  optimization:{
		          splitChunks:{
		              chunks:'all'
		          }
		      },

	- 其他

		- 这个配置项optimization的原理是
webpack官方推荐的插件：
SplitChunksPlugin

			- 配置

			  optimization: {
			          splitChunks: {
			              chunks: "async",//async表示分割异步引人的模块
			              minSize: 30000,//表示大于30000字节的模块才会被打包
			              minChunks: 1,//表示这个模块被引入>=1的时候，才会被分割打包
			              maxAsyncRequests: 5,//最多打包5个
			              maxInitialRequests: 3,
			              automaticNameDelimiter: '~',//生成文件的名字里面的连接符，例如：vendors~main.js
			              name: true,
			              cacheGroups: {
			                  vendors: {//表示模块组
			                      test: /[\\/]node_modules[\\/]/,
			                      priority: -10//优先级为-10
			                  },
			                  default: {//表示模块组
			                      minChunks: 2,
			                      priority: -20,//优先级为-20
			                      reuseExistingChunk: true,//表示两个地方引用模块，只打包一个
			                  }
			              }
			          },
			      },

		- 一般配置的默认是async，异步打包，表示你点击一个文件，才会http请求点击事件的文件，提高网站的性能

- 懒加载的概念

	- 什么是

		- 比如有很多网页，初始加载的时候，不引入，当你切换路由的时候，才会请求引入文件

- 整个打包过程的全面分析 

	- 什么是？

		- 配置命令，打包之后，生成一个json文件，里面记录了你整个打包的各种信息，可以给你用来分析你的打包流程

	- 怎么用

		- 1、配置命令

			- webpack --profile --json > stats.json --config

			  例子："dev-build": "webpack --profile --json > stats.json --config ./build/webpack.dev.js",

		- 2、将生成的stats.json文件放到一些分析网站，就可以看到了可视化的分析

			- 分析网站，webpack官网就有很多分析网站网址推荐
			- webpack网站-》指南-》代码分离-》bundle分析    有好几个网站

	- 其他

		- 查看网站所有js代码的使用率

			- 1、浏览器控制台    crtl+Shift+p
			- 2、搜索coverage，选择第一个工具，再点击左上角圆圈，圆圈变红，刷新页面，查看各个js文件里面的使用情况：绿色表示使用，红色表示没有使用

- PreLoading ，Prefetching

	- 什么是

		- PreLoading 

			- 第一次加载就和主页文件加载了

		- Prefetching

			- 首页所需文件先加载，可能要用到的代码在网络空下来的时候加载（比如登录的js代码）

	- 怎么用

		- 魔法字符串

			- 例

			  document.addEventListener(/* webpackPrefetch: true */'click',()=>{
			      const element = document.createElement('div')
			      element.innerHTML = 'Dell Lee';
			      document.body.appendChild(element)
			  })

## 配置项

### 什么是

- diy你的webpack打包

### 怎么用

- 创建一个webpack.config.js , 在里面配置

  module.exports = {
  	你的配置
  }

### 其他

- entry和output

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

- fileLoader  文件打包

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

- cssLoader

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

- sourceMap

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

- devServer

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

- hot  module  replacement
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

- Tree shaking

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

- 浏览器缓存

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

- Shimming

	- 什么是

		- 你在一个a.js里面引用import $ from 'jquery' 
		- 又引入import { ui } from './b.js'

			- 这是b.js的内容：
export function ui(){
    $('body').css('background','green')
}

		- 然后在a.js下面，执行   ui()
		- 会报错：$不存在，因为每个js之间作用域不相通
		- 解决看下面

	- 怎么用

		- 1、在webpack。config.js中配置

		  const webpack = require('webpack')
		  
		  plugins: [
		          new webpack.ProvidePlugin({//表示任意js文件中使用到了$，就自动在文件中import $ from 'jquery'
		              $:'jquery'
		          })
		      ],

## 插件

### 什么是

- plugin可以在webpack运行到某个时刻的时候做一些事情
有点像vue的生命周期

### 怎么用

- 一般是去百度或google，查到webpack的配置，然后看需要哪些插件，去看插件的官方文档

### 其他

- htmlWebpackPlugin 

	- 什么是

		- 在你打包后的dist目录里，新建一个html，并引入打包的js文件

	- 怎么用

		- 1、按照官网安装

			- npm install --save-dev html-webpack-plugin

		- 2、配置

			- var HtmlWebpackPlugin = require('html-webpack-plugin');
			- plugins: [new HtmlWebpackPlugin()]

	- 其他

		- 1、因为在html中想要一个id为root的div，
需要配置

		  plugins: [new HtmlWebpackPlugin(
		          {
		              template:'src/index.html'
		          }
		      )],

			- 它先会以index.html为模板创建一个html，再引入js

- clean-Webpack-plugin
(是个第三方插件，不在webpack官网中找到)

	- 什么是

		- 在你第二次打包的时候，dist目录里还有你上次打包留下的东西
		- 这个插件可以删除dist目录，重新生成dist文件

	- 怎么用

		- 安装 ： cnpm i clean-webpack-plugin -D
		- 配置

			- 引入

			  const { CleanWebpackPlugin } = require('clean-webpack-plugin');

			- 配置到plugins（必须得在数组的第一个）

			  plugins: [new CleanWebpackPlugin(), new HtmlWebpackPlugin(
			          {
			              template: 'index.html'
			          }
			      )],

- babel

	- 什么是

		- 将ES6语法转换成ES5语法

	- 怎么用

		- 1、下载依赖

			- npm install --save-dev babel-loader @babel/core

		- 2、下载依赖

			- npm install @babel/preset-env --save-dev

		- 3、配置

		  module: {
		          rules: [
		              { 
		                  test: /\.js$/, 
		                  exclude: /node_modules/, 
		                  loader: "babel-loader" ,
		                  options:{
		                      "presets": ["@babel/preset-env"]
		                  }]
		  }
		              },

	- 其他

		- 1、虽然将ES6语法转换成ES5，但是Promise等新的对象在Ie老旧浏览器中依然没有，所以就需要引人babel的第三方模块引入这些新对象的实现

			- 1、下载依赖polyfill

			  npm install --save @babel/polyfill

			- 2、在你打包的js文件前面，引入
import "@babel/polyfill"

				- 打包的main.js会很大，因为引入的是所有的新的对象的实现
				- 所以需要第三步，按你js需要哪些模块，才引入
				- 这个第二步也可以不用写了，直接第三步就好了

			- 3、配置

			  module: {
			          rules: [
			              { 
			                  test: /\.js$/, 
			                  exclude: /node_modules/, 
			                  loader: "babel-loader" ,
			                  options:{//新增加的内容
			                      "presets": [["@babel/preset-env",{
			                          useBuiltIns:'usage'//实现按需引入
			                      }]]
			                  }
			              },

		- 配置项targets

			- 什么是

				- 告诉babel你要用浏览器的版本，自动检测该版本浏览器是否已经有这些新对象，再决定是否要放入Promise等新的对象的实现

			- 怎么用

			  module: {
			          rules: [
			              { 
			                  test: /\.js$/, 
			                  exclude: /node_modules/, 
			                  loader: "babel-loader" ,
			                  options:{//新增加的内容
			                      "presets": [["@babel/preset-env",{
			                          targets:{
			                              chrome:"67",//告诉babel浏览器版本
			                          },
			                          useBuiltIns:'usage'//实现按需引入
			                      }]]
			                  }
			              },

		- 因为前面引入polyfill是全局引入，可能会污染，你使用UI类库等

			- 怎么用

				- 0.去除之前polyfill的配置，没有就不用去除
				- 1、安装

					- npm install --save-dev @babel/plugin-transform-runtime
					- npm install --save @babel/runtime
					- npm install --save @babel/runtime-corejs2

				- 2、配置

				  module: {
				          rules: [
				              { 
				                  test: /\.js$/, 
				                  exclude: /node_modules/, 
				                  loader: "babel-loader" ,
				                  options:{
				                      "plugins": [["@babel/plugin-transform-runtime",
				                          {
				                              "absoluteRuntime": false,
				                              "corejs": 2,
				                              "helpers": true,
				                              "regenerator": true,
				                              "useESModules": false,
				                              "version": "7.0.0-beta.0"
				                          }
				                      ]]
				                  }
				              },

		- 如果配置项过多，可以创建.babelrc文件,把babel的options内容放到这里就可以了

			- .babelrb文件的内容：

			  {
			      "plugins": [["@babel/plugin-transform-runtime",
			          {
			              "absoluteRuntime": false,
			              "corejs": false,
			              "helpers": true,
			              "regenerator": true,
			              "useESModules": false,
			              "version": "7.0.0-beta.0"
			          }
			      ]]
			  }

- lodash

	- 什么是

		- 常用函数的工具包

	- 怎么用

- 打包css文件

	- 什么是

		- 平常打包后，没有css文件的，css存在在js中
		- 现在想要打包出css文件

	- 怎么用

		- 1、下载插件mini-css=extract-plugin

			- cnpm install mini-css-extract-plugin --save-dev
			- 目前该插件的缺点

				- 现在不支持热模块更新
				- 所以只能用在生产打包的时候，不然在开发环境下不热更新很难受

			- 比较适合线上环境使用

		- 2、配置webpack.config.js

		  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
		  
		  
		  plugins:[
		          new MiniCssExtractPlugin ({})
		      ]

		- 3、把module中的style-loader替换成CssMinimizerPlugin.loader

		  module:{
		          rules:[
		              {
		                  test: /\.scss$/,
		                  use: [MiniCssExtractPlugin.loader, {
		                      loader: 'css-loader',
		                      options: {
		                          importLoaders: 2,//表示一个scss文件引入其他scss文件时，其他scss也同样经过下面这两个loader
		                      }
		                  }, 'sass-loader', 'postcss-loader']//loader都是从右到左一次执行的
		              },
		              {
		                  test: /\.css$/,
		                  use: [
		                      MiniCssExtractPlugin.loader,
		                      'css-loader',
		                      'postcss-loader'
		                  ]
		              }
		          ]
		      },

	- 其他

		- 压缩css文件

			- 怎么用

				- 1、下载依赖

					- npm install --save-dev optimize-css-assets-webpack-plugin

				- 2、webpack.config.js配置

				  const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
				  
				  
				  optimization:{
				          minimizer:[ new OptimizeCSSAssetsPlugin({})]
				      },

## 看到4-11 , 看到7.17，代码跟视频中的同步

*XMind: ZEN - Trial Version*