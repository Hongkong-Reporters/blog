## build a simple database

### 一、设计结构

#### 1）`Tokenizer`

拆分语法

[解析引擎 :: ShardingSphere (apache.org)](https://shardingsphere.apache.org/document/current/cn/features/sharding/principle/parse/)

涉及到AST（抽象语法树）

词法分析：https://developer.aliyun.com/article/71979

不采用纯手写，使用第三方工具：

+ `antlr`：自动生成语法树的开源语法分析器，可以适用 Java、C、C++ 等多语言。

+ `yacc/lex`：参考 [`Github`](https://github.com/westes/flex/)

#### 2）Parser

解析语法

prepare_statement()

美团使用SQL解析：https://zhuanlan.zhihu.com/p/37093463



#### 3）Code Generator

execute_statement()



#### 4）存储结构

内存型：Map

MongoDB：JSON

MySQL：B-Tree



#### 5）Pager

disk的分页机制，可以参考MySQL



#### 6）OS和disk



### 二、设计的难题

+ 如何拆分？空格分隔、分号结束

+ 解析

+ data如何存到disk

  MySQL采取两个文件存储：一个存放表结构；一个存放表数据

  数据分页、数据检查（check）

+ log日志

+ 事物提交和回滚

+ 集群



### 三、思路

+ `Tokenizer` 和 `AST` 设计难度偏大，不如采取类似 `elastic search` 的形式，采取 `RestFul` + `JSON` 的形式，这样不要考虑词法和解析，而且 `GET`，`POST`，`DELETE`，`UPDATE` 和数据库的增删改查操作正好对应。
+ 借鉴 `MongoDB` 



### 四、数据库规则

#### 4.1、数据库和表操作

```js
db.list()					// 获取所有database
db.create('demo', {})		// 创建database，第二个参数为config (定义编码等)
db.tableList()				// 获取所有table
db.createTable('demo', {})  // 创建table，第二个参数为config (定义编码等)
db.use('test')				// 使用test数据库
```



#### 4.2、数据操作

```js
db.table('test').select({})							// 查询
db.table('test').insertOne({})						// 添加一条
db.table('test').insertMany([])						// 批量添加
db.table('test').updateOne({}, {$set:{}})			// 修改一条
db.table('test').updateMany({}, {$set:{}})			// 批量修改
db.table('test').deleteOne({})						// 删除一条
db.table('test').deleteMany({})						// 删除全部
```



### 五、数据库结构（参考 `MongoDB`）

数据 —— disk和缓存

线程一：接收 `JavaScript` 解析的语句，执行操作，操作缓存

线程二：将缓存的数据生成 `journal`，保存在硬盘，频率 `100ms` 一次

线程三：将日志数据同步到硬盘数据区，频率 `60s` 一次

