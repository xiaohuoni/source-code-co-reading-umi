```js
import assert from 'assert';
```
该模块用于编写应用程序的单元测试
```js
import ejs from 'ejs';
```
嵌入式JavaScript模板
```js
import uppercamelcase from 'uppercamelcase';
```
首字母大写转换 如 foo-bar => FooBar
```js
import { join, basename } from 'path';
import { existsSync, statSync, readFileSync, writeFileSync } from 'fs';
```
属性     | 说明|
-------- | ------|
existsSync|测试某个路径下的文件是否存在
statSync|获取文件信息
readFileSync|读取文件
writeFileSync|写文件

```js
//目录存在
function directoryExists(path) {
  return existsSync(path) && statSync(path).isDirectory();
}
```
```js
//文件存在
function fileExists(path) {
  return existsSync(path) && statSync(path).isFile();
}
```
```js
//去除空行
function stripEmptyLine(content) {
  const ret = content
    .trim()
    // 两行 -> 一行
    .replace(/\n\n/g, '\n');

  // 结尾空行
  return `${ret}\n`;
}
```
```js
//看一下代码时，可先忽略assert
export default function(opts = {}) {
  const { file } = opts;
  //断言
  assert(
    !('isDirectory' in opts) || typeof opts.isDirectory === 'boolean',
    'opts.isDirectory should be boolean',
  );
  assert(
    !('useClass' in opts) || typeof opts.useClass === 'boolean',
    'opts.useClass should be boolean',
  );
  //目录
  const isDirectory = opts.isDirectory || false;
  //当前执行路径
  const cwd = opts.cwd || process.cwd();

  console.log(`[DEBUG] generate page ${file} with isDirectory ${isDirectory}`);

  let cssTargetPath;
  let jsTargetPath;
//是目录的时候，在file目录下新建page文件
//不是目录的时候，新建file文件
  if (isDirectory) {
    assert(
      !directoryExists(join(cwd, 'src', 'page', file)),
      `directory src/page/${file} exists`,
    );
    jsTargetPath = join(cwd, 'src', 'page', file, 'page.js');
    cssTargetPath = join(cwd, 'src', 'page', file, 'page.css');
  } else {
    jsTargetPath = join(cwd, 'src', 'page', `${file}.js`);
    cssTargetPath = join(cwd, 'src', 'page', `${file}.css`);
  }

  assert(!fileExists(jsTargetPath), `file src/page/${file} exists`);
  assert(!fileExists(cssTargetPath), `file src/page/${file} exists`);

  //从模板中读取文件
  const jsTpl = readFileSync(
    join(__dirname, '../../template/page.js'),
    'utf-8',
  );
  const cssTpl = readFileSync(
    join(__dirname, '../../template/page.css'),
    'utf-8',
  );


  const fileName = basename(file);
  const componentName = uppercamelcase(fileName);
  //使用ejs render文件
  //ejs.render(str, data, options);
  //options https://www.npmjs.com/package/ejs#options
  const jsContent = ejs.render(
    jsTpl,
    {
      useClass: opts.useClass,
      fileName,
      componentName,
    },
    {
      _with: false,
      localsName: 'umi',
    },
  );

  const cssContent = ejs.render(
    cssTpl,
    {},
    {
      _with: false,
      localsName: 'umi',
    },
  );

  //写入文件
  writeFileSync(jsTargetPath, stripEmptyLine(jsContent), 'utf-8');
  writeFileSync(cssTargetPath, stripEmptyLine(cssContent), 'utf-8');
}
```