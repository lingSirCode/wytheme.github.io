[TOC]

# React Native and Strophe for real-time Apps @ 环信

## 协议

- http
	- rest api 主要用户登陆
- xmpp
	- websocket 用于实时通信部分
- msync（此版本不涉及）
	- protobuf
	- 基于tcp的socket

## 知识点

- [官方文档](https://facebook.github.io/react-native/docs/getting-started.html)
- [开源插件](https://js.coach/react-native?sort=popular)
- 布局 （掌握）
	- [react-native 之布局篇](https://segmentfault.com/a/1190000002658374)
- react native and web （了解）
	- [react-web 淘宝](https://github.com/taobaofed/react-web)
	- [react-native-web](https://github.com/necolas/react-native-web#why)
	- [React Web: 让React Native 代码跑在Web上](http://www.ghugo.com/react-native-to-web/)
- redux（掌握）
	- [Redux 核心解读](http://www.jianshu.com/p/3334467e4b32)
	- [Redux 官方文档](http://cn.redux.js.org/docs/)
- demo（了解）
	- [ignite](https://github.com/infinitered/ignite)
	- [f8app](http://makeitopen.com/)
	- [f8app 中文解析](https://f8-app.liaohuqiu.net/tutorials/building-the-f8-app/data/)
- 工具
	- [deco ide](https://github.com/decosoftware/deco-ide)  （未使用）
		- ide 提供一些可视化便捷操作，及动态添加插件的功能，不过插件并不完整
	- [Reactotron](https://github.com/reactotron/reactotron/blob/master/docs/quick-start-react-native.md) （了解）
		- debug 工具，提供时间旅行的功能，日志记录（日志可以直接使用native自带的debug工具）

## 选型

- Ignite
	- react native starter 封装很多现成的组件和命令，省却很多配置时间
- react-web	 no
	- 不采用，native 和 web 的方案不成熟，从项目时间、维护、反馈及扩展角度来看，小团队会是问题
	- 更趋向于基于Redux的共用公共代码的方法来减少重复开发，这也是React提倡的，一次学习多处应用，而不是一次编写多处应用
	- [Code Sharing Between React Native and React Web Apps](https://medium.com/the-many/code-sharing-between-react-native-and-react-web-apps-b1e1de22fc53#.v5r4urqvg)
- Strophe	yes
	- 通信框架，现有sdk的基础框架，复用可以提高开发效果，不过需要处理一些兼容性问题，基本可以handle
- Reactotron	yes
	- 赋予时间旅行的功能
- redux	yes
	- 代码复用，状态的高效维护 必备
- 依赖模块 见`package.json`

## IDE

> 建议webstorm，其他ide多少都有性能或功能的问题

- webstorm
	- plugin 代码提示
		- [ReactNative-LiveTemplate](https://github.com/virtoolswebplayer/ReactNative-LiveTemplate)
		- [react-templates](https://github.com/wix/react-templates)

## Todo and Flow

1. 主要目录
	- App
		- Containers 容器 | 页面 | 路由
			- App.js 总入口
				- Redux/ 初始化
				- I18n/ 初始化
				- Config/index.js 系统初始配置
			- RootContainer.js  根容器
				- Navigation/NavigationRouter.js 初始化路由
				- /Config/ReduxPersist 持久化初始化
		- Components 常用组件
		- I18n 多语言支持
		- Images 图片资源
		- Lib WebIM初始化
		- Navigation 路由相关
		- Redux actions / reducers
		- Sdk webim-easemobo SDK
1. 设计及素材
	- zeplin
1. todo
	- https的问题
	- splash 开机过渡动画 no
		- [react-native-svg 背景渐变](https://github.com/react-native-community/react-native-svg) todo
		- [react-native-svg 中文](https://segmentfault.com/a/1190000004422456) todo
		- [ios lanuch image](https://medium.com/the-react-native-log/change-default-launch-screen-in-react-native-ios-app-544f94f1e947#.j3bqtaogf)
		- [react-native-splash-screen](https://github.com/crazycodeboy/react-native-splash-screen)
	- 热加载
		- react-native-code-push
	- https://github.com/marcshilling/react-native-image-picker
		- 只添加了ios的依赖，android并没有添加
		- 直接通过xcode编译的debug模式编译是能测试的，但是run-ios无效，即使 react-native link也无效
	- webrtc
		- https://webrtchacks.com/reacting-to-react-native-for-native-webrtc-apps-alexey-aylarov/
		- https://github.com/oney/react-native-webrtc
	- ajax 异步回调的问题
	- 退出账号redux信息销毁问题
	- 点击多次调起相机和照片会崩溃的问题
	- 图片预加载动画、缩略图、缓存、预览
	- 弱网环境
	- 音视频
	 - webrtc



## Android debug and publish

> 项目初始化 `$ npm run newclear`

1. 基础环境安装 ios 和 android https://facebook.github.io/react-native/docs/getting-started.html
	- `$ brew install android-sdk`

	```bash
		// zshrc 依照个人环境不同配置
		export ANDROID_HOME=~/Library/Android/sdk
		export PATH=${PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tools
		export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_112.jdk/Contents/Home/
		// 配置完成记得
		source .zshrc
	```

2. 模拟器和SDK安装 https://developer.android.com/studio/run/managing-avds.html
	- react-native 支持 api 最低到 16
	- 建议通过`$ android ` 管理API和镜像的安装
		- 根据自己平台测试需求安装对应的API和Image
		- 安装成功之后才能去`AVD Mannger `创建镜像
		- 必装 Android xx (API xx)
			- SDK Platform
			- Google APIS Intel x86 Atom System Image
	- 建议通过`Android studio` 管理镜像的创建和运行 (也可用 `$ android avd ` )
		- 打开任意项目 -> Tools -> Android -> AVD Mannger / SDK Manager
3. 模拟器测试
	- 运行任意Image
		- `emulator -avd <avd name>`
	- 根目录 `$ npm start ` 启动server
	- 根目录 `$ react-native run-android`，会将app安装到Image当中（如果连接真机也会安装到真机）
		- 可能会有一些权限之类的错误，可以用sudo
		- 一旦app正常安装且运行，以后不需要每次都编译，因为内容是通过`main.jsbundle`加载的
	- 运行后
		- `ctr + m` or `cmd + m` 调出控制
			- 如果按键无效：模拟器 -> settings -> send keyboards shortcuts to -> Emulator controls(default)
	- 模拟正式环境测试：`$ react-native run-android --variant=release`

4. 正式（签名）版 https://facebook.github.io/react-native/docs/signed-apk-android.html
	- 按照上述步骤添加签名
	- `build.gradle` 位于android/app目录
	- 编译 `$ cd android && ./gradlew assembleRelease`
5. 安装到设备，3种
	- `$ react-native run-android  --variant=release`
	- `$ npm run android:install`
		- 先删除手机上已经安装的包
	- `$ npm run android:build`
		- 到 `android/app/build/outpus/apk` 执行  `adb install xx.apk`，保证只有一个device正在运行
		- 下载 `Android File Transfer`, 连接设备后会自动弹出上传界面（设备需要解锁、允许USB调试、非充电模式）
6. log
	- `$ npm run android:logcat`
	- 程序异常终止也可以通过此命令查看，需要设备和本地在同网络下

常用命令

```bash
$ npm start
$ android
$ android avd
$ emulator -avd n4-768
$ react-native run-android
$ react-native run-android  --variant=release
$ npm run android:install
$ npm run android:build
$ npm run android:logcat
$ ./gradlew assembleRelease
$ ./gradlew installRelease
$ cd android/app/build/outpus/apk && adb install app-release.apk
```

**注：很多快捷命令见根目录 `package.json` scripts 内容**

### 可能遇到的问题

#### Q: 模拟器第一可以正常启动，以后均启动失败
A: 删除镜像文件，重新创建，并运行

#### Q: 真机总是安装失败
A: 尝试删除已经安装的app

- https://github.com/facebook/react-native/issues/2720

## IOS debug and publish

> 项目初始化 `$ npm run newclear`

1. 基础环境安装 ios 和 android https://facebook.github.io/react-native/docs/getting-started.html
2. 模拟器安装
	- xcode -> Preferences-> Components -> iOS x.x Simulator
3. 模拟器测试
	- `react-native run-ios --simulator "iPhone 7"`
	- `cmd + d` 调出控制台
	- `cmd + r` reload
4. 真机测试
	- xcode config
		- Targets -> app -> General -> Signing ->添加一个个人icloud账户
		- 添加team，并定义一个唯一的Bundle Identifier   如：org.reactjs.native.example.app.lwz
			- Targets -> app -> General -> Signing -> Team
			- Targets -> app -> General -> Signing -> Signing Certificate
			- Targets -> app -> General -> Identity -> Bundle Identifier -> 修改唯一id标示
			- Targets -> appTests -> General -> Signing -> Team
			- Targets -> appTests -> General -> Signing -> Signing Certificate
		- `Product -> Scheme -> Edit Scheme (cmd + <), make sure you're in the Run tab from the side` 修改 debug or release
	- xcode 保证app/main.jsbundle可用，没有自行添加索引
		- main.jsbundle 生成方式
			- curl的方式打包  https://github.com/facebook/react-native/issues/5747
			- `react-native bundle --dev false --platform ios --entry-file ./index.ios.js --bundle-output ./ios/app/main.jsbundle`
				- 此种方式打包不支持npm link， 需要先`npm unlink easemob-websdk`
	- 关掉`npm start`启动的控制台
		- 因为打包时会判断是否有packager在运行，有的话直接使用
		- 但是打包用的是release版本， 非dev，打包后会报babel的错，如上
	- 在xcode通过正常run编译到手机
		- 没有packager启动时，会自动启动一个packager，这时打包的即为发布版本！！！
		- 注意：跟本地之前引入的main.jsbundle没有半毛钱关系，打包文件并不会更新到本地
	- xcode -> 选择设备 -> run
	- 信任证书：ios设备 -> General -> Device Management ->  persion Certificate -> trust it
	- 成功运行后，如果是debug模式可以晃动手机调出控制台

	如果编译不生效 ，先clean，然后run-ios引入图片

- [Signing for requires a development team](https://github.com/CocoaPods/CocoaPo ds/issues/5531)
- [Running On Device](https://facebook.github.io/react-native/docs/running-on-device-ios.html)

```bash
$ npm start
$ react-native run-ios --simulator "iPhone 7"
$ react-native run-ios
$ react-native bundle --dev false --platform ios --entry-file ./index.ios.js --bundle-output ./ios/app/main.jsbundle
```

## QA

### Q: babel typeof

Debug remote 和 Hot loading 同时开启

### Q: socket vs websokcet and strophe vs socket io

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

### Q: react native and strophe

> no document / no window ? how to make strophe work

react-native 有全局window，但是不同浏览器端的window，没有document、location等，由于strophe是基于浏览器版本的, 需要自己兼容

```js
// 解决 strophe 对 DOMParser 的依赖
window.DOMParser = require('xmldom').DOMParser;å
// 解决 strophe 对 document 的依赖
window.document = document = new DOMParser().parseFromString("<?xml version='1.0'?>",'text/xml');
// 引入 strophe 库，顺序不能颠倒
import Strophe from 'strophe';
// 连接 strophe, 注意 strophe 绑定到了 window 上面
new window.Strophe.Connection();
```

- [xmldom](https://github.com/jindw/xmldom)
- [react-native-html-parser](https://www.npmjs.com/package/react-native-html-parser)

### Q: what's protobuf

- [Google Protocol Buffer 的使用和原理](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/)
- [Protocol Buffers](https://github.com/google/protobuf)

### Q: Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`

- [Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`](https://github.com/CocoaPods/CocoaPods/issues/4293)

### Q: F8app ":CFBundleIdentifier", Does Not Exist

- https://github.com/facebook/react-native/issues/7308

1. killing processes on port 8081
1. changing the port
1. running it with sudo
1. updating react-native (I'm on the latest version)

### Q: Build process failed - :app:dexDebug FAILED

**yep. i solved by: cd android/ && ./gradlew clean && cd .. && react-native run-android**

- https://github.com/airbnb/react-native-maps/issues/378

### Q: android 版本支持

**+Supported operating systems are >= Android 4.1 (API 16) and >= iOS 7.0.**

- https://github.com/facebook/react-native/commit/e2841c05b4bc67aad90854c7013b278ca4abe03a

### Q: iphone is busy: processing symbol files

- 重启xcode
- 等待xcode symbol 和 indexing 完成，再run

- http://stackoverflow.com/questions/19187857/processing-symbol-files-in-xcode

### Q: PCH was compiled with module cache path

This is often a caching problem. Usually it can be resolved by holding down the Option key and choosing Product > Clean Build Folder...

非 `ignite new app`方式创建的app， 需要先执行 `npm run newclear`

### Q: shadow in react-native

https://github.com/facebook/react-native/commit/b65f1f223488b52963f80a67bb41956103263d27

- 其他厂家问题 http://reactnative.cn/docs/0.20/known-issues.html

### Q: vscode debug react native

https://marketplace.visualstudio.com/items?itemName=vsmobile.vscode-react-native

### Q: Possible Unhandled Promise Rejection (id:0)

- https://github.com/facebook/react-native/issues/4415
- http://reactnative.cn/docs/0.37/network.html#content

- 默认情况下，iOS会阻止所有非HTTPS的请求。如果你请求的接口是http协议，那么首先需要添加一个App Transport Securty的例外，详细可参考[这篇帖子](https://segmentfault.com/a/1190000002933776)
- 如果必须使用http
 - https://ste.vn/2015/06/10/configuring-app-transport-security-ios-9-osx-10-11/

### Q: redux usage
- http://stackoverflow.com/questions/33749759/read-stores-initial-state-in-redux-reducer/33791942#33791942
- https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html
- https://github.com/reactjs/redux/issues/1189
- https://github.com/reactjs/redux/issues/601
- https://github.com/captainill/real-world/blob/master/reducers/index.js
- ** http://redux.js.org/docs/recipes/ComputingDerivedData.html
- https://github.com/reactjs/redux/issues/749
- https://github.com/reactjs/redux/issues/1784
- ** http://www.jianshu.com/p/3334467e4b32
- https://github.com/reactjs/redux/issues/601

### Q: 如何实现一个登录页面的，底部fix的布局

- flex 方式： debug有效 publish无效
	- 父级 direction: column
		- 自适应元素 flex：1
		- 定高元素
- postion: absolute, left/bottom 0


### Q: ua 是什么

iOS debug 模式下的 ua： `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36`

### Q: Could not parse the simulator list output

### Q: Error: ignoring return value of function declared with warn_unused_result attribute

- https://github.com/facebook/react-native/issues/8584



#### Q: debug正常 release 打包失败

babel的配置可以在production时去掉console，但是此配置会导致release打包失败，原因暂未知
```
 .babelrc
    "production": {
      "plugins": [
        "transform-remove-console"
      ]
    }
```
## Redux State
1. state
	- ui
	 	- 作为页面数据相关存储
	 		- common 控制公共的loading
	- entities
		- 作为数据实体相关存储
1. redux
	- Roster
		- updateRoster
		- [a]addContact
		- [a]removeContact
		- [a]getContacts
	- Subscribe
		- addSubscribe
		- removeSubscribe
		- [a]acceptSubscribe
		- [a]declineSubscribe
	- ContactInfoScreen
		- contactDeleted
		- contactShowed
	- CommonRedux
		- fetching
		- fetched
	- Login
		- 登陆和注册融合版，需要重构
	- WebIM
		- logout

1. add / update / remove 跟根目录融合组成作为state关键字，不要用于异步请求
	- 如RosterRedux:   addRoster -> ADD_ROSTER （注意ROSTER不要省略，log可以用过action清楚分辨操作)


```js
{
	ui: [
		common: {
			fetching:false
		},
		login: { }
		register: { },
		contactInfo: { },
	],
	im: [
	],
	user: {
		username: '',
		password: '',
		isSigned: false,
	},
	entities: {
		roster: {
			byName: {
				{
					jid, name, subscription, groups?
				}
			},
			names: ['lwz2'...],
			// 好友列表在此，因为好友列表来源于roster，息息相关
			friends: [],
		},
		// 订阅通知
		subscribe: {
			byFrom: {}
		},
		room: {},
		group: {
			byId: {},
			names: []
		},
		members: {
			byName: [],
			byGroupId: []
		}
		blacklist: {},
		message: {
			byId: {}
			chat: {
				[chatId]: [messageId1, messageId2]
			},
			groupChat: {
				[chatId]: {}
			},
		}
	}
}
```

## 知识点

### ListView

- dataSouce

#### 与ScrollView的区别？

`ListView` 只渲染展示的元素，而不是一次展示所有的元素

####  ListView -> ScrollView row不居顶部 （ios）

`automaticallyAdjustContentInsets={false} `

Controls whether iOS should automatically adjust the content inset for scroll views that are placed behind a navigation bar or tab bar/ toolbar. The default value is true.

`ScrollView` 需要动态设置宽度

#### ScrollView onScroll

```js
onScroll={(e) => {
	console.log(e, e.nativeEvent.contentOffset)
}}
scrollEventThrottle={10}

```

#### swipeablelistview

https://github.com/facebook/react-native/commit/381a0051c7ec5bde4c35967e336050c8dca6a677


#### 参考

- https://facebook.github.io/react-native/docs/using-a-listview.html
- https://facebook.github.io/react-native/docs/listview.html
- https://facebook.github.io/react-native/docs/refreshcontrol.html

### Button.js

https://github.com/facebook/react-native/blob/master/Libraries/Components/Button.js

### 携程是如何做React Native优化的

- https://www.sdk.cn/news/5913

### react native meetup

- http://www.lcode.org/react-native-meetup-share/

### 核心概念

- http://reactnative.cn/docs/0.37/integration-with-existing-apps.html#content

### 与现有的应用程序集成

- http://wiki.jikexueyuan.com/project/react-native/integration-existing.html

### 原生UI组件

- http://reactnative.cn/docs/0.37/native-component-ios.html

### 在原生和React Native间通信

- http://reactnative.cn/docs/0.37/communication-ios.html

### PropTypes

- https://facebook.github.io/react/docs/typechecking-with-proptypes.html

### axios/lib/utils.js

- https://github.com/mzabriskie/axios/issues/554

```js
function isStandardBrowserEnv() {
  return false
  return (
    typeof window !== 'undefined' &&
    typeof document !== 'undefined' &&
    typeof document.createElement === 'function'
  );
}
```

### http上传文件

```js
let formData = new FormData()
formData.append('file', {
	uri: response.uri, type: 'application/octet-stream', name: 'test.jpg'
})

var msg = new WebIM.message('img', WebIM.conn.getUniqueId());
msg.set({
	apiUrl: WebIM.config.apiURL,
	file: {
	  url: source.uri,
	  filename: response.fileName,
	  filetype: 'jpg',
	  data: {
	    uri: response.uri, type: 'application/octet-stream', name: 'test.jpg'
	  }
	}
})
```

### 聊天框中图片的自适应，两种方式

1. api提供款高比
1. 或者通过`Image`的预加载函数，加载完成后获取宽高比

本项目采用方式一，发送图片时即提供图片的宽高

### http上传文件原理

- http流
 - https://zh.wikipedia.org/wiki/%E5%AD%97%E7%AF%80%E6%B5%81
 - https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E5%AA%92%E4%BD%93%E7%B1%BB%E5%9E%8B#Type_application

### 键盘事件时需要调整页面布局

 需要监听键盘布局，键盘出现时候需要动态调整页面的高度 参考 MessageScreen

 - ios 用keyboardWillShow/Hide 会更快捷，但是android下面无效
 - ios 下面键盘是不会压缩视口的高度，但是android 会压缩，所以适配时需要区别对待，大多数情况下，android不需要处理

### ios 和 android 一些布局的注意点
- android: flexDirection: column 要写
- android: alignSelf 需要搭配使用，ios则不需要

### 顶部状态条

- infobar 规则不同
	- ios 布局需要考虑剔除infobar的高度
	- android不需要考虑，statusbar不在布局当中

### signatures do not match the previously installed version

重复签名修改配置文件导致的, 连接设备后执行下面命令

- `adb uninstall "com.yourapp.yourapp"`

### 修改android包信息 logo / 名称 等

- http://reactnative.cn/post/651

### TouchableHighlight 的问题

- TouchableOpacity 替换 TouchableHighlight， TouchableHighlight 无效而且长按会报错




