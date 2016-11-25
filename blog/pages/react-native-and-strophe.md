---
title: React Native and Strophe for real-time Apps
---

[TOC]

## 业务场景

- rest
- msync
	- protobuf
	- 基于tcp的socket
- xmpp
	- websocket

## 常用

- [官方文档](https://facebook.github.io/react-native/docs/getting-started.html)
	- 基础环境搭建
	- ios和android都需要单独安装
- [开源插件](https://js.coach/react-native?sort=popular)
- [f8app](http://makeitopen.com/)
	- 官方f8app
	- [App需要mongo支持](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
- [iOS android web 三合一方案](https://github.com/taobaofed/react-web)
- [react-native-web](https://github.com/necolas/react-native-web#why)
- [deco ide](https://github.com/decosoftware/deco-ide)
- [React Web: 让React Native 代码跑在Web上](http://www.ghugo.com/react-native-to-web/)
- [react-native 之布局篇](https://segmentfault.com/a/1190000002658374)
- [reactotron](https://github.com/reactotron/reactotron/blob/master/docs/quick-start-react-native.md)
	- debug工具


## 可用性

- react-web ?
- 通信 ？ strophe ?e
- reactotron done
- Reactotron


## QA

### socket vs websokcet and strophe vs socket io

- 类库
	- socket io 客户端和服务端均支持
		- WebSocket
		- 轮询（Polling）
			- Adobe Flash Socket
			- AJAX长轮询
			- AJAX multipart streaming
			- 持久Iframe
			- JSONP轮询
	- strophe.js
		- WebSocket
		- BOSH Bidirectional-streams Over Synchronous HTTP
			- AJAX长轮询
- 协议
	- WebSocket
		- 基于HTTP，因此需要HTTP server的支持
	- Socket
		- 基于TCP

**思考：react native 本来支持 WebSocket，如果改成 socket + protobuf ?**

- [Socket.IO：支持WebSocket协议、用于实时通信和跨平台的框架](http://www.infoq.com/cn/news/2015/01/socket-io-websocket)
- [Strophe.js](http://strophe.im/strophejs/doc/1.2.9/files/strophe-js.html)

### react native and strophe

- no document / no window ? how to make strophe work
	- https://www.npmjs.com/package/react-native-html-parser
	- https://github.com/jindw/xmldom

### what's protobuf

- [Google Protocol Buffer 的使用和原理](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/)
- [Protocol Buffers](https://github.com/google/protobuf)

### Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`

- [Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`](https://github.com/CocoaPods/CocoaPods/issues/4293)
