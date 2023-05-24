## TypeScript

### 1. 环境搭建

- `npm i -g typescript`

- 编译：通过编译后会生成一个JS文件

  `tsc tsFileName.ts`

### 2. TS的类型声明

- 可以给变量指定类型

- `let a: number = 10;`    a的类型设置为了number，在以后的使用过程中a的值只能是数字

- 类型种类：

  | 类型    | 例子 | 描述                             |
  | ------- | ---- | -------------------------------- |
  | number  |      | 数字                             |
  | string  |      | 字符串                           |
  | boolean |      | 布尔值                           |
  | any     |      | 任意类型                         |
  | unknown |      | 类型安全的any                    |
  | void    |      | 没有值                           |
  | never   |      | 不能是任何值                     |
  | object  |      | 任意的js对象                     |
  | array   |      | 任意的JS数组                     |
  | tuple   |      | 元素，TS新增类型，固定长度数组   |
  | enum    |      | 枚举，TS中新增类型               |
  | 字面量  |      | let a: 10; a = 10; 表示a只能为10 |

## 3. TS的编译选项

### 3.1 自动编译

- `tsc fileName.ts -w`  编译器会监视当前ts文件，如果发生改变，则进行编译

- 第二种方式：
  - 当前目录下创建`tsConfig.json`文件，里面写一个空的json
  - 命令启动`tsc -w`即可编译全部ts

### 3.2 tsConfig.json的配置文件

```json
{
    // 指定那些TS文件需要被编译
    // 一个*表示任意文件，两个**表示任意目录
    // 可以写多个目录
    "include": [
        “./src/**/*”,
        "",
    ],
    // 不变异这里的
    "exclude": [
        “./src/test",
    ],
    "compilerOptions": {
    	// 指定编译的ES版本
    	"target": "",
    	// 指定模块化的规范：‘none’、‘commonjs’等
    	"module": "",
    	// lib指定项目使用的库
    	"lib": ["dom", ""],
    	// 指定编译后文件所在的目录
    	"outDir": "./dist",
        // 将代码合并为一个文件---一般也不太用了
        "outFile": "",
        // 是否对JS文件进行编译
        "allowJs": false,
        // 是否检查JS代码是否符合语法规范，默认是false
        "checkJs": false,
        // 是否移除注释
        "removeComments": true,
        // 不生成编译后的文件
        "noEmit": false,
        // 当有错误时不生成编译后的文件
        "noEmitOnError": false,
        
        // 和语法检查有关的
        // 所有严格检查的总开关
        "strict": true, 
        // 用来设置编译后的文件是否使用严格模式，默认false 
        "alwaysStrict": false,
        // 不允许隐式any类型
        "noImpliciAny"： true,
        // 不允许不明确类型的this
        "noImplicitThis": false, 
        // 严格检查null值
        "strictNullChecks":false,
        
    }
}
```

### 4. WebPack打包TS代码

- 生成package.json：`npm init -y`

- 安装webpack的依赖：`npm i -D webpack webpack-cli typescript ts-loader`

- 编写webpack配置文件：

  ```js
  // 创建webpack.config.js
  
  // 具体编写
  // 引入包
  const path = require('path');
  
  // webpack的所有配置信息写到这里面
  module.exports = {
      // 指定入口文件
      entry: "./src/index.ts",
      
      // 指定打包文件所处的目录
      output: {
          // 指定打包文件的目录
          path: path.resolve(__dirname, "dist");
          // 打包后文件的名字
          filename: "bundle.js",
      },
      // 指定webpack打包时要使用模块
      module: {
          // 指定加载的规则
          rules: {
              // test制定的是规则生效的文件
              test: ts文件的正则表达式,
              // 要使用的loader
              use: 'ts-loader',
              // 要排除的文件
              exclude: /node-modules/
          }
      }
  }
  
  // 设置使用
  // package.json中
  {
      "scripts": {
          "build": "webpack",
      }
  }
  ```

- 后面的webpack没看了

### 5. TS的面向对象

#### 5.1 类

- 定义：

  ```js
  class Person {
      // 定义属性---默认就能读写了，不需要和Java一样写getter和setter
      name: string;
      age: number;
      static home: string;
      // 只能读取了，不能写了
      readonly game: string;
  
  	// 构造函数
  	constructor() {
          
      }
  
  	sayHello() {
          console.log(`Hello`);
      }
  }
  ```

#### 5.2 继承

- 我都懒得做笔记了























