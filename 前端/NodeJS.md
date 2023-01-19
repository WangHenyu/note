NodeJS下载地址 https://nodejs.org/dist/v14.18.3/



# 设置工作区

1.创建空文件夹

2.用VSCode打开该文件夹

3.将工作区另存为



# npm

npm仓库地址  https://www.npmjs.com/

```shell
# 查看npm版本
npm -v

# 查看帮助命令
npm help

# 初始化
npm init
# -y 无需再填写信息
# name:项目名称
# version：项目版本号
# description：项目描述
# entry point：项目入口
# test command：测试命令
# git repository：git仓库地址
# author：作者
# keywords：关键字
# license：授权协议
# 最后会生成package.json文件,这个文件是包的配置闻不见，相当于maven中的pom.xml

# 安装模块
npm install xxx 或者 npm i xxx
# 安装多个模块
npm install 模块1 模块2 模块3
# 安装指定版本号模块
npm install xxx@版本号
# 安装的模块放在node_modules文件夹中

# 卸载模块
npm uninstall xxx

# 配置阿里云镜像
npm config set registry https://registry.npm.taobao.org
# 或
npm install -g cnpm --registry=https://registry.npm.taobao.org
# -g 全局安装

#查看npm配置信息
npm config list

# 查看cnpm版本信息
cnpm -v

# cnpm用法和npm一样
cnpm install xxx 
cnpm i xxx 
cnpm uninstall xxx

# 查看安装的模块
npm ls
# 检查模块是否过时
npm outdated
# 更新模块
npm update

# 查看包的安装路径
npm root

# 清理npm本地缓存
npm cache clean

# 查看模块的注册信息
npm view 

# 查看模块的依赖关系
npm view xxx dependencies
# 查看模块的源文件地址
npm view xxx repository.url
```



```shell
npm常用的安装命令
 
npm i 就是npm install 简写
npm i xxx -D   就是 npm i xxx --save-dev
npm i xxx -S   就是 npm i xxx --save
npm i xxx -g   就是 全局安装
npm i xxx      就是本地安装

# 卸载也有 -D -S -g 属性
# npm i xxx --save-dev 是把依赖写入进devDependencies对象里面
# npm i xxx --save 是把依赖写入进dependencies对象里面
# npm i xxx -g 就是安装到全局下，在命令行的任何地方都可以操作，不会提示“命令不存在等错误”
# npm i xxx 就是安装到当前命令行下的目录中
```



# Babel转码

安装命令行转码工具

~~~shell
npm install --global babel-cli
# 查看是否安装
babel --version
~~~

1.初始化项目

2.创建ES6语法的JS文件

3.配置.babelrc(固定名字)

~~~js
{
 "presets": ["es2015"],
 "plugins": []
}
~~~



4.安装转码器

~~~shell
npm install --save-dev babel-preset-es2015
~~~



5.转码

~~~shell
# 转码单个文件 [--out-file 简写 -o]
babel src/demo.js --out-file dist/demoCompiled.js
# 转码整个目录 [--out-dir 简写 -d]
babel src --out-dir dist
~~~



# Nodejs

Nodejs中文文档	https://www.nodeapp.cn/

## Nodejs使用express框架

```
1.创建文件夹用VSCode打开
2.npm init初始化
3.npm i express 安装express
4.创建server.js文件
5.编写下面代码
6.node server启动
```

```js
const express = require('express')

const app = express()

app.use(express.static(__dirname+'/static'))

app.get('/person',(req,res)=>{
    res.send({
        name:'Jack',
        age: 20
    })
})

app.listen(5000,(err)=>{
    if(!err)
        console.log('服务器启动成功');
})
```



## Nodejs实现Httpserver服务

```js
//导入模块：require（ES5）、import（ES6）
const http = require('http');

//1.创建httpserver服务
//2.监听端口8888
//3.启动运行服务
//4.在浏览器访问

//创建httpserver服务
http.createServer(function(request,response){
    //设置响应头
    //text/html 以html的方式进行解析。text/plain 以文本方式进行解析
    response.writeHead(200,{'Content-typr':'text/html'});
    //给浏览器输出内容
    response.end("<h1>hello httpServer</h1>");
}).listen(8888); //监听端口8888

console.log('服务器启动成功');
console.log('服务器地址为 http://localhost:8888');

//启动运行服务: node xxx.js (.js可以省略)
```

## Nodejs连接数据库

mysql模块  https://www.npmjs.com/package/mysql

```js
// 1.安装mysql模块  npm install mysql 简写 nmp i mysql
// 2.引入mysql模块
// 3.创建连接 mysql的Connection对象
// 4.配置数据库连接信息
// 5.开启连接
// 6.执行crud
// 7.关闭连接

var mysql = require('mysql');

//创建连接
var connection= mysql.createConnection({
    //配置数据库连接信息
    host: '127.0.0.1',
    user: 'root',
    port: 3306,
    password: 'why0417',
    database: 'whyuser'
})

//开辟连接
connection.connect();

//执行crud
connection.query('select * from tb_user',function(error,results,fields){
    //如果出错就抛出
    if(error) throw error
    //查询成功
    console.log(results)
});

//关闭连接
connection.end();

/*
[
    RowDataPacket { id: 1, username: '张三', address: '浙江省杭州市' },
    RowDataPacket { id: 2, username: '李四', address: '浙江省宁波市' },
    RowDataPacket { id: 3, username: '王五', address: '浙江省温州市' },
    RowDataPacket { id: 4, username: '赵六', address: '浙江省台州市' },
    RowDataPacket { id: 5, username: '田七', address: '浙江省金华市' },
    RowDataPacket { id: 6, username: '赵八', address: '浙江省嘉兴市' }
  ]
*/
```



# WebPack

1.创建文件夹并初始化

~~~shell
npm init -y
~~~

2.安装webpack

~~~shell
npm i -g webpack webpack-cli
~~~

3.查看是否安装成功

~~~shell
webpack -v
~~~

4.创建用于打包的JS文件

5.创建配置文件webpack.config.js

~~~js
// Node.js内置模块
const path = require("path");
module.exports = {
    // 配置入口文件
	entry: './src/main.js', 
	output: {
		// 输出路径，__dirname：当前文件所在路径
		path: path.resolve(__dirname, './dist'), 
		// 输出文件
		filename: 'bundle.js' 
	}
}
~~~

6.命令行执行编译命令

~~~shell
# 有黄色警告
webpack 
# 没有警告
webpack --mode=development 
~~~



方式2

也可以配置项目的npm运行命令修改package.json文件

~~~js
"scripts": {
 	//...,
 	"dev": "webpack --mode=development"
 }
~~~

运行npm命令执行打包

~~~shell
npm run dev
~~~



打包CSS

安装style-loader和 css-loader

~~~shell
npm i --save-dev style-loader css-loader
~~~

修改配置文件webpack.config.js(添加module)

~~~js
const path = require("path"); 
module.exports = {
	//...,
	module: {
		rules: [ 
			{ 
				// 打包规则应用到以css结尾的文件上
				test: /\.css$/, 
				use: ['style-loader', 'css-loader']
 			}
		] 
	}
}
~~~

在main.js中引入

~~~js
require('./style.css');
~~~

打包

~~~shell
webpack --mode=development 
~~~

