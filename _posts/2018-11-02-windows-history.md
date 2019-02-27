---
layout: post
title: 获取浏览器url各类值
categories: JavaScript
---
# 获取浏览器url的各类值
> 在日常的前端开发过程中，我们不可避免地会遇到一些场景：在js中判断浏览器url中地各类值分别是什么，或者直接改变他们的值向服务器发起请求。在这些场景中我们需要打交道的一个对象就是window.location，通过window.location,我们可以实现一下几种需求
> - 导航到一个新页面
> - 强制从服务器重新加载当前页面
> - 通过修改search属性向服务器发送字符串数据
> - 获取window.location.search的键值，以实现我们的特殊需求

下面我们就来讲讲这几种需求的实现方式：
#### 导航到一个新页面
事实上，window.location是一个只读location对象，但我们仍然可以通过他改变浏览器中的url值，从而导航到一个新页面：
```javascript
	window.location.assign("https://google.com")
	window.location = "https://google.com"  // 这两种方式都是可以实现导航到新页面的
```
#### 强制从服务器重新加载页面
这个需求的场景其实我遇到过很多次，当我们的异步请求成功了，但是也页面中还存在着缓存，用户刷新的时候还是停留在异步请求之前的场景中，这时候我们就可以考虑一下使用这种方式了，reload方式会废弃缓存，强制从服务器中请求一遍页面的所有资源
```javascript
	window.location.reload(true)
```
#### 通过修改search属性向服务器发送数据
举例应用场景：
```javascript
	function sendData (data) {
		window.location.search = data
	}
	// html中的埋点：
	// <button onclick="sendDaata("hello")"></button>
```
当url带上我们指定的数据（"hello"），被发送到服务端，如果服务端不处理（没有响应），则使用修改后的查询字符串重新加载文档
#### 获取window.location.search的一个键值
```javascript
	function loadPageVar (sVar) {
		  return decodeURI(window.location.search.replace(new RegExp("^(?:.*[&\\?]" + encodeURI(sVar).replace(/[\.\+\*]/g, "\\$&") + "(?:\\=([^&]*))?)?.*$", "i"), "$1"));
	}
```
#### 获取url中的查询参数
```javascript
	var oGetVars = {};
	if( window.location.search.length > 1 ){
		for (var aItKey, nKeyId = 0, aCouples = window.location.search.substr(1).split("&");nKeyId < aCouples.length; nKeyId++) {
		aItKey = aCouples[nKeyId].split("=");
		oGetVars[decodeURIComponent(aItKey)] = aItKey.length > 1 ? decodeURIComponent(aItKey[1]) : "";
		}
	}
```



















