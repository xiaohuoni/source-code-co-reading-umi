```js 
import buildDevOpts from './buildDevOpts';
```
引入buildDevOpts，用于配置开发环境所需要的option。
```js
process.env.NODE_ENV = 'production';
```
设置环境变量
```js
export default function(opts = {}) {
  return require('umi-build-dev/lib/build').default(
    buildDevOpts({
      ...opts,
      hash: true,
    }),
  );
}
```
这里传入的opts={ plugins: [] }
buildDevOpts将会返回如下对象
```json
{ 
    cwd: undefined,
  babel: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\babel',
  extraResolveModules:
   [ 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\node_modules' ],
  libraryAlias:
   { dynamic: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\dynamic.js',
     link: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\link.js',
     navlink: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\navlink.js',
     redirect: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\redirect.js',
     router: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\router.js',
     withRouter: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\withRouter.js',
     _renderRoutes: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\renderRoutes.js',
     _createHistory: 'C:\\Users\\admin\\AppData\\Roaming\\npm\\node_modules\\umi\\lib\\createHistory.js' },
  hash: true,
  plugins: [] }
```
