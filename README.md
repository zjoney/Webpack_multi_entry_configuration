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



![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/4.gif)
