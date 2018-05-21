```js
import chalk from 'chalk';
```
把现有的支持的命令都罗列出来，名字，简介，别名
```js
const commands = {
  build: {
    name: 'build',
    description: 'Create a production build',
  },
  dev: {
    name: 'dev',
    description: 'Start a development server',
  },
  test: {
    name: 'test',
    description: 'Start a development server',
  },
  help: {
    name: 'help',
    description: 'show help',
    aliases: ['h'],
  },
  version: {
    name: 'version',
    description: 'Outputs Umi version.',
    aliases: ['v'],
  },
};
```
统一调用的打印方法，其实这个应该放别的地方，做util工具使用
```js
class Logger {
  info = message => {
    console.log(`${message}`);
  };

  error = message => {
    console.error(chalk.red(message));
  };

  success = message => {
    console.error(chalk.green(message));
  };
}
```
对现有支持的数组做一个遍历，打印出所有的使用方法
```js
export default function(opts = {}) {
  const { type } = opts;
  const logger = new Logger();
  logger.info(`\nUsage: umi <command>\n`);
  if (!commands[type]) {
    logger.error(`Unknown script : ${chalk.cyan(type)}.`);
  }
  logger.info(`Available Commands:`);
  for (const key in commands) {
    if (commands.hasOwnProperty(key)) {
      const cmd = commands[key];
      logger.info(`  ${chalk.cyan(cmd.name)} ${cmd.description}`);
    }
  }
  // 这里应该是留做后续接口扩展说明的，如使用umi dev --help,应该要打印出dev的说明，还有所支持的环境变量等等。
  // Support for subsequent extensions
  // logger.info(`\nFor more detailed help run "umi [command name] --help"`);
}
```
文中注释可扩展的地方是因为现在umi版本还不是那么稳定，这一块的功能我没想好怎么处理。应该要支持多语言。