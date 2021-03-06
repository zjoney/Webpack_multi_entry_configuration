### 3.2 简单配置
要想从不同入口，打包出不同 HTML，我们可以改变一下 entry 和 output 两个配置，
```javascript

// build/webpack.prod.conf.js

module.exports = {
  entry: {
    entry1: './src/main.js',
    entry2: './src/main2.js'
  },
  output: {
    path: config.build.assetsRoot,
    filename: '[name].js',
    publicPath: process.env.NODE_ENV === 'production'
      ? config.build.assetsPublicPath
      : config.dev.assetsPublicPath
  },

  plugins: [
    new HtmlWebpackPlugin({
      filename: process.env.NODE_ENV === 'testing'
        ? 'index.html'
        : config.build.index,
      template: 'index.html',// 打包输出后该html文件的名称
    ]
}
```
根据上面一小节我们知道，webpack 配置里的 output.filename 如果有 [name] 意为根据入口文件的名称，打包成对应名称的 JS 文件，那么现在我们是可以根据两个入口打包出 entry.js 和 entry2.js。

打包的结果如下：

![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/1.png)

当前代码：Github - multi-entry-vue1

如上图，此时我们 npm run build 打包出一个引用了这两个文件的 index.html，那么如何打包出不同 HTML 文件，分别应用不同入口 JS 文件呢，此时我们需要借助于 HtmlWebpackPlugin 这个插件。

HtmlWebpackPlugin 这个插件， new 一个，就打包一个 HTML 页面，所以我们在 plugins 配置里 new 两个，就能打包出两个页面来。

### 3.3 打包出不同的 HTML 页面
我们把配置文件改成下面这样：

```javascript
// build/webpack.base.conf.js
module.exports = {
  entry: {
    entry1: './src/main.js', // 打包输出的chunk名为entry
    entry2: './src/main2.js'
  },
  output: {
    path: config.build.assetsRoot,
    filename: '[name].js',
    publicPath: process.env.NODE_ENV === 'production'
      ? config.build.assetsPublicPath
      : config.dev.assetsPublicPath
  },

  plugins: [
    new HtmlWebpackPlugin({
      filename: 'entry.html',
      template: 'index.html',
      chunks: ['manifest', 'vendor', 'entry'], //  输出的html文件引入的入口chunk
    ],
    new HtmlWebpackPlugin({
      filename: 'entry2.html',
      template: 'index.html',
      chunks: ['manifest', 'vendor', 'entry2'], 
    ]
}
```
上面一个配置要注意的是 chunks，如果没有配置，那么生成的 HTML 会引入所有入口 JS 文件，在上面的例子就是，生成的两个 HTML 文件都会引入 entry.js 和 entry2.js，所以要使用 chunks 配置来指定生成的 HTML 文件应该引入哪个 JS 文件。配置了 chunks 之后，才能达到不同的 HTML 只引入对应 chunks 的 JS 文件的目的。

大家可以看到除了我们打包生成的 chunk 文件 entry.js 和 entry2.js 之外，还有 manifest 和 vendor 这两个，这里稍微解释一下这两个 chunk：

 1. vendor 是指提取涉及 node_modules 中的公共模块；

 2. manifest 是对 vendor 模块做的缓存；

打包完的结果如下:

![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/2.png)

文件结构：

![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/3.png)



现在打包出来的样式正是我们所需要的，此时我们在 `dist` 目录下启动 `live-server`（如果你没安装的话可以先安装 `npm i-g live-server`），就可以看到效果出来了：

![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/4.gif)

至此就实现了一个简单的多入口项目的配置。

## 4. 配置改进

### 4.1 文件结构改动

我们在前文进行了多入口的配置，要想新建一个新的入口，就复制多个文件，再手动改一下对应配置。

但是如果不同的 HTML 文件下不同的 vue-router、 vuex 都放到 src 目录下，多个入口的内容平铺在一起，项目目录会变得凌乱不清晰，因此在下将多入口相关的文件放到一个单独的文件夹中，以后如果有多入口的内容，就到这个文件夹中处理。

下面我们进行文件结构的改造：

首先我们在根目录创建一个 entries 文件夹，把不同入口的 router、 store、 main.js 都放这里，每个入口相关单独放在一个文件夹；

在 src 目录下建立一个 common 文件夹，用来存放多入口共用的组件等；

现在的目录结构：

```javascript
.

├── build

├── config

├── entries
│   ├── entry1

│   │   ├── router # entry1 的 router

│   │   │   └── index.js

│   │   ├── store # entry1 的 store

│   │   │   └── index.js

│   │   ├── App.vue # entry1 的根组件

│   │   ├── index.html # entry1 的页面模版

│   │   └── main.js # entry1 的入口

│   └── entry2

│       ├── router

│       │   └── index.js

│       ├── store

│       │   └── index.js

│       ├── App.vue

│       ├── index.html

│       └── main.js

├── src

│   ├── assets

│   │   └── logo.png

│   ├── components
│       ├── HelloWorld.vue

│       ├── test1.vue

│       ├── test2.vue

│       └── test3.vue

│   ├── router

│   │   └── index.js

│   ├── App.vue

│   ├── App2.vue # 新增的入口

│   ├── main.js

│   └── main2.js # 新增的入口

├── static

├── README.md

├── index.html

└── package.json
```

### 4.2 webpack 配置





