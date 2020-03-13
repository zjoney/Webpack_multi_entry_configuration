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

![image](https://github.com/zjoney/Webpack_multi_entry_configuration/blob/entry-vue3/images/4.gif)
