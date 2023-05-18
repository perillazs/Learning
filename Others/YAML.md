# YAML配置文件

## What
YAML(Ain't a Markup Language) 不是一种标记语言，通常以 .yml 为后缀，是一种直观的能够被电脑识别的数据序列化格式。

## Why
1. YAML易于阅读
2. 在编程语言之间是可移植的
3. 支持单程处理

## 语法
### 1. 约定
* K: v 表示键值对关系，冒号后面必须有一个空格
* 使用空格的缩进表示层级关系，空格数目不重要，只要是左对齐的一列数据，都是同一个层级的
* 大小写敏感
* 缩进不允许使用 Tab 键，只允许使用空格
* 松散表示，对于驼峰命名法可使用原名或使用 - 代替驼峰，如 java 中的 lastName 在 yml 中使用 lastName 或 last-name 都可正确映射

### 2. 键值关系

1. 普通值
   K: v 字面量直接写
   字符串默认不用加上单引号或者双引号
   双引号 "" 不会转义里面的特殊字符，如 "hello \n world" 会输出 hello 换行 world
   单引号 '' 会转义特殊字符，最终展示的是一个普通字符串数据
2. 日期
   > date: 2019/01/01
3. 对象(属性和值)，Map(键值对)
   在下一行来写对象的属性和值的关系
   ``` yml
   people: 
      name: ZhangSan
      age: 20
   ```
   行内写法
   ``` yml
   people: {name: zhangsan, age: 20}
   ```
4. 数组、list、set
   用 `- 值` 表示数组的一个元素
   ``` yml
   pets: 
      - dog
      - pig
      - cat
   ```
   行内写法
   ``` yml
   pets: [dog, pig, cat]
   ```
5. 数组对象、list对象、set对象
   ``` yml
   people: 
      - name: zhangsan
        age: 22
      - name: lisi
        age: 20
      - { name: wangwu, age: 18 }
   ```

### 3. 文档块
对于测试环境、生产环境可以使用不同的配置，如果只想写到一个文件中，yml支持使用 --- 隔开每个块
``` yml
server: 
  port: 8081

spring:
  profiles:
    active: prod # 激活 prod 文档块

---
server: 
  port: 8083
spring: 
  profiles: dev # 指定属于 dev 环境的配置

---
server: 
  port: 8084
spring:
  profile: prod # 指定属于 prod 环境的配置
```