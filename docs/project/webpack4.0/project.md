创建项目

+ 怎么用

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

+ 其他

### 一些指令

	- npx webpack-v  

		- //表示在该目录下执行webpack -v,不会去找全局的webpack

	- npx webpack --config   a.js

		- //a.js就是你的webpack配置文件

### 生产环境和开发环境
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

### 懒加载的概念

	- 什么是

		- 比如有很多网页，初始加载的时候，不引入，当你切换路由的时候，才会请求引入文件

### 整个打包过程的全面分析 

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

### PreLoading ，Prefetching

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