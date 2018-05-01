```js
import chalk from 'chalk';
import yParser from 'yargs-parser';
import build from '../build';
const argv = yParser(process.argv.slice(2));
```
```js
//回顾
[require.resolve(`../lib/scripts/${script}`)].concat(args),
```
yargs-parser取出命令行参数，如
```base
umi build abc --xiaohu=haha --bcd=123
```
```json
argv = { _: [ 'abc' ], xiaohu: 'haha', bcd: 123 }
```
```js
build({
  plugins: argv.plugins ? argv.plugins.split(',') : [],
}).catch(e => {
  console.error(chalk.red(`Build failed: ${e.message}`));
  console.log(e);
});
```
执行build。
注，这里取得是argv.plugins的值，但是我执行了很多种情况的命令，都没有plugins这个值。
