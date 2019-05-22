---
title: web侠客行（十）--使用uni-app开发项目教程
date: 2000-04-13 10:02:12
tags: 
    - vue
    - app
    - uni-app
categories: 
    - 教程
    - web
---

使用uni-app开发app应用。

# 什么是 uni-app

uni-app 是一个使用 Vue.js 开发跨平台应用的前端框架，开发者编写一套代码，可编译到iOS、Android、H5、小程序等多个平台。 

uni-app在跨端数量、扩展能力、性能体验、周边生态、学习成本、开发成本等6大关键指标上拥有极强的竞争优势 。

* 跨端数量更多
* 平台能力不受限
* 性能体验更优秀
* 周边生态丰富
* 学习成本低
* 开发成本低

# quick start
参考： https://uniapp.dcloud.io/quickstart
## 1.创建uni-app
HBuildx，在点击工具栏里的文件 -> 新建 -> 项目，选择uni-app，输入工程名，如：hello-uniapp，点击创建，即可成功创建 uni-app。点击模板里的 Hello uni-app 即可体验官方示例。

## 2.运行uni-app
* 浏览器运行：进入hello-uniapp项目，点击工具栏的运行 -> 运行到浏览器 -> 选择浏览器，即可在浏览器里面体验uni-app 的 H5 版。
* 真机运行：连接手机，开启USB调试，进入hello-uniapp项目，点击工具栏的运行 -> 真机运行 -> 选择运行的设备，即可在该设备里面体验uni-app。
* 在微信开发者工具里运行：进入hello-uniapp项目，点击工具栏的运行 -> 运行到小程序模拟器 -> 微信开发者工具，即可在微信开发者工具里面体验uni-app。 
注意：如果是第一次使用，需要先配置小程序ide的相关路径，才能运行成功。如下图，需在输入框输入微信开发者工具的安装路径。 若HBuilderX不能正常启动微信开发者工具，需要开发者手动启动，然后将uni-app生成小程序工程的路径拷贝到微信开发者工具里面，在HBuilderX里面开发，在微信开发者工具里面就可看到实时的效果。
uni-app默认把项目编译到根目录的unpackage目录。 
* 在支付宝小程序开发者工具里运行：进入hello-uniapp项目，点击工具栏的运行 -> 运行到小程序模拟器 -> 支付宝小程序开发者工具，即可在支付宝小程序开发者工具里面体验uni-app。 
* 在百度开发者工具里运行：进入hello-uniapp项目，点击工具栏的运行 -> 运行到小程序模拟器 -> 百度开发者工具，即可在百度开发者工具里面体验uni-app。 
* 在字节跳动开发者工具里运行：进入hello-uniapp项目，点击工具栏的运行 -> 运行到小程序模拟器 -> 字节跳动开发者工具，即可在字节跳动开发者工具里面体验uni-app。 

如果是第一次使用，需要配置开发工具的相关路径。点击工具栏的运行 -> 运行到小程序模拟器 -> 运行设置，配置相应小程序开发者工具的路径。

支付宝/百度/字节跳动小程序工具，不支持直接指定项目启动并运行。因此开发工具启动后，请将 HBuilderX 控制台中提示的项目路径，在相应小程序开发者工具中打开。

如果自动启动小程序开发工具失败，请手动启动小程序开发工具并将 HBuilderX 控制台提示的项目路径，打开项目。

## 3.发布uni-app
### 打包为原生App（云端）
在HBuilderX工具栏，点击发行，选择原生app-云端打包。

### 打包为原生App（离线）
uni-app 支持离线打包，在 HBuilderX 生成离线打包资源，然后参考 离线打包（或参考其他用户写的 离线打包日记），即可进行离线打包。

在HBuilderX工具栏，点击发行，选择本地打包，点击即可生成离线打包资源。

### 发布为H5
1.在 manifest.json 的可视化界面，进行如下配置（发行在网站根目录可不配置应用基本路径），此时发行网站路径是 www.xxx.com/h5，如：https://uniapp.dcloud.io/h5。

2.在HBuilderX工具栏，点击发行，选择网站-H5手机版，如下图，点击即可生成 H5 的相关资源文件，保存于 unpackage 目录。

### 发布为小程序
#### 发布为微信小程序

申请微信小程序AppID。
在HBuilderX中顶部菜单依次点击 "发行" => "小程序-微信"，输入小程序名称和appid点击发行即可在 unpackage/dist/build/mp-weixin 生成微信小程序项目代码。

在微信开小程序发者工具中，导入生成的微信小程序项目，测试项目代码运行正常后，点击“上传”按钮，之后按照 “提交审核” => “发布” 小程序标准流程，逐步操作即可，详细查看：微信官方教程。
#### 发布为百度小程序

入驻小程序并申请百度小程序AppID，参考：百度小程序教程。
在HBuilderX中顶部菜单依次点击 "发行" => "小程序-百度"，输入小程序名称和appid点击发行即可在 /unpackage/dist/build/mp-baidu 生成百度小程序项目代码。

在百度小程序开发者工具中，导入生成的百度小程序项目，测试项目代码运行正常后，点击“上传”按钮上传代码，之后在百度小程序的 管理中心 选择创建的应用点击前往发布，选择对应的版本然后提交审核。
#### 发布为支付宝小程序

入驻支付宝小程序，参考：支付宝小程序教程。
在HBuilderX中顶部菜单依次点击 "发行" => "小程序-支付宝"，即可在 /unpackage/dist/build/mp-alipay 生成支付宝小程序项目代码。

在支付宝小程序开发者工具中，导入生成的支付宝小程序项目，测试项目代码运行正常后，点击“上传”按钮上传代码，在 支付宝小程序后台，选择刚提交的版本点击提交审核，详见：支付宝小程序文档。
#### 发布为头条小程序

入驻头条小程序，参考：头条小程序教程。
在HBuilderX中顶部菜单依次点击 "发行" => "小程序-字节跳动"，即可在 /unpackage/dist/build/mp-alipay 生成头条小程序项目代码。

在字节跳动小程序开发者工具中，导入生成的头条小程序项目，测试项目代码运行正常后，点击“上传”按钮上传代码，在 头条小程序后台，选择刚提交的版本点击提交审核，详见：头条小程序文档。

# vue-cli创建uni-app
## 环境安装
全局安装vue-cli
```
npm install -g @vue/cli
```
## 创建uni-app
```
vue create -p dcloudio/uni-preset-vue my-project
```
## 运行并发布uni-app
```
npm run dev:%PLATFORM%
npm run build:%PLATFORM%
```
%PLATFORM% 可取值如下：

值	平台
h5	H5
mp-alipay	支付宝小程序
mp-baidu	百度小程序
mp-weixin	微信小程序
mp-toutiao	头条小程序

# 项目开发
## 项目基础工作
* common目录拷贝到项目目录
* App.vue需要引入 @import "./common/uni.css"

如果按照helloworld模板建的项目则不需要。
## pages/index/index.vue

1.删除template,script,style多余的部分

2.template用**uli**生成uListMedia代码块，url改为https://unidemo.dcloud.net.cn/api/news

3.success中返回res，需求接口如下：
```
访问地址：https://unidemo.dcloud.net.cn/api/news
- 返回数据格式
- post_id 新闻id 如 ： 72980
- title 标题
- created_at 创建时间
- author_avatar 图标

地址：https://unidemo.dcloud.net.cn/api/news/36kr/ + id（id为新闻id，上个页面传过来的）

使用 rich-text 【富文本组件】来展示新闻内容
<rich-text class="richText" :nodes="strings"></rich-text>
```
4.定义data数组news，赋值this.news=res.data，template循环改为news

5.修改item对象的属性，author_avatar为图片，created_at为时间

6.修改样式uni-media-list-body每个新闻块高度，uni-media-list-text-top新闻标题行高

7.用navigator标签包裹，或者js实现（推荐），绑定单击事件@click="openinfo"。

8.methods增加openinfo方法，**unav**激活代码块，url写../info/info，.vue可以省略

9.pages.json增加
```
{
    "path" : "pages/info/info",
    "style" : {}
}
```
最终pages.json为
```
{
	"pages": [ //pages数组中第一项表示应用启动页，参考：https://uniapp.dcloud.io/collocation/pages
		{
			"path": "pages/index/index",
			"style": {
				"navigationBarTitleText": "uni-app"
			}
		},
		{
            "path" : "pages/info/info",
            "style" : {}
        }
	],
	"globalStyle": {
		"navigationBarTextStyle": "black",
		"navigationBarTitleText": "uni-app",
		"navigationBarBackgroundColor": "#F8F8F8",
		"backgroundColor": "#F8F8F8"
	}
}
```
10.绑定newsid  :data-newsid="item.post_id"

11.修改openinfo，改为带参数openinfo(e)，e通过打印日志查看获取。
```
var newsid = e.currentTarget.dataset.newsid;
uni.navigateTo({
    url: '../info/info?newsid=' + newsid
});
```
最终，index.vue如下，主要处理，页面载入访问远程服务器，点击访问本地info.vue组件，组件中再去访问远程服务器。
```
<template>
	<view class="content">
		<view class="uni-list">
			<view class="uni-list-cell" hover-class="uni-list-cell-hover" v-for="(item,index) in news" :key="index" @tap="openinfo" :data-newsid="item.post_id">
				<view class="uni-media-list">
					<image class="uni-media-list-logo" :src="item.author_avatar"></image>
					<view class="uni-media-list-body">
						<view class="uni-media-list-text-top">{{item.title}}</view>
						<view class="uni-media-list-text-bottom uni-ellipsis">{{item.created_at}}</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				news : []
			};
		},
		onLoad:function(){
			uni.showLoading({
				title:"加载中...."
			})
			uni.request({
				url: 'https://unidemo.dcloud.net.cn/api/news',
				method: 'GET',
				data: {},
				success: res => {
					this.news = res.data;
					uni.hideLoading();
				},
				fail: () => {},
				complete: () => {}
			});
		},
		methods: {
			openinfo(e) {
				// console.log(e);
				var newsid = e.currentTarget.dataset.newsid;
				uni.navigateTo({
					url: '../info/info?newsid=' + newsid
				});
			}
		},
	}
</script>

<style>
.uni-media-list-body{height: auto;}
.uni-media-list-text-top{line-height:1.6em;}
</style>

```
## pages/info/info.vue

12.onload方法，onload:function(e)，e传输了newsid参数。

13.**ure**快捷输入代码块，uni.request，url: 'https://unidemo.dcloud.net.cn/api/news/36kr/' + e.newsid,

14.success，测试res，console.log(res)

15.处理title
* 模板增加{{title}}
* data增加title
* title变量赋值
* 样式改写
```
// 模板增加title，值为{{title}}
<view class="title">
    {{title}}
</view>

// data中增加title变量
data() {
    return {
        title:''
    }
},

// title变量赋值
success: res => {
    console.log(res);
    this.title = res.data.title
},

// content与title样式
.content{
	padding: 10upx 2%;
	width: 96%;
	flex-wrap: wrap; /* 允许元素换行 */
}
.title{
	line-height: 2em;
	font-weight: 700;
	font-size: 38upx;
}
```

16.处理内容，rich-text展示html内容
```
<view class="art-content">
    <rich-text class="richText" :nodes="content"></rich-text>
</view>

data() {
    return {
        title:'',
        content:''
    }
},

success: res => {
    console.log(res);
    this.title = res.data.title
    this.content = res.data.content
},
```

17.最终info.vue内容
```
<template>
	<view class="content">
		<view class="title">
			{{title}}
		</view>
		<view class="art-content">
			<rich-text class="richText" :nodes="content"></rich-text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title:'',
				content:''
			}
		},
		onLoad:function(e){
			console.log(e);
			uni.request({
				url: 'https://unidemo.dcloud.net.cn/api/news/36kr/' + e.newsid,
				method: 'GET',
				data: {},
				success: res => {
					console.log(res);
					this.title = res.data.title
					this.content = res.data.content
				},
				fail: () => {},
				complete: () => {}
			});

		}
	}
</script>

<style>
.content{
	padding: 10upx 2%;
	width: 96%;
	flex-wrap: wrap; /* 允许元素换行 */
}
.title{
	line-height: 2em;
	font-weight: 700;
	font-size: 38upx;
}
.art-content{
	line-height: 2em;
}
</style>
```

## 开发模式的配置condition
```
"condition": { //模式配置，仅开发期间生效
    "current": 0, //当前激活的模式（list 的索引项）
    "list": [{
            "name": "test", //模式名称
            "path": "pages/info/info", //启动页面，必选
            "query": "newsid=5158607" //启动参数，在页面的onLoad函数里面得到。
        }
    ]
}
```
然后ctrl+r，运行test即可。