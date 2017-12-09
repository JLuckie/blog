---
layout: post
title: 《ORACLE PL SQL入门到精通——学习笔记》
date:   2017-11-14
categories: "database"
tags: [database, oracle, pl/sql, note]
author: LiangJie
comment: false
---

# 第1章 Oracle 11g数据库系统
## 关系型数据库系统介绍
### 关系型数据库
* **关系型数据库**：使用关系或者二维表储存信息
* **Excel**：平面文件数据模型
* 不同的二维表，使用主键/外键关联，就是**关系型数据模型**
### 数据库系统范式
* **第一范式**：字段必须具有单一属性特性，不可再拆分
* **第二范式**：表要具有唯一性的主键列
* **第三范式**：表中的字段不能包含在其他表中已经出现的非主键字段
### 关系型数据库管理系统
* **DBMS**：关系型数据库管理系统
* **SQL**(Structured Query Language)：结构化查询语言
* SQL语言分为如下两大类：
    * **DML**：数据操纵语言，增删改查
    * **DDL**：数据定义语言，创建/修改表、视图、存储过程、用户等
    * *DCL*：数据控制语言
## PL/SQL
* **PL/SQL**(Procedural Language/SQL)：为标准SQL语言添加过程化功能的程序设计语言
* 最基本的是语句块，块与块可嵌套，可以在块中包含多条SQL及PL/SQL语句，网络上只需要发送一次块，可以完成所有数据库里，减小网络开销
* PL/SQL块被PL/SQL引擎提取，将其中SQL语句交给SQL引擎处理，两种引擎在数据库内部完成数据交互和处理过程
* PL/SQL引擎包括编译器和运行系统，服务端和客户端都包含PL/SQL引擎
### 服务端的PL/SQL引擎
* 大多数使用PL/SQL的数据库应用，都使用了服务端的引擎
* 由服务端的PL/SQL和SQL引擎分别处理
### 客户端的PL/SQL引擎
* 比较少见，语句块中过程性语句就在客户端执行
* SQL语句或者对服务端存储过程调用，将在服务端执行
### 使用SQL*Plus
* SQL使用`;`结尾，或者按Enter后，使用`/`
* PL/SQL语句，输入DECLEAR或BEGIN时，会检测到开始PL/SQL语句块，通过`/`结束语句
* 实际`/`并不是语句终结符，只是SQL*Plus中RUN命令简记符号
* **替换变量**：SQL*Plus中&或者&&使用替换变量，提升交互性
* **DMBS_OUTPUT包显示信息**：
    * `SET SERVEROUT[PUT] {ON | OFF}`
    * 打开后，可以使用DMBS_OUTPUT.PUT/PUT_LINE向输出缓冲区添加参数

# 第2章 PL/SQL基本概念
* PL/SQL具有第三代语言(3GL)的特性，诸如C/Java/C++
* 在PL/SQL代码中只能**直接**使用DML语句，**直接**使用DDL语句会提示绑定错误，需要使用`EXECUTE IMMEDIATE`执行
* PL/SQL通过提供对象类型类支持面向对象的设计
### PL/SQL块结构
* 匿名块：没有名称的PL/SQL块，可以内嵌到程序或SQL*Plus中直接执行，不会保存到数据库中
* 命名块：具有名称的块，一般保存在服务器端数据库中以备调用
    * 块名称标示的块，多级嵌套使用命名区分
    * 函数或过程，保存在数据库中
    * 触发器，某个事件触发后要执行的语句块
### 块的组成
* 定义部分(**DECLARE**可选)：常量、变量、游标、异常、复杂数据类型等
* 执行部分(**BEGIN**必须)：PL/SQL语句和SQL语句
* 异常处理部分(**EXCEPTION**可选)：捕捉可能出现的运行错误，编写出错后的代码
* 结束(**END**必须)
# 第3章 变量和类型
## 变量
### 定义
* 变量可以是PL/SQL类型或者SQL语言类型
* 定义位置：PL/SQL块、子程序和包中
* variable_name [CONSTANT] type [NOT NULL] [:=value];
    * CONSTANT表示声明为常量
    * NOT NUULL表示不能为空
    * := value 用户赋值，与Pascal语言一样，DEFAULT和前者可以互换使用
* 默认变量被初始化为NULL，直接对NULL值进行运算，依然是NULL
* %TYPE
    * V_EMPNO EMP.EMPNO%TYPE; 使用%TYPE声明基于已有的变量/数据库列类型，通过SELECT INTO将结果写入变量中(返回多行TOO_MANY_ROWS,无数据NO_DATA_FOUND)
    * 当表中的字段类型发生变化，变量类型会自动发生变化
    * 已有变量，NOT NULL会被映射；数据库列，NOT NULL不会
* %ROWTYPE
    * 绑定一整行的所有类型，SELECT INTO直接将一整列数据插入变量中
    * 可以直接为rowtype的变量赋值，然后全部插入数据库，values v_emp;
    * rowtype也可以定义游标类型的变量
### 作用域和可见性
* outer.xxx，访问外层变量
* 本地变量高于全局变量
### 数据类型
* PL/SQL是静态类型化(强类型化)的语言
* 4大类：
    * 标量类型：单个值。字符、数字、布尔、日期
    * 复合类型：具有内部子组件的类型，可以包含多个标量类型。记录、嵌套表、索引表、变长数组
    * 引用类型：指向不同储存位置的指针。REF CURSOR和REF
    * LOB类型：大对象类型，用来处理二进制和大于4G的字符串
* PL/SQL使用了所有SQL类型，同时包含布尔和从SQL派生的子类型
![Imgur](https://i.imgur.com/u9BJUsF.png)
#### 字符类型
* 大型字符串(通常大于32KB的字符/二进制)，提供了CLOB/NCLOB数据类型，向下兼容提供LONE类型。LOB可以存储128TB的信息量
![Imgur](https://i.imgur.com/VgYwDZN.png)
* CHAR：
    * CHAR[(max_size [CHAR | BYTE])]
    * 长度默认为1，PL/SQL中char最大为32767个字符，但ORACLE数据库中为2000字
    * CHAR还是BYTE，使用NLS_LENGTH_SEMANTICS初始化
* VARCHAR2
    * 必须指定最大长度，1~32767
    * VARCHAR2(max_size [CHAR | BYTE])
    * 数据库中最大为4000字节
* LONG和LONG ROW
    * LONG最大长度比VARCHAR2少7个字节
    * LONG ROW存储二进制和字节
    * ORACLE这两个类型最大2GB
* ROWID和UROWID
    * 每个数据表都有名为ROWID的伪列，每一行数据的地址二进制
    * SELECT ROWIDTOCHAR(ROWID) FROM EMP;转换为字符串显示
    * 物理ROWID普通数据表一行信息；逻辑ROWID索引组织表中一行信息
    * ROWID只能存储物理内容，UROWID(通用ROWID)可以存储物理、逻辑、非ORACLE的ROWID
    * ROWIDTOCHAR、CHARTOROWID函数
* NCHAR和NVARCHAR2
    * 是CHAR和VARCHAR2的Unicode版本
    * CHAR到NCHAR的转换是安全的，但是相反会造成数据丢失
#### 数字类型
![Imgur](https://i.imgur.com/By1LYMh.png) 
 
* NUMBER既可以表示正数，也可以表示浮点数
* NUMBER[(precision, scale)]
    * 精度(precision)：总长度，最大38
    * 刻度(scale)：正数，小数点开始右边个数；负数，小数点开始左边个数，-84~127。如果值超过范围，四舍五入
* 如果不指定，默认使用最大
* NUMBER类型的几个子类型，与ANSI/IOS或IBM类型兼容
    * DEC、DECIMAL、NUMBERIC具有38位最大精度固定刻度的数值
    * DOUBLE、PERCISION、FLOAT最大精度有126位二进制，REAL 63位二进制
    * INTEGER、INT、SMALLINT最大精度38位整数型
* PLS_INTEGER和BINARY_INTEGER具有相同取值范围，-2147483647~+2147483647，与NUMBER赖少需要更少空间存储数据，计算方面比NUMBER更高
* 使用2的补码格式进行计算，性能更好
#### 日期和时间类型
* DATE
    * 世纪、年、月、日、小时、分钟和秒，但是不包含秒的小数部分，每部分一个字节，即7个字节，使用TO_DATE和TO_CHAR内置函数转换
    * 公元前4712/1/1到公元9999/12/31，默认格式NLS_DATA_FORMAT
    * 直接对日期加减运算，返回两个日期之间相关天数
* TIMESTAMP
    * 可以存储秒的小数部分
    * TIMESTAMP[(P)]，P小数部分精度，默认6
* TIMESTAMP WITH TIME ZONE
    * 包含时区偏移，TIMESTAMP[(precision)] WITH TIME ZONE
    * precision，NLS_TIMESTAMP_TZ_FORMAT
    * 输出实例：`timestamp结果 +08:00`
* TIMESTAMP WITH LOCAL TIME ZONE
    * 使用数据库的时区
* INTERVAL
    * 存储两个时间戳之间的间隔
    * INTERVAL YEAR TO MONTH和INTERVAL DAY TO SECOND
#### 布尔类型
* oracle数据库不包含布尔类型，多数情况使用CHAR(1)代替布尔值
* 不能往数据库插入或检索布尔类型
* 可以存储TRUE、False、NULL
#### LOB对象类型
* 包含BFILE、BLOB、CLOB和NCLOB，最大存储4GB非结构数据
* 与LONG不同：可作为对象类型的属性、最大支持4GB、支持随机访问
* BFILE：数据库外操作系统文件中大型二进制，存储着文件定位器，指向服务器上文件
* BLOB：数据库内部存储二进制，大小不超过4GB，参数事务处理，可以复制和恢复，一般使用DBMS_LOB来提交和回滚事物
* CLOB：数据库中存储大型字符型数据，支持定长和边长字符集，不超过4GB
* NCLOB:存储大型NCHAR类型，可以参与事务处理，可以被恢复和复制
#### 引用类型
* 引用类型和C语言中指针概念相同
* 引用类型在声明过程中没有分配内存，指向某个变量才指向该内存区
* REF CURSOR
    * 游标变量，定义一个SYS_REFCURSOR类型变量，从过程或函数中获取一个记录集，SYS_REFCURSOR是弱类型的引用类型
* REF：指向对象类型实例的指针
#### 用户自定义子类型
* 在标准类型基础上进行进一步约束创建的新类型
* 子类型总有一个派生的基类型
* SUBTYPE subtype_name IS base_type [(constraint)] [NOT NULL]
* 还可以使用%TYPE或%ROWTYPE来指定类型，但此时不继承NOT NULL
* 优势：
    * 检查数组越界：为NUMBER定义子类型
    * 未约束的子类型可以和基类型交互使用
    * 基类型相同，子类型可以相互使用
#### 数据类型转换
* 显示转换：使用内置函数进行转换
* 隐式转换：如果可能，PL/SQL自动转换
## 运算符和表达式 
### 运算符类型
* 赋值运算符
    * variable:=expression;
    * PL/SQL中一个左值仅能有一个右值，val1:=val2:=0，非法的
* 连接运算符
    * ||，通常用于连接两个字符串
    * 会忽略NULL值
* 逻辑运算符
    * AND OR NOT
* 比较运算符
![Imgur](https://i.imgur.com/0FCCLiJ.png)

# 第4章 PL/SQL控制语句
## 分支控制语句
### IF-THEN-ELSE
* IF语句：仅允许单组选择，条件成立语句块1，条件不成立语句块2
* ELSIF语句：条件1不成立，判断ELSIF中的代码
```
IF condition
THEN
    ...
ELSIF condition
THEN
    ...
ELSE
    ...
END IF;
```
### CASE语句
* Oracle 9i提供CASE，与SWITCH类似
* 简单CASE语句
    ```
    CASE selector
    WHEN condition THEN
    ...
    WHEN condition THEN
    ...
    ELSE
    ...
    END CASE;
    ```
    * CASE关键字后是选择器变量或函数，可以返回任何PL/SQL类型但不能是布尔
    * 如果CASE语句中不指定ELSE语句，会触发ORA-6592异常
* 搜索CASE语句:仅能用于布尔型，选择器被隐含设置为TRUE，也可以显式设置为FALSE
```
CASE [{True | False}]
    WHEN [condition] THEN
...
    WHEN [condition] THEN
...
    ELSE
...
END CASE; 
```  
## 循环控制语句
### LOOP循环
```
LOOP
    ...
    IF condition THEN
        EXIT;
    END IF;
END LOOP;
```
* 除了使用条件+EXIT退出，还可以EXIT WHEN condition；
* CONTINUE，跳到语句开头执行下一次循环，也可以CONTINUE WHEN;
* 属于**出口值守循环**
### WHILE-LOOP循环
```
WHILE codition LOOP
...
END LOOP;
```
* 属于**入口值守循环**
### FOR-LOOP循环
```
FOR index IN [REVERSE] low .. high
LOOP
...
END LOOP;
```
* FOR循环中，不用显式声明index，能引用，但不能赋值
* 使用了REVERSE，从高到底
* 边界值除了可以是数组，还可以是变量/表达式
### 循环语句使用建议
* 不要再FOR和WHILE中使用EXIT/EXIT WHEN
* 在循环内部避免使用RETURN或GOTO语句
## 顺序控制语句
### GOTO语句和标签
* 无条件跳转语句，可以跳转到某个标签
* `GOTO label_name`
* label创建，`<<label name>>`
### NULL语句
* NULL显式指明此处代码不执行任何操作，只是提高程序可读性的占位符
# 第5章 管理数据表
## 创建表
* 一般使用ER图来构造实体关系模型
* 数据表又成为二维数据的集合
### 数据定义语言DDL
* **DDL**：Data Definition Language
* 主要由CREATE、ALTER、DROP3个语句组成
#### CREATE TABLE语句
* 想在其它方案中创建表，可以“方案名.表名称”
* 标准命名规则
    * 标明和列明必须是具有描述性名称的字符串，1~30字符
    * 只能包含：字母、数字、下划线、$和#(不建议使用)
    * 表明和列名不能与同一Oracle服务器用户其它对象重名
    * 标明和列明不能使Oracle服务器的保留字
* 列类型属性基本约束
    * NOT NULL：不接受NULL值
    * UNIQUE：储存在列中的每个值必须唯一
    * DEFAULT xxx：指定列的默认值
### 创建表副本
* AS SELECT语句，从现有表创建新的表
* `CREATE TABLE <newtable>` AS SELECT {* | comumn(s)} FROM <oldtable> [WHERE <condition>];
* 注意：
    * 不能够复制约束条件和默认值
    * 不能指定表空间，默认采用当前用户默认表空间
    * 包含大型数据类型(LOB、Long等)不能创建成功
## 创建约束
* **约束**，一个或多个为了保证数据完整性的机制，是数据库服务器强制用户必须遵从的业务逻辑
* 最常使用的约束：
    * 非空约束
    * 唯一约束
    * 检查约束：满足特定要求，比如大于0等
    * 主键约束：SQL 92建议在定义表时定义主键，其实就是唯一约束+非空约束
    * 外键约束：表间关联的约束，关系型数据库的精髓
### 创建主键约束
* 列类型后使用PRIMARY KEY指定列类型为主键，会根据这个列自动创建一个索引
* 如果没有为约束指定名称，将使用SYS_Cn格式自动生成名称
* 建议方法是在列或表级别使用CONSTRAINT关键字，指定约束名：`id NUMBER CONSTRAINT id_pk PRIMARY KEY`
* 可以在表级别使用CONSTRAINT设置约束：
```
CREATE TABLE table_name 
(
    id NUMBER,
    name VARCHAR2(100),
    CONSTRAINT id_pk PRIMARY KEY (id),
    CONSTRAINT name_un UNIQUE (name) 
)
```
* 上述代码好处是可以多列，例如`CONSTRAINT id_name_un UNIQUE (id, name)`
* 规则：
    * 主键应该是对用户没有意义的，应该是一些具有唯一的标识，比如自增长的数字
    * 主键应该是单列的，符合主键通常导致不良的外键，尽量避免
    * 主键应该是不能被更新的
    * 主键不应该包含动态更新的数据，比如时间戳
    * 主键最好由计算机自动生成，在Oracle中可以使用序列来为主键生成值
### 创建外键约束
* 外键约束又称为引用约束，也可以在列级别和表级别创建，使用关键字REFERENCES定义
* `[CONSTRAINT name] REFERENCES table_name (column_name) [ON DELETE {CASCADE | SET NULL}]`,ON DELETE指示是否级联删除，默认使用级联删除
* 引用的表键必须是唯一性键值，一般建议使用关联表的主键作为关联字段
### 创建检查约束
* 允许指定一个布尔表达式，记录值被存储到列前进行检查
* 如果为False或NULL，将产生异常，通常用于单列，或者同一行多列
* 列级别的检查约束只能一次一个字段，表级别的可以多个字段
* `CONSTRAINT name` CHECK (condition)
* 在约束中可以使用各种逻辑运算以及标准SQL函数来计算布尔值结果，例如BETWEEN、IN、IS NULL等
### 查看表约束
* Oracle将用户创建的表、约束等信息都放在数据字典表中，允许查询数据字典表或试图来获取
* 数据字典表根据前缀分4类：
    * users：用户创建对象对应的，例如user_objects、user_tables等
    * all：用户所能访问的对象，例如all_objects、all_tables等
    * dba：所有对象对应的，例如all_objects、all_tables等
    * v$：描述系统性能相关的，例如v$version数据库版本信息
* 约束信息的数据字典：
    * user_constrain
    * user_cons_columns
## 修改表
### 修改表列
* 新增表列
    * `ALTER TABLE table_name ADD col_name VARCHAR2(20);`
    * 会添加到最右侧
    * 还可以添加列属性，比如NOT NULL、UNIQUE、CHECK属性
* 修改表列
    * 小心使用，Oracle并不允许可能会导致数据丢失的任何更改
    * `ALTER TABLE table_name DROP CONSTRAINT xxx`
    * `ALTER TABLE table_name MODIFY col_name xxx`
* 删除表列
    * `ALTER TABLE table_name DROP COLUMN xxx`
    * 将删除字段，无论是否有值
* 重命名表列
    * `ALTER TABLE table_name RENAME COLUMN XXX TO XXX`
## 修改约束
```
ALTER TABLE table_name {
    ADD CONSTRAINT name xxx [DISABLE] |
    DROP CONSTRAINT name |
    ENABLE [NOVALIDATE] name |
    DISABLE name
}
```
## 移除数据表
* DROP TABLE可以移除表数据、表结构及约束
## 索引
* 定义主键或唯一索引时，会自动在相应字段创建唯一索引，用户也可以手动创建非唯一索引
### 索引简介
* 索引是建立在数据库表中的一列或多列用来加速访问表中数据的辅助对象
* 优点：
    * 可以大大加快检索数据的速度
    * 使用唯一索引可以保证数据库表中每一行数据的唯一性
    * 可以加快表与表之间的连接
    * 在使用分组和排序子句进行检索时，可以显著减少时间
* 缺点：
    * 占用额外的物理空间
    * 修改时，需要动态维护，降低数据维护的速度
* 可以通过all_indexes表获知索引基本信息：`SELECT * FROM all_indexes WHERE TABLE_NAME = 'xxx'`
### 索引原理
* ROWID伪劣，唯一标识一条记录所在物理地址，固定而且唯一的
* 只有在表发生移动或表空间变化等物理位置变化时，才会改变
* Oracle在创建索引时，会进行全表扫描，获取每条记录了索引列的数据，进行升序排序，同时获取ROWID值，一起存储到索引段中，格式是(索引列值， ROWID)，组合称为**索引条目**
### 创建索引
* 创建方式：
    * 自动创建：主键约束或唯一约束时，Oracle不推荐认为创建唯一索引
    * 手动创建：用户在其他列创建非唯一索引
* 类型：
    * 单列索引
    * 复合索引
    * B树索引：Oracle默认使用的索引
    * 位图索引：每个取值创建一个位图，每行使用1位
    * 函数索引：索引的取值不直接来自列，来自包含有列的函数表达式
### 修改索引
* 重命名索引：`ALTER INDEX name RENAME TO xxx`
* 合并和重建索引
    * 表在使用一段时间后，频繁操作产生的碎片，可以清理碎片
    * 合并索引不改变索引无力阻止，简单讲碎片合并在一起`ALTER INDEX name COALESCE`
    * 重建索引删除原来的索引，创建新的`ALTER INDEX name REBUILD`
* 分配和释放索引空间
    * 分配空间：`ALLOCATE EXTENT (SIZE xxxK)`
    * 释放空间：`DEALLOCATE UNUSED`
### 删除索引
* `DROP INDEX`,需要具备删除的系统权限
* 如果是删除其它用户的，需要DROP ANY INDEX系统权限
* 以下情况需要删除索引：
    * 不再需要时，应该删除释放空间
    * 没有经常使用，只是极少数查询
    * 索引中包含损坏的数据块或碎片过多，重建索引
    * 数据被移动后导致索引无效，应删除重建
    * 使用SQL*Loader装载大量数据时，为了加快速度，先删除，装载后重建
## 使用视图
### 视图简介
* 视图本身不包含任何数据，通过SELECT语句使用一个或多个表中的数据创建逻辑表，可以看成一个**虚表**，或只是一个“储存的查询”
* 优点：
    * 限制数据的访问，能够选择性显示表中的数据
    * 构成简单的查询取回复杂的查询的结果
    * 对特别的用户和应用程序提供数据独立性
    * 提供用户组，按照他们的特殊标准访问数据
### 创建视图
```
CREATE [OR REPLACE] [FORCE | NOFORCE] VIEW name [(alias[, alias]...)]
AS query
[WITH CHECK OPTION [CONSTRAIN name]]
[WITH READ ONLY [CONSTRAIN name]];
```
* 视图按照是否涉及DML操作，分两类
    * 简单视图：仅来自一个表，不包含函数或数据分组，可以通过视图来执行DML操作
    * 复杂视图：来自多个表，可以包含函数或分组，不总是可以通过视图进行DML
* OR REPLACE：如果已经存在，重新创建它
* FORCE：不管基表是否存在
* NOFORCE：只在基表存在情况下创建(默认值)
* alias：为由试图查询选择的表达式指定名字
* WITH CHECK OPTION：视图中只有可访问的行才能被插入或修改
* CONSTRAINT：为CHECK OPTION约束指定的名字
* WITH READ ONLY：确保该视图中没有DML操作被执行
### 修改视图
* 视图只是续表，对视图更改不会影响底层的基础表
* 视图具有WITH CHECK OPTION选项，重定义没有使用，则自动删除
* 更改视图后，依赖该视图的所有视图和PL/SQL程序将INVALID状态
* 更新基础表后，视图会失效，可以对视图进行重新编译后生效
### 删除视图
`DROP VIEW xxx`
# 第6章 查询数据表
## 简单查询
```
SELECT {[alias.]column | expression | [alias.]* [, ...]} FROM [schema.]table [alias];

```
* 使用DISTINCT查询唯一列数据，`SELECT DISTINCT job FROM emp`
* 实际使用BETWEEN-AND操作符时，Oracle会转换为一对AND条件，并没有限制提高性能
* LIKE条件，%表示0到多个字符，_表示一个字符
* 优先规则：
![Imgur](https://i.imgur.com/Pshwx1L.png)
* ORDER BY 必须是SELECT语句的最后一个子句
```
SELECT * FROM table
[WHERE condition(s)]
[ORDER BY {column, expr} [ASC | DESC]];
```
* 排序规则：
    * 日期类型：较早的日期在前面
    * 字符类型：依字母顺序显示
    * 控制：升序在最后面，降序在最前面
    * 可以对多个列的数据进行排序，并且可以对不在SELECT字段列表中的列进行排序
    * 除了指定列，还可以根据SELECT字段列表索引顺序，指定第几个字段排序
* 使用函数：
    * 可以在SELECT、WHERE、ORDER BY子句中使用函数，也可以在函数中嵌套函数
* 统计函数：
    * 分组函数对行的集合进行操作，对每组给出一个结果
    * SUM、AVG、MIN、MAX、COUNT值的数目、COUNT(*)结果记录数
    * 每个函数都接受DISTINCT或者默认值ALL加字段名称
    * 所有组函数忽略空值，为了用一个值替代空值，请使用NVL、NVL2、COALESE替换空值为具体值
    1. 记录统计：
        * 返回满足SELECT语句标准的行数，包括重复和空值列的行
        * COUNT(*)只统计条数，字段不重要。COUNT(某个字段)，记录非空个数
    2. 汇总和平均值：SUM结果与字段数据具有相同基本数据类型，但可能有更大精度范围
    3. 最小值最大值：MAX、MIN可以用户任意的类型
    4. 统计函数的NULL值处理，所有统计函数都会忽略空值
* 分组统计
    * 可选GROUP BY子句，配合统计函数
    * 除了作为分组函数参数的列不用包含在GROUP BY子句中，其它任何SELECT列表中的列都必须包括
    * 如果使用了单独的列和统计函数，必须知道你跟一个GROUP BY子句进行分组，例如`SELECT deptno, SUM(sal) FROM emp;`
* HAVING子句
    * 约束要分组的数据，使用WHERE子句
    * 如果对分组的结果进一步过滤，可以使用HAVING子句
* ROWNUM伪列
    * ROWNUM伪列是Oracle先查到结果之后再加上去的，添加一个从1开始的序列号
    * ROWNUM是动态的，如果条件为ROWNUM > 10，产生一条为1的记录，不匹配，被过滤，之后生成的依然为1，因此得不到任何结果
    * 因此要提取记录中间的记录，必须使用子查询
## 复杂查询
### 多表连接查询
* 在Oracle中，有两种类型的连接格式，分别是ASCI SQL连接格式和Oracle特有格式，见识使用符合ANSI标准的连接格式
#### 内连接
* 关联两个表中相等的列，成为等值连接，也成为简单连接或**内连接**
* `SELECT x.empno, y.dname FROM emp x, dept y WHERE x.deptno = y.deptno AND x.deptno = 20`
* ANSI SQL表示：`SELECT x.empno, y.dname FROM emp x INNER JOIN dept y ON x.deptno = y.deptno WHERE x.deptno = 20`,使用INNER JOIN连接，ON子句表达连接条件，WHERE指定条件子句
#### 外连接
* 内连接缺陷：如果emp表中存在deptno为NULL的记录，则不会出现在连接列表中
* 可以使用外连接语法来保留连接坐标或者右边的数据
* 保存左侧表内容的**左外连接**
* 保存右侧表格内容的**右外连接**
* 使用(+)表示外连接查询时，返回(+)另一边表中的所有数据，不能左右两侧都放置(+)号
* ANSI SQL中，FULL OUTER JOIN表示**全连接**，除了包含连接的数据外，还包含表中都不符合连接条件的数据：`SELECT table1.col, table2,col FROM table1 [LEFT|RIGHT|FULL] OUTER JOIN table2 ON table1.col = table2.col;`
#### 交叉连接
* 用A表记录行数与B表记录行数相乘得到笛卡尔积，如果连接查询时不指定任何连接条件，将产生交叉查询
* ANSI SQL：`SELECT x.empno, y.dname FROM emp x CROSS JOIN dept y;`
#### 自然连接
* 两个表中由于相同名字和数据类型的列，用**自然连接**自动匹配数据类型和列明
* 使用NATURAL JOIN关键字
* `SELECT x.empno, y.dname FROM emp x NATURAL JOIN dept y`
### 使用子查询
* 查询必须要基于另一查询的结果才能完成数据的提取，构造方式称为**子查询**
* 两类：
    * 相关子查询：子查询的执行依赖于外部查询的数据，外部查询执行一次，子查询执行一次
    * 非相关子查询：独立于外部子查询的子查询，总共执行一次，将值传递给外部查询，比相关子查询效率高
* 基本原则：
    * 子查询放圆括号中
    * 子查询放在比较条件的右边
    * 单行子查询中用单行运算符，多行子查询用多行运算符
#### 非相关子查询
* 细分3类：
    * 单行单列子查询：标量子查询，通常与比较运算符联合使用
    * 多行单列子查询：不允许与比较运算符组合运算，必须使用特定关键字如ANY何ALL将外层查询单个值与子查询多行进行比较
    * 多列子查询：通常用在UPDATE语句中
* IN关键字表示主查询中的列只在子查询的多行列表中存在，则返回该行数据
* ANY:
    * <ANY小于最大值
    * >ANY大于最小值
    * =ANY等同于IN
* ALL：
    * <ALL小于最小值
    * >ALL大于最大值
* ANY具有同义词SOME，在使用ANY/SOME时，通常使用DISTINCT防止返回被多次选择的行，提升查询性能
#### 相关子查询
* `SELECT e1.empno, e1.ename, e1.deptno FROM emp e1 WHERE e1.sal > (SELECT AVG(sal) FROM emp e2 WHERE e2.deptno = e1.deptno) ORDER BY e1.deptno;`
### 表集合操作
* 通过结合运算，将查询结果的一个或多个部分结合到一个结果中，包含集合运算的查询成为复合查询
#### 联合与全联合运算
* 主要用户合并两个表中的数据，联合不包括重复值，全联合包含重复值
* 原则：
    * 被选择的列数和列的类型必须与所有用在查询中的SELECT语句一致，列的类型可以不相同
    * 重复检查的时候不忽略空值
    * 默认输出以SELECT子句第一列的升序排序
    * IN运算比UNION优先级高
    * 在所有被选择的列上进行
* UNION ALL不消除重复行，默认不输出排序，不能使用DISTINCT关键字
#### 相交运算
* INTERSECT返回多个查询中相同的行
* 原则：
    * 列数和数据类型必须一样，列名可以不同
    * 相交的表倒序排序不改变结果
    * 相交不忽略空值
#### 相减运算
* 去除重复的值，MINUS语句，在第一个表不在第二个表中
* 可以在一个查询中使用多个集合运算符，可以用圆括号改变执行顺序，ORDER BY只能出现在语句最后
### 层次化查询
* Oracle提供对于ANSI SQL扩展简化树状层次结构遍历
* 提供3个构造：
    * START WITH ... CONNECT BY 子句
    * PRIOR操作符
    * LEVEL伪列
* START WITH condition1：指定层次结构中的根节点，所有满足条件的都考虑为根节点
* CONNECT BY condition2：指定层次结构中父子行关系，必须包含PRIOR操作符，用来指定列来自父行，不能包含子查询
# 第7章 操纵数据表
//TODO 增删改
## 使用序列
* Oracle序列是一种数据库对象，主要工作是用来为表产生唯一值
* 被创建后，可以通过数据字典找到，因此可以被多个对象共享
* 序列独立于表被储存和生成，相同序列可以被多个表使用
### 创建数据序列
```
CREATE SEQUENCE name [INCREMENT BY n] --步长，默认为1，负值则递减
[START WITH n] --初始值，默认1
[{MAXVALUE n | NOMAXVALUE}] --最大值，NOMAXVALUE默认，10^27，递减最大-1
[{MINVALUE n | NOMINVALUE}] --最小值，NOMINVALUE默认，1，递减10^26
[{CYCEL | NOCYCEL}] --达到限制值是否循环
[{CACHE n | NOCACHE}] --内存块大小，默认20，有利于改善性能
```
### NEXTVAL和CURRVAL伪列
* 必须用序列名限定，name.NEXTVAL
* 使用CURRVAL之前，必须先NEXTVAL初始化一次
### 修改序列
* 不能改变序列起始值
* 最小值不能大于序列当前值
* 最大值不能小于序列当前值
* 规则不影响以前的值，值有未来序列值受影响
* 必须具有ALTER SEQUENCE权限
//TODO 同义词

# 第8章 记录与集合
* 记录与集合是Oracle提供的两种复合类型
## 记录类型
* 记录类型从Oracle7中被引入，类似于C语言中的结构，记录可以一次性处理多个类型的值
* 记录类型类似于表的一行
### 定义记录类型
* 记录只是组织其他标量类型的容器，本身没有值
* 必须先定义一个标量类型的类型，然后声明这种类型的变量。比声明标量类型多了一个步骤
```
TYPE name IS RECORD
(
    field_declaration
    [,field_declaration]
    ...
)
```
* field_declaration是除了REF CURSOR意外任何类型，还可以是%TYPE或%ROWTYPE
* 可以对记录中的成员进行初始化，例如:=/DEFAULT，也可以指定NOT NULL约束
### 记录类型赋值
* 简单赋值：
    * `record_name.field_name := expression;`
* 记录类型赋值：
    * 相同记录类型的变量，可以互相赋值
    * 不同的记录类型的变量，赋值会出错，即使成员相同
    * 记录成员与表字段相同，%ROWTYPE可以赋值成功
* 使用SELECT或FETCH语句赋值
    * SELECT INTO record_name xxx
    * FETCH INTO record_name xxx
### 操纵记录类型
* 在Oracle 9i之前的版本中，是不能使用记录插入或更新的，只能将记录每个成员赋值给相应的列
* INSERT语句中，列的个数、顺序与类型必须要与表中的个数、顺序、类型完全匹配
* UPDATE语句，同上。用关键字ROW来代表完整的一行数据：`UPDATE table_name SET ROW = record_name WHERE ...`。
* RETURNING子句中使用记录，可以在DML语句中包含该子句，返回被UPDATE、DELETE、INSERT操作影响到的行。`RETURNING  xxx, yyy, zzz INTO record_name`
* 记录变量不允许出现在SELECT列表、WHERE子句、GOURP BY子句、ORDER BY子句中
### 使用嵌套记录
* 嵌套记录指记录成员也为记录类型
## 理解集合类型
* 如果记录是单行多列数据结构，集合就是单列多行的数据结构
* 类似于高级语言中的列表或数组，存储具有相同类型的元素的有序集合，每个元素都有唯一下标
* 3种类型的集合：
    * 索引表：也称关联数组，类似于其它语言的哈希表，仅在PL/SQL中使用的数据结构
    * 嵌套表：可以容纳任一个数的元素，可以把嵌套表存储到数据库中，能通过SQL语句进行操作
    * 边长数组：保存固定数量的元素，可以运行时改变容量，不如嵌套表灵活
* 索引表只能在PL/SQL使用，需要在内存中保存和维护，优先使用索引表，与高级语言数组非常相似
* 如果需要存储到数据库，在嵌套表/变长数组间选择。嵌套表是索引表扩充，添加了额外的方法
* 默认情况，只能定义以为集合，可以通过集合中包含集合模拟多维集合
### 定义索引表
* 索引表由键值对组成，键是唯一识别的标识符，可以是整数和字符串
* 索引表不需要进行初始化，没有构造语法，不需要分配初始空间，不需要动态扩展容量
* 使用数字作为下标时，可以为正数、负数、0，可以不连续
* `TYPE name AS TABLE OF type [NOT NULL] INDEX BY [PLS_INTEGER | BINARY_INTEGER | VARCHAR2(size)]`
* 定义了索引表类型后，需要声明变量才能操作
* 使用VARCHAR2作为索引键时，必须指定大小
### 操纵索引表
* `index_val (index) := value;`
* 如果没有赋值，就不会为元素分配内存
### 定义嵌套表
* 使用嵌套表必须使用其构造语法对嵌套表进行初始化
* 没有INDEX BY子句，因为必须使用有序的关键字创建，不能为负数
* `TYPE name AS TABLE OF type [NOT NULL]`
* `val_name table_name := table_name ('value1', 'value2'[, value3...])`
### 操纵嵌套表
* 可以使用IS NULL来判断嵌套表是否被构造
* 使用table_name()，不传入参数，未分配元素并不表示元素已存在，为了插入元素，必须先使用嵌套表EXTEND方法扩充指定元素个数，可以使用NULL代替元素值(NULL, NULL, NULL, ...)
### 数据库中的嵌套表
* 为了嵌套表在数据表中使用，必须保存到数据字典中，需要使用`CREATE TYPE`语句创建一个持久的嵌套表类型
* `CREATE OR REPLACE TYPE name AS TABLE OF type [NOT NULL]`
* `CREATA TABLE table_name(name 嵌套表类型) NESTED TABLE  col_name STORE AS table_name`
### 定义变长数组
* 变长数组会具有一个上界值，下标1开始，不能为负数
* `TYPE name IS {VARRAY | VARYING ARRAY} (size_limit) OF type [NOT NULL]`
* 未初始化之前，本身为NULL，直接访问触发异常
* 需要构造语法进行初始化
### 选择集合类型
* 嵌套表与索引表：PL/SQL表
    * 嵌套表自索引表扩展而来，包含索引表所有属性
    * 都是使用下标对集合中元素访问
    * 具有相同结构
    * 索引表在每次调用时自动构建，保存容量不固定信息，长度大小可变，不能为NULL
    * 嵌套表可以EXTEND/TRIM方法进行操作
    * PL/SQL会自动在主机数组和索引表间转换，嵌套表不能
* 嵌套表与变长数组：可以保存在数据库中
    * 都使用下表符号对单个元素访问，必须使用构造函数初始化
    * 变长数组一旦声明，元素数目固定，嵌套表没有明确上限
    * 存储到数据库，变长数组保持了元素排序和下标，嵌套表不同
* 只需要PL/SQL使用，元素个数少，优先索引表
* 存储在数据库中，选择嵌套表，元素固定，优先变长数组
## 使用集合方法
* `集合变量名.集合方法名[(参数)];`
* EXISTS:判断集合中置顶元素是否存在
* COUNT：返回集合中包含元素的个数。变长数组，COUNT与LAST值恒等，嵌套表如果删除元素，COUNT小于LAST   
* LIMIT：返回集合元素的最大个数，嵌套表/索引表，个数没限制，返回NULL
* FIRST和LAST：返回集合第一个和最后一个元素(最小和最大)的索引数字
* PRIOR和NEXT：特定索引值参数的前一个索引值和后一个索引值
* EXTEND：为嵌套表和变长数组扩展元素，不能用于索引表
* TRIM：从嵌套表和变长数组尾端删除元素
* DELETE方法：从索引表和嵌套表中删除一个或多个元素
### 使用批量绑定
* 循环频繁的发送SQL回大大降低效率
* 可以通过集合批量绑定，一次发送所有SQL
* 使用FORALL，`FORALL index IN low .. upuper sql_statement`
```
FORALL i IN depts.FIRST .. depts.LAST
    DELETE FROM emp WHERE deptno = depts(i);
```
### 使用BULK COLLECT
* 相反，BULK COLLECT可以批量从SQL引擎中接收数据到集合
* `SELECT xxx  BULK COLLECT INTO collection_name[, collection_name]...`
# 第9章 SQL内置函数
> 除了分组与分析函数不能在PL/SQL中使用，大多数可以直接使用 
* 进行数据计算，例如MOD计算余数
* 修改单个数据项，例如ROUND对数字进行四舍五入
* 操纵数据进行数据行分组，例如SUM、AVG查询分组
* 格式化显示，例如TO_CHAR格式化显示日期
* 转换数据类型，TO_DATA字符类型日期转为日期型
## 基本函数
* 单行函数：单个值进行计算，返回一个结果
* 多行函数：操纵成组的行
* 大小写转换：UPPER、LOWER、INITCAP(每个单词第一个字母大写)
* 字符串处理：CONCAT(连接)、SUBSTR(截取位置长度)、LENGTH(长度)、INSTR(字符位置)、LPAD(左填充)、RPAD、TRIM(去除头尾字符)
* 字符串代替：REPLACE(string,searchstr,[replacestr])没有replacestr匹配的都删除、TRANSLATE(string,fromstr,tostr)tostr替换所有fromstr，tostr不能缺少，不能空白，Oracle会把空白理解为NULL，造成结果为NULL
* 数字型函数：ROUND四舍五入、TRUNK截断、MOD余数
* 日期时间函数：MONTHS_BETWEEN之间月数、ADD_MOUNTHS添加、NEXT_DAY指定多少天、LAST_DAY月最后一天日期、ROUND四舍五入、TRUNC截断
* 类型转换：TO_CHAR转字符串、TO_DATE转日期、TO_NUMBER转数字
* 分组函数：AVG平均值、COUNT数目、SUM求和、MIN最小、MAX最大
* 其它函数：NVL NULL转实际值、NVL2不为NULL返回表达式2为、NULL表达式3、NULLIF相等返回NUULL不相等返回表达式、COALESEC返回第一个非NULL表达式、DECODE特定条件IF-THEN-ELSE
## Oracle分析函数
//TODO

# 第10章 使用游标
## 游标的基本结构
### 游标简介
* 一个指向查询语句返回结果的指针，定义游标时，将包含一个查询定义
* 游标打开时，数据被接收到一块内存区域，直到游标关闭
* 游标指向的内存区域，，位于进程全局区内部，称为上下文区域(Context Area)，保存3类信息：
    * 查询返回的数据行
    * 查询说处理的数据行号
    * 指向共享池中已分析的SQL语句
* 游标定义时并不会获取数据，只有被打开后，相关的查询语句被执行
* 过程：
    * 声明游标并为游标关联SELECT语句
    * 执行SELECT语句打开游标
    * 将游标结果放到PL/SQL变量中
    * 关闭游标
### 游标分类
* 显式游标：使用CURSOR语句定义，需要打开并提取
* 隐式游标：Oracle为每一个不属于显示的SQL DML语句都创建隐式游标，没有名称，可以叫SQL游标，不能显示操作
* Oracle允许通过关键字SQL来访问游标属性
```
BEGIN
  UPDATE emp set comm = comm * 1.12 where empno = 7369;
  DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' 行被更新');
  IF SQL%NOTFOUND THEN
    DMBS_OUTPUT.PUT_LINE('不能更新!');
  END IF;
  COMMIT;
EXCEPTION
  WHEN OTHERS THEN
    DMBS_OUTPUT.OUT_LINE(SQLERRM);
END;
```
* 在Oracle中，INSERT、UPDATE、DELETE、SELECT INTO都会隐式创建游标
* 通过%FOUND、%ISOPEN、%NOTFOUND、%ROWCOUNT来访问游标相关属性
### 定义游标类型
```
CURSOR name [param_list]
[RETURN return_type]
IS query
[FOR UPDATE [OF (column_list)] [NOWAIT]];
```
* param_list，指定一个或多个可选的参数，用户查询执行
* return_type，必须是记录或者数据表的行类型
* FOR UPDATE，打开期间锁定游标记录，对其它用户来说是只读模式
* 游标名是一个标识符而不是变量名，因此不能把值赋给游标名或在表达式中使用，但和变量有同样的作用域规则
* 游标查询中使用的变量，必须在游标声明之前声明
* 游标定义指定的参数，必须是IN模式，不能给游标参数添加NOT NULL约束
* 定义游标时蒂尼了参数，打开游标可以传入
```
DECLARE
  CURSOR emp_cursor(p_deptno IN NUMBER) RETURN emp%ROWTYPE IS
    SELECT * FROM EMP WHERE deptno = p_deptno;
BEGIN
  OPEN emp_cursor(20);
  CLOSE emp_cursor;
END;
/
```
### 打开游标
* 如果定义时使用了FOR UPDATE，打开时会锁定游标记录
* `OPEN cursor_name [(param_values)]`
* 将会发生几个动作：
    * 检验绑定变量的值
    * 基于查询语句确定游标的活动集
    * 游标指针指向游标活动集的第一行
### 使用游标属性
* %ISOPEN：游标变量是否打开，打开返回True
* %FOUND：调用FETCH语句之前，会产生NULL值
* %NOTFOUND：FETCH之前，属性值为NULL
* %ROWCOUNT：到目前为止已经从由表中取出的记录行数，游标被打开时，为0，每获取一条加1
### 提取游标数据
* `FETCH name INTO val_name(s) | PL/SQL_record;`
* 必须注意变量类型要与表中字段类型一致
### 批量提取游标数据
* 提取只能向前，如果要提取已提取过的数据，只有重新打开
* 使用`BULK COLLECT`可以一次性将由表中结果保存到集合中
* //TODO 先学习集合
### 关闭游标
* 关闭游标释放所有相关资源
* CLOSE关闭一个未打开的游标，将触发异常
## 操纵游标数据
* LOOP循环：循环中FETCH后及时检查，EXIT WHEN
* WHILE循环：打开游标后FETCH一次，循环中再次FATCH
* FOR循环：PL/SQL特殊处理，使用隐式的变量处理游标FOR循环。不需要OPEN、FETCH和CLOSE
```
DECLARE
  CURSOR dept_cursor IS
    SELECT * FROM dept;
BEGIN
  FOR dept_row IN dept_cursor LOOP
  END LOOP;
END;
/
-- 更简洁的写法：
BEGIN
  FOR dept_row IN (SELECT * FROM dept) LOOP
  END LOOP;
END;
/
```
### 修改游标数据
#### FOR UPDATE子句
* 相当于给结果集的行加了一个互斥锁，行级锁定
* OF (column_list)选定要锁表的列，NOWAIT当有别的会话锁定相同列时，不等待直接返回
#### WHERE CURRENT OF子句
* 使用时一定要有FOR UPDATE子句，并且游标打开并且至少返回一行
* COMMIT一定要放到循环语句后面，否则游标更新或删除失败
## 游标变量
### 游标变量简介
* 可以把静态游标与游标变量想象为常量与变量的区别
* 游标变量类似于高级语言中的指针，指向内存地址而不是具体的内容
* PL/SQL中，指针是使用REF作为前缀定义的，游标变量REF CURSOR
```
DECLARE
  TYPE emp_type IS REF CURSOR RETURN emp%ROWTYPE;
  emp_cur emp_type;
  emp_row emp%ROWTYPE;
BEGIN
  OPEN emp_cur FOR
    SELECT * FROM emp;
  LOOP
    FETCH emp_cur
      INTO emp_row;
    EXIT WHEN emp_cur%NOTFOUND;
  END LOOP;
END;

```
* 使用OPEN FOR动态指定一个SELECT子句
### 声明游标类型变量
* 创建一个游标变量类型
* 基于该类型创建实际的游标变量
* 指定了RETURN子句后，游标变量是受约束的，要求游标必须具有特定返回类型
### 打开游标类型变量
* 打开游标时，必须与RETURN type一致
* 如果打开游标时，游标已经指向一个游标对象，并不会创建新对象，会重用，绑定一个新的查询
### 处理游标变量异常
### 在包中使用游标变量
//TODO
### 游标变量的限制
* 不能在包中声明游标变量
* 不能再创建表/视图的语句中，把字段类型指定为REF CURSOR类型，数据库字段不能存放游标变量类型
* 游标类型参数不支持使用远程调用传递到另一个服务器
* 不能用比较操作符来判断游标是否相等、不等或NULL
* 不能为游标变量赋空值
* 不能将REF CURSOR类型作为集合的元素类型
* 不能将在由表中使用的FOR循环用在游标变量上，游标和游标变量不能互相替换
# 第11章 事务处理和锁定
## 事务处理
* 事务是一个由多条SQL语句组成的工作逻辑单元
* 事务满足ACID属性：
    * 原子性：要么都执行，要么都不执行
    * 一致性：数据保持抑制状态，所有数据都要发生更改
    * 隔离性：两个事务互不干扰，一个事物不能看到其他事务数据
    * 持久性：事务提交后，数据变化永久保留
* 不需要显式开始一个事物，遇到第一个DML语句时，事物开始，以下条件事务结束：
    * COMMIT或ROLLBACK
    * 退出Oracle工具时
    * 机器失效或系统崩溃时
* 事务管理语句
    * COMMIT：保存上次COMMIT或ROLLBACK所有改变，释放所有锁
    * ROLLBACK：回滚上次所有改变，释放锁
    * ROLLBACK TO SAVEPOINT：回滚至一个已经保存的店，释放该范围锁
    * SET TRANSECTION：允许开始一个只读或读写会话，建立一个隔离级别，或将当前事务赋给特定回滚段
    * LOCK TABLE：特定模式锁定整个数据表，覆盖默认行级锁定
### COMMIT提交事务
* 会结束数据库事务
    * 如果数据库使用了DML语句进行更改，会被永久写入数据库中，其它用户可以立即看到所做更改
    * 事务上的所有锁及事务所占有一切资源(游标、内存等)自动被释放
* `COMMIT [WORK] [COMMENT text]`
* WORK仅为增强可读性，没有任何其它作用
* COMMENT分布式事务添加注释，出现网路或机器故障，会在数据字典保存文本内容和相关事务ID，不超过50字符
### ROLLBACK回滚事务
* 使用ROLLBACK语句情形：
    * 误删了数据或者更改了错误的数据，帮助恢复原始的数据
    * 出发了异常或者SQL执行失败不能完成事务，回滚能将数据恢复到初始状态，便于再次执行
* `ROLLBACK [WORK] [TO [SAVEPOTIN] savapotint_name]`
* WORK增强可读性
* TO指定保存点，不指定任何参数，回滚所有更改
### 使用SAVEPOINT保存点
* PL/SQL提供语句级别的回滚，将大的事务分成很多语句级的小块
* ROLLBACK TO SAVEPOINT后，发生：
    * 保存点之后所有工作撤销，保存点未被释放，可再次使用
    * 保存点后锁和资源都被释放
    * 并不是结束整个事物，SQL语句处于挂起
### SET TRANSACTION设置事物属性
* `SET TRANSACTION param`
* READ ONLY:只读事物，任何INSERT、DELETE、UPDATE、SELECT FOR UPUDATE都属于非法操作，不能使用制定回滚段
* READ WRITE：没有只读事物的限制
* ISOLATION LEVEL：隔离级别，可设置`SERIALIZABLE`对已修改但没有提交的数据对象DML事务失败，`READ COMMITTED`对已修改但没提交的数据库对象DML事务修改时，会等待前面DML锁消失，默认特性
* USE ROLLBACK SEGMENT：给事务定义一个合适的回滚段，`SET TRANSACTION IOSLATION USE ROLLBACK SEGMENT name;`
## 使用锁定
* 表锁定：对整个表实行数据锁定，确保当前数据可以访问，用户保护整张表的数据
* 记录锁定：行锁定，总是以独占方式进行，一个事物结束之前，其它事务要等待该事务结束
### 记录锁定
* Oracle隐式实现记录锁定，INSERT、DELETE、SELECT FOR UPDATE将进行记录锁定，又成为互斥锁/排它锁，记录添加这种锁，总以独占方式运行，其它事务只有等待直到锁定释放
* 如果使用NOWAIT，Oracle马上回拒绝返回ORA-0054异常
### 表锁定
* Oracle默认不会进行表锁定，但可以使用LOCK TABLE把整张表以指定模式锁定，可以共享或拒绝对这些表的访问
* 常见的几种表锁定模式：
    * ROW SHARE：行共享锁。最小限制的锁定，锁表同时允许别的事务并发对表进行SELECT、INSERT、UPDATE、DELETE、LOCK TABLE操作，不允许任何事务对同一个表进行独占写访问
    * ROW EXCLUSIVE：行排它锁，一个表多条记录被更新时，也允许对同一个表执行SELECT、INSERT、UPDATE、DELETE、LOCK TABLE操作，它不能防止别的事务对同一个表的手工锁定或独占式读写
    * SHARE LOCK：共享锁，只允许别的事务查询或锁定特定记录，防止任何事务对同一个表插入、修改、删除操作
    * SHARE ROW EXCLUSIVE：共享排它锁，查看整个表，允许别的事务查看表中记录，不允许别的事务以共享模式锁定表或更新表记录，一般只允许用于SELECT FOR UPDATE语句中
    * EXCLUSIVE：排它锁，独占方式写一个表，允许别的用户读取和查询，但不允许任何INSERT、UPDATE、DELETE工作
* 如果没有显式指定锁定，自动隐式锁定，一般不需要对锁进行惊喜控制，特殊需要才手工控制
### 使用LOCK TABLE
* `LOCK TABLE table_list IN lock_mode MODE [NOWAIT]`
* 还可以对视图进行锁定，实际是对视图所组成的基础表进行锁定

# 第12章 异常处理机制
## 理解异常处理
* Oracle错误分两类：
    * 编译时的错误
    * 运行时的错误，需要异常处理机制来处理
### 异常处理的语法
* 定义区：定义异常，异常类型EXCEPTION，如果使用预定义，不用自定义
* 执行器，可以显式触发，也可以用PL/SQL引擎触发，RAISE xxx
* 出现异常，后续语句立即停止执行，跳转到异常处理区, `WHEN xxx THEN ... WHEN OTHERS THEN xxx`
### 预定义异常
* 每个错误都有一个序号，SQLCODE就是异常的编码，SQLERRM获取异常的信息
* PL/SQL异常处理时，不能直接使用异常编码，必须使用一个名字
## 自定义异常
* 异常的声明，类型为EXCEPTION
* 异常是一种错误表示形式，不是一个真正的变量，不能再赋值语句或SQL语句中使用，作用范围规则相同
* 同一块中不能声明一个异常超过2次，不同块中可以
* 子块重新声明外部块中异常，将会覆盖
### 使用EXCEPTION_INIT
* 有一些异常并没有异常名称，比如一些ORA-开头的，可以通过EXCEPTION_INIT编译指示，可以不在预定义范围的异常添加名称
* `PRAGMA EXCEPTION_INIT (exception_name, oracle_error_number)`
### 使用RAISE_APPLICATION_ERROR
* 子程序内部使用，帮助用户从存储子程序中抛出用户自定义的错误消息，避免未捕获异常
* `RAISE_APPLICATION_ERROR(error_number, error_message, [keep_errors])`
* keep_errors为True时，新错误将被添加到已经抛出的错误列表中
### 异常处理
* 可以单个异常处理器处理多个异常，使用OR关键词分隔即可
### 使用SQLCODE和SQLERRM
* 用户自定义异常，SQLCODE总是返回1
* 用户自定义异常，SQLERRM总是返回“User-defined Exception”
* Oracle错误信息最大长度为512个字节
* SQLERRM和SQLCODE都是函数，
* SQLERRM可接受一个附属的单精度参数，返回与该数字相关文本，一般不用
## 异常的传递
* 当异常抛出时，立即跳转到EXCEPTION语句块的异常处理器，查找是否匹配，如果没有找到，会向其外层或子程序的调用方传递，知道没有可搜索到的块为止
* 异常处理部分匹配-->传递到外层异常处理器-->传递到调用环境，例如SQL*PLUS
* 声明时异常传递：语句块声明部分抛出了异常，并不会被当前块所在处理器捕获，会立即向外层传递
* 异常处理器中的异常：异常立即传递到外层的语句块，不管本地是否具有相同的异常处理器
* 重新抛出异常：只需要写一个`RAISE;`即可
### 异常处理准则
* 从异常中恢复：要在异常处理结束后，继续执行之后的逻辑。建议将异常包装到子块中，子块中处理结束后，外层语句可以继续执行
* 获取异常抛出的位置：
    * 定义递增的计数器标示
    * 将每一个SELECT语句定义到一个子块中去
    * 使用DBMS_UTILITY.FORMAT_ERROR_BACKTRACE函数获取错误位置，Oracle 10g以后版本提供
* 异常与事务处理：
    * 抛出一个异常并不会终止事务，除非在异常处理器显式调用ROLLBACK/COMMIT;
    * 如果顶层语句存在未处理异常，异常被传递到调用环境，事务会被服务器自动回滚
    * 如果想异常发生后不放弃事务，重新处理：
        * 事务放在一个子块中
        * 子块放入循环中，重复执行事务
        * 设置SAVEPOINT，如果事务执行成功，就提交事务退出循环，执行失败，回滚保存点，ROLLBACK TO xxx
