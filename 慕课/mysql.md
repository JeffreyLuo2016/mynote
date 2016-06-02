# 与MySql的零距离接触
<!-- MarkdownTOC -->

- [初涉MySql](#初涉mysql)
	- [修改msyql的命令提示符](#修改msyql的命令提示符)
	- [MySQL常用命令](#mysql常用命令)
	- [MySQL语句的规范](#mysql语句的规范)
	- [创建数据库](#创建数据库)
	- [查看当前服务器下的数据库列表](#查看当前服务器下的数据库列表)
	- [修改数据库](#修改数据库)
	- [删除数据库](#删除数据库)
- [操作数据表](#操作数据表)
	- [创建数据表](#创建数据表)
	- [查看数据表列表](#查看数据表列表)
	- [查看数据表的结构](#查看数据表的结构)
	- [记录的插入与查找](#记录的插入与查找)
	- [空值与非空](#空值与非空)
	- [自动编号](#自动编号)
	- [初涉主键约束\(PRIMARY KEY\)](#初涉主键约束primary-key)
	- [初涉唯一约束](#初涉唯一约束)
	- [默认约束 DEFAULT](#默认约束-default)
- [约束以及修改数据表](#约束以及修改数据表)
	- [外键约束的要求解析](#外键约束的要求解析)
	- [编辑数据库的默认存储引擎](#编辑数据库的默认存储引擎)
	- [外键约束的参照操作](#外键约束的参照操作)
	- [表级约束与列级约束](#表级约束与列级约束)
	- [修改数据表](#修改数据表)
		- [添加单列](#添加单列)
		- [添加多列](#添加多列)
		- [删除列](#删除列)
		- [添加约束](#添加约束)
			- [添加主键约束](#添加主键约束)
			- [添加唯一约束](#添加唯一约束)
			- [添加外键约束](#添加外键约束)
			- [添加/删除默认约束](#添加删除默认约束)
			- [删除主键约束](#删除主键约束)
			- [删除唯一约束](#删除唯一约束)
			- [删除外键约束](#删除外键约束)
		- [修改列定义](#修改列定义)

<!-- /MarkdownTOC -->

## 初涉MySql	
### 修改msyql的命令提示符	
```sql
# mysql -uroot -proot --prompt 提示符
mysql -uroot -proot --prompt \\h  //将以主机名作为命令提示符
```

命令提示符参数说明：	

| 参数 | 描述 |
| ------------- | ------------- |
| \D | 完整的日期 |
| \d | 当前数据库 |
| \h | 服务器名称 |
| \u | 当前用户 |



### MySQL常用命令
- 显示当前服务器版本: `SELECT VERSION();`		
- 显示当前日期时间: `SELECT NOW();`		
- 显示当前用户： `SELECT USER();`	
- 显示当前打开数据库： `SELECT DATABASE()`	

### MySQL语句的规范
- 关键字与函数名称全部大写		
- 数据库名称、表名称、字段名称全部小写		
- SQL语句必须以分号结尾		

### 创建数据库 
- `CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name [DEFAULT] CHARACTER SET [=] charset_name;`	
- `SHOW CREATE DATABASE database_name`查看数据库的创建信息		

### 查看当前服务器下的数据库列表	
- `SHOW {DATABASES | SCHEMAS} [LIKE 'pattern' | WHERE expr];`	

### 修改数据库	
- `ALTER {DATABASE | SCHEMA} [db_name] [DEFAULT] CHARACTER SET [=] charset_name` 	

### 删除数据库 
- `DROP {DATABASE | SCHEMA} [IF EXISTS] db_name;`		

- `SHOW WARNINGS;` 显示数据库的警告信息。		


## 操作数据表
- 登录mysql 		
```shell
mysql -uroot -proot -P3306 -h127.0.0.1 //登录mysql
```

### 创建数据表	

- 语法

		CREATE TABLE [IF NOT EXISTS] table_name(
			column_name data_type,
			...
		) 


- demo		

		CREATE TABLE tb1(
			username VARCHAR(20),
			age TINYINT unsigned,
			salary FLOAT(8,2) unsigned
		);


### 查看数据表列表		

		SHOW TABLES [FROM db_name] [LIKE 'pattern' | WHERE expr]

### 查看数据表的结构	
		
		SHOW COLUMNS FROM tb_name 


### 记录的插入与查找
- 记录插入

		INSERT [INTO] tb1_name [(col_name,...)] VALUES (val,...)

- 记录查找

		SELECT expr, ... FROM tb1_name

### 空值与非空
- NULL, 字段值可以为空；
- NOT NULL, 字段值不能为空。
- demo:
	
		CREATE TABLE tb2(
			username VARCHAR(20) NOT NULL,
			age TINYINT UNSIGNED NULL
		);

### 自动编号
- AUTO_INCREMENT 

### 初涉主键约束(PRIMARY KEY)
- 主键约束
- 每张数据表中只能存在一个主键
- 主键保证记录的唯一性
- 主键自动为NOT NULL

### 初涉唯一约束
- 唯一约束
- 唯一约束可以保证记录的唯一性
- 唯一约束字段可以为空值(NULL)
- 每张数据表可以存在多个唯一约束
- demo 
		
		 CREATE TABLE tb5(
    		id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    		username VARCHAR(20) NOT NULL UNIQUE KEY,
    		age TINYINT UNSIGNED
    	 );


### 默认约束 DEFAULT
- 默认值
- 当插入记录时， 如果没有明确为字段赋值， 则自动赋予默认值。
- demo
		
		CREATE TABLE tb6(
			id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
			username VARCHAR(20) NOT NULL UNIQUE KEY,
			sex ENUM('1', '2', '3') DEFAULT '3'
		);

## 约束以及修改数据表

### 外键约束的要求解析
- **约束**
	- 约束保证数据的完整性和一致性；
	- 约束分为表级约束和列级约束；
	- 约束类型包括
		+ NOT NULL (非空约束)
		+ PRIMARY KEY （主键约束）
		+ UNIQUE KEY (唯一约束)
		+ DEFAULT（默认约束）
		+ foreign key (外键约束)
- **外键约束的要求**
	- 父表和子表必须使用相同的存储引擎，而且禁止使用临时表；
	- 数据表的存储引擎只能为InnoDB。
	- 外键列和参照列必须具有相似的数据类型。 其中数字的长度或是否有符号位必须相同；而字符的长度则可以不相同。
	- 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引。

### 编辑数据库的默认存储引擎
- MysSQL配置文件
		
		default-storage-engine=INNODB

- 查询表中的索引	

		show indexes from tb6;
		show indexes from tb6\G; #加入\G 以网格的形式显示查询出来的数据 表列太多的时候使用。

### 外键约束的参照操作
1. CASCADE: 从父表删除或更新且自动删除或更新子表中匹配的行；
2. SET NULL: 从父表删除或更新行，并设置子表中的外键列为NULL. 如果使用该选项，必须保证子表列没有指定NOT NULL;
3. RESTRICT: 拒绝对父表的删除或更新操作；
4. NO ACTION: 标准SQL的关键字，在MySQL与RESTRICT相同；

demo 创建带外键约束参照的数据表

		CREATE TABLE  user ( 
			id SMALLINT(10) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
		 	username VARCHAR(32), 
		 	pid SMALLINT(10) UNSIGNED, 
		 	FOREIGN KEY (pid) REFERENCES province (pid) ON DELETE CASCADE
		);


### 表级约束与列级约束
- 对一个数据列建立的约束，称为列级约束。
- 对多个数据列建立的约束，称为表级约束。
- 列级约束既可以在列定义时声明，也可以在列定义后声明。
- 表级约束只能在列定义后声明。

### 修改数据表

#### 添加单列

	ALTER TABLE tb1_name ADD [COLUMN] col_name column_definition [FIRST | AFTER col_name]

demo 

	alter table user add age smallint not null default 10; #给user表添加age列
	alter table user add password varchar(20) not null after username; #在user表的username列后添加password列
	alter table user add truename  varchar(20) not null first; #在所有列的前面插入truename列


#### 添加多列

	ALTER TABLE tbl_name ADD [COLUMN] (col_name column_definition,...) #添加多列时只能添加到原有列后面，不能指定位置关系

#### 删除列

	ALTER TABLE tbl_name DROP [COLUMN] column_name

demo:

	 alter table user drop truename; #删除user表的truename列
	 alter table user drop password,drop age; #同时删除password和age列
	 #可以同时对列进行删除和添加，用','隔开

#### 添加约束
##### 添加主键约束

	ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (index_col_name,...)

demo 
	
	alter table user2 add constraint PK_user2_id primary key (id); #将id设置为名称为PK_user2_id的主键

##### 添加唯一约束

	ALTER TABLE tbl_name ADD [CONSTRAINT[SYMBOL]] UNIQUE [INDEX|KEY] [index_name] [index_type] (index_col_name,...)

demo 
	
	alter table user2 add unique (username); #为user2的username添加唯一约束

##### 添加外键约束

	ALTER TABLE tbl_name ADD [CONSTRAINT[symbol]] FOREIGN KEY [index_name](index_col_name,...) reference_difinition

demo 

	alter table user2 add foreign key (pid) references province (pid); #给user2表的pid字段添加外键约束，外键为province表的pid字段。

##### 添加/删除默认约束

	ALTER TABLE tbl_name ALTER	[COLUMN] col_name {SET DEFAULT literal | DROP DEAULT}

demo 
	
	alter table user2 alter age set default 15; #为user2表的age字段添加默认值15
	alter table user2 alter age drop default; #去掉user2表的age字段的默认值

##### 删除主键约束

	ALTER TABLE tbl_name DROP PRIMARY KEY;

demo 	

	alter table user2 drop primary key; #删除user2表的主键

##### 删除唯一约束

	ALTER TABLE tbl_name DROP {INDEX|KEY} index_name

demo:		
	
	show indexes from user2\G; #查看表上的约束
	alter table user2 drop index username; #删除名称为username的唯一约束

##### 删除外键约束

	ALTER TABLE tbl_name DROP FOREIGN KEY fk_symbol

demo:

	alter table user2 drop foreign key user2_ibfk_1; #删除user2表上的名称为'user2_ibfk_1'的外键，外键名称可通过`SHOW CREATE TABLE table_name`查看。
	alter table user2 drop index pid; #删除创建外键约束时自动创建的索引。

#### 修改列定义

	ALTER TABLE tbl_name MODIFY [COLUMN] col_name column_definition [FIRST | AFTER col_name]


