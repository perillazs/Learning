## Java 学习
### 下载安装

下载jdk需要 oracle 账号密码，网上找了一个
账号：1985479344@qq.com
密码：Oracle123

### 环境变量配置
1. 添加 JAVA_HOME D:\Program Files\Java\jdk1.8.0_271  
2. 添加 CLASSPATH %JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar  
3. 修改 PATH 添加 %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin   

### 第一个程序 Hello World
java里字符串需要用双引号 `"`，不能用单引号 `'`  
java语句后必须加上分号 `;`  

源文件名称必须是文件中主类的名称，扩展名必须为 `.java`  
main 方法大小写严格  
修饰符 public static void 缺一不可  
参数：
  1. string[] 表示参数类型为字符串数组
  2. args 参数名称只要符合 java 命名规范即可

编译文件指令
> javac JAVA_FILE  

## 数据类型
### 分类
从存储的角度可以分为两类，一类是基本数据类型，一类是引用数据类型  
||||
|:-|:-|:-|