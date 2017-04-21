
# Jest

## 安装

1. 安装

```bash
$ yarn add jest -D
$ yarn add jest-cli -D
$ yarn add enzyme -D
$ yarn add react-addons-test-utils -D


```

### bable-jest : es6 support

```bash
$ yarn add bable-jest -D
```

```json
// package.json
{
  "scripts": {
    "test": "jest"
  },
  "jest": {
    "rootDir": "__test__",
    "transform": {
      "^.+\\.jsx?$": "babel-jest"
    }
  }
}
```

Jest sets the env variable to test

```json
"env": {
	"test": {
	  "presets": [
	    "es2015",
	    "react"
	  ]
	}
}
```

### alias

React 项目在webpack 中定义了很多 alias 的依赖

```json
{
  "plugins": [
    ["module-resolver", {
      "root": ["./src"],
      "alias": {
        "test": "./test",
        "underscore": "lodash"
      }
    }]
  ]
}
```

[babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver)

### package.json 添加

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

### 测试

```bash
$ npm test
```

## 简介

###  基本用法

```js
// for ===
expect(2 + 2).toBe(4)
// for object
expect(data).toEqual({one: 1, two: 2});
// for
expect().toBeNull()
expect().toBeDefined()
expect().toBeUndefined()
expect().toBeTruthy()
expect().toBeFalsy()
// for not
expect().not.toBeFalsy()
// for numbers
expect(value).toBeGreaterThan(3);
expect(value).toBeGreaterThanOrEqual(3.5);
expect(value).toBeLessThan(5);
expect(value).toBeLessThanOrEqual(4.5);
expect(value).toBe(4);
expect(value).toEqual(4);
// for float
expect(0.1 + 0.2).not.toBe(0.3);    // It isn't! Because rounding error
expect(0.1 + 0.2).toBeCloseTo(0.3); // This works.
// for Strings
expect('team').not.toMatch(/I/);
// for Arrays
expect(shoppingList).toContain('beer');
// for Exceptions
// function compileAndroidCode() {
//  throw new ConfigError('you are using the wrong JDK');
// }
expect(compileAndroidCode).toThrow(ConfigError);
expect(compileAndroidCode).toThrow('you are using the wrong JDK');
```

### 异步
```js
// 保证确保test的结束标示在callback之后，防止test提前退出
test('', done => {
	...
	function callback() {
		expect()...
		done()
	}
})
// promise
test('', () => fetch().then( () => {
	expect()...
}) )
//
test('', () => {
	return expect(fetch()).resolves.toBe('')
} )
// async/await
test('the data is peanut butter', async () => {
  await expect(fetchData()).resolves.toBe('peanut butter');
});

```

### 前置后置等控制条件

```js
// 每个test都会执行
beforeEach(() => { });
afterEach(() => { });
// 整个测试文件 一次性设置
beforeAll(() => { });
afterAll(() => { });
// 域设置
describe('matching cities to foods', () => {
	beforeEach(() => { });
})
// 建议 出现异常及时推出
test.only('this will be the only test that runs', () => {})
```

### Mock函数

```js
// 创建一个Mock函数，通过.mock.calls获取调用参数
const mockFn = jest.fn()
mockFn('1')
mockFn('a', 'b')
console.log(mockFn.mock.calls) // > [ [1], ['a', 'b'] ]
// .mock 所有mock函数皆有此属性，专门存放函数的如果被调用的相关数据
const myMock = jest.fn();
const a = new myMock();
const b = {};
const bound = myMock.bind(b);
bound();
console.log(myMock.mock.instances); // > [ <a>, <b> ]
// 测试调用过程
expect(someMockFunction.mock.instances[0].name).toEqual('test');
// 模拟返回值： 每次返回不同的数值
myMock.mockReturnValueOnce(10)
 .mockReturnValueOnce('x')
 .mockReturnValue(true);
myMock()
//
filterTestFn
  .mockReturnValueOnce(true)
  .mockReturnValueOnce(false);
const result = [11, 12].filter(filterTestFn);
console.log(filterTestFn.mock.calls);
//
const myMockFn = jest.fn(cb => cb(null, true));
// test.js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');
// foo is a mock function
foo.mockImplementation(() => 42);
foo();
// > 42
const myMockFn = jest.fn()
  .mockImplementationOnce(cb => cb(null, true))
  .mockImplementationOnce(cb => cb(null, false));

myMockFn((err, val) => console.log(val));
// > true

myMockFn((err, val) => console.log(val));
// > false

```





# Enzyme