```js
import fork from 'umi-build-dev/lib/fork';

fork(require.resolve('./realDev.js'));
```
从其他文件引入
此处的fork来自child_process
