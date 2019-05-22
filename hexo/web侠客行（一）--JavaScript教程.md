---
title: web侠客行（一）--JavaScript教程
date: 2000-04-13 01:02:12
tags: 
    - spring
    - javascript
    - JQuery
categories: 
    - 教程
    - web
---

# 开始第一个js

innerHTML，当前页面交互并更新页面，下面是示例代码：
```
<script>
function displayDate(){
	document.getElementById("demo").innerHTML=Date();
}
</script>
```

对象

var person = {
    firstName:"John",
    lastName:"Doe",
    age:50,
    eyeColor:"blue"
};

对象的使用
person.lastName;  
或者 person["lastName"];

常见的HTML事件的列表：
* onchange	HTML 元素改变
* onclick	用户点击 HTML 元素
* onmouseover	用户在一个HTML元素上移动鼠标
* onmouseout	用户从一个HTML元素上移开鼠标
* onkeydown	用户按下键盘按键
* onload	浏览器已完成页面的加载

示例：
```
<button onclick="displayDate()">现在的时间是?</button>
```

# html表单验证

onsubmit 事件
```
<form name="myForm" action="demo_form.php" onsubmit="return validateForm()" method="post">
名字: <input type="text" name="fname">
<input type="submit" value="提交">
</form>
```

checkValidity()方法，如果 input 元素中的数据是合法的返回 true，否则返回 false。

这个例子限制了 min 和 max：
```
<input id="id1" type="number" min="100" max="300" required>
<button onclick="myFunction()">验证</button>
 
<p id="demo"></p>
 
<script>
function myFunction() {
    var inpObj = document.getElementById("id1");
    if (inpObj.checkValidity() == false) {
        document.getElementById("demo").innerHTML = inpObj.validationMessage;
    }
}
</script>
```
# DOM
约束属性
* validity	布尔属性值，返回 input 输入值是否合法
* validationMessage	浏览器错误提示信息
* willValidate	指定 input 是否需要验证

validity属性
* customError	设置为 true, 如果设置了自定义的 validity 信息。
* patternMismatch	设置为 true, 如果元素的值不匹配它的模式属性。
* rangeOverflow	设置为 true, 如果元素的值大于设置的最大值。
* rangeUnderflow	设置为 true, 如果元素的值小于它的最小值。
* stepMismatch	设置为 true, 如果元素的值不是按照规定的 step 属性设置。
* tooLong	设置为 true, 如果元素的值超过了 maxLength 属性设置的长度。
* typeMismatch	设置为 true, 如果元素的值不是预期相匹配的类型。
* valueMissing	设置为 true，如果元素 (required 属性) 没有值。
* valid	设置为 true，如果元素的值是合法的。

# this关键字

在 JavaScript 中 this 不是固定不变的，它会随着执行环境的改变而改变。

* 在方法中，this 表示该方法所属的对象。
* 如果单独使用，this 表示全局对象。
* 在函数中，this 表示全局对象。
* 在函数中，在严格模式下，this 是未定义的(undefined)。
* 在事件中，this 表示接收事件的元素。
* 类似 call() 和 apply() 方法可以将 this 引用到任何对象。

# json
JSON 字符串转换为 JavaScript 对象：
```
var text = '{ "sites" : [' +
'{ "name":"Runoob" , "url":"www.runoob.com" },' +
'{ "name":"Google" , "url":"www.google.com" },' +
'{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
```

* JSON.parse()	用于将一个 JSON 字符串转换为 JavaScript 对象。
* JSON.stringify()	用于将 JavaScript 值转换为 JSON 字符串。

javascript:void(0)代表不返回值
```
<a href="javascript:void(0);">点我没有反应的!</a>
```

函数实例：
```
const x = (x, y) => { return x * y };
```

# js函数
Arguments 对象，函数的参数列表
```
x = findMax(1, 123, 500, 115, 44, 88);
 
function findMax() {
    var i, max = arguments[0];
    
    if(arguments.length < 2) return max;
 
    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
```
# JavaScript HTML DOM 事件
HTML 事件的例子：

* 当用户点击鼠标时
* 当网页已加载时
* 当图像已加载时
* 当鼠标移动到元素上时
* 当输入字段被改变时
*  当提交 HTML 表单时
* 当用户触发按键时

点击事件示例：
```
<!DOCTYPE html>
<html>
<body>
<h1 onclick="this.innerHTML='Ooops!'">点击文本!</h1>
</body>
</html>
```
向 button 元素分配 onclick 事件：
```
<script>
document.getElementById("myBtn").onclick=function(){displayDate()};
</script>
```
onload事件：
```
<body onload="checkCookies()">
```
onchange 事件:onchange 事件常结合对输入字段的验证来使用。
```
<input type="text" id="fname" onchange="upperCase()">
```
onmouseover 和 onmouseout 事件：
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>

<div onmouseover="mOver(this)" onmouseout="mOut(this)" style="background-color:#D94A38;width:120px;height:20px;padding:40px;">Mouse Over Me</div>
<script>
function mOver(obj){
	obj.innerHTML="Thank You"
}
function mOut(obj){
	obj.innerHTML="Mouse Over Me"
}
</script>

</body>
</html>
```

# 事件监听
事件监听列表：
http://www.runoob.com/jsref/dom-obj-event.html
```

<script>
document.getElementById("myBtn").addEventListener("click", displayDate);
function displayDate() {
    document.getElementById("demo").innerHTML = Date();
}
</script>

```
removeEventListener() 方法
```
<script>
document.getElementById("myDIV").addEventListener("mousemove", myFunction);
function myFunction() {
    document.getElementById("demo").innerHTML = Math.random();
}
function removeHandler() {
    document.getElementById("myDIV").removeEventListener("mousemove", myFunction);
}
</script>
```

createElement与createTextNode
```
<script>
var para = document.createElement("p");
var node = document.createTextNode("这是一个新的段落。");
para.appendChild(node);
 
var element = document.getElementById("div1");
element.appendChild(para);
</script>
```
appendChild 添加新节点、insertBefore 插入到开始位置、removeChild 删除子节点、

# BOM
所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。
```
window.document.getElementById("header");
```

其他 Window 方法
一些其他方法：

* window.open() - 打开新窗口
* window.close() - 关闭当前窗口
* window.moveTo() - 移动当前窗口
* window.resizeTo() - 调整当前窗口的尺寸

Window Location Assign：
```
<script>
function newDoc(){
	window.location.assign("http://www.w3cschool.cc")
}
</script>
```
Window History Back：
```
<script>
    function goBack()
    {
        window.history.back()
    }
</script>
```

Window History Forward：
```
<script>
function goForward()
{
    window.history.forward()
}
</script>
```
Window Navigator：
```
<script>
txt = "<p>浏览器代号: " + navigator.appCodeName + "</p>";
txt+= "<p>浏览器名称: " + navigator.appName + "</p>";
txt+= "<p>浏览器版本: " + navigator.appVersion + "</p>";
txt+= "<p>启用Cookies: " + navigator.cookieEnabled + "</p>";
txt+= "<p>硬件平台: " + navigator.platform + "</p>";
txt+= "<p>用户代理: " + navigator.userAgent + "</p>";
txt+= "<p>用户代理语言: " + navigator.systemLanguage + "</p>";
document.getElementById("example").innerHTML=txt;
</script>
```

# 弹窗
常用三种弹窗：

警告框
window.alert("sometext");

确认框
window.confirm("sometext");

提示框
window.prompt("sometext","defaultvalue");

# JavaScript 计时事件

setInterval() 方法
setInterval() 间隔指定的毫秒数不停地执行指定的代码

setInterval(function(){alert("Hello")},3000);

clearInterval() 方法 停止执行
window.clearInterval(intervalVariable)

setTimeout() 方法
setTimeout(function(){alert("Hello")},3000);

# JavaScript Cookie
Cookie 用于存储 web 页面的用户信息。

Cookie 是一些数据, 存储于你电脑上的文本文件中。

当 web 服务器向浏览器发送 web 页面时，在连接关闭后，服务端不会记录用户的信息。

Cookie 的作用就是用于解决 "如何记录客户端的用户信息":

当用户访问 web 页面时，他的名字可以记录在 cookie 中。
在用户下一次访问该页面时，可以在 cookie 中读取用户访问记录。

读取cookie
```
var x = document.cookie;
```
修改cookie
```
document.cookie="username=John Smith; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/";
```
删除cookie
```
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT";
```
综合案例
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<head>
<script>
function setCookie(cname,cvalue,exdays){
	var d = new Date();
	d.setTime(d.getTime()+(exdays*24*60*60*1000));
	var expires = "expires="+d.toGMTString();
	document.cookie = cname+"="+cvalue+"; "+expires;
}
function getCookie(cname){
	var name = cname + "=";
	var ca = document.cookie.split(';');
	for(var i=0; i<ca.length; i++) {
		var c = ca[i].trim();
		if (c.indexOf(name)==0) { return c.substring(name.length,c.length); }
	}
	return "";
}
function checkCookie(){
	var user=getCookie("username");
	if (user!=""){
		alert("欢迎 " + user + " 再次访问");
	}
	else {
		user = prompt("请输入你的名字:","");
  		if (user!="" && user!=null){
    		setCookie("username",user,30);
    	}
	}
}
</script>
</head>
	
<body onload="checkCookie()"></body>
	
</html>
```

# JQUERY、Prototype、MooTools
jQuery 是目前最受欢迎的 JavaScript 框架。它使用 CSS 选择器来访问和操作网页上的 HTML 元素（DOM 对象）。jQuery 同时提供 companion UI（用户界面）和插件。

Prototype 是一种库，提供用于执行常见 web 任务的简单 API。API 是应用程序编程接口（Application Programming Interface）的缩写。它是包含属性和方法的库，用于操作 HTML DOM。Prototype 通过提供类和继承，实现了对 JavaScript 的增强。

MooTools 也是一个框架，提供了可使常见的 JavaScript 编程更为简单的 API。MooTools 也含有一些轻量级的效果和动画函数。

其他框架
下面是其他一些在上面未涉及的框架：

YUI - Yahoo! User Interface Framework，涵盖大量函数的大型库，从简单的 JavaScript 功能到完整的 internet widget。

Ext JS - 可定制的 widget，用于构建富因特网应用程序（rich Internet applications）。

Dojo - 用于 DOM 操作、事件、widget 等的工具包。

script.aculo.us - 开源的 JavaScript 框架，针对可视效果和界面行为。

UIZE - Widget、AJAX、DOM、模板等等。

# CDN -内容分发网络
您总是希望网页可以尽可能地快。您希望页面的容量尽可能地小，同时您希望浏览器尽可能多地进行缓存。

如果许多不同的网站使用相同的 JavaScript 框架，那么把框架库存放在一个通用的位置供每个网页分享就变得很有意义了。

CDN (Content Delivery Network) 解决了这个问题。CDN 是包含可分享代码库的服务器网络。

Google 为一系列 JavaScript 库提供了免费的 CDN，包括：

* jQuery
* Prototype
* MooTools
* Dojo
* Yahoo! YUI


# JQ study
## 文档加载完成的事件

javascript:
```
<script type="text/javascript">
    window.onload = function(){
        alert("window onload success")
    }
</script>
```

JQ:
```
<script type="text/javascript">
    jQuery(document).ready(function(){
        alert("jQuery load success")
    })
</script>
```

JQ简写:
```
<script type="text/javascript">
    $(document).ready(function(){
        alert("JQ load success 2")
    })
    
    $(function(){
        alert("JQ load success 3")
    })
</script>
```

## JS与JQ取值，对象的转换
```
var var1 = $('#h1').html();
var var2 = document.getElementById("h1").innerHTML;

# JS对象转换JQ对象
var div = document.getElementById("h1")
$(div).html("hello div")

# JQ对象转换JS对象
var div1 = $('#h1').get(0)
var div2 = $('#h1')[0]
```

## JQ的开发步骤
(将我们页面的JS代码和HTML页面代码进行分离)

	1. 导入JQ相关的文件
	2.  文档加载完成事件: $(function)  : 页面初始化的操作: 绑定事件, 启动页面定时器
	3. 确定相关操作的事件
	4. 事件触发函数
	5. 函数里面再去操作相关的元素

```
# 需求
1. 导入JQ的文件
2. 编写JQ的文档加载事件
3. 启动定时器 setTimeout("",3000);
4. 编写显示广告的函数
5. 在显示广告里面再启动一个定时器
6. 编写隐藏广告的函数

<div id="">
    <img src="images/qihoo.png" id="img1" hidden/>			
</div>
<script type="text/javascript">
    //显示广告
    function showAd() {
        $("#img1").slideDown(2000);
        setTimeout("hideAd()", 3000);
    }
    //隐藏广告
    function hideAd() {
        $("#img1").slideUp(2000);
    }
    $(function() {
        setTimeout("showAd()", 3000);
    });
</script>
```

## JQ选择器
- ID选择器 :     #ID的名称
- 类选择器:     以 . 开头  .类名
- 元素选择器:    标签的名称
- 通配符选择器:   * 
- 选择器,选择器:  选择器1,选择器2
```
<h1 id="h1">index4.html</h1>
<p >111111111</p>
<p >222222222</p>
<span id="" class="p_font">333333333333</span><br />
<span id="" class="p_font">444444444444</span><br />
<input type="button" id="btn1" value="button1" />
<input type="button" id="btn2" value="button2" />
<input type="button" id="btn3" value="button3" />
<input type="button" id="btn4" value="button4" />

<script type="text/javascript">
    //文档加载完成事件
    $(function(){
        //改变h1的css
        $('#btn1').click(function(){
            $('#h1').css("background-color","palegreen")
        })
        
        //改变所有p的样式
        $('#btn2').click(function(){
            $('p').css('font-size','x-large')
        })
        
        //改变用了p_font类的元素样式
        $('#btn3').click(function(){
            $('.p_font').css("background-color","palegreen")
        })
        
        //改变所有样式
        $('#btn4').click(function(){
            $('*').css("background-color","darkgoldenrod")
        })
    })
</script>
```

## JQ中的层级选择器

- 子元素选择器:   选择器1 > 选择器2
- 后代选择器:  选择器1 儿孙
- 相邻兄弟选择器 :  选择器1 + 选择器2 : 找出紧挨着的一个弟弟
- 找出所有弟弟:  选择器1~ 选择器2   : 找出所有的弟弟

```
<div id="div">
    <h1>index5.html</h1>
    <ol>
        <li>肉</li>
        <li>蛋</li>
        <li>奶</li>
        <h1>hahaha</h1>
    </ol>
</div>

<div id="div1">
    <h1>index5.html</h1>
    <ol>
        <li>肉1</li>
        <li>蛋1</li>
        <li>奶1</li>
        <h1>hahaha111</h1>
    </ol>
</div>

<div id="div2">
    <h1>index5.html</h1>
    <ol>
        <li>肉1</li>
        <li>蛋1</li>
        <li>奶1</li>
        <h1>hahaha111</h1>
    </ol>
</div>

<input type="button" id="btn1" value="botton1" />
<input type="button" id="btn2" value="botton2" />
<input type="button" id="btn3" value="botton3" />
<input type="button" id="btn4" value="botton4" />

<script type="text/javascript">
    //文档加载事件,页面初始化的操作
    $(function(){
        //改变子元素css
        $('#btn1').click(function(){
            $('ol > li').css("background-color","palegreen")
        })
        
        //找出div下的所有h1，找儿孙
        $('#btn2').click(function(){
            $('#div h1').css("background-color","palegreen")
            //或者 $('div h1').css("background-color","palegreen")
        })
        
        //找紧挨着的弟弟
        $('#btn3').click(function(){
            $('#div+div').css("background-color","palegreen")
        })
        
        //找所有弟弟
        $('#btn4').click(function(){
            $('#div~div').css("background-color","palegreen")
        })
    })
</script>
```

## JQ中的基本过滤器
```
<div id="div">
    <h1>index5.html</h1>
    <ol>
        <li>肉</li>
        <li>蛋</li>
        <li>奶</li>
        <h1>hahaha</h1>
    </ol>
</div>

<div id="div1">
    <h1>index5.html</h1>
    <ol>
        <li>肉1</li>
        <li>蛋1</li>
        <li>奶1</li>
        <h1>hahaha111</h1>
    </ol>
</div>

<div id="div2">
    <h1>index5.html</h1>
    <ol>
        <li>肉1</li>
        <li>蛋1</li>
        <li>奶1</li>
        <h1>hahaha111</h1>
    </ol>
</div>

<input type="button" id="btn1" value="botton1" />
<input type="button" id="btn2" value="botton2" />
<input type="button" id="btn3" value="botton3" />
<input type="button" id="btn4" value="botton4" />

<script type="text/javascript">
    //文档加载事件,页面初始化的操作
    $(function(){
        //选择上下文的第一个div
        $('#btn1').click(function(){
            $('div:first').css("background-color","palegreen")
        })
        
        //偶数位div
        $('#btn2').click(function(){
            $('div:even').css("background-color","palegreen")
            //或者 $('div h1').css("background-color","palegreen")
        })
        
        //未知
        $('#btn3').click(function(){
            $('div:odd').css("background-color","palegreen")
        })
        
        //未知
        $('#btn4').click(function(){
            $('div:gt(0)').css("background-color","palegreen")
        })
    })
</script>
```

## JQ中的属性选择器

```
<script type="text/javascript">
    //文档加载事件,页面初始化的操作
    $(function(){
        //选择有name属性的input
        $('#btn1').click(function(){
            $("input[name]").css("background-color","palegreen")
        })
        
        //选择name=accept的input
        $("#btn2").click(function(){
            $("input[name='accept']").css("background-color","palegreen")
        });
        
        //选择name=tom，value=hot的input
        $("#btn3").click(function(){
            $("input[name='tom'][value='hot']").css("background-color","palegreen")
        });
        
    })
</script>
```

## JQ中的表单过滤器

```
<script type="text/javascript">
    $(function(){
        $(':button').css("background-color","pink");
    })
</script>
```
