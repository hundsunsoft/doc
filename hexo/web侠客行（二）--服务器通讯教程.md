---
title: web侠客行（二）--服务器通讯教程
date: 2000-04-13 02:02:12
tags: 
    - spring
    - XmlHttpRequest
categories: 
    - 教程
    - web
---

# XML是存储和传输数据的


* XML 指可扩展标记语言（EXtensible Markup Language）
* XML 是一种标记语言，很类似 HTML
* XML 的设计宗旨是传输数据，而非显示数据
* XML 标签没有被预定义。您需要自行定义标签。
* XML 被设计为具有自我描述性。
* XML 是 W3C 的推荐标准

XML 不会做任何事情。XML 被设计用来结构化、存储以及传输信息。

XML 和 HTML 的不同：

XML 被设计为传输和存储数据，其焦点是数据的内容。

HTML 被设计用来显示数据，其焦点是数据的外观。

HTML 旨在显示信息，而 XML 旨在传输信息。

# 一个XML例子
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

# 根据 DTD 来验证 XML
```
<?xml version="1.0" ?> 
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>
<note>
<to>George</to> 
<from>John</Ffrom> 
<heading>Reminder</heading> 
<body>Don't forget the meeting!</body> 
</note>
```

# 使用 XSLT 显示 XML
XSLT 是首选的 XML 样式表语言。

XSLT (eXtensible Stylesheet Language Transformations) 远比 CSS 更加完善。

使用 XSLT 的方法之一是在浏览器显示 XML 文件之前，先把它转换为 HTML，正如以下的这些例子演示的那样:
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<?xml-stylesheet type="text/xsl" href="simple.xsl"?>
<breakfast_menu>
  <food>
    <name>Belgian Waffles</name>
    <price>$5.95</price>
    <description>
       two of our famous Belgian Waffles
    </description>
    <calories>650</calories>
  </food>
</breakfast_menu>
```

# XMLHttpRequest 对象
先看一个例子：
```
<html>
	<head>
		<script type="text/javascript">
			var xmlhttp;

			function loadXMLDoc(url) {
				xmlhttp = new XMLHttpRequest();
				xmlhttp.onreadystatechange = state_Change;
				xmlhttp.open("GET", url, true);
				xmlhttp.send(null);
			}

			function state_Change() {
				if (xmlhttp.readyState == 4) { // 4 = "loaded"
					if (xmlhttp.status == 200) { // 200 = "OK"
						document.getElementById('A1').innerHTML = xmlhttp.status;
						document.getElementById('A2').innerHTML = xmlhttp.statusText;
						document.getElementById('A3').innerHTML = xmlhttp.responseText;
					} else {
						alert("Problem retrieving XML data:" + xmlhttp.statusText);
					}
				}
			}
		</script>
	</head>

	<body>
		<h2>Using the HttpRequest Object</h2>
		<p><b>Status:</b>
			<span id="A1"></span>
		</p>
		<p><b>Status text:</b>
			<span id="A2"></span>
		</p>
		<p><b>Response:</b>
			<br /><span id="A3"></span>
		</p>
		<button onclick="loadXMLDoc('node.xml')">Get XML</button>
	</body>
</html>
```

## 属性readyState
HTTP 请求的状态.当一个 XMLHttpRequest 初次创建时，这个属性的值从 0 开始，直到接收到完整的 HTTP 响应，这个值增加到 4。
* 0	Uninitialized	初始化状态。XMLHttpRequest 对象已创建或已被 abort() 方法重置。
* 1	Open	open() 方法已调用，但是 send() 方法未调用。请求还没有被发送。
* 2	Sent	Send() 方法已调用，HTTP 请求已发送到 Web 服务器。未接收到响应。
* 3	Receiving	所有响应头部都已经接收到。响应体开始接收但未完成。
* 4	Loaded	HTTP 响应已经完全接收。

## responseText
目前为止为服务器接收到的响应体（不包括头部），或者如果还没有接收到数据的话，就是空字符串。

如果 readyState 小于 3，这个属性就是一个空字符串。当 readyState 为 3，这个属性返回目前已经接收的响应部分。如果 readyState 为 4，这个属性保存了完整的响应体。

如果响应包含了为响应体指定字符编码的头部，就使用该编码。否则，假定使用 Unicode UTF-8。

## responseXML
对请求的响应，解析为 XML 并作为 Document 对象返回。

## status
由服务器返回的 HTTP 状态代码，如 200 表示成功，而 404 表示 "Not Found" 错误。当 readyState 小于 3 的时候读取这一属性会导致一个异常。

## statusText
这个属性用名称而不是数字指定了请求的 HTTP 的状态代码。也就是说，当状态为 200 的时候它是 "OK"，当状态为 404 的时候它是 "Not Found"。和 status 属性一样，当 readyState 小于 3 的时候读取这一属性会导致一个异常。

## 事件句柄onreadystatechange
每次 readyState 属性改变的时候调用的事件句柄函数。当 readyState 为 3 时，它也可能调用多次。

## 方法abort()
取消当前响应，关闭连接并且结束任何未决的网络活动。

这个方法把 XMLHttpRequest 对象重置为 readyState 为 0 的状态，并且取消所有未决的网络活动。例如，如果请求用了太长时间，而且响应不再必要的时候，可以调用这个方法。

## getAllResponseHeaders()
把 HTTP 响应头部作为未解析的字符串返回。

如果 readyState 小于 3，这个方法返回 null。否则，它返回服务器发送的所有 HTTP 响应的头部。头部作为单个的字符串返回，一行一个头部。每行用换行符 "\r\n" 隔开。

## getResponseHeader()
返回指定的 HTTP 响应头部的值。其参数是要返回的 HTTP 响应头部的名称。可以使用任何大小写来制定这个头部名字，和响应头部的比较是不区分大小写的。

该方法的返回值是指定的 HTTP 响应头部的值，如果没有接收到这个头部或者 readyState 小于 3 则为空字符串。如果接收到多个有指定名称的头部，这个头部的值被连接起来并返回，使用逗号和空格分隔开各个头部的值。

## open()
初始化 HTTP 请求参数，例如 URL 和 HTTP 方法，但是并不发送请求。

## send()
发送 HTTP 请求，使用传递给 open() 方法的参数，以及传递给该方法的可选请求体。

## setRequestHeader()
向一个打开但未发送的请求设置或添加一个 HTTP 请求。

# 使用FormData对象上传文件
form:
```
<form enctype="multipart/form-data" method="post" name="fileinfo">
  <label>Your email address:</label>
  <input type="email" autocomplete="on" autofocus name="userid" placeholder="email" required size="32" maxlength="64" /><br />
  <label>Custom file label:</label>
  <input type="text" name="filelabel" size="12" maxlength="32" /><br />
  <label>File to stash:</label>
  <input type="file" name="file" required />
  <input type="submit" value="Stash the file!" />
</form>
<div></div>
```
js:
```
var form = document.forms.namedItem("fileinfo");
form.addEventListener('submit', function(ev) {

  var oOutput = document.querySelector("div"),
      oData = new FormData(form);

  oData.append("CustomField", "This is some extra data");

  var oReq = new XMLHttpRequest();
  oReq.open("POST", "stash.php", true);
  oReq.onload = function(oEvent) {
    if (oReq.status == 200) {
      oOutput.innerHTML = "Uploaded!";
    } else {
      oOutput.innerHTML = "Error " + oReq.status + " occurred when trying to upload your file.<br \/>";
    }
  };

  oReq.send(oData);
  ev.preventDefault();
}, false);
```

# load事件
HTML
```
<div class="controls">
    <input class="xhr success" type="button" name="xhr" value="Click to start XHR (success)" />
    <input class="xhr error" type="button" name="xhr" value="Click to start XHR (error)" />
    <input class="xhr abort" type="button" name="xhr" value="Click to start XHR (abort)" />
</div>

<textarea readonly class="event-log"></textarea>
```

JS
```
const xhrButtonSuccess = document.querySelector('.xhr.success');
const xhrButtonError = document.querySelector('.xhr.error');
const xhrButtonAbort = document.querySelector('.xhr.abort');
const log = document.querySelector('.event-log');

function handleEvent(e) {
    log.textContent = log.textContent + `${e.type}: ${e.loaded} bytes transferred\n`;
}

function addListeners(xhr) {
    xhr.addEventListener('loadstart', handleEvent);
    xhr.addEventListener('load', handleEvent);
    xhr.addEventListener('loadend', handleEvent);
    xhr.addEventListener('progress', handleEvent);
    xhr.addEventListener('error', handleEvent);
    xhr.addEventListener('abort', handleEvent);
}

function runXHR(url) {
    log.textContent = '';

    const xhr = new XMLHttpRequest();
    addListeners(xhr);
    xhr.open("GET", url);
    xhr.send();
    return xhr;  
}

xhrButtonSuccess.addEventListener('click', () => {
    runXHR('https://mdn.mozillademos.org/files/16553/DgsZYJNXcAIPwzy.jpg');
});

xhrButtonError.addEventListener('click', () => {
    runXHR('https://somewhere.org/i-dont-exist');
});

xhrButtonAbort.addEventListener('click', () => {
    runXHR('https://mdn.mozillademos.org/files/16553/DgsZYJNXcAIPwzy.jpg').abort();
});
```

# ajax
未完待续

# jquery
未完待续

# asp.net
未完待续