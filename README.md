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

打包完的结果如下
![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/2.png)


![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/4.gif)
