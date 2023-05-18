# 深入理解 TypeScript

## TypeScript 项目

### 编译上下文
编译上下文就是给文件分组，告诉 TypeScript 哪些文件是有效的，哪些是无效的
定义这种逻辑分组，一个较好的方式是 tsconfig.json 文件

#### tsconfig.json
**基础**  
再项目根目录下创建一个空的 JSON 文件。此时 TypeScript 会把当前目录及所有子目录下的 ts 文件作为编译上下文的一部分

**编译选项**  
可通过 compilerOptions 来定制编译选项
``` JSON
{
  "compilerOptions": {

    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件作为单独的模块 （与 'ts.transpileModule' 类似）.

    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'

    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）

    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。

    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性

    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  }
}
```

**TypeScript 编译**  
一些 IDE 支持对 tsconfig.json 的即时编译，手动编译方式有：
* 运行 tsc, 它会在当前目录或父级目录寻找 tsconfig.json 文件  
* 运行 `tsc -p ./path-to-project-directory` 该路径可以是绝对路径或相对路径

可以使用 `tsc -w` 启动 TypeScript 编译器的观测模式，在检测到文件改动之后会重新编译

**指定文件**  
可以显式指定需要编译的文件
``` JSON
{
  "files": [
    "./some/file.ts"
  ]
}
```
还可以使用 include 和 exclude 选项来指定需要包含的和排除的文件
``` JSON
{
  "include": [
    "./folder"
  ],
  "exclude": [
    "./folder/**/*.spec.ts",
    "./folder/someSubFolder"
  ]
}
```

### 声明空间
在 TypeScript 中存在两种声明空间，类型声明空间与变量声明空间
#### 类型声明空间
类型声明空间包含用来当作类型注解的内容，例如
``` TypeScript
class Foo {}
interface Bar {}
type Bas = {}
```
可以把 Foo/Bar/Bas 当作类型注解使用
``` TypeScript
let foo: Foo
let bar: Bar
let bas: Bas
```
注意，不能把 Bar 当作一个变量来用，因为它没有定义在变量声明空间中

#### 变量声明空间
变量声明空间包含可用作变量的内容，上文中的 `class Foo` 提供了一个类型 `Foo` 到类型声明空间，此外也提供了一个变量 `Foo` 到变量声明空间
``` TypeScript
class Foo {}
const someVar = Foo
const someOtherVar = 123
```
注意，不能把 `interface` 定义的内容当变量使用

与此类似，使用 `var` 声明的变量只能再变量声明空间使用，不能用作类型注解
``` TypeScript
const foo = 123
let bar: foo // Error: 'cannot find name foo'
```

### 模块
#### 全局模块
在默认情况下，当你开始在一个新的TypeScript文件中定义变量时，它处于全局命名空间中。
``` TypeScript
const foo = 123
```
如果再相同项目里创建了一个新文件 bar.ts，TypeScript 类型系统将会允许你使用变量 `foo` 就好像它在全局可用
``` TypeScript
const bar = foo; // allowed
```
#### 文件模块
文件模块也被称为外部模块，如果在TypeScript 文件的根级别位置含有 import 或者 export，那么他会在这个文件中创建一个本地的作用域。因此需把上网呢 foo.ts 改为如下方式
``` TypeScript
// foo.ts
export const foo = 123

// bar.ts
const bar = foo; // ERROR: "cannot find anme 'foo'"

import { foo } from './foo.ts'
const bar = foo; // allow
```
使用 `import` 时，它允许你使用其他文件导入的内容，还会将文件 `bar.ts` 标记为一个模块，文件内定义的声明也不会污染全局命名空间


#### 文件模块详情
下面讨论文件模块的功能及一些用法  

**commonjs, amd, es modules, others**
根据 module 选项可以把 TypeScript 编译成不同的 JavaScript 谋爱类型
* AMD：不要使用，它仅能在浏览器工作
* SystemJS：已经被 ES 模块取代
* ES 模块：未准备好

建议使用 `module: commonjs` 来替代上述模块  
* 放弃使用 `import/require` 语法，即 `import foo = require('foo')` 写法
* 推荐使用 ES 模块语法  

使用 `module: commonjs` 选项及使用 ES 模块语法导入、导出、编写模块

**ES 模块语法**
  ``` TypeScript
  // -------------foo.ts

  // 使用 `export` 关键字导出一个变量或类型
  export const someVar = 123
  export type someType = {
    foo: string
  }

  // export` 的另一种写法
  const someVar = 123
  type someType = {
    type: string
  }
  export { someVar, someType }

  // 可以用重命名变量方式导出
  const someVar = 123
  export { someVar as aDifferentName }

  // -------------bar.ts
  // 使用 import 关键字导入一个变量或者一个类型
  import { someVar, someType } from './foo'

  // 通过重命名方式导入变量或者类型
  import { someVar as aDifferentName } from './foo'

  // 除了加载指定的某个输出值，也可使用整体加载
  import * as foo from './foo'

  // 只导入模块
  import 'core-js'

  // 从其他模块导入后整体导出
  export * from './foo'

  // 从其他模块导入后部分导出
  export { someVar } from './foo'

  // 通过重命名，部分导出从另一个模块导入的项目
  export { someVar as aDifferentName } from './foo'

  // 默认导入导出 export default
  export default (someVar = 123)
  export default function someFunction() {}
  export default class someClass {}
  // 导入使用 import someName from 'someModule' 语法
  import someLocalNameForThisFile from './foo'
  ```
**模块路径**  
存在两种截然不同的模块，他们的区别在于系统如何解析模块
* 相对模块路径(路径以 . 开头，例如： `./someFile` 或 `../../someFolder/someFile` 等)
* 其他动态查找模块(如：`core-js`, `typestyle`, `react` 或者 `react/core` 等)

这里说下 Node 模块解析策略
* 当使用 `import * as foo from 'foo'` 将会按如下顺序查找模块
  - ./node_modules/foo
  - ../node_modules/foo
  - ../../node_modules/foo
  - 直到系统的根目录
* 当使用 `import * as foo from 'something/foo'` 将会按如下顺序查找内容
  - ./node_modules/something/foo
  - ../node_modules/something/foo
  - ../../node_modules/something/foo
  - 直到系统的根目录

**重写类型的动态查找**  
在项目中可通过 `declare module 'somePath'` 声明一个全局模块的方式，来解决查找模块路径的问题
``` TypeScript
// global.d.ts
  declare module 'foo' {
    // some variable declarations
    export var bar: number
  }

// anyOtherTsFileInProject.ts
  import * as foo from 'foo'
  // 
```

**import/require 仅仅是导入类型**  
``` TypeScript
import foo = require('foo')
```
以上导入语法实际上只做了两件事：
* 导入 foo 模块的所有类型信息
* 确定 foo 模块运行时的依赖关系

如果没有把导入的名称当作变量声明空间使用，在编译成 JavaScript 时导入的模块将会被完全移除

#### global.d.ts
该文件可用来将一些接口或者类型放入全局命名空间里，方便再所有 TypeScript 代码中使用

注意：对于需要编译成 JavaScript 的代码，强烈建议放入文件模块里

### 命名空间
在 JavaScript 使用命名空间时，有一个常用语法
``` JavaScript
(function(something) {
  something.foo = 123
}) (something || (something = {}))

console.log(something) // { foo: 123 }

(function(something) {
  something.bar = 456
})(something || (something = {}))

console.log(something)
// { foo: 123, bar: 456 }
```

``` TypeScript
namespace Utility {
  export function log(msg) {
    console.log(msg)
  }
  export function error(msg) {
    console.log(msg)
  }
}
// usage
Utility.log('call me')
Utility.error('maybe')
```
`namespace` 关键字编译后的代码，与我们早些时候看到的 `JavaScript` 代码一样
``` JavaScript
(function (Utility) {
  // code
})(Utility || (Utility = {}))
```
命名空间可以嵌套
对大多数项目建议使用外部模块和命名空间，方便移植旧的 JavaScript 代码


### 动态导入表达式
动态导入表达式时 ECMAScript 的一个新功能，允许在程序任意位置异步加载一个模块

## 语法特性
### 基础类型
基础类型和 JavaScript 几乎相同  
**布尔值**  
最基本的数据类型 boolean, 简单的 true/false 值
``` TypeScript
let isDone: boolean = false
```

**数字**  
浮点数的类型是 number，支持二进制、八进制、十进制、十六进制
``` TypeScript
let decLiteral: number = 6
let hexLiteral: number = 0xf00d
let binaryLiteral: number = 0b1010
let octalLiteral: number = 0o744
```

**字符串**  
文本数据类型 string ，可使用双引号 `"` 或单引号 `'` 表示
``` TypeScript
let name: string = "John"
name = "Smith"
```
也可以使用模板字符串，可定义多行文本和内嵌表达式，用反引号 \` 表示，以 `${expr}` 形式嵌入表达式
``` TypeScript
let name: string = `John`
let age: number = 37
let sentence: string = ` Hello, my name is ${name}.

I'll be ${age + 1} years old next month.`
```

**数组**  
有两种方式可以定义数组，一是在元素类型后面接上 `[]` 表示由此类型元素组成的一个数组, 一是使用数组泛型 `Array<元素类型>`
``` TypeScript
let list: number[] = [1, 2, 3]
let list: Array<number> = [1, 2, 3]
```

**元组 Tuple**  
元组类型允许表示一个已知元素数量和类型的数组，个元素的类型不必相同。
``` TypeScript
let x: [string, number]
x = ['hello', 10]
x = [10, 'hello'] // Error
```
当访问一个已知索引的元素，会得到正确的类型，当访问一个越界的元素，会使用联合类型替代(实际测试会报数组越界错误)
``` TypeScript
x[3] = 'world'
console.log(x[5].toString())
x[6] = true
```

**枚举**  
enum 类型是对 JavaScript 标准数据类型的一个补充
可以为一组数值赋予友好的名字
``` TypeScript
enum Color { Red, Green, Blue}
let c: Color = Color.Green // c: 1

// 默认从 0 开始编号，也可以手动指定
enum Color { Red = 1, Green = 2, Blue = 4 }
let c: Color = Color.Green // c: 2

let colorName: string = Color[4]
console.log(colorName) // Blue
```

**Any**  
有时候需要为还不清楚类型的变量指定一个类型，这些变量可能来自用户输入或第三方库，此时不希望类型检查器对这些值检查，而是直接让它们通过编译阶段，此时可用 any 类型 标记这些变量
``` TypeScript
let notSure: any = 4
notSure = 'maybe a string instead'
notSure = false

let list: any[] = [1, true, 'free']
list[1] = 100
```

**Void**  
void 类型像是与 any 类型相反，表示没有任何类型。
通常用于没有返回值的函数
``` TypeScript
function warnUser(): void {
  console.log('This is a warning message.')
}
```
声明一个 void 类型的变量没有什么用，因为只能赋值 undefined 和 null
``` TypeScript
let unuseable: void = undefined
```

**Null 和 Undefined**  
null 和 undefined 都有各自的类型分别是 null 和 undefined  
与 void 类似，这两个类型用处不大
``` TypeScript
let u: undefined = undefined
let n: null = null
```
默认情况下 null 和 undefined 是所有类型的子类型，即可以把 null 和 undefined 赋值给其他类型的变量

可以使用 `--strictNullChecks` 标记，使得 null 和 undefined 只能赋值给 void 和他们各自

**Never**  
never 类型表示那些用不存在的值的类型  
例如 never 类型是那些总是会抛出异常或不会有返回值的函数表达式或箭头函数表达式的返回值类型  
never 类型是任何类型的子类型，也可以赋值给任何类型  
没有类型是 never 的子类型或可以赋值给 never 类型（除了 never 自身），any 也不能赋值给 never
``` TypeScript
// 返回 never 的函数必须存在无法达到的终点
function error(message: string): never {
  throw new Error(message)
}
// 推断返回值类型为 never
function fail() {
  return error('something failed')
}
// 返回 never 的函数必须存在无法达到的终点
function infiniteLoop(): never {
  while(true) {
  }
}
```

**Object**  
object 不是原始类型

**类型断言**  
有时候会遇到一些情况，你比 TypeScript 更了解某个值的详细信息，通过 *类型断言* 可以告诉编译器，开发者直到自己在做什么事  
类型断言没有运行时影响，只在编译阶段起作用  
类型断言有两种形式，一是尖括号语法，一是 as 语法，两者是等价的，看喜好选择。当使用 JSX 时，只允许使用 as
``` TypeScript
// 尖括号语法
let someValue: any = 'This is a string'
let strLength: number = (<string>someValue).length

// as 语法
let someValue: any = 'This is a string'
let strLength: number = (someValue as string).length
```

### 变量声明
**概述**  
let 和 const 是 JavaScript 较新的特性  
TypeScript 是 JavaScript 的超集，因此也可以支持 let 和 const
推荐使用 let 和 const 代替 var 避免出现一些预料外的问题

**var 声明**  
var 的作用域 和变量提升
``` JavaScript
for (var i = 0; i < 10; i++) {
  setTimeout(function() { console.log(i); }, 100 * i);
}
// 打印结果为 10 10 10 10 10 10 10 10 10 10 

for (var i = 0; i < 10; i++) {
  (function(i) {
    setTimeout(function() { console.log(i); }, 100 * i);
  }) (i)
}
// 打印结果为 0 1 2 3 4 5 6 7 8 9

```

来看另一个例子 [原作链接](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)
``` JavaScript
var a = 1
function foo() {
  if (!a) {
    var a = 2
  }
  alert(a)
}
foo() // 打印结果为 2
```
上面例子打印结果并不是预料中的1，在于 JavaScript 语言的一个特写 Hoisting 即变量提升  
先看一个简单例子 `var a = 1`
这条语句做了两件事，声明变量 `var a` 和定义变量 `a = 1`  
因此上面的例子会解析成
``` JavaScript
var a
a = 1
function foo() {
  var a // 关键在这里
  if (!a) {
    a = 2
  }
  alert(a) // 此处的 a 不是函数外的全局变量 a
}
foo()
```
因为函数内部有同名的变量 a，因此在函数内部变量 a 会进行变量提升，但是为什么不是在 if 语句内声明变量 a 呢？  
**因为 JavaScript 没有块级作用域(Block Scoping)，只有函数作用域(Function Scoping)，所以不是一对花括号 {} 就代表是新的作用域。**  
所以解析器在读到 if 语句的时候，发现此处有一个变量声明和赋值，于是解析器会把声明部分提升到 **当前作用域** 的顶部，这个行为就叫做变量提升(Hoisting)


如果想让 `alert(a)` 输出 1 应该怎么做？
`alert(a)` 在执行的时候回去寻找变量 a 的位置，他从当前作用域开始向上一直查找到顶层作用域为止，若是找不到就报 undefined  
因此可以把 var a = 2 再包一层，让它处于下一级的作用域，这样 `alert(a)` 就找不到它了
``` JavaScript
var a = 1
function foo() {
  if (!a) {
    (function () { 
      var a = 2
    }())
  }
  alert(a)
}
foo() // 打印 1
```
这是 IIFE，他会创建一个新的函数作用域，并且该作用域再 foo() 内部，所以 alert 访问不到，不过这个作用域可以访问上层作用域，这就叫闭包

在 JavaScript 中尽量保持作用域内所有变量的声明放置在作用域的顶部，避免 Hoisting 特性带来的困扰，也可以明确告诉所有阅读代码的人在当前作用域内有哪些变量可以访问  

在 JavaScript 中，有四众阿哥方式可以让命名进入到作用域(按优先级)
1. 语言定义的命名  
  如 this 或 arguments，他们在所有作用域内都有效且优先级最高，因此在任何地方你都不能把变量命名为 this 之类的
2. 形式参数  
  函数定义时声明的形式参数会作为变量被 Hoisting 至该函数作用域内。所以形式参数是本地的，不是外部或全局的
3. 函数声明  
  函数体内部还可以声明函数，不过他们也都是本地的了
4. 变量声明  
  这个优先级是最低的，也是最常用的

**Hoisting只提升了命名，没有提升定义**

**函数声明和函数表达式的区别**
``` JavaScript
// 第一个例子
function test() {
  foo()
  function foo() {
    console.log('Hello!')
  }
}
test() // 打印 Hello!

// 第二个例子
function test() {
  foo()
  var foo = function() {
    console.log('Hi!')
  }
}
test() // 不打印且会报错 Uncaught TypeError: foo is not a function
```
函数声明会被提升，函数表达式只会提升命名，定义的函数体则不会，因此报错  

**块作用域**  
当用 let 声明一个变量，它使用的是 *词法作用域* 或 *块作用域*  
块级作用域变量在包含他们的块或 for 循环之外是不能访问的  
块级作用域的变量的另一个特点是，不能再被声明之前读或写。虽然这些变量始终存在于他们的作用域里，但在直到声明它的代码之前的区域都属于 *暂时性死区*，用来说明我们不能再 let 语句之前访问他们  
当 let 声明出现在循环体时，不仅在循环里引入了一个新的变量环境，而是**针对每次迭代都会创建一个新作用域**，类似立即执行函数表达式，在 setTimeOut 例子里我们使用 let 声明即可
``` JavaScript
for (let i = 0; i < 10; i++) {
  setTimeout(function() { console.log(i); }, 100 * i);
}
// 打印结果为 0 1 2 3 4 5 6 7 8 9
```

**const 声明**  
const 声明是声明变量的另一种方式，与 let 有相同的作用域规则，不能重新赋值
``` JavaScript
const num = 9
const kitty = {
  name: 'Rory'
}
num = 3 // Error
kitty.name = 'Cat' // ok
kitty.age = num // ok
```

**let vs const**  
基本原则：所有变量除了计划去修改的，都应该使用 const  

**解构**  
解构数组
``` JavaScript
let input = [1, 2]
let [first, second] = input
// first: 1, second: 2

// 变量交换
[first, second] = [second, first]

// 剩余变量
let [first, ...rest] = [1, 2, 3, 4]
// first: 1, rest: [2, 3, 4]

// 获取部分
let [, second, , fourth] = [1, 2, 3, 4]
// second: 2, fourth: 4
```

对象解构
``` JavaScript
let o = {
  a: 'foo', 
  b: 12,
  c: 'bar'
}
let {a, b} = o  // a: 'foo', b: 12

// 类似数组解构，可以用没有声明的赋值
({a, b} = { a: 'baz', b: 101})

// 可使用 ...语法创建剩余变量
let  {a, ...others} = o
console.log(others) // {b: 12, c: "bar"}
```

属性重命名
``` JavaScript
let { a: newName1, b: newName2 } = o
// 上面相当于
let newName1 = o.a // newName1: 'foo'
let newName2 = o.b // newName2: 12
```
需注意，这里的冒号不是指示类型的，如果想指定类型需要在后面写上完整的模式
``` TypeScript
let {a, b}: {a: string, b: number} = o
```

默认值  
默认值可以让你在属性为 undefined 的时候使用缺省值
``` TypeScript
function keepWholeObject(wholeObject: { a: string, b?: number}) {
  let { a, b = 1001 } = wholeObject
}
```
现在即使 b 为 undefined，keepWholeObject 函数的变量 wholeObject 的属性 a 和 b 都会有值

函数声明  
解构也能用于函数声明
``` TypeScript
type C = { a: string, b?: number}
function f({ a, b }: C): void {
  // ...
}
```
通常情况下是指定默认值，解构默认值有些棘手，首先要在默认值之前设置格式
``` TypeScript
function f({ a, b = 0 } = { a: "" }): void {
  // ...
}
f({ a: "yes" }) // ok
f() // ok 
f({}) // error, 'a' is required if you supply a argument
```
解构表达式混入 重命名、默认值和类型注解 可能让代码变得难以理解，因此要尽量小而简单

展开  
展开操作符和解构相反，允许你将一个数组展开为另一个数组，或将一个对象展开为另一个对象
``` JavaScript
let first = [1, 2]
let second = [3, 4]
let bothPlus = [0, ...first, ...second, 5]
// bothPlus: [0, 1, 2, 3, 4, 5]
```
展开操作创建了 first 和 second 的一份浅拷贝  
``` JavaScript
let defaults = { food: 'spicy', price: '$$', ambiance: 'noisy' }
let search = { ...defaults, food: 'rich'}
// search { food: 'rich', price: '$$', ambiance: 'noisy' } 
```
展开对象后面的属性会覆盖前面的属性  
展开对象仅包含对象 **自身的可枚举属性** ，即会丢失展开对象的方法
``` JavaScript
class C {
  p = 12
  m() {
  }
}
let c = new C()
let clone = { ...c }
clone.p // ok
clone.m() // error!
```

### 接口
TypeScript 的核心原则之一是对值所具有的解构进行类型检查。接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约

#### 接口初探
简单示例接口
``` TypeScript
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label)
}
let myObj = { size: 10, label: 'size 10 object'}
printLabel(myObj)
```
类型查看器会查看 printLabel 的调用，printLabel 有一个参数，并要求这个对象参数有一个名为 label 类型为 string 的属性。我们传入的对象参数可能有多个属性，但编译器只会检查那些必须的属性是否存在，类型是否匹配。  
使用接口的方式重写上面例子
``` TypeScript
interface LabelledValue {
  label: string
}
function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label)
}
let myObj = { size: 10, label: 'size 10 object'}
printLabel(myObj)
```
只要传入的对象满足必要条件，他就是被允许的  
类型检查其不会去检查属性的顺序，只要相应的属性存在且类型正确就可以  

#### 可选属性
接口里的属性不全都是必须的
``` TypeScript
interface SquareConfig {
  color?: string
  width?: number
}
function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = { color: 'white', area: 100}
  if (config.color) {
    newSquare.color = config.color
  }
  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}
let mySquare = createSquare({color: 'black'})
```
可选属性即在属性名字后加一个 ? 符号  
可选属性的好处一是对可能存在的属性进行预定义，二是可以捕获引用了不存在的属性时的错误  

#### 只读属性
一些对象属性只能在对香港创建的时候修改其值，可以在属性名前用 readonly 来指定只读属性  
``` TypeScript
interface Point {
  readonly x: number
  readonly y: number
}
// 可通过复制一个对象字面量来构造一个 Point
let p1: Point = { x: 10, y: 20}
p1.x = 5 // error!
```

**readonly vs const**  
最简单判断改用 readonly 还是 const 的方法是把它作为变量使用还是作为一个属性  
作为变量使用的话用 const，作为属性使用则用 readonly  

#### 额外的属性检查
``` TypeScript
interface SquareConfig {
  color?: string
  width?: number
  [propName: string]: any
}
```
此时 SquareConfig 可以有任意数量的属性，只要不是 color 和 width，就无所谓他们的类型是什么  

#### 函数类型
接口能描述 JavaScript 对象拥有的各种外形，除了描述带有属性的普通对象外，也可以描述函数类型  
为了使用接口表示函数类型，需要给接口定义一个调用签名，就像是一个只有参数列表和返回值类型的函数定义。  
参数列表里每个参数都需要名字和类型  
``` TypeScript
interface SearchFunc {
  (source: string, subString: string): boolean
}
// 创建函数类型变量，并将一个同类型函数赋值给该变量
let mySearch: SearchFunc
// 函数参数名可以不和接口定义的名字匹配
mySearch = function(src: string, sub: string) {
  let result = source.search(sub)
  return result > -1
}
```
函数的参数会逐个检查，要求对应位置上的参数类型是兼容的  

#### 可索引的类型
可索引类型具有一个索引签名，她描述了对象索引的类型，还有相应的索引返回值类型  
``` TypeScript
interface StringArray {
  [index: number]: string
}
let myArray: StringArray
myArray = ['Bob', 'Fred']
let myStr: string = myArray[0]
```

TypeScript 支持两种索引签名：字符串和数字。  
可以同时使用两种类型的索引，但数字索引的返回值必须是字符串索引返回值类型的子类型。  
即是说用 `100 (number)` 去索引等同于使用 `"100" (string)` 去索引，因此两者需要保持一致  
``` TypeScript
class Animal {
  name: string
}
class Dog extends Animal {
  breed: string
}
// 错误:使用数值型的字符串索引，有时会得到完全不同的Animal
interface NotOkay {
  [x: number]: Animal
  [x: string]: Dog
}
```

#### 类类型
TypeScript可用它来明确的强制一个类去符合某种契约
``` TypeScript
interface ColockInterface {
  currentTime: Date
}
class Clock implements ClockInterface {
  currentTime: Date;
  constructor(h: number, m: number) { }
}
```
可以在接口中描述一个方法，在类中实现
``` TypeScript
interface ClockInterface {
  currentTime: Date
  setTime(d: Date)
}
class Clock implements ClockInterface {
  currentTime: Date
  setTime(d: Date) {
    this.currentTime = d
  }
  constructor(h: number, m: number) { }
}
```

**类静态部分和实例部分**  
类是具有两个类型的，静态部分的类型和实例的类型  
当用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误  
``` TypeScript
interface ClockConstructor {
  new (hour: number, minute: number)
}
class Clock implements ClockConstructor {
  currentTime: Date
  constructor(h: number, m: number)
}
```
因为此处当一个类实现一个接口时，只对其实例部分进行类型检查。  
Constructor存在于类的静态部分，所以不在检查范围内。  
因此应该直接操作类的静态部分。  
下例中，我们定义两个接口，ClockConstructor为构造函数所用，ClockInterface为实例方法所用。  
为了方便再定义一个构造函数 createClock，他用传入的类型创建实例
``` TypeScript
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface
}
interface ClockInterface {
  tick()
}
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute)
}
class DigitalClock implements ClockInterface {
  constructor (h: number, m: number) {}
  tick() {
    console.log('beep...')
  }
}
class AnalogClock implements ClockInterface {
  constructor (h: number, m: number) {}
  tick() {
    console.log('tick...')
  }
}
let digital = createClock(DigitalClock, 12, 17)
let analog = createClock(AnalogClock, 6, 30)
```

#### 继承接口
和类一样，接口也可以相互继承。这方便我们从一个接口复制成员到另一个接口
``` TypeScript
interface Shape {
  color: string
}
interface Square extends Shape {
  sideLength: number
}
let square = <Square>{}
square.color = 'blue'
square.sideLength = 10
```
一个接口可以继承多个接口，创建出多个接口的合成接口
``` TypeScript
interface Shape {
  color: string
}
interface PenStroke {
  penWidth: number
}
interface Square extends Shape, PenStroke {
  sideLength: number
}
let square = <Square>{}
square.color = 'blue'
square.sideLength = 10
square.penWidth = 5.0
```

#### 混合类型
一个对象可以同时作为函数和对象使用，并带有额外属性  
``` TypeScript
interface Counter {
  (start: number): string
  interval: number
  reset(): void
}
function getCounter(): Counter {
  let counter = <Counter>function (start: number) {}
  counter.interval = 123
  counter.reset = function () {}
  return counter
}
let c = getCounter()
c(10)
c.reset()
c.interval = 5.0
```

#### 接口继承类
当接口继承类时，他会继承类的成员(包括 private 和 protected 成员)但不包括其实现。  
当创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现(implement)  
``` TypeScript
class Control {
  private state: any
}
interface SelectableControl extends Control {
  select(): void
}
class Button extends Control implements SelectableControl {
  select() {}
}
class TextBox extends Control {
  select() { }
}

// 错误 Image 类型缺少 state 属性
class Image implements SelectableControl {
  select () {}
}
```

### 类
传统的JavaScript 程序使用函数和基于原型的继承来创建可重用的组件，但从 ECMAScript 2015 开始，JavaScript 程序员能够使用基于类的面向对象的方式

``` TypeScript
class Greeter {
  greeting: string
  constructor(message: string) {
    this.greeting = message
  }
  greet() {
    return 'Hello, ' + this.greeting
  }
}
let greeter = new Greeter('world')
```

### 函数
### 泛型
### 枚举
### 类型推论
### 类型兼容性
### 高级类型
### Symbols
### 迭代器和生成器
### 模块
### 命名空间
### 命名空间和模块
### 模块解析
### 声明合并
### JSX
### 装饰器
### Mixins
### 三斜线指令
### JavaScript文件类型检查




## JSX
## TypeScript错误提示
## TIPs
## TypeScript编译原理
## TypeScript FAQs