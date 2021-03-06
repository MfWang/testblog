---
layout: default
title: Webpack+React+多语言(一) 基础配置
---
>已经看过这一节的朋友，可以看[Webpack+React+多语言(二) 插件配置+多语言](http://www.jianshu.com/p/dce81ab8940f)
github地址：[webpack-react-language](https://github.com/AveSore/webpack-react-language)
关键字：react 多语言 国际版 React-intl

移动端网站需要重构，当前的想法是使用webpack+react来重构移动端，当然，是需要支持多语言的。

* #####1.创建一个项目：webpack-react-language
这个项目名字有点长，我主要是为了与本地其他的项目区别开来
目录结构
```
|- build
    |-- index.html
    |-- bundle.js(该文件是webpack打包后生成的)
|- wap
    |-- components
        |--- Dialog.jsx
    |-- main.js
|- package.json(第二步生成)
|- webpack.config.js(第四步生成)
```
* #####2.创建package.json
```
npm init
```
* #####3.安装webpack
```
//全局安装webpack，优点是打包时可以直接输webpack命令
npm install -g webpack
//在本项目中安装webpack，--save-dev的意思是将依赖写入项目的package.json文件
npm install --save-dev webpack
```
* #####4.创建webpack.config.js配置文件
```
module.exports = { 
    entry: __dirname + "/wap/main.js",//唯一入口文件，就像Java中的main方法 
    output: {//输出目录 
        path: __dirname + "/build",//打包后的js文件存放的地方 
        filename: "bundle.js"//打包后的js文件名 
    }
};
```
运行`webpack`，webpack非全局安装需输入`node_modules/.bin/webpack`
![webpack打包成功.png](http://upload-images.jianshu.io/upload_images/1874069-c8032cf277d955ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
看到以上提示说明打包成功了，可以看到build目录下的bundle.js里多了很多自动生成的代码，但预览index.html却什么都没有看到，这是因为还没有往页面中写入内容。
* #####5.更方便地执行打包命令
执行类似于`node_modules/.bin/webpack`这样的命令其实是比较烦人且容易出错的，不过值得庆幸的是npm可以引导任务执行，对其进行配置后可以使用简单的npm start命令来代替这些繁琐的命令。在package.json中对npm的脚本部分进行相关设置即可，设置方法如下。
打开package.json，找到script代码块，更改为：
```
"scripts": { 
    "build": "webpack"
    或者
    "start":"webpack"
}
```
但是start是一个特殊的脚本名称，如果脚本名称不是start，则需要`npm run (script name)`，如`npm run build`，但是start可以直接执行`npm start`
* #####6.使用Source Maps，使调试更容易

| devtool选项 | 配置结果 |
| ------------- |-------------|
|source-map |在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包文件的构建速度 | 
| cheap-module-source-map| 在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便 | 
| eval-source-map | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项 | 
|cheap-module-eval-source-map|这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点|

>正如上表所述，上述选项由上到下打包速度越来越快，不过同时也具有越来越多的负面作用，较快的构建速度的后果就是对打包后的文件的的执行有一定影响。

>在学习阶段以及在小到中性的项目上，eval-source-map是一个很好的选项，不过记得只在开发阶段使用它，继续上面的例子，进行如下配置

```
/webpack.config.js
module.exports = { 
    devtool: 'eval-source-map',//生成Source Maps,这里选择eval-source-map 
    entry: __dirname + '/app/main.js',//唯一入口文件,__dirname是node.js中的一个全局变量，它指向当前执行脚本所在的目录 
    output: {//输出目录 
        path: __dirname + '/build',//打包后的js文件存放的地方 
        filename: 'bundle.js'//打包后输出的js的文件名 
    }
};
```

* #####7.安装React、Babel
```
npm install react --save-dev
npm install react-dom --save-dev
npm install babel-core --save-dev
npm install babel-loader --save-dev
npm install babel-preset-es2015 --save-dev
npm install babel-preset-react --save-dev
```
![基本插件安装成功之后的package.json.png](http://upload-images.jianshu.io/upload_images/1874069-489388792f567e65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在webpack.config.js中
```
/webpack.config.js
module.exports = { 
    devtool: 'eval-source-map',//生成Source Maps,这里选择eval-source-map 
    entry: __dirname + '/app/main.js',//唯一入口文件,__dirname是node.js中的一个全局变量，它指向当前执行脚本所在的目录 
    output: {//输出目录 
        path: __dirname + '/build',//打包后的js文件存放的地方 
        filename: 'bundle.js'//打包后输出的js的文件名 
    },
    module: {    //loaders加载器    
        loaders: [{            
            test: /\.(js|jsx)$/,//一个匹配loaders所处理的文件的拓展名的正则表达式，这里用来匹配js和jsx文件（必须）            
            exclude: /node_modules/,//屏蔽不需要处理的文件（文件夹）（可选）            
            loader: 'babel'//loader的名称（必须）        
        }]
    }
};
```
在项目根目录下新建.babelrc文件，没错你没看错，就是只有后缀名的文件，添加如下代码：
```
//.babelrc
{ 
    "presets": [ 
        "react",
        "es2015" 
    ]
}
```
* #####8.编写页面文件，为后续测试功能做准备
`wap/main.js`
```
import React from 'react';
import ReactDom from 'react-dom';
import Component1 from './components/dialog.jsx';
ReactDom.render(    
    <Component1 />,    
    document.getElementById('content')
);
```
`wap/components/dialog.js`
```
import React from 'react';
class Dialog extends React.Component {    
    render() {        
        return (            
            <div>Hello World!</div>        
        )    
    }
}
//导出组件
export default Dialog;
```
`build/index.html`
```
<!DOCTYPE html>
<html lang="en">
<head>    
    <meta charset="UTF-8">    
    <title>Title</title>
</head>
<body>    
    <div id="content"></div>    
    <script src="bundle.js"></script>
</body>
</html>
```
我使用的编辑器是webstorm，上述步骤完成之后，执行`npm start`，打包成功后，运行index.html，可以看到页面上
![成功运行.png](http://upload-images.jianshu.io/upload_images/1874069-eaa2c46bc0dcb668.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* #####9.安装并启用webpack-dev-server

执行到这一步，基本的运作是可以了，但是每次修改文件之后，都需要重新打包，再运行，才可以看到新的内容，如何能忍受这么low的执行过程呢？

我们可以使用webpack-dev-server来搭建本地开发服务器，webpack-dev-server允许我们可以把本地项目跑在像nginx那样的web服务器上，修改代码后，立即可以看到变化；所见即所得，大大增加开发效率。

安装webpack-dev-server
```
npm install webpack-dev-server -g
npm install webpack-dev-server --save-dev
```
更改packaje.json中的代码块
```
"scripts": { 
    "start": "webpack", 
    "dev": "webpack-dev-server --devtool eval --progress --colors --content-base build"
}
```
ps：dev里各属性值的意思是：
>* webpack-dev-server: 在 localhost:8080 建立一个 Web 服务器
>*  --devtool eval:为你的代码创建源地址。当有任何报错的时候可以让你更加精确地定位到文件和行号
>*  --progress: 显示合并代码进度
>*  --colors: 在命令行中显示颜色
>* --content-base build:指向设置的输出目录

在webpack.config.js中配置webpack-dev-server，在这里需要修改下entry的路径，给它加一个webpack/hot/dev-server，这里会用到Hot Module Replacement（热替换）插件，所以需要增加这个前缀，后文会提到，代码如下：
```
//webpack.config.js
module.exports = { 
    devtool: 'eval-source-map',//生成Source Maps,这里选择eval-source-map 
    entry: ['webpack/hot/dev-server', __dirname + '/app/main.js'],//唯一入口文件,__dirname是node.js中的一个全局变量，它指向当前执行脚本所在的目录 
    output: {//输出目录 
        path: __dirname + '/build',//打包后的js文件存放的地方 
        filename: 'bundle.js'//打包后输出的js的文件名 
    }, 
    module: { //loaders加载器 
        loaders: [ { 
            test: /\.(js|jsx)$/,//一个匹配loaders所处理的文件的拓展名的正则表达式，这里用来匹配js和jsx文件（必须） 
            exclude: /node_modules/,//屏蔽不需要处理的文件（文件夹）（可选） 
            loader: 'babel'//loader的名称（必须） 
        } ] 
    }, 
    //webpack-dev-server配置 
    devServer: { 
        contentBase: './build',//默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到"build"目录） 
        colors: true,//在cmd终端中输出彩色日志 
        historyApiFallback: true,//在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html 
        inline: true,//设置为true，当源文件改变时会自动刷新页面 
        port: 8080,//设置默认监听端口，如果省略，默认为"8080" 
        process: true,//显示合并代码进度 
    }
};
```
在浏览器中打开`http://localhost:8080/`，如果页面上是空白的，没有内容，按F12打开控制台，可以清除地看到错误提示：`Uncaught Error: [HMR] Hot Module Replacement is disabled`
现在我们来安装和配置这个Hot Module Replacement。
* #####10.Hot Module Replacement

Hot Module Replacement（HMR）是webpack里很有用的一个插件，它允许你在修改组件代码后，自动刷新实时预览修改后的效果。
在webpack中实现HMR也很简单，只需要做两项配置
>1.在webpack配置文件中添加HMR插件；
2.在Webpack Dev Server中添加“hot”参数；

不过配置完这些后，JS模块其实还是不能自动热加载的，还需要在你的JS模块中执行一个Webpack提供的API才能实现热加载，虽然这个API不难使用，但是如果是React模块，使用我们已经熟悉的Babel可以更方便的实现功能热加载。
整理下我们的思路，具体实现方法如下
>* Babel和webpack是独立的工具
* 二者可以一起工作
* 二者都可以通过插件拓展功能
*  HMR是一个webpack插件，它让你能浏览器中实时观察模块修改后的效果，但是如果你想让它工作，需要对模块进行额外的配额；
* Babel有一个叫做react-transform-hrm的插件，可以在不对React模块进行额外的配置的前提下让HMR正常工作；

在webpack.config.js中配置如下：
```
//webpack.config.js
var webpack = require('webpack')
module.exports = { 
    devtool: 'eval-source-map',//生成Source Maps,这里选择eval-source-map 
    entry: ['webpack/hot/dev-server', __dirname + '/app/main.js'],//唯一入口文件,__dirname是node.js中的一个全局变量，它指向当前执行脚本所在的目录 
    output: {//输出目录 
        path: __dirname + '/build',//打包后的js文件存放的地方 
        filename: 'bundle.js'//打包后输出的js的文件名 
    }, 
    module: { //loaders加载器 
        loaders: [ { 
            test: /\.(js|jsx)$/,//一个匹配loaders所处理的文件的拓展名的正则表达式，这里用来匹配js和jsx文件（必须） 
            exclude: /node_modules/,//屏蔽不需要处理的文件（文件夹）（可选） 
            loader: 'babel'//loader的名称（必须） 
        } ] 
    }, 
    plugins: [    
        new webpack.HotModuleReplacementPlugin()//热模块替换插件
    ],
    //webpack-dev-server配置 
    devServer: { 
        contentBase: './build',//默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到"build"目录） 
        colors: true,//在cmd终端中输出彩色日志 
        historyApiFallback: true,//在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html 
        inline: true,//设置为true，当源文件改变时会自动刷新页面 
        port: 8080,//设置默认监听端口，如果省略，默认为"8080" 
        process: true,//显示合并代码进度 
    }
};
```
现在可以正常工作了，运行`npm run dev`，待命令行提示`webpack: bundle is now VALID`后，在浏览器中输入`http://localhost:8080`，可以看到，正常显示Hello World
如果觉得这样不放心，让我们进行如下额外配置

安装react-transform-hmr，在不对React模块进行额外的配置的前提下让HMR正常工作
```
npm install --save-dev babel-plugin-react-transform react-transform-hmr
```
在.babelrc文件里配置babel，注意这里有一堆括号，别写错了
```
//.babelrc
{ 
    "presets": [ 
        "react", 
        "es2015" 
    ], 
    "env": { 
        "development": { 
            "plugins": [ 
                [ 
                    "react-transform", { 
                        "transforms": [{ 
                                "transform": "react-transform-hmr", 
                                "imports": [ "react" ], 
                                "locals": [ "module" ] 
                        }] 
                    }
                ]
             ]
         } 
    }
}
```
至此，已经用webpack构建好了React项目的基础依赖，可以愉快的开发React程序了。

>由于内容太多，于是将本文分成了多个部分，下一部分[Webpack+React+多语言(二) 插件配置+多语言](http://www.jianshu.com/p/dce81ab8940f)
github地址：[webpack-react-language](https://github.com/AveSore/webpack-react-language)
