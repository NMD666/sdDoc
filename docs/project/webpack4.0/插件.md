> 插件

+ 什么是

- plugin可以在webpack运行到某个时刻的时候做一些事情
有点像vue的生命周期

+ 怎么用

    一般是去百度或google，查到webpack的配置，然后看需要哪些插件，去看插件的官方文档

+ 其他

### htmlWebpackPlugin 

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

### clean-Webpack-plugin
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

### babel

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
		                  }
]
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

### lodash

	- 什么是

		- 常用函数的工具包

	- 怎么用

### 打包css文件

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
