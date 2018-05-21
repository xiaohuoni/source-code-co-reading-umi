```js
import yParser from 'yargs-parser';
import help from '../help';

const argv = yParser(process.argv.slice(2));
const [type] = argv._;

help({
  type,
});
```
从别的文件中引入help方法