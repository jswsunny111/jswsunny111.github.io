---
title: webpack配置学习
date: 2022-06-25 22:27:33
tags: 原生
category: webpack
---

## 认识 [webpack](https://webpack.docschina.org/)

webpack 是前端一直流行很火的打包，构建工具。 所需要 node 环境 ，今天从零开始学习

-   entry 入口
-   output 出口
-   mode 环境 process.env.NODE_ENV 生产环境还是开发环境等等
-   loader 老生常谈的 用于各源代码进行转换 加载 引用等等
-   plugins 是我们在配置插件的地方。一般用来解决 loader 实现不了的工作

## 创建 webpack 工程

1. 生成 package.json 文件
   npm init -y

2.安装 webpack webpack-cli

npm i -D webpack webpack-cli

-S 需要发布的
—D 不需要发布

## webpack 零配置 默认打包

只打包 js 压缩 生成 main.js
默认出口 dist/main.js
默认出口 src/index.js

## 安装 webpack 脚手架

npm i -D webpack-dev-server

```
 devServer: {
        // 配置webpack-dev-server
        port: 8083, //配置服务器端口
        open: true, // 自动打开浏览器
        progress: true, //进度
        contentBase: './dist', //指定的web服务器根目录
    }
```

## css

webpack 默认只加载 js 不支持 css 加载
需要 css 装载器 css-loader style-loader 把 css 直接添加到 Html style 标签中

## 装载器执行顺序

先加载 css 后整合到 html style 标签中 数组从后至前执行

## less 装载器

npm i -D less-loader

## css 抽取器

npm i -D mini-css-extract-plugin
便于 css 压缩合并 处理缓存
npm i -D optimize-css-assets-webpack-plugin
js: terser-webpack-plugin

## Es6 Es7 转 Es5

npm i -D babel-loader
@babel/core
@babel/preset-env 转换 Es5
@babel/plugin-proposal-decorators //装饰器

## js 模块化

commonjs:
导出： module.exports 导入：require()
导出： export default xxx 导入 import xx from xxx

# tree shaking(去掉无引用代码)

webpack4 有这个功能 支持当前 js 中无用的代码
webpack5 加强这个功能
a.js （有 无引用代码 ）去掉
只在生产模式下才会清掉

# 打包生成的 dist

没有分类存储 js css 这些文件
在配置中分别输出的 filename 项中 配置 输出路径 js/index.js || css/index.css

灵活配置 不同的 css /js 文件 将 js/index.js => js/[name].[hash].js

```
  output: {
        path: path.resolve(__dirname, 'dist'), //出口
        filename: 'js/[name].[hash].js', //出口名称
    },
```

因为浏览器的缓存机制 加上 hash 值 每次改动后的文件长度不一致 所以每次文件都会 确认使用的最新文件

# 全局变量

使用 improt 引入的文件需要在使用的地方都要做一次引入

使用 webpack 自带插件 webpack.ProvidepPlugin 可以设置全局变量
如 jq 类似第三方包项目中引用

```
 plugins: [
        new webpack.ProvidePlugin({
            $: 'jqery',
        }),
    ],
```

## 配置后，文件可以不用引入 ，但打包体积不会变小

配置 externals 使用 不打包 文件

```
externals :{ //排除第三方
   jqery :'$'
}
```

# 图片导入

使用装载器 引入 file-loader | url-loader
npm i -D file-loader url-loader
在 module 中配置

```
module :{
    rules:[
         {
                test: /\.(jpg|png|jpeg)$/,
                use: {
                    loader: 'file-loader',
                    options: { esModule: false},//使用common.js 方式
                },
            },
    ]
}
```

## 使用 html-withimg-loader

在 html 中加载图片

```
module :{
    rules:[
         {
                test: /\.html$/,
                use: { loader: 'html-with-loader' },
            },
    ]
}
```

# 图片加载 url-loader

1. 对图片小的 做 base64 转换
2. 对较大的图片 直接提供 url 下载 可以使用 cdn 优化 ，页面请求过多 减少请求次数

```
module :{
    rules:[
         {
                test: /\.(jpg|png|jpeg)$/,
                use: {
                    loader: 'url-loader',
                    options: { esModule: false,
                    limt:100*1024,//指小于100KB的图片自动转换base64
                    },//使用common.js 方式
                },
            },
    ]
}
```

# 样式兼容

因为浏览器厂商不一致 导致样式的适配不一致
一些 c3 新属性 兼容程度不一致  
npm i -D outoprefixer postcss-loader

```
// 新建postcss.config.js

//在 page.json 文件中配置
"browserslist":[
    "> 1%", //使用率大于1/100
    "last 100 versions", //对当前浏览器100个版本转换
    "not ie <= 8" //不转换ie8以下
]
// webpack.config.js 中配置
{
    test: /\.less$/,
    use: [
        MiniCssExtractPlugin.loader,
        'css-loader',
        'less-loader',
        'postcss-loader',
    ], //从后至前加载
},
```

![BG图片](/img/1.jpg)
