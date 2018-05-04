```js
import assert from 'assert';
import page from './generators/page';

const generators = {
  page,
};
```
这里应该是为了后续扩展使用的，这里暂时只有一个page
```js
export default function(opts = {}) {
  const { type, file } = opts;
  assert(type, 'opts.type should be supplied');
  assert(typeof type === 'string', 'opts.file should be string');
  assert(file, 'opts.file should be supplied');
  assert(typeof file === 'string', 'opts.file should be string');
  assert(generators[type], `generator of type (${type}) not found`);

  delete opts.type;
  generators[type](opts);
}
```