```base
#!/usr/bin/env node
```
声明文件执行环境 node
```js
const resolveCwd = require('resolve-cwd');
```
resolve-cwd是指从当前工作目录解析像[require.resolve()](https://nodejs.org/api/globals.html#globals_require_resolve)这样的模块的路径.
这里有个小例子，可以看明白它的功能。
```js
const resolveCwd = require('resolve-cwd');
 
console.log(__dirname);
//=> '/Users/sindresorhus/rainbow'
 
console.log(process.cwd());
//=> '/Users/sindresorhus/unicorn'
 
resolveCwd('./foo');
//=> '/Users/sindresorhus/unicorn/foo.js'
```
下面的就比较没有阅读难度了，用于调试本地的umi
```js
const localCLI = resolveCwd.silent('umi/bin/umi');
if (localCLI && localCLI !== __filename) {
  const debug = require('debug')('umi');
  debug('Using local install of umi');
  require(localCLI);
} else {
  require('../lib/cli');
}
```
以下代码是指，开启了debug之后，打印日志。
```js
const debug = require('debug')('umi');
debug('Using local install of umi');
```
> 关于引用路径说明：因为umi里面的src目录都会被编译到lib目录，所以在代码中引用的都是类似 **../lib/cli** 这样的路径，但是实际上我们要查看的是 **../src/cli** 