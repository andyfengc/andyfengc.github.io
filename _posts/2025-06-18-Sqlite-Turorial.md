---
layout: post
title: Sqlite Tutorial
author: Andy Feng
---
# Introduction
**SQLite** 是一个轻量级、嵌入式的开源数据库引擎。

- **零配置**：无需安装服务器或守护进程
    
- **单文件数据库**：所有数据保存在一个 `.sqlite` 或 `.db` 文件中
    
- **跨平台支持**：可运行于 Windows、Linux、macOS、Android、iOS 等
    
- **内置于多种应用和语言中**：如 Python、Android、Firefox、Chrome、Git 等
## 📦使用场景

- 移动应用（如 Android、iOS）
    
- 桌面软件的数据存储（如记事本++ 插件）
    
- 嵌入式系统（如 IoT、车载系统）
    
- 配置文件存储或缓存层
    
- 单用户应用程序
    
- 单元测试中的临时数据库
    

---

## ⚠️ 不适用场景

SQLite 并非万能，**不适合**以下情况：

- 高并发多用户写入
    
- 大型企业级系统或分布式系统
    
- 大量数据分析 / 多表 JOIN 的复杂查询
# Install
download at [sqlite download](https://sqlite.org/download.html)
download full version : sqlite-tools-win-x64-3500100.zip
![](images/posts/Pasted%20image%2020250618184838.png)
查看版本并启动 
sqlite3
sqlite> .help

-- 创建表
CREATE TABLE Users (
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    Name TEXT NOT NULL,
    Age INTEGER
);

-- 插入数据
INSERT INTO Users (Name, Age) VALUES ('Alice', 30);

-- 查询
SELECT * FROM Users WHERE Age > 25;
# SQLite statements
SQLite ANALYZE Statement
```
ANALYZE; 
ANALYZE database_name; 
ANALYZE database_name.table_name;
```
GLOB Clause, 区分大小写
```
SELECT column1, column2....columnN 
FROM table_name 
WHERE column_name GLOB { PATTERN };

匹配模式使用通配符：
- `*`：匹配任意长度的任意字符    
- `?`：匹配单个任意字符    
- `[abc]`：匹配指定集合中任一字符    
- `[a-z]`：匹配字符范围
```
ALTER TABLE
```
ALTER TABLE table_name ADD COLUMN column_def...;
ALTER TABLE table_name RENAME TO new_table_name;
```
Transaction
```
BEGIN; 
or 
BEGIN EXCLUSIVE TRANSACTION;

COMMIT;
```
SAVEPOINT
```
SAVEPOINT savepoint_name;
RELEASE savepoint_name;
```
ROLLBACK
```
ROLLBACK; 
or 
ROLLBACK TO SAVEPOINT savepoint_name;
```
CREATE INDEX
```
CREATE INDEX index_name ON table_name ( column_name COLLATE NOCASE );
CREATE UNIQUE INDEX index_name ON table_name ( column1, column2,...columnN);
DROP INDEX database_name.index_name;
```
REINDEX
```
REINDEX collation_name; 
REINDEX database_name.index_name; 
REINDEX database_name.table_name;
```
CREATE TRIGGER
```
CREATE TRIGGER database_name.trigger_name 
BEFORE INSERT ON table_name FOR EACH ROW 
BEGIN 
stmt1; stmt2; .... 
END;

DROP INDEX database_name.trigger_name;
```
CREATE VIEW
```
CREATE VIEW database_name.view_name AS SELECT statement....;
DROP INDEX database_name.view_name;
```
CREATE VIRTUAL TABLE 
```
CREATE VIRTUAL TABLE database_name.table_name USING weblog( access.log ); 
or 
CREATE VIRTUAL TABLE database_name.table_name USING fts3( );
```
EXISTS
```
SELECT column1, column2....columnN FROM table_name WHERE column_name EXISTS (SELECT * FROM table_name );
```
EXPLAIN Statement
```
EXPLAIN INSERT statement...; 
or
EXPLAIN QUERY PLAN SELECT statement...;
```

PRAGMA statement 
**PRAGMA** 是 SQLite 提供的**数据库内部设置命令**，用于控制数据库引擎的各种底层行为，或获取其内部信息。它是一个特殊的 **命令/语句**，用于查询或设置数据库的运行参数、行为模式、性能选项、安全策略等。
```
PRAGMA pragma_name; 
For example: 
PRAGMA page_size; 
PRAGMA cache_size = 1024; 
PRAGMA table_info(table_name);
```
# Programming
## data type 
sqlite采用类型亲和性系统，他的数据类型可以有很多种，creat table时都能使用，很灵活

| 声明类型示例（可以写任意名字）定义时可以随便使用   | SQLite 归类的类型亲和性 | 底层内部存储类型（Storage Class）      | 说明                             |
| -------------------------- | --------------- | ---------------------------- | ------------------------------ |
| `INTEGER`、`INT`、`BIGINT`   | INTEGER         | INTEGER                      | 任意大小整数，最大 8 字节                 |
| `REAL`、`FLOAT`、`DOUBLE`    | REAL            | REAL                         | 浮点数，IEEE 8 字节                  |
| `TEXT`、`CHAR(n)`、`VARCHAR` | TEXT            | TEXT                         | 字符串（UTF-8 / UTF-16）, up to 1gb |
| `BLOB`、`BYTEA`             | BLOB            | BLOB                         | 二进制大对象, up to 1gb              |
| `NUMERIC`、`DECIMAL`        | NUMERIC         | INTEGER / REAL / TEXT / NULL | 精度优先，会尝试以整数或浮点存储               |
|                            |                 | NULL                         | 空值                             |
 ### boolean
 SQLite **没有专门的 `BOOLEAN` 或 `DATETIME` 类型**，但你可以通过 **类型亲和性（Type Affinity）机制 + 约定方式** 来处理这两种类型。
用 `INTEGER` 来表示布尔值.

|值|说明|
|---|---|
|`0`|FALSE|
|`1`|TRUE|
```
is_active INTEGER NOT NULL DEFAULT 0
```
插入时用 `0`/`1`，或直接用 `FALSE`/`TRUE`（SQLite 会自动转换）：
`INSERT INTO users (is_active) VALUES (TRUE);`
### datetime
SQLite 也没有 `DATETIME` 或 `DATE` 类型，建议使用以下三种方式之一：

| 存储格式  | 类型建议      | 示例                      | 说明                 |
| ----- | --------- | ----------------------- | ------------------ |
| 文本格式  | `TEXT`    | `'2025-06-18 21:05:00'` | ISO 8601 格式，**推荐** |
| 整数时间戳 | `INTEGER` | `1729372800`（UNIX 时间）   | 秒（或毫秒）表示，可排序高效     |
| 浮点数   | `REAL`    | `2459487.5`（Julian）     | 支持更高精度，但不常用        |

# FAQ
## Storage class和Affinity Type有什么区别，跟传统的data type什么关系 
### 传统数据库的数据类型（Data Type）
大多数关系型数据库（如 SQL Server、MySQL、PostgreSQL）都有严格的数据类型系统，每个字段定义时绑定一个具体类型，比如 INT、VARCHAR(50)、DATETIME。
数据库会严格限制该字段只能存储该类型数据，否则报错或转换失败。
### 类型亲和性Type Affinity
SQLite 没有严格的类型系统，而是采用了“类型亲和性”机制：
**类型亲和性**：字段声明的类型名称被映射为五类亲和性之一，分别是：

| 类型亲和性（Affinity） | 说明                | 典型声明示例                     |
| --------------- | ----------------- | -------------------------- |
| INTEGER         | 倾向存储整数            | `INT`, `INTEGER`, `BIGINT` |
| REAL            | 倾向存储浮点数           | `REAL`, `FLOAT`, `DOUBLE`  |
| TEXT            | 倾向存储文本字符串         | `TEXT`, `CHAR`, `VARCHAR`  |
| BLOB            | 无转换，原样存储二进制数据     | `BLOB`或无类型声明               |
| NUMERIC         | 数值类型，尝试存为整数或浮点或文本 | `NUMERIC`, `DECIMAL`       |
当插入数据时，SQLite 会根据字段的类型亲和性**尽量转换数据类型**，但不会严格限制，比如你可以往 `TEXT` 字段插入数字，反之亦然。
### Storage Class（存储类别）
**Storage Class** 是 SQLite 实际在磁盘和内存中存储数据时使用的**物理类型**，只有以下五种：

|Storage Class|说明|
|---|---|
|NULL|空值|
|INTEGER|整数（最多8字节）|
|REAL|浮点数（8字节）|
|TEXT|字符串（UTF-8 或 UTF-16）|
|BLOB|二进制数据，原样存储|
**存储类别是数据实际存储格式**，比如你给一个 TEXT 字段插入整数，SQLite 会根据规则决定是以 INTEGER 还是 TEXT 存储。
### 三者关系总结
|名称|作用|示例|
|---|---|---|
|传统数据类型|强类型定义字段，限制存储的数据类型|`INT`, `VARCHAR(50)`, `DATE`|
|SQLite 类型亲和性|字段声明时的类型指导，影响存储时的转换|声明 `VARCHAR(20)` => `TEXT` 亲和性|
|Storage Class|实际存储数据的物理类型|某行数据实际存为 `INTEGER`|
例如，
```
CREATE TABLE example (
  id INTEGER,       -- 类型亲和性 INTEGER
  name TEXT,        -- 类型亲和性 TEXT
  value NUMERIC     -- 类型亲和性 NUMERIC
);
INSERT INTO example VALUES ('123', 456, 78.9);
```
- `id` 字段：传入 `'123'`，SQLite 尝试转换为 INTEGER，实际存储为 INTEGER 123。    
- `name` 字段：传入数字 `456`，SQLite 转换为字符串 `"456"`，存储为 TEXT。    
- `value` 字段：传入浮点数 `78.9`，NUMERIC 亲和性尝试保留原类型，存储为 REAL。
## `LIKE` vs `GLOB`
SQLite 中为什么同时存在 `LIKE` 和 `GLOB`，它们到底有什么区别？

|特性|`LIKE`|`GLOB`|
|---|---|---|
|**用途**|SQL 标准的模糊匹配|Unix 风格的通配符匹配|
|**是否区分大小写**|❌ 默认**不区分**（可配置）|✅ 始终**区分大小写**|
|**通配符语法**|`%` = 任意长度，`_` = 任意单个字符|`*` = 任意长度，`?` = 任意单个字符|
|**是否跨平台通用**|✅ 是，通用 SQL 语法|❌ 否，仅限 SQLite|
|**转义机制**|支持 `ESCAPE` 字符|❌ 不支持转义|
|**语法扩展性**|可自定义 Collation 和大小写规则|固定实现，不支持扩展|
|**性能差异**|基本相同（简单匹配）|基本相同（都无法走索引）|
1. **通配符语法不同**

|匹配意图|`LIKE` 语法|`GLOB` 语法|
|---|---|---|
|任意字符|`%`|`*`|
|单个任意字符|`_`|`?`|
|字符集合匹配|❌ 不支持|✅ `[abc]`|
|字符范围匹配|❌ 不支持|✅ `[a-z]`|

2. **大小写敏感性不同**

```
-- 假设 name = 'Alice'

SELECT * FROM users WHERE name LIKE 'alice'; -- ✅ 能匹配
SELECT * FROM users WHERE name GLOB 'alice'; -- ❌ 不能匹配（区分大小写）
```
你可以通过设置 PRAGMA 让 LIKE 区分大小写：
```
PRAGMA case_sensitive_like = ON;
```
但 `GLOB` **始终大小写敏感**，不能改变。

3. **转义字符支持**
```
SELECT * FROM files WHERE path LIKE '%\_%' ESCAPE '\';
```
- `LIKE` 支持 `ESCAPE` 字符（可匹配字面上的 `%` 或 `_`）
- `GLOB` 不支持任何转义字符，`*` 和 `?` 总是通配

4. **SQL 标准支持**
- `LIKE` 是 SQL 标准的一部分，几乎所有数据库都支持
- `GLOB` 是 SQLite 独有（模仿 Unix Shell 的 `glob()`），不具备跨数据库兼容性

# Reference
https://www.tutorialspoint.com/sqlite/index.htm

[https://www.sqlitetutorial.net/](https://www.sqlitetutorial.net/)