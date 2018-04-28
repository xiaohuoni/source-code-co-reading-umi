```base
#!/usr/bin/env node
```
声明文件执行环境 node

```js
const spawn = require('cross-spawn');
```
```js
const chalk = require('chalk');
```
chalk是一个颜色的插件
```js
const script = process.argv[2];
const args = process.argv.slice(3);
```
process模块用来与当前进程互动，可以通过全局变量process访问，不必使用require命令加载。它是一个EventEmitter对象的实例。

process对象提供一系列属性，用于返回系统信息。
- process.pid：当前进程的进程号。
- process.version：Node的版本，比如v0.10.18。
- process.platform：当前系统平台，比如Linux。
- process.title：默认值为“node”，可以自定义该值。
- process.argv：当前进程的命令行参数数组。
- process.env：指向当前shell的环境变量，比如process.env.HOME。
- process.execPath：运行当前进程的可执行文件的绝对路径。
- process.stdout：指向标准输出。
- process.stdin：指向标准输入。
- process.stderr：指向标准错误。
比如执行 umi dev haha xixi
```base
// process.argv
[ 
  'C:\\Program Files\\nodejs\\node.exe',
  'C:\\Users\\Administrator\\AppData\\Roaming\\npm\\node_modules\\umi\\bin\\umi.js',
  'dev',
  'haha',
  'xixi' 
]
```
那么此时

script = dev

args = [ 'haha', 'xixi' ]
```js
const nodeVersion = process.versions.node;
const versions = nodeVersion.split('.');
const major = versions[0];
const minor = versions[1];

if (major * 10 + minor * 1 < 65) {
  console.log(`Node version must >= 6.5, but got ${major}.${minor}`);
  process.exit(1);
}
```
判断当前node的版本大于v6.5
- process.exit(0)表示成功完成，回调函数中，err将为null；
- process.exit(非0)表示执行失败，回调函数中，err不为null，err.code就是我们传给exit的数字。
```js
// Notify update when process exits
const updater = require('update-notifier');
const pkg = require('../package.json');
updater({ pkg: pkg }).notify({ defer: true });
```
进程退出时通知更新,其中defer:true,表示在进程退出后显示通知。

```js
// Add help command 
const cmds = {
  build: 'create a production build',
  dev: 'start a development server',
  test: 'do unit/ui test with jest',
  help: 'show help',
  '-v, --version': 'show version',
};

function help(aliasedScript) {
  let usage = `\nUsage: umi <command>\n`;
  let helpArea = '';
  for (var cmd in cmds) {
    helpArea += (' ' + cmd + Array(25 - cmd.length).join(' ') + cmds[cmd] + '\n');
  }
  console.log([usage,helpArea].join(`\nCommands:\n\n`));
  if (!['help', '-h', '--help'].includes(aliasedScript)) {
    console.log(`Unknown script ${chalk.cyan(aliasedScript)}.`);
  }
}
```
help输入，当执行umi help时
```base
Usage: umi <command>

Commands:

 build                   create a production build
 dev                     start a development server
 test                    do unit/ui test with jest
 help                    show help
 -v, --version           show version
```
```js
// Script area
const scriptAlias = {
  g: 'generate' // eslint-disable-line
};
```
给命令取别名，比如这里给generate取了个别名叫做g
当执行umi generate 时，相当于执行umi g
```js
const aliasedScript = scriptAlias[script] || script;
```
取出别名，如果有别名取别名，没有就保留。
```js
switch (aliasedScript) {
  case '-v':
  case '--version':
    console.log(pkg.version);
    if (!(pkg._from && pkg._resolved)) {
      console.log(chalk.cyan('@local'));
    }
    break;
  case 'build':
  case 'dev':
  case 'generate':
    runScript(aliasedScript, args, /* isFork */true);
    break;
  case 'test':
    runScript(aliasedScript, args);
    break;
  default:
    help(aliasedScript);
    break;
}
```
```js
  case '--version':
    console.log(pkg.version);
    if (!(pkg._from && pkg._resolved)) {
      console.log(chalk.cyan('@local'));
    }
  break;
```
umi -v 这里通过package.json中的 *_from* 和 *_resolved* 来判断是否是本地包

其他的通过runScript执行。
```js
function runScript(script, args, isFork) {
  if (isFork) {
    const result = spawn.sync(
      'node',
      [require.resolve(`../lib/scripts/${script}`)].concat(args),
      {stdio: 'inherit'} // eslint-disable-line
    );
    process.exit(result.status);
  } else {
    require(`../lib/scripts/${script}`);
  }
}
```
执行lib目录下面对应的scripts
