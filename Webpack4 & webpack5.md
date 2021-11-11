# Webpack4 & webpack5



## 一. webpack4

### 1. 基本概念

#### 1.1 五个核心配置

##### Entry

​	入口（entry）指示 webpack 以哪个文件为入口开始打包。分析构建内部依赖图。

##### Output

​	输出（output）指示 webpack 打包后的资源 bundles 输出到那里去。以及如何命名。

##### Loader

​	Loader 让 webpack 去处理非 js 类型的文件 （ webpack 自身只理解js ）。

##### Plugins

​	即插件，让 webpack 可以执行更多任务。打包、优化、压缩、重新定义环境变量等等。

##### Mode

​	指示 webpack 使用相应模式的配置 

- development：让代码本地运行调试的环境
- production：让代码优化上线运行的环境





#### 1.2 loader 和 plugins 的区别

##### 1.2.1 什么是 loader

loader是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中

1. 处理一个文件可以使用多个loader，最后一个loader最先执行，第一个loader最后执行，即从尾到头
2. 第一个执行的loader接收源文件内容作为参数，其它loader接收前一个执行的loader的返回值作为参数，最后执行的loader会返回此模块的 JavaScript 源码



##### 1.2.2 什么是 plugins

在webpack运行的生命周期中会广播出许多事件，plugin可以监听这些事件，在合适的时机通过webpack提供的API改变输出结果。

使用该plugin后，执行的顺序：

1. webpack启动后，在读取配置的过程中会执行 new MyPlugin(options) 初始化一个 MyPlugin 获取其实例
2. 在初始化 compiler 对象后，就会通过 compiler.plugin (事件名称，回调函数)监听到 webpack 广播出来的事件
3. 并且可以通过 compiler 对象去操作 webpack



##### 1.2.3 区别

- 对于loader，它是一个转换器，将A文件进行编译形成B文件，这里操作的是文件，比如将A.scss转换为A.css，单纯的文件转换过程

- plugin是一个扩展器，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务



#### 1.3 使用版本

webpack@4.41.6

webpack-cli@3.3.11

### 2.基本使用

#### 2.1 开发环境

~~~bash
webpack ./src/index.js -o ./build/build.js --mode=development
将 ./src/index.js 为入口文件开始打包输出到 ./build/build.js
~~~

#### 2.2 生产环境

~~~bash
webpack ./src/index.js -o ./build/build.js --mode=production
~~~

#### 2.3 结论

1. webpack 本体只能处理 js / json 文件，要处理其它文件（css/img...）需要另外安装插件	
2. 两种生产环境可以将 es6 中的模块化编译为浏览器可以识别的代码
3. 生产环境下可以压缩代码



### 3. webpack.config.js

- 与 src 同级的 js 文件，是 webpack 的配置文件。

- 当使用 webpack 时，会加载这个文件里面的配置。
- 模块化方式使用 commonjs 的规范 `module.exports={ //写入 webpack 配置 }`。而项目（src）的文件使用 es6 的规范 

~~~js
const path = require('path');

module.exports = {
    // 入口文件
    entry: "./src/index.js",
    output: {
        // 输出文件名
        filename: "build.js",
        // 输出文件目录，__dirname 是当前的绝对路径
        path: path.resolve(__dirname, 'build'),
    },
    // loader的配置
    module: {
        rules:[
            // loaders的详细配置
        ]
    },
    // plugin的配置
    plugins: [

    ],
    // 模式
    mode: "development", 
    // mode: "production"
}
~~~



### 开发环境的基本配置

### 4. 打包文件

#### 4.1 打包样式文件

只有 index.js 引用的样式文件（css/less/scss/sass）才会被编译

~~~js
const path = require('path');

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module: {
        rules:[
            {
                // 匹配哪些文件（正则）
                test: /\.css$/,
                use: [
                    // use中的执行顺序，从尾到头，依次执行
                    // 创建 style 标签，将 js 中的样式资源插入，添加到 head 生效
                    'style-loader',
                    // 将 css 文件变成 commonjs 模块加载到 js 中，里面内容是样式字符串
                    'css-loader'
                ]
            },
            {
                test: /\.scss$/,
                use: ['style-loader', 'css-loader', 'sass-loader']
            }
        ]
    },
    mode: "development", 
}
~~~

##### 4.1.1 坑

css-loader 版本过高，node-sass 版本过高 sass-loader 版本过高

~~~js
// 可用方案之一
	"css-loader": "^3.6.0",
    "node-sass": "^4.14.1",
    "sass-loader": "^10.2.0",
    "style-loader": "^1.3.0",
    "webpack": "^4.41.6",
    "webpack-cli": "^3.3.11"
~~~



#### 4.2 打包html文件

~~~js
const path = require('path');
// 安装的 html-webpack-plugin 版本要与 webpack 版本相符
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "build.js",
        path: path.resolve(__dirname, 'build'),
    },
    plugins: [
        // html-webpack-plugin
        // 功能：默认会创建一个名为'index.html'的空的 HTML 文件，自动引入打包输出的所有资源（js / css）
        new HtmlWebpackPlugin({
            // 复制 './src/index.html' 文件，并自动引入打包输出的所有资源
            template: './src/index.html'
        })
    ],
    mode: "development", 
}
~~~

#### 4.3 打包图片

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "js/build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module : {
        rules:[
            {
                test: /\.scss$/,
                use: ['style-loader', 'css-loader', 'sass-loader']
            },
            {
                // 处理图片资源，但是无法处理 html 文件中的 img标签的图片
                test: /\.(jpg|png|gif)$/,
                // 使用一个 loader
                // 下载 url-loader file-loader
                loader: 'url-loader',
                options: {
                    // 图片小于 8kb，就会被 base64 处理
                    // 优点：减少请求数量（减轻服务器压力）
                    // 缺点：图片体积会更大（文件请求速度更慢）
                    limit: 8 * 1024,
                    // 给图片重命名，[hash:10]取图片hash的前十位，[ext]取文件原来拓展名
                    name: '[hash:10].[ext]',
                    // 输出目录
                    outputPath: 'imgs',
                    esModule: false,
                    // webpack4 低版本存在的问题，高版本已修复
                    // 问题：因为 url-loader 默认使用 es6 模块化解析，而 html-loader 引入图片是 commonjs 解析
                    // 解析时会出现问题：[object Module]
                    // 所以需要关掉 url-loader 的 es6 模块化，使用 commonjs 解析
                }
            },
            {
                test: /\.html$/,
                // 处理 html 文件中的 img 图片（负责引入 img ，从而能被 url-loader进行处理）
                use: ['html-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    mode: "development", 
}
~~~



#### 4.4 打包其他资源

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module : {
        rules:[
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                // 处理除了选中的文件（iconfont.ttf...）
                exclude: /\.(css|html|js|scss)$/,
                use: ['file-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    mode: "development", 
}
~~~





### 5. devServer



~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module : {
        rules:[
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    mode: "development",
    // 开发服务器 devServer：自动编译，自动打开浏览器
    // 特点：只会在内存中打包，不会有任何输出。所以调试完成之后需要重新打包
    //  运行指令：npx webpack-dev-server
    devServer: {
        // 项目构建后的路径
        contentBase: path.resolve(__dirname, 'build'),
        // 启动 gzip 压缩
        compress: true,
        // 端口号
        port: 3000,
        open: true
    }

}
~~~



### 生产环境的基本配置

### 6. 文件处理

#### 6.1 css

##### 6.1.1 css 初步提取以及兼容性处理

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

process.env.NODE_ENV = 'development';

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "js/build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module : {
        rules:[
            {
                test: /\.css$/,
                use: [
                    // 'style-loader'
                    // 用 MiniCssExtractPlugin.loader 取代 style-loader。作用：提取 js 中的 css 成单独文件
                    MiniCssExtractPlugin.loader,
                    'css-loader', // 将css文件整合到 js 文件中
                    /*
                        css兼容性处理：postcss --> postcss-loader postcss-preset-env
                        帮 postcss 找到package.json中 browserslist 里面的配置,通过配置加载指定的 css 兼容性样式

                        node 环境变量： process.env.NODE_ENV = 'development' (默认为 production)
						以下内容写在 package.json 中
                        "browserslist": {
                            "development": [
                            "last 1 chrome version",
                            "last 1 firefox version",
                            "last 1 safari version"
                            ],
                            "production": [
                            ">0.2%",
                            "not dead",
                            "not op_mini all"
                            ]
                        }
                    */
                    {
                        loader: 'postcss-loader',
                        options: {
                            ident: 'postcss',
                            plugins: [
                                // postcss 的插件
                                require('postcss-preset-env')()
                            ]
                        }
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
            // 指定css文件存放的目标
            filename: 'css/main.css'
        })
    ],
    mode: 'development'
}
~~~



##### 6.1.2 css 压缩

~~~js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin';
// 在webpack5 中使用 css-minimizer-webpack-plugin
/***********/
plugins: [
	new OptimizeCssAssetsWebpackPlugin() 
]
~~~



##### 6.1.3 package.json 配置

~~~json
{
  "name": "webpack_start",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {},
  "devDependencies": {
    "@webpack-cli/serve": "^1.6.0",
    "css-loader": "^3.6.0",
    "file-loader": "^6.2.0",
    "html-loader": "^1.1.0",
    "html-webpack-plugin": "^4.5.0",
    "mini-css-extract-plugin": "^1.6.2",
    "node-sass": "^4.14.1",
    "optimize-css-assets-webpack-plugin": "^5.0.8",
    "postcss-loader": "^3.0.0",
    "postcss-preset-env": "^6.7.0",
    "sass-loader": "^10.2.0",
    "style-loader": "^1.3.0",
    "url-loader": "^4.1.1",
    "webpack": "^4.41.6",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.11.0"
  },
  "browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ]
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": ""
}

~~~

#### 6.2 js

##### 6.2.1 eslint 安装

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "js/build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module : {
        rules:[
            /*
                语法检查：只检查自己的写的源代码，第三方的库不用检查
                需要安装 eslint eslint-loader
                airbnb 规范(工作指南)需要安装：exlint-config-airbnb-base  eslint-plugin-import  eslint
                在 package.json 中配置
                "eslintConfig": {
                    "extends": "airbnb-base"
                },
            */
            {
                test: /\.js$/,
                exclude: /node_modules/,
                // 如果有js文件，优先处理这个rule
                enforce: 'pre',
                loader: 'eslint-loader',
                options: {
                    // 开启代码自动修复
                    fix: true
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
    ],
    mode: 'development'
}
~~~

**package.json 配置之一**

~~~json
"eslint": "^6.8.0",
"eslint-config-airbnb-base": "^14.2.1",
"eslint-loader": "^3.0.3",
"eslint-plugin-import": "^2.25.2",
......
"eslintConfig": {
    "extends": "airbnb-base"
  },
~~~


##### 6.2.1 js 兼容性处理

将 es6 以上的语法转化为低版本的语法

注意嵌套格式

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

process.env.NODE_ENV = 'development';

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "js/build.js",
        path: path.resolve(__dirname, 'build'),
    },
    module: {
        rules: [
            /*
                js兼容性处理： babel-loader  @babel/core
                1. 基本 js 兼容性处理  -->  @babel/preset-env
                 问题： 只能转换基本语法，如promise等高级语法不能转换
                2. 全部 js 兼容性处理 --> @babel/polyfill  (在js 文件中引入)
                 问题： 会将所有兼容性代码引入，体积大
                3. 按需加载 --> core-js
            */
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [{
                    loader: 'babel-loader',
                    options: {
                        // 预设：指示 babel 做怎么样的兼容性处理
                        presets: [
                            ['@babel/preset-env',
                                {
                                    // 按需加载
                                    useBuiltIns: 'usage',
                                    // 指定 core-js 版本
                                    corejs: {
                                        version: 3
                                    },
                                    // 指定兼容性做到哪个版本的浏览器
                                    targets: {
                                        chrome: '60',
                                        firefox: '60',
                                        ie: '9',
                                        safari: '10',
                                        edge: '17'
                                    }
                                }]
                        ]
                    }
                }]

            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
    ],
    mode: 'development'
}
~~~

##### 6.2.2 js & html 压缩

~~~js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: "./src/index.js",
    output: {
        filename: "js/build.js",
        path: path.resolve(__dirname, 'build'),
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            minify: {
                // 移除空格
                collapseWhitespace: true,
                // 移除注释
                removeComments: true
            }
        }),
    ],
    // 生产环境下会自动压缩 js 代码
    mode: 'production'
}
~~~

### 7. 开发环境性能优化

#### 7.1 HMR

**HMR** ：`hot module replacement` 热模块替换 / 模块热替换

- 作用：一个模块发生变化，指挥重新打包这一个模块，而不是所有模块，可以极大提升构建速度



**样式文件**：可以使用 HMR 功能：因为 `style-loader` 内部实现了

~~~js
// 只需要 webpack.config.js 中的 devServer 配置的 hot 修改为 true
devServer: {
        contentBase: path.resolve(__dirname, 'build'),
        compress: true,
        port: 3000,
        // 开启 HMR
        hot: true,
        open: true
    }
~~~



**js 文件**：默认不能使用 HMR 功能 --> 需要修改 js 代码，添加支持 HMR 功能的代码

- HMR 功能只能处理非入口 js 文件

~~~js
// index.js
import ('./common.css')
import ('./print.js')

if(module.hot){
    // 一旦 module.hot 为 true，说明开启了 HMR 功能 --> 让 HMR 功能生效
    module.hot.accept('./print.js', ()=> {
        // 方法会监听 print.js 文件的变化，一旦发生变化，其它模块不会重新打包构建
        // 会执行后面的回调函数
        print();
    })
}
~~~



**html 文件**：不能使用 HMR 功能，只能开启热替换

1. 安装 `raw-loader` 

2. ~~~js
   // rules 中配置
   {
       test: /\.(htm|html)/,
       use: ['raw-loader']
   }
   ~~~

3. 在 `entry` 中添加路径 `./src/index.html` 即 `entry: ['./src/index.js', './src/index.html']`



#### 7.2 source map

source-map：一种提供**源代码到构建后代码的映射**的技术 （如果构建后代码出错了，通过映射可以追踪源代码错误）

参数：`[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map`

代码：

```
devtool: 'eval-source-map'
```

可选方案：[生成source-map的位置|给出的错误代码信息]

- source-map：外部，错误代码准确信息 和 源代码的错误位置
- inline-source-map：内联，只生成一个内联 source-map，错误代码准确信息 和 源代码的错误位置
- hidden-source-map：外部，错误代码错误原因，但是没有错误位置（为了隐藏源代码），不能追踪源代码错误，只能提示到构建后代码的错误位置
- eval-source-map：内联，每一个文件都生成对应的 source-map，都在 eval 中，错误代码准确信息 和 源代码的错误位
- nosources-source-map：外部，错误代码准确信息，但是没有任何源代码信息（为了隐藏源代码）
- cheap-source-map：外部，错误代码准确信息 和 源代码的错误位置，只能把错误精确到整行，忽略列
- cheap-module-source-map：外部，错误代码准确信息 和 源代码的错误位置，module 会加入 loader 的 source-map

内联 和 外部的区别：1. 外部生成了文件，内联没有 2. 内联构建速度更快

开发/生产环境可做的选择：

**开发环境**：需要考虑速度快，调试更友好

- 速度快( eval > inline > cheap >... )
  1. eval-cheap-souce-map
  2. eval-source-map
- 调试更友好
  1. souce-map
  2. cheap-module-souce-map
  3. cheap-souce-map

**最终得出最好的两种方案 --> eval-source-map（完整度高，内联速度快） / eval-cheap-module-souce-map（错误提示忽略列但是包含其他信息，内联速度快）**

**生产环境**：需要考虑源代码要不要隐藏，调试要不要更友好

- 内联会让代码体积变大，所以在生产环境不用内联

- 隐藏源代码

  1. nosources-source-map 全部隐藏
  2. hidden-source-map 只隐藏源代码，会提示构建后代码错误信息

  

**最终得出最好的两种方案 --> source-map（最完整） / cheap-module-souce-map（错误提示一整行忽略列）**





### 8. 生产环境性能优化

#### 8.1 oneOf

**oneOf**：匹配到 loader 后就不再向后进行匹配，优化生产环境的打包构建速度

代码：

```js
module: {
  rules: [
    {
      // js 语法检查
      test: /\.js$/,
      exclude: /node_modules/,
      // 优先执行
      enforce: 'pre',
      loader: 'eslint-loader',
      options: {
        fix: true
      }
    },
    {
      // oneOf 优化生产环境的打包构建速度
      // 以下loader只会匹配一个（匹配到了后就不会再往下匹配了）
      // 注意：不能有两个配置处理同一种类型文件（所以把eslint-loader提取出去放外面）
      oneOf: [
        {
          test: /\.css$/,
          use: [...commonCssLoader]
        },
        {
          test: /\.less$/,
          use: [...commonCssLoader, 'less-loader']
        },
        {
          // js 兼容性处理
          test: /\.js$/,
          exclude: /node_modules/,
          loader: 'babel-loader',
          options: {
            presets: [
              [
                '@babel/preset-env',
                {
                  useBuiltIns: 'usage',
                  corejs: {version: 3},
                  targets: {
                    chrome: '60',
                    firefox: '50'
                  }
                }
              ]
            ]
          }
        },
        {
          test: /\.(jpg|png|gif)/,
          loader: 'url-loader',
          options: {
            limit: 8 * 1024,
            name: '[hash:10].[ext]',
            outputPath: 'imgs',
            esModule: false
          }
        },
        {
          test: /\.html$/,
          loader: 'html-loader'
        },
        {
          exclude: /\.(js|css|less|html|jpg|png|gif)/,
          loader: 'file-loader',
          options: {
            outputPath: 'media'
          }
        }
      ]
    }
  ]
},
```



#### 8.2 babel 缓存

**babel 缓存**：类似 HMR，将 babel 处理后的资源缓存起来（哪里的 js 改变就更新哪里，其他 js 还是用之前缓存的资源），让第二次打包构建速度更快

```js
{
  test: /\.js$/,
  exclude: /node_modules/,
  loader: 'babel-loader',
  options: {
    presets: [
      [
        '@babel/preset-env',
        {
          useBuiltIns: 'usage',
          corejs: { version: 3 },
          targets: {
            chrome: '60',
            firefox: '50'
          }
        }
      ]
    ],
    // 开启babel缓存
    // 第二次构建时，会读取之前的缓存
    cacheDirectory: true
  }
},
```

**文件资源缓存**：

文件名不变，就不会重新请求，而是再次用之前缓存的资源

`filename: 'css/built.[hash/chunkhash/contenthash:10].css'`

1. **hash**: 每次 wepack 打包时会生成一个唯一的 hash 值。

 问题：重新打包，所有文件的 hsah 值都改变，会导致所有缓存失效。（可能只改动了一个文件）



2. **chunkhash**：根据 chunk 生成的 hash 值。来源于同一个 chunk的 hash 值一样

 问题：js 和 css 来自同一个chunk，hash 值是一样的（因为 css-loader 会将 css 文件加载到 js 中，所以同属于一个chunk）



3. **contenthash**: 根据文件的内容生成 hash 值。不同文件 hash 值一定不一样(文件内容修改，文件名里的 hash 才会改变)

修改 css 文件内容，打包后的 css 文件名 hash 值就改变，而 js 文件没有改变 hash 值就不变，这样 css 和 js 缓存就会分开判断要不要重新请求资源 --> 让代码上线运行缓存更好使用



#### 8.3 tree shaking

**tree shaking**：去除无用代码（如没被调用的函数）

前提：1. 必须使用 ES6 模块化 2. 开启 production 环境 （这样就自动会把无用代码去掉）

作用：减少代码体积

在 package.json 中配置：

`"sideEffects": false` 表示会对所有代码进行 tree shaking

这样会导致的问题：可能会把 css / @babel/polyfill 文件干掉（副作用）

所以可以配置：`"sideEffects": ["*.css", "*.scss"]` 不会对css/less文件tree shaking处理

#### 8.4 code split

**代码分割**：将打包输出的一个大的 bundle.js 文件拆分成多个小文件，这样可以并行加载多个文件，比加载一个文件更快。

**1.多入口拆分**

```js
// webpack.config.js
entry: {
    // 多入口：有一个入口，最终输出就有一个bundle
    index: './src/js/index.js',
    test: './src/js/test.js'
  },
  output: {
    // [name]：取文件名
    filename: 'js/[name].[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
```

**2.optimization：**

```js
// webpack.config.js	
optimization: {
    splitChunks: {
      chunks: 'all'
    }
  },
```

- 将 import 的代码单独打包（大小超过30kb）
- 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk（比如两个模块中都引入了 jquery ， jquery 会被打包成单独的文件）（大小超过30kb）

**3.import 动态导入语法：**

```js
// index.js
/*
  通过js代码，让某个文件被单独打包成一个chunk
  import动态导入语法：能将某个文件单独打包(test文件不会和index打包在同一个文件而是单独打包)
  webpackChunkName:指定test单独打包后文件的名字
*/
import(/* webpackChunkName: 'test' */'./test')
  .then(({ mul, count }) => {
    // 文件加载成功
    // eslint-disable-next-line
    console.log(mul(2, 5));
  })
  .catch(() => {
    // eslint-disable-next-line
    console.log('文件加载失败~');
  });
```

#### 8.5 lazy loading（懒加载/预加载）

1. **懒加载**：当文件需要使用时才加载（需要代码分割）。但是如果资源较大，加载时间就会较长，有延迟。

2. **正常加载**：可以认为是并行加载（同一时间加载多个文件）没有先后顺序，先加载了不需要的资源就会浪费时间。

3. **预加载 prefetch**（兼容性很差）：会在使用之前，提前加载。等其他资源加载完毕，浏览器空闲了，再偷偷加载这个资源。这样在使用时已经加载好了，速度很快。所以在懒加载的基础上加上预加载会更好。

```js
// index.js
document.getElementById('btn').onclick = function() {
  // 将import的内容放在异步回调函数中使用，点击按钮，test.js才会被加载(不会重复加载)
  // webpackPrefetch: true表示开启预加载
  import(/* webpackChunkName: 'test', webpackPrefetch: true */'./test').then(({ mul }) => {
    console.log(mul(4, 5));
  });
  import('./test').then(({ mul }) => {
    console.log(mul(2, 5))
  })
};
```

#### 8.6 多进程打包

多进程打包：某个任务消耗时间较长会卡顿，多进程可以同一时间干多件事，效率更高。

优点是提升打包速度，缺点是每个进程的开启和交流都会有开销（babel-loader消耗时间最久，所以使用thread-loader针对其进行优化）

```js
{
  test: /\.js$/,
  exclude: /node_modules/,
  use: [
    /* 
      thread-loader会对其后面的loader（这里是babel-loader）开启多进程打包。 
      进程启动大概为600ms，进程通信也有开销。(启动的开销比较昂贵，不要滥用)
      只有工作消耗时间比较长，才需要多进程打包
    */
    {
      loader: 'thread-loader',
      options: {
        workers: 2 // 进程2个
      }
    },
    {
      loader: 'babel-loader',
      options: {
        presets: [
          [
            '@babel/preset-env',
            {
              useBuiltIns: 'usage',
              corejs: { version: 3 },
              targets: {
                chrome: '60',
                firefox: '50'
              }
            }
          ]
        ],
        // 开启babel缓存
        // 第二次构建时，会读取之前的缓存
        cacheDirectory: true
      }
    }
  ]
},
```

#### 8.7 externals

externals：让某些库不打包，通过 cdn 引入

webpack.config.js 中配置：

```js
externals: {
  // 拒绝jQuery被打包进来(通过cdn引入，速度会快一些)
  // 忽略的库名 -- npm包名
  jquery: 'jQuery'
}
```

需要在 index.html 中通过 cdn 引入：

```html
<script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
```

#### 8.8 dll

dll：让某些库单独打包，后直接引入到 build 中。可以在 code split 分割出 node_modules 后再用 dll 更细的分割，优化代码运行的性能。

webpack.dll.js 配置：(将 jquery 单独打包)

```js
/*
  node_modules的库会打包到一起，但是很多库的时候打包输出的js文件就太大了
  使用dll技术，对某些库（第三方库：jquery、react、vue...）进行单独打包
  当运行webpack时，默认查找webpack.config.js配置文件
  需求：需要运行webpack.dll.js文件
    --> webpack --config webpack.dll.js（运行这个指令表示以这个配置文件打包）
*/
const { resolve } = require('path');
const webpack = require('webpack');

module.exports = {
  entry: {
    // 最终打包生成的[name] --> jquery
    // ['jquery] --> 要打包的库是jquery
    jquery: ['jquery']
  },
  output: {
    // 输出出口指定
    filename: '[name].js', // name就是jquery
    path: resolve(__dirname, 'dll'), // 打包到dll目录下
    library: '[name]_[hash]', // 打包的库里面向外暴露出去的内容叫什么名字
  },
  plugins: [
    // 打包生成一个manifest.json --> 提供jquery的映射关系（告诉webpack：jquery之后不需要再打包和暴露内容的名称）
    new webpack.DllPlugin({
      name: '[name]_[hash]', // 映射库的暴露的内容名称
      path: resolve(__dirname, 'dll/manifest.json') // 输出文件路径
    })
  ],
  mode: 'production'
};
```

webpack.config.js 配置：(告诉 webpack 不需要再打包 jquery，并将之前打包好的 jquery 跟其他打包好的资源一同输出到 build 目录下)

```js
// 引入插件
const webpack = require('webpack');
const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');

// plugins中配置：
plugins: [
  new HtmlWebpackPlugin({
    template: './src/index.html'
  }),
  // 告诉webpack哪些库不参与打包，同时使用时的名称也得变
  new webpack.DllReferencePlugin({
    manifest: resolve(__dirname, 'dll/manifest.json')
  }),
  // 将某个文件打包输出到build目录下，并在html中自动引入该资源
  new AddAssetHtmlWebpackPlugin({
    filepath: resolve(__dirname, 'dll/jquery.js')
  })
],
```





### 9. webpack配置详情

entry: 入口起点

1. string --> './src/index.js'，单入口

   打包形成一个 chunk。 输出一个 bundle 文件。此时 chunk 的名称默认是 main

2. array --> ['./src/index.js', './src/add.js']，多入口

   所有入口文件最终只会形成一个 chunk，输出出去只有一个 bundle 文件。

   （一般只用在 HMR 功能中让 html 热更新生效）

3. object，多入口

   有几个入口文件就形成几个 chunk，输出几个 bundle 文件，此时 chunk 的名称是 key 值

--> 特殊用法：

```
entry: {
  // 最终只会形成一个chunk, 输出出去只有一个bundle文件。
  index: ['./src/index.js', './src/count.js'], 
  // 形成一个chunk，输出一个bundle文件。
  add: './src/add.js'
}
```

#### 9.1 output

```js
output: {
  // 文件名称（指定名称+目录）
  filename: 'js/[name].js',
  // 输出文件目录（将来所有资源输出的公共目录）
  path: resolve(__dirname, 'build'),
  // 所有资源引入公共路径前缀 --> 'imgs/a.jpg' --> '/imgs/a.jpg'
  publicPath: '/',
  chunkFilename: 'js/[name]_chunk.js', // 指定非入口chunk的名称
  library: '[name]', // 打包整个库后向外暴露的变量名
  libraryTarget: 'window' // 变量名添加到哪个上 browser：window
  // libraryTarget: 'global' // node：global
  // libraryTarget: 'commonjs' // conmmonjs模块 exports
},
```

#### 9.2 module

```js
module: {
  rules: [
    // loader的配置
    {
      test: /\.css$/,
      // 多个loader用use
      use: ['style-loader', 'css-loader']
    },
    {
      test: /\.js$/,
      // 排除node_modules下的js文件
      exclude: /node_modules/,
      // 只检查src下的js文件
      include: resolve(__dirname, 'src'),
      enforce: 'pre', // 优先执行
      // enforce: 'post', // 延后执行
      // 单个loader用loader
      loader: 'eslint-loader',
      options: {} // 指定配置选项
    },
    {
      // 以下配置只会生效一个
      oneOf: []
    }
  ]
},
```

#### 9.3 resolve

```js
// 解析模块的规则
resolve: {
  // 配置解析模块路径别名: 优点：当目录层级很复杂时，简写路径；缺点：路径不会提示
  alias: {
    $css: resolve(__dirname, 'src/css')
  },
  // 配置省略文件路径的后缀名（引入时就可以不写文件后缀名了）
  extensions: ['.js', '.json', '.jsx', '.css'],
  // 告诉 webpack 解析模块应该去找哪个目录
  modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
}
```

这样配置后，引入文件就可以这样简写：`import '$css/index';`

#### 9.4 dev server

```js
devServer: {
  // 运行代码所在的目录
  contentBase: resolve(__dirname, 'build'),
  // 监视contentBase目录下的所有文件，一旦文件变化就会reload
  watchContentBase: true,
  watchOptions: {
    // 忽略文件
    ignored: /node_modules/
  },
  // 启动gzip压缩
  compress: true,
  // 端口号
  port: 5000,
  // 域名
  host: 'localhost',
  // 自动打开浏览器
  open: true,
  // 开启HMR功能
  hot: true,
  // 不要显示启动服务器日志信息
  clientLogLevel: 'none',
  // 除了一些基本信息外，其他内容都不要显示
  quiet: true,
  // 如果出错了，不要全屏提示
  overlay: false,
  // 服务器代理，--> 解决开发环境跨域问题
  proxy: {
    // 一旦devServer(5000)服务器接收到/api/xxx的请求，就会把请求转发到另外一个服务器3000
    '/api': {
      target: 'http://localhost:3000',
      // 发送请求时，请求路径重写：将/api/xxx --> /xxx （去掉/api）
      pathRewrite: {
        '^/api': ''
      }
    }
  }
}
```

其中，跨域问题：同源策略中不同的协议、端口号、域名就会产生跨域。

正常的浏览器和服务器之间有跨域，但是服务器之间没有跨域。代码通过代理服务器运行，所以浏览器和代理服务器之间没有跨域，浏览器把请求发送到代理服务器上，代理服务器替你转发到另外一个服务器上，服务器之间没有跨域，所以请求成功。代理服务器再把接收到的响应响应给浏览器。这样就解决开发环境下的跨域问题。

#### 9.5 optimization

contenthash 缓存会导致一个问题：修改 a 文件导致 b 文件 contenthash 变化。
因为在 index.js 中引入 a.js，打包后 index.js 中记录了 a.js 的 hash 值，而 a.js 改变，其重新打包后的 hash 改变，导致 index.js 文件内容中记录的 a.js 的 hash 也改变，从而重新打包后 index.js 的 hash 值也会变，这样就会使缓存失效。（改变的是a.js文件但是 index.js 文件的 hash 值也改变了）
解决办法：runtimeChunk --> 将当前模块记录其他模块的 hash 单独打包为一个文件 runtime，这样 a.js 的 hash 改变只会影响 runtime 文件，不会影响到 index.js 文件

```js
output: {
  filename: 'js/[name].[contenthash:10].js',
  path: resolve(__dirname, 'build'),
  chunkFilename: 'js/[name].[contenthash:10]_chunk.js' // 指定非入口文件的其他chunk的名字加_chunk
},
optimization: {
  splitChunks: {
    chunks: 'all',
    /* 以下都是splitChunks默认配置，可以不写
    miniSize: 30 * 1024, // 分割的chunk最小为30kb（大于30kb的才分割）
    maxSize: 0, // 最大没有限制
    minChunks: 1, // 要提取的chunk最少被引用1次
    maxAsyncRequests: 5, // 按需加载时并行加载的文件的最大数量为5
    maxInitialRequests: 3, // 入口js文件最大并行请求数量
    automaticNameDelimiter: '~', // 名称连接符
    name: true, // 可以使用命名规则
    cacheGroups: { // 分割chunk的组
      vendors: {
        // node_modules中的文件会被打包到vendors组的chunk中，--> vendors~xxx.js
        // 满足上面的公共规则，大小超过30kb、至少被引用一次
        test: /[\\/]node_modules[\\/]/,
        // 优先级
        priority: -10
      },
      default: {
        // 要提取的chunk最少被引用2次
        minChunks: 2,
        prority: -20,
        // 如果当前要打包的模块和之前已经被提取的模块是同一个，就会复用，而不是重新打包
        reuseExistingChunk: true
      }
    } */
  },
  // 将index.js记录的a.js的hash值单独打包到runtime文件中
  runtimeChunk: {
    name: entrypoint => `runtime-${entrypoint.name}`
  },
  minimizer: [
    // 配置生产环境的压缩方案：js/css
    new TerserWebpackPlugin({
      // 开启缓存
      cache: true,
      // 开启多进程打包
      parallel: true,
      // 启用sourceMap(否则会被压缩掉)
      sourceMap: true
    })
  ]
}
```