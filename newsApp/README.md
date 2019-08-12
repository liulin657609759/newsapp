## 项目开发要注意的点
- 减少http请求
> 百度上线的html源码中有许多影响性能的css样式，和js代码
## 使用webpack开发项目
- 本质：帮助开发者实现项目工程自动化
- 问题：为什么要实现项目工程化？
> 将开发版本转化成满足浏览器需求的一个线上版本
- webpack文件目录
![webpack文件目录](https://github.com/liulin657609759/newsapp/blob/master/newsApp/img-folder/%E9%A1%B9%E7%9B%AE%E7%9B%AE%E5%BD%95.png)
- webpack的基本依赖
![webpack依赖](https://github.com/liulin657609759/newsapp/blob/master/newsApp/img-folder/%E9%9C%80%E8%A6%81%E7%9A%84%E4%BE%9D%E8%B5%96.png)
## 工程组件化、模块化
- 组件化
> 将页面的结构或页面的功能控件拆分成多个部分进行开发，组件与组件之间可以相互独立，也可以相互嵌套
- 模块化
> 在模块化的概念中，每个js程序都是一个模块，模块功能可以相互依赖，相互独立
- 使用
1. 以结构或控件抽离组件
2. 以功能抽离模块
3. 以任务抽离程序
4. 以典型功能抽离工具LIULI
## 其他配置
- 字体设置(common.js)
> document.documentElement.style.fontSize=document.documentElement.clientWidth/37.5+"px"
> html{
>    height: 100%;
>    font-size: 1.2rem
>}
- html文件中移动端视口
```
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
```
- 300ms延迟/多点阻止默认事件
```
window.addEventListener('load', function () {
  FastClick.attach(document.body);
}, false);

document.documentElement.addEventListener('touchmove', function (event) {
  if (event.touches.length > 1) {
  	event.preventDefault();
  }
}, false);
```
- css公共样式
```
html {
	height: 100%;
	font-size: 1.2rem;

	body {
		height: 100%;
		margin: 0;

        h1,
        h2,
        h3,
        h4,
        h5,
        h6,
        p {
        	font-weight: normal;
        	margin: 0;
        }

        a {
        	text-decoration: none;
        	color: #000;
        }

        img {
        	width: 100%;
        }

        .app {
            position: relative;
            height: 100%;
            background-color: #f5f7f8;

            .list {
            	overflow-y: auto;
            	background-color: #fff;
            }
        }
	}
}
```