```js
import test from '../test';

const args = process.argv.slice(2);

const watch = args.indexOf('-w') > -1 || args.indexOf('--watch') > -1;
const coverage = args.indexOf('--coverage') > -1;

test({
  watch,
  coverage,
});
```
从umi-test中引入test模块，然后执行。