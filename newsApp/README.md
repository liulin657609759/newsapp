# 一个使用原生js模块化项目(头条新闻)
## 本项目的所有数据来自“聚合”
[api地址](https://www.juhe.cn/docs/api/id/235)
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
## 组件创建
- ES6引入图片(相对路径)
```
<img src="${require('../../images/backward.png')}" class="img-icon" style="display: {{showLeftIcon}}" />
```
- 点击后退
```
<a href="javascript:history.back(-1)"></a>
```
- 点击到其他页面
```
<a href="collections.html"></a>
```
*** 
- 导出视图
1. 将tpl模板模块,scss样式模块导入到js文件
> import tpl from './index.tpl';//tpl是一个函数
> import './index.scss';
2. 再由index.js文件导出
```
export default () => {
	return {
		name: 'header',
		tpl (options) {//options为要替换的内容
      //模板替换
      return tpl().replace(tools.tplReplace(), (node, key) => {
      	return {
          title: options.title,
          showLeftIcon: !options.showLeftIcon && 'none',
          showRightIcon: !options.showRightIcon && 'none'
      	}[key];
      })
		}
	}
}
```
3. 在入口文件中引入
```
import Header from '../components/header/index';
const header = new Header(),
const App = ($, win) => {
  
  const $app = $('#app');//拿到#app元素

  //入口文件必须要有的方法
  const init=()=>{
    render();
  }
  const render=()=>{
    _renderHeader();
  }
  const _renderHeader = () => {
    //调用组件的模板替换方法并将组件模板append到页面
    //header.tpl()方法的返回值就是替换完成的组件模板字符串
    $app.append(header.tpl({
  		title: 'JS++新闻头条',
  		showLeftIcon: false,
  		showRightIcon: true
  	}));
  }
}
```
> 这样组件就可以在页面中渲染出来了
- 导航条组件
1. 导航条的滚动
> HTML
```
<nav class="nav">
	<div class="scroll">
		<div class="nav-wrapper" style="width: {{wrapperW}}">
			
		</div>
	</div>
</nav>

<div class="item {{isCurrent}}" data-type="{{type}}">{{typeName}}</div>
```
> sass
```
.nav {
	position: fixed;
	top: 4.4rem;
	left: 0;
	z-index: 2;
	width: 100%;
	height: 3.5rem;
	border-bottom: 1px solid #ddd;
	background-color: #fff;
	overflow: hidden;
//导航条下面有滚动条的问题！
//1. 将.scroll的height 变大（本来是3.5，现在4.2）
//2. 然后给父元素.nav设置over-flow：hidden；
	.scroll {
		height: 4.2rem;
		-webkit-overflow-scrolling: touch;
		overflow-x: auto;

		.nav-wrapper {
			display: flex;
			flex-direction: row;
			height: 3.5rem;

			.item {
				display: flex;
				justify-content: center;
				align-items: center;
				width: 6rem;
				font-size: 1.6rem;
        <!-- 如果有.current类，就添加样式 -->
				&.current {
					color: #ec7a70;
				}
			}
		}
	}
}
```
2. 导航条点击字体颜色改变
```
const bindEvent = () => {
    $('.nav .nav-wrapper').on('click', '.item', navSelect);
    $list.on('click', '.news-item', toDetailPage);
  }
```
```
// 点击哪个就给哪个添加类名current，然后把兄弟元素的current类名全都移除
    $this.addClass('current').siblings('.item').removeClass('current');
    _renderList(field, pageNum, showCount);
  }
```
- 前端获取数据并进行分页
1.  后台返回的数据本来是三十条，如今要分成三页，每页十条
2. 直接使用Ajax 获取数据的话，因为Ajax是异步的，如果有多个异步请求的话
> 前端向后端发送请求request1，需要获得数据data1，发起请求request2同时需要请求参数data1，需要获得数据data2，然后紧接着需要利用data2进行某项运算。。。。理想状态下，按思路代码一路执行，是没有问题的
> 但是！ajax是一个异步操作，由于网路延迟request2执行的时候，很可能request1还没有返回数据data1，于是获取不到data1，request2失败，SO，data2页获取不到了。。。 
```
class IndexModel extends HTTP {
  getNewsList (field, showCount) {
  	return new Promise((resolve, reject) => {
  		this.ajax({
	      url: 'Juhe/getNewsList',
	      type: 'POST',
	      dataType: 'JSON',
	      data: {
	      	field
	      },
        <!-- 得到数据后，进行数据分页 -->
	      success (data) {
	      	const listDatas = data.result.data,
	      	      len = listDatas.length;

	      	let pageData = [],
	      	    index = 0;

	      	while (index < len) {
	      		pageData.push(listDatas.slice(index, index += showCount));
	      	}

	      	resolve(pageData);
	      },

	      error () {
          resolve(404);
	      }
	  	});
  	});
  }
}
```
3. 然后
```
indexModel.getNewsList(field,10).then((res)=>{
  <!-- 对数据进行处理，参数res就是返回的数据 -->
})
```
- 新闻列表(new_item)
1. 每条新闻中图片不一(多模板渲染)
> 1. 针对不同个图片设计多个模板
> 2. 在sass文件中使用“&”(连体符)设置不同的样式
> 3. 在js中根据数据内容判断这条新闻数据有几张图片
```
data.forEach((item, index) => {
        if (!item.thumbnail_pic_s) {
        	template = tpl_0();
        } else if (item.thumbnail_pic_s && !item.thumbnail_pic_s02) {
        	template = tpl_1();
        } else if (item.thumbnail_pic_s02 && !item.thumbnail_pic_s03) {
        	template = tpl_2();
        } else if (item.thumbnail_pic_s03) {
        	template = tpl_3();
        }
```
2. 图片预加载
> 1. 设置css
```
img {
		height: 100%;
		opacity: 0;
		transition: opacity .5s;
	}
```
> 2. 在utils/tools.js中定义加载图片函数
```
function thumbShow (dom) {
	dom.on('load', function () {
      $(this).css('opacity', 1);
	});
}
```
> 3. 在入口文件中调用(参数根据类名找到的所有图片)
```
tools.thumbShow($('.news-thumb'));
```
- 缓存池
1. 声明一个对象
```
let field = 'top',
      pageNum = 0,
      pageCount = 0,
      showCount = 10,
      dataCache = {},
```
2. 在数据渲染到页面之前进行判断，若果缓存池里有当前点击的项目的数据，就从缓存池里拿，如果没有就进行Ajax请求数据，
3. 在请求数据之前要先渲染pageLoading组件
```
  const _handlePageLoading = (how) => {
    switch(how) {
      case 'append':
        $list.html('');
        $app.append(pageLoading.tpl());
        break;
      case 'remove':
        $('.loading-icon').remove();
        break;
      default:
        break;
    }
  }
```
4. 然后
```
const _renderList = (field, pageNum, showCount) => {
    if (dataCache[field]) {
      pageCount = dataCache[field].length;
      _insertList('cover');
    } else {
      _handlePageLoading();
      indexModel.getNewsList(field, showCount).then((res) => {
        dataCache[field] = res;
        pageCount = dataCache[field].length;
        
        setTimeout(() => {
          _insertList('cover');
        }, 1000);
      });
    }
  }
```
5. 其中_insertList('cover');函数是渲染新闻列表
```
  const _insertList = (method) => {
    switch (method) {
      case 'cover':
        $list.html(newsItem.tpl(dataCache[field][pageNum], pageNum));
        _handlePageLoading('remove');
        break;

      case 'append':
        $list.append(newsItem.tpl(dataCache[field][pageNum], pageNum));
		<!-- _afterRender是加载图片的函数 -->
        _afterRender(false);
    }
  }
```
6. 再点击导航条的时候调用_renderList(field, pageNum, showCount);

- 上拉加载更多
1. 创建正在加载的组件(bottom_tip)
2. 创建让其在页面渲染的函数
```
  const _handleBottomTip = (how, isLoading, text) => {
    switch (how) {
      case 'append':
        $app.append(bottomTip.tpl(isLoading, text));
        break;
      case 'remove': 
        $('.bottom-tip').remove();
        break;
      case 'removeAndAppend':
        $('.bottom-tip').remove();
        $app.append(bottomTip.tpl(isLoading, text));
        break;
      default:
        break;
    }
  }
```
3. 当导航条到底部时加载数据(utils/tools下)
```
function scrollToBottom (callback) {
  if (_getScrollTop() + _getWindowHeight() == _getScrollHeight()) {
    callback();
  }
}
```
4. 在入口函数引用3.中的函数，并传入参数
```
newScrollToBottom = tools.scrollToBottom.bind(null, scrollToBottom);
```
5. 创建scrollToBottom函数
```
function scrollToBottom () {
    if (pageNum < pageCount - 1) {
      if (!bottomLock) {
        bottomLock = true;
        _handleBottomTip('append', 'loading', '正在努力加载中');
        setTimeout(() => {
          pageNum ++;
          _insertList('append');
        }, 1000);
      }
    } else {
      _handleBottomTip('removeAndAppend', 'final', '已加载完所有内容');
    }
  }
```
6. 在渲染新闻列表的时候，绑定滚动条事件
> 注意：当点击其他导航条列表的时候，要让滚动条回到顶部；所以在每次
```
  const _insertList = (method) => {
    switch (method) {
      case 'cover':
        $list.html(newsItem.tpl(dataCache[field][pageNum], pageNum));
        <!-- 滚动条回到顶端 -->
        _scrollToTop(150);
        _handlePageLoading('remove');
        _afterRender(true);
        break;

      case 'append':
        $list.append(newsItem.tpl(dataCache[field][pageNum], pageNum));
        _afterRender(false);
    }
    
    bottomLock = false;
    _handleBottomTip('remove');
  }
  <!-- 在渲染图片的时候绑定事件 -->
  const _afterRender = (bindScroll) => {
    bindScroll && $window.on('scroll', newScrollToBottom);
    tools.thumbShow($('.news-thumb'));
  }
```
- 跳转页面与数据传递
1. 首先，我们要知道当前点击的是哪个(使用事件绑定机制)
> newScrollToBottom = tools.scrollToBottom.bind(null, scrollToBottom);
2. 如何将被点击的元素的内容及属性传递给新的页面(将当前元素的数据当做属性写在最外层标签里)
```
<div class="news-item type-0" data-url="{{url}}" data-page="{{pageNum}}" data-index="{{index}}" data-uniquekey="{{uniquekey}}">
	<h1>{{title}}</h1>
	<div class="info">
		<span class="author">{{author}}</span>
		<span class="date">{{date}}</span>
	</div>
</div>
```
3. 获取并存储当前元素通过标签属性传递过来的数据(通过localstorage存储，并通过bom跳转到相应的页面，并且将参数写在url地址里)
```
function toDetailPage () {
    const $this = $(this),
          url = $this.attr('data-url'),
          idx = $this.attr('data-index'),
          pageNum = $this.attr('data-page');

    localStorage.setItem('target', JSON.stringify(dataCache[field][pageNum][idx]));
    win.location.href = `detail.html?news_url=${url}&uniquekey=${dataCache[field][pageNum][idx].uniquekey}`;
  }
```
- 新闻详情页的渲染
1. 创建一个iframe组件，使用iframe标签包裹要传过来的页面
```
<iframe src="{{news_url}}" frameborder="0" width="100%" height="100%" id="js-news-frame"></iframe>
```
2. 在js文件中进行字符串替换
```
export default () => {
	return {
		name: 'newsFrame',
		tpl (url) {
      return tpl().replace(tools.tplReplace(), url);     
		}
	}
}
```
3. 在utils文件下的tools文件中添加截取url字段的方法(该函数返回的是新闻详情页的地址)
> 事实上，也可以通过localstorage中的target取得数据
```
function getUrlQueryValue (key) {
  const reg = new RegExp('(^|&)' + key + '=([^&]*)(&|$)', 'i'),
        res = window.location.search.substr(1).match(reg);

  return res != null ? decodeURIComponent(res[2]) : null;
}
```
3. 在detail.js文件中保存newUrl并将其传给组件函数newsFrame.tpl(newUrl),然后append到detail.html页面中
```
const _renderFrame = (newsUrl) => {
    $frameWrapper.append(newsFrame.tpl(newsUrl));
  }
```
- 点击收藏按钮进行收藏
1. 新闻详情页右上角的五角星收藏按钮为一个组件(collector)
```
<div class="collector {{type_class}}"></div>
```
```
export default () => {
	return {
		name: 'collector',
		tpl (collected) {
			return tpl().replace(tools.tplReplace(), collected ? 'full' : 'o');
		},

		changeCollector (collected) {
			$('.collector').addClass(collected ? 'full' : 'o')
                           .removeClass(collected ? 'o' : 'full');
		}
	}
}
```
2. 判断localStorage中有没有收藏过的新闻（collections）没有的话就创建一个collections,并且判断这个collections中有没有当前新闻的uniquekey，如果有的话，就代表这个新闻被收藏过，返回boolean值
```
let collections = JSON.parse(localStorage.getItem('collections')) || {},
      collected = Boolean(collections[uniquekey]);
```
3. 将collector组件append到detail页面中
```
const _renderCollector = (collected) => {
    $app.append(collector.tpl(collected));
  }
```
4. 设置绑定事件
```
const bindEvent = () => {
    $('.collector').on('click', newsCollect);
  }
```
> 点击事件的回调函数(如果collections中有本条新闻，即已经收藏过，那么点击收藏就删除这条数据，否则就添加这条数据，并且将collected的值进行反转，然后将collections存储到localStorage中)
```
  function newsCollect () {
    console.log(1);
    if (collections[uniquekey]) {
      delete collections[uniquekey];
      collected = false;
    } else {
      collections[uniquekey] = JSON.parse(localStorage.getItem('target'));
      collected = true;
    }

    localStorage.setItem('collections', JSON.stringify(collections));
    collector.changeCollector(collected);
  }
```
- 收藏页面
1. 若果没有新闻收藏，创建没有收藏新闻的组件(no_content_tip)
2. 如果有有收藏，即collections不为空或者Object.keys(collections).length 不等于0；那么就调用新闻列表newsItem.tpl()添加到收藏页面内；并且调用tools.thumbShow()方法对图片进行加载
```
const render = () => {
    return new Promise((resolve, reject) => {
      _renderHeader();

      if (!collections || Object.keys(collections).length === 0) {
        _renderNoContentTip('没有收藏新闻');
      } else {
        _renderList(collections);
      }

      resolve();
    });
  }
```
> 调用newItem组件进行收藏页面渲染，并随图片进行加载
```
  const _renderList = (data) => {
    $list.append(newsItem.tpl(_arrangeDatas(data)));
    tools.thumbShow($('.news-thumb'));
  }
```
> 其中，_arrangeDatas(data)函数是对数据进行数组化
```
function _arrangeDatas (data) {
    let _arr = [];

    for (let key in data) {
      _arr.push(data[key]);
    }

    return _arr;
  }
```
3. 添加点击事件（收藏页面的新闻也要像新闻列表一样可以点击到详情页,在点击时，也要像在新闻列表一样，在localStorage中添加一个target，具体做法是：选中当前元素的'data-uniquekey'属性，从collections中获取到，添加到target中）
```
const bindEvent = () => {
    $list.on('click', '.news-item', toDetailPage);
  }
```
```
  function toDetailPage () {
    const $this = $(this),
          url = $this.attr('data-url'),
          uniquekey = $this.attr('data-uniquekey');

    localStorage.setItem('target', JSON.stringify(collections[uniquekey]));
    window.location.href = `detail.html?news_url=${url}&uniquekey=${uniquekey}`;
  }
```
- 最后对项目进行异常处理
1. 如果我们没有收藏东西即没有localStorage.getItem('target'),那么无论url无论定位到哪个页面都要回到主页index.html
> 最主要的是将这个方法写到script标签中，并且放到html代码里，这样就减少了http请求，有助于性能
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
	<title><%= htmlWebpackPlugin.options.title %></title>
	<script type="text/javascript">
		if (!localStorage.getItem('target')) {
			window.location.href = 'index.html';
		}
	</script>
</head>
<body>
	<div id="app" class="app">
		<div class="frame-wrapper"></div>
	</div>

	<script src="scripts/zepto.min.js"></script>
	<script src="scripts/fastclick.js"></script>
	<script src="scripts/common.js"></script>
</body>
</html>
```
2. 当出现网路错误，404等，需要创建错误组件(error_tip)
> 引用
```
const _renderList = (field, pageNum, showCount) => {
    if (dataCache[field]) {
      pageCount = dataCache[field].length;
      _insertList('cover');
    } else {
      _handlePageLoading('append');
      indexModel.getNewsList(field, showCount).then((res) => {
        if (res == 404) {
          _handleErrorTip('append', '没有找到网络');
          _handlePageLoading('remove');
          return;
        }
```
```
const _handleErrorTip = (how, text) => {
    switch (how) {
      case 'append': 
        $app.append(errorTip.tpl(text));
        break;
      case 'remove':
        $('.error-tip').remove();
        break;
      default:
        break;
    }
  }
```
## 至此，本项目结束。。。




