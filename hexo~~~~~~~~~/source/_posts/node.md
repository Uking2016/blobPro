---
title: node前后端分离
---

node前后端分离


思考：前后端不分离有哪些缺点？

前后端分离的定义
就是前端和后端的分开嘛

前后端分离
啊，怎么分开，那我直接cx把前端代码弄走吗？

咦，请求不了了，
XMLHttpRequest cannot load https://s103.ggwan.com/mainlinghitv2/css/trends.css. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'null' is therefore not allowed access. The response had HTTP status code 404.
无法加载https://s103.ggwan.com/mainlinghitv2/css/trends.cs XMLHttpRequest。在所请求的资源上没有“访问控制允许源站点”报头。因此，不允许源站点“null”访问。响应具有HTTP状态代码404。

我们看看发送ajax的时候浏览器干了什么



站点null想访问https://s103.ggwan.com站点上的内容，结果报错。啊？为啥不能访问？
因为浏览器的同源策略

浏览器的同源策略

跨域处理的方法
1.CORS

跨域的处理方法
2.JSONP

跨域的处理方法
3.websocket
不受同源策略限制

跨域的处理方法
用一个代理服务器访问后端服务（就是避免让浏览器去请求不同的域，让我们的服务器去请求，就不存在同源策略啦）

node作为中间层实现跨域
接下来，我们一起用node（express）来创建一个服务器吧
var app = express();
App.listen(8080);
这个app就是我们的代理服务器啦，用它可以管理我们的静态资源（就是我们的前端代码啊，html,css,js）
app.use(express.static(path.join(__dirname, '/')));
就是这一句话！！就是表示静态资源就在当前目录，然后express就会自己去找index.html啦

结合webapack
A: Webpack除了用来打包，还能在开发环境下作为一个node的服务器（webpack-dev-server）哦。
B:  哇，那你的意思是，webpack-dev-server就可以跨域访问资源咯？
A: 没错噢，只要简单的配置啦，请看代码
在webpack.config.js里，我们这样配置

    devServer:{
              historyApiFallback:true,
              hot:true,
              inline:true,
              progress:true,//报错无法识别，删除后也能正常刷新
              proxy: {//用devserver作跨域
                '/mainlinghitv2/css/trends.css': {
                  target: 'https://s103.ggwan.com',//真正要请求的域名
                  changeOrigin: true
                }
              }
          }

B: 就是说，我们在开发的时候，可以直接用webpack来调试接口就不用在本地搭建php(/java)的环境咯？
A: 是的哦
B: /哭笑 那为什么还要用node作中间层？
A: 因为webpack-dev-server是只能在开发环境运行的哦，webpack-dev-server生成的包并没有放在你的真实目录中,而是放在了内存中，就是说，webpack-dev-server不会生成html,js这些资源的目录里面，而这些静态文件在生产环境中是必须的，看下图
开发环境下

B: 也就是说，用了webpack-dev-server我们只是在开发环境下实现了前后端分离
A: 是的，生产环境下，我们其实也是同域访问的情况
B：就是说，上线的时候，又出现了前后端在一起的情况

