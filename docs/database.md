## build a simple database

### 一、设计结构

#### 1）`Tokenizer`

拆分语法

[解析引擎 :: ShardingSphere (apache.org)](https://shardingsphere.apache.org/document/current/cn/features/sharding/principle/parse/)

涉及到AST（抽象语法树）

`antlr`：自动生成语法树的开源语法分析器，可以适用 Java、C、C++ 等多语言。



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
