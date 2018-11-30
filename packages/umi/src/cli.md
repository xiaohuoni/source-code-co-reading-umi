```js
import { dirname } from 'path';
import yParser from 'yargs-parser';
import signale from 'signale';
import semver from 'semver';
import buildDevOpts from './buildDevOpts';
```
```js
let script = process.argv[2];
const args = yParser(process.argv.slice(3));
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

yParser 用于取出执行命令时携带的参数。
比如执行 umi dev haha xixi --me=xiaohuoni
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

args = { _: [], haha: true, xixi: true, me:'xiaohuoni' }

```js
// Node version check
const nodeVersion = process.versions.node;
if (semver.satisfies(nodeVersion, '<6.5')) {
  signale.error(`Node version must >= 6.5, but got ${nodeVersion}`);
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
updater({ pkg }).notify({ defer: true });
```
进程退出时通知更新,其中defer:true,表示在进程退出后显示通知。
```js
process.env.UMI_DIR = dirname(require.resolve('../package'));
process.env.UMI_VERSION = pkg.version;
```
定义了两个环境变量，用于后续功能使用吧。
```js
const aliasMap = {
  '-v': 'version',
  '--version': 'version',
  '-h': 'help',
  '--help': 'help',
};
```
给命令取别名，比如这里给version取了个别名叫做-v
当执行umi -v 时，相当于执行umi version
```js
switch (script) {
  case 'build':
  case 'dev':
  case 'test':
  case 'inspect':
    require(`./scripts/${script}`);
    break;
  default: {
    const Service = require('umi-build-dev/lib/Service').default;
    new Service(buildDevOpts(args)).run(aliasMap[script] || script, args);
    break;
  }
}
```

defaule中使用service启动命令，是为了执行那些使用```api.registerCommand```注册的命令行，比如内置的umi help就是这么执行的。
