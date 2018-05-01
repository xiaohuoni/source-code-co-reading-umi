```js
import { resolve } from 'path';

const debug = require('debug')('umi:devDevOpts');
```
```js
export default function(opts = {}) {
  const { extraResolveModules, hash } = opts;
  debug(`opts: ${JSON.stringify(opts)}`);
  delete opts.extraResolveModules;

  return {
    cwd: process.env.APP_ROOT,
    // eslint-disable-line
    babel: resolve(__dirname, './babel'),
    extraResolveModules: [
      ...(extraResolveModules || []),
      resolve(__dirname, '../../node_modules'),
    ],
    libraryAlias: {
      dynamic: require.resolve('./dynamic'),
      link: require.resolve('./link'),
      navlink: require.resolve('./navlink'),
      redirect: require.resolve('./redirect'),
      router: require.resolve('./router'),
      withRouter: require.resolve('./withRouter'),
      _renderRoutes: require.resolve('./renderRoutes'),
      _createHistory: require.resolve('./createHistory'),
    },
    hash,
    ...opts,
  };
}
```
当执行umi build时，这里opts={plugins: [],hash: true}
在这里组装成开发环境中需要的option
