```js
import { join, basename, extname } from 'path';
```
# join
对于以/开始的路径片段，path.join只是简单的将该路径片段进行拼接
```js
path.join('/a','/b')   // 'a/b'
```
# basename
提取出用 '/' 隔开的path的最后一部分
```js
path.basename('/a/b/c/d/e.html')  // 'e.html'
```
# extname
返回path路径文件扩展名，如果path以 ‘.' 为结尾，将返回 ‘.'，如果无扩展名 又 不以'.'结尾，将返回空值
```js
path.extname('/a/b/c/d/e.html')  // '.html'
path.extname('/a/b/c/d/e.')  // '.'
path.extname('/a/b/c/d/e')  // ''
```
```import { readFileSync } from 'fs';```
readFileSync 同步读取
```js
/**
** 
**/
export function patchRoutes(routes) {
  let index = null;
  for (const [i, value] of routes.entries()) {
    const { component } = value;
    if (basename(component, extname(component)) === '404') {
      index = i;
    }
    if (value.routes) {
      value.routes = patchRoutes(value.routes);
    }
  }
  if (index !== null) {
    const route = routes.splice(index, 1)[0];
    routes = routes.concat({
      component: route.component,
    });
  }
  return routes;
}
```
export default function(api) {
  const { paths, config } = api.service;

  if (process.env.NODE_ENV === 'production' && !config.exportStatic) {
    api.register('modifyRoutes', ({ memo }) => {
      return patchRoutes(memo);
    });
  }

  api.register('beforeServer', ({ args: { devServer } }) => {
    function UMI_PLUGIN_404(req, res, next) {
      if (req.accepts('html')) {
        let pageContent = readFileSync(
          join(__dirname, '../../../template/404.html'),
          'utf-8',
        );
        const routes = [...api.service.routes];
        const rootRoute = routes.filter(route => route.path === '/')[0];
        if (rootRoute) {
          routes.unshift({
            ...rootRoute,
            path: '/index.html',
          });
        }
        pageContent = pageContent
          .replace('<%= PAGES_PATH %>', paths.pagesPath)
          .replace(
            '<%= PAGES_LIST %>',
            routes
              .map(route => {
                return `<li><a href="${route.path}">${route.path}</a></li>`;
              })
              .join('\r\n'),
          );
        res.writeHead(404, { 'Content-Type': 'text/html' });
        res.write(pageContent);
        res.end();
      } else {
        next();
      }
    }
    devServer.use(UMI_PLUGIN_404);
  });
}
