[TOC]

## 简介

如果相对前端测试框架有一个基本的了解，可以先阅读 [**An Overview of JavaScript Testing in 2017**](https://medium.com/powtoon-engineering/a-complete-guide-to-testing-javascript-in-2017-a217b4cd5a2a)，本文也会对文中内容做简单梳理，方便选型

### Test Tools Types

1. Provide a **test environment** (Mocha, Jasmine, Jest, Karma)
2. Provide a **testing structure** (Mocha, Jasmine, Jest, Cucumber)
3. Provide an **assertions functions** (Chai, Jasmine, Jest, Unexpected)
4. Generate, **display, and watch** test results (Mocha, Jasmine, Jest, Karma)
5. Generate and compare **snapshots** of component and data structures to make sure changes from previous runs are intended (Jest, Ava)
6. Provide **mocks, spies, and stubs** (Sinon, Jasmine, enzyme, Jest, testdouble)
7. Generate **code coverage** reports (Istanbul, Jest)
8. Provide a **browser or browser-like environment** with a control on their scenarios execution (Protractor, Nightwatch, Phantom, Casper)
    - Phantom - Headless
9. DOM support **JSDom**

### Putting it All Together

1. For **unit tests**, provide all units with a mocked input (6) and make sure their output is expected (3), Also make sure to use a coverage reporting tool (7) to know what units are covered.
2. For **integration tests**, define important cross-module internal scenarios. Comparing to unit tests, you would use spies and stubs to test expected behavior instead of just asserting the output (6). Also, a browser or browser-like environment could test integration between processes and their results on the UI.
    - 推荐：**JSDom** **Karma**
3. For **functional tests**, A browser or browser-like environment with a programmable API (8) will be used to create user-like behavior scenarios.
    - **TestCafe** 简单 （ **Selenium** ）
    - **Selenium** 完整 （ **SeleniumHQ**， **Protractor**， **WebdriverIO**， **Nightwatch** ）
    - **Casper** 无头，快 （ **Phantom** ）

### List of General Prominent Testing Tools

- **JSDom** is a JavaScript implementation of the WHATWG DOM and HTML standards
- **Istanbul** will tell you how much of your code is covered with unit tests.
- **Phantom** implements a “headless” Webkit browser which is between a real browser and JSDom in speed and stability.
- **Karma** lets you run tests in browsers, including real browsers, Phantom, jsdom, and even legacy browsers.
- **Chai** is the most popular assertion library.
- **Unexpected** is an assertion library with a slightly different syntax from Chai
- **Sinon** is a very powerful standalone test spies, stubs and mocks for JavaScript that works with any unit testing framework.
- **Testdouble** is a new library that is similar to Sinon
- **Wallaby** not free, CI with IDE
- **Enzyme** JavaScript Testing utilities for React

### Choose Your Framework

 - **Jasmine**
 - **Mocha**
 - **Jest**
 - **Ava**
 - **Tape**

## 需求和选型

当前架构：Webpack + React

- 自动化和持续集成、自动监听
    - Karma - Test Runner
- 真实浏览器测试环境支持、Headless browsers
    - 可选: PhantomJS
    - 排除: Jest
- 基于React
    - Enzyme
- 测试框架
    - Jasmine
- 覆盖率
    - Istanbul

## 安装

### 注意

注意：此套配置面向 react 0.14 版本，不适应于最新版

具体请看 [react-webpack-karma-jasmine-enzyme-boilerplate](https://github.com/wytheme/react-webpack-karma-jasmine-enzyme-boilerplate)

### package.json

**各模块版本严格依赖，随意修改可能导致未知问题**

```json
"dependencies": {
    "babel-runtime": "^6.9.2",
    // react 0.14
    "react": "0.14",
    "react-dom": "0.14",
    // react 0.14 test support for enzyme
    "react-addons-test-utils": "0.14.6",
    // fix phantom promise
    "es6-promise": "^4.0.5",
  },
"devDependencies": {
  // react + es6
  "babel-core": "^6.9.1",
  "babel-eslint": "^6.0.4",
  "babel-loader": "^6.2.4",
  "babel-preset-es2015": "^6.9.0",
  "babel-preset-react": "^6.5.0",
  // enzyme
  "enzyme": "^2.8.2",
  // eslint
  "eslint": "^2.11.1",
  "eslint-config-airbnb": "^9.0.1",
  "eslint-import-resolver-node": "^0.2.0",
  "eslint-plugin-babel": "^3.2.0",
  "eslint-plugin-import": "^1.8.1",
  "eslint-plugin-jsx-a11y": "^1.2.2",
  "eslint-plugin-react": "^5.1.1",
  // coverage
  "istanbul-instrumenter-loader": "^0.2.0",
  // jasmine
  "jasmine-core": "^2.4.1",
  // enzyme for jsmine
  "jasmine-enzyme": "^3.1.0",
  "json-loader": "^0.5.4",
  // karma
  "karma": "^0.13.22",
  "karma-chrome-launcher": "^1.0.1",
  "karma-coverage": "^1.0.0",
  "karma-firefox-launcher": "^1.0.0",
  "karma-jasmine": "^1.0.2",
  "karma-phantomjs-launcher": "^1.0.0",
  "karma-spec-reporter": "0.0.26",
  "karma-coverage-istanbul-reporter": "^1.2.1",
  // webpack support
  "karma-webpack": "^1.7.0",
  // phantoms
  "phantomjs-polyfill": "0.0.2",
  "phantomjs-prebuilt": "^2.1.7",
  "react-hot-loader": "^1.3.0",
  "webpack": "^1.13.1",
  "webpack-dev-server": "^1.14.1"
}
```

### karma.config.js

```js
var webpack = require('karma-webpack');
var webpackConfig = require('./webpack.config');
webpackConfig.module.loaders = [
  {
    test: /\.(js|jsx)$/, exclude: /(bower_components|node_modules)/,
    loader: 'babel-loader'
  },
  {
    test: /\.json$/,
    loader: 'json',
  },
];
webpackConfig.module.postLoaders = [{
  test: /\.(js|jsx)$/, exclude: /(node_modules|bower_components|tests)/,
  loader: 'istanbul-instrumenter'
}];
// Using Enzyme with Karma - https://github.com/airbnb/enzyme/blob/master/docs/guides/karma.md
webpackConfig.externals = {
  'react/lib/ExecutionEnvironment': true,
  'react/lib/ReactContext': true,
  // 'react-addons-test-utils': true,
}

module.exports = function (config) {
  config.set({
    // 依赖框架
    frameworks: [ 'jasmine' ],
    files: [
      './node_modules/phantomjs-polyfill/bind-polyfill.js',
      'tests/**/*_spec.js'
    ],
    // karma support for webpack / jasmine
    plugins: [
      webpack,
      'karma-jasmine',
      'karma-chrome-launcher',
      'karma-firefox-launcher',
      'karma-phantomjs-launcher',
      'karma-coverage',
      'karma-spec-reporter'
    ],
    // 测试使用的浏览器
    browsers: [ 'Chrome' ],
    // 预编译操作，类似webpack的entry
    preprocessors: {
      'tests/**/*_spec.js': ['webpack'],
      'src/**/*.js': ['webpack']
    },
    reporters: [ 'spec', 'coverage' ],
    coverageReporter: {
      dir: 'build/reports/coverage',
      reporters: [
        { type: 'html', subdir: 'report-html' },
        { type: 'lcov', subdir: 'report-lcov' },
        { type: 'cobertura', subdir: '.', file: 'cobertura.txt' }
      ]
    },
    //  webpack 相关支持
    webpack: webpackConfig,
    webpackMiddleware: { noInfo: true }
  });
};
```

###  Enzyme support with jasmine

jasmine-enzyme

- 测试文件：example_spec.js

```js
import jasmineEnzyme from 'jasmine-enzyme';
beforeEach(() => {
  jasmineEnzyme();
})

```

### phantomjs - Headless browser
1. 管网下载bin目录 http://phantomjs.org/download.html
2. 将phatomjs拷贝到path下面
3. 配置zshrc配置  https://github.com/karma-runner/karma-phantomjs-launcher/issues/80

### Istanbul - Coverage
1. coverage for promise -> todo
2, support sourcemap -> done

## 覆盖率

http://www.ruanyifeng.com/blog/2015/06/istanbul.html

- 行覆盖率（line coverage）：是否每一行都执行了？
- 函数覆盖率（function coverage）：是否每个函数都调用了？
- 分支覆盖率（branch coverage）：是否每个if代码块都执行了？
- 语句覆盖率（statement coverage）：是否每个语句都执行了？

```
=============================== Coverage summary ===============================
Statements   : 35.38% ( 1141/3225 )
Branches     : 17.31% ( 210/1213 )
Functions    : 20.61% ( 156/757 )
Lines        : 35.53% ( 1123/3161 )
================================================================================
15 05 2017 17:42:00.361:ERROR [reporter.coverage-istanbul]: Coverage for statements (35.38%) does not meet global threshold (70%)
15 05 2017 17:42:00.361:ERROR [reporter.coverage-istanbul]: Coverage for lines (35.53%) does not meet global threshold (70%)
15 05 2017 17:42:00.361:ERROR [reporter.coverage-istanbul]: Coverage for branches (17.31%) does not meet global threshold (70%)
15 05 2017 17:42:00.361:ERROR [reporter.coverage-istanbul]: Coverage for functions (20.61%) does not meet global threshold (70%)
```

## 结果输出

```
  test
    ✓ login: do login

PhantomJS 2.1.1 (Mac OS X 0.0.0): Executed 1 of 1 SUCCESS (0.78 secs / 0.342 secs)
TOTAL: 1 SUCCESS
```

## CI

## 难点

Karma 整体测试结构都是启动浏览器并渲染浏览器真实DOM， 所以才不要fake ajax等，同样可以引入全局文件，例如setup.js，可以理解成一个“白盒的功能测试”.
可以通过切换到真实浏览器，看页面如何加载框架

1. 版本间强依赖，多框架
2. 模拟一个纯基于webpack的环境时候
    - 如何处理静态资源 ？
    - 是否支持多入口
3. 如何校验页面渲染已经完成，router已经正常调整，节点如何判断？ ***
    - simulate 和 functions
4. 代码覆盖率
    - 如何支持es6
    - 部分promise代码未检测到  ?
    - 部分页面标注不正常 ?
4. Why not update simulate() to return a promise #898
   - https://github.com/airbnb/enzyme/pull/898
   - https://github.com/airbnb/enzyme/issues/823
5. webpack融合后部分插件失效
    - json-loader karma 不自动解析 > 全部修改成固定的json-loader的形式去掉 json! 前缀
    - commonPlugin不支持， karma要干掉 > 其中依赖的模块需要单独引入
6. enzyme 获取react组件时，需要修改组件结构 见：Login.jsx


## 参考

- [**An Overview of JavaScript Testing in 2017**](https://medium.com/powtoon-engineering/a-complete-guide-to-testing-javascript-in-2017-a217b4cd5a2a)
- [Testing Frameworks](http://stateofjs.com/2016/testing/)
- [karma 测试框架的前世今生](http://taobaofed.org/blog/2016/01/08/karma-origin/)
- [如何进行前端自动化测试？](https://www.zhihu.com/question/29922082)
- [TESTING REACT APPLICATIONS WITH KARMA, JEST OR MOCHA](http://instea.sk/2016/08/testing-react-applications-with-karma-jest-or-mocha/)
- [Enzyme](https://github.com/airbnb/enzyme)
- [Enzyme](https://github.com/airbnb/enzyme/blob/master/docs/api/mount.md)
- [jasmine-enzyme](https://github.com/blainekasten/enzyme-matchers/tree/master/packages/jasmine-enzyme)
- [jasmine-enzyme: ensure-matchers-are-added](https://github.com/blainekasten/enzyme-matchers/blob/master/packages/jasmine-enzyme/spec/ensure-matchers-are-added--spec.js)
- [Using Enzyme with Karma](https://github.com/airbnb/enzyme/blob/master/docs/guides/karma.md)
- [Using Enzyme with Webpack](https://github.com/airbnb/enzyme/blob/master/docs/guides/webpack.md)
- [karma](http://karma-runner.github.io/1.0/config/files.html)
- [karma-adapter](https://www.npmjs.com/browse/keyword/karma-adapter)
- [karma-webpack](https://github.com/webpack-contrib/karma-webpack)
- [聊一聊前端自动化测试](https://github.com/tmallfe/tmallfe.github.io/issues/37)
- [Add support for headless Chrome/ChromeCanary](https://github.com/karma-runner/karma-chrome-launcher/pull/111)
- [enzyme-example-karma-webpack](https://github.com/lelandrichardson/enzyme-example-karma-webpack)
- [enzyme-example-karma-webpack/0.1.4](https://www.versioneye.com/nodejs/enzyme-example-karma-webpack/0.1.4)
- [karma-webpack: Support having multiple entries](https://github.com/webpack-contrib/karma-webpack/issues/165)
- [PhantomJS：基于WebKit、开源的服务器端JavaScript API](http://www.infoq.com/cn/news/2015/01/phantomjs-webkit-javascript-api)

引用 https://github.com/tmallfe/tmallfe.github.io/issues/37 一句话

使用PhantomJS构建一个伪造的浏览器环境跑单元测试，好处是解决了代码覆盖率问题，也可以做持续集成。这个方案的缺点是PhantomJS毕竟是Qt的webkit，并不是真实浏览器环境，PhantomJS也有各种各样兼容性坑
通过Karma调用本机各种浏览器进行测试，好处是可以跨浏览器做测试，也可以测试覆盖率，但持续集成时需要注意只能开PhantomJS做测试，毕竟集成的Linux环境不可能有浏览器。这可以说是目前看到的最好的前端代码测试方式了

## 其他

- karma 对 headless chrome 支持中，届时PhatomJS可能就没什么用途了
    - [Add support for headless Chrome/ChromeCanary](https://github.com/karma-runner/karma-chrome-launcher/pull/111)
- jest 针对支持真实浏览器环境的讨论
    - [Running tests in the browser](https://github.com/facebook/jest/issues/139)
    - [Targeting more than JSDOM](https://github.com/facebook/jest/issues/848)



