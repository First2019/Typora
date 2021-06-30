创建一个基于 webpack 模板的新项目，my-project为项目名称，我这边是test011

vue init webpack my-project

推荐使用 npm 的方式安装，它能更好地和 [webpack](https://webpack.js.org/) 打包工具配合使用。

```shell
elementUI: npm i element-ui -S
```

vue项目中使用less：
1、安装依赖：
npm install less less-loader --save-dev
或
cnpm install less less-loader --save-dev  

2、配置文件：
在webpack.config.js文件中添加如下代码：

```
module:{
  rules:[
  	{
	  test: /\.less$/,
	  loader: "style-loader!css-loader!less-loader",
    }
  ]
}
```


3、在页面中使用：
代码如下：

<style scoped lang="less">
/* 样式代码 */
</style>
4、问题解决：
当npm run dev运行时，出现报错，有可能是less-loader版本过高导致的，解决办法如下：
	npm uni less less-loader
	npm i less less-loader@4.1.0  --save-dev
