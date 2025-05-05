# Introduction

## 1 Database Systems

Database 是一个单位中相关联的数据集，由DBMS(Database Management System)管理

最基本的功能是数据的存和取与管理

数据管理既包括定义信息存储的结构，也包括提供信息操作的机制。

数据库系统必须保证存储数据的安全性

并发访问时应保证有序访问不发生混乱

## 2 Purpose of Database Systems

- Intergrity problems 完整性问题
    - 完整性约束被“埋藏”在程序代码中，而非被显式地表述
    - 难以添加新的约束和修改现存的
- Atomicity problems 原子性问题，问题之间是相互关联的
    - 故障可能会使数据库处于不一致的状态，其中部分更新已执行。
- Concurrent access anomalies  并发访问异常
    - 不受控制的并发访问可能导致不一致
- Security problems 安全性问题
    - 难以向用户提供对一部分但不是全部的数据的访问
    - Authentication 认证
    - privilege 权限
    - Audit 审计

**Characteristics of Databases:**
- data persistence(数据持久性)
- convenience in accessing data(数据访问便利性)
- data integrity （数据完整性）
- concurrency control for multiple user(多用户并发控制)
- failure recovery（故障恢复） 
- security  control（安全控制）

## 3 View of Data
**数据库的三级抽象：**
![](assets/chaper%201%20Introduction/file-20250217111524611.png)

### 3.1 Schema and Instance

- Similar to types and variables in programming languages
- Schema 模式，数据库的逻辑结构
- Instance实例，某一时间点数据库的具体内容

### 3.2 Data Independence

**物理数据独立性：**


**逻辑数据独立性：**



## 4 Data Models

- A collection of tools for describing 
    - Data (数据)
    - Data relationships(联系)
    - Data semantics(语义)
    - Data constraints(约束)
- Relational model(关系模型)
- Entity-Relationship(实体-联系) data model 
- Object-based data models 
    - Object-oriented  (面向对象数据模型)
    - Object-relational(对象-关系模型模型)
- Semistructured data model  (XML)(半结构化数据模型)
- Other older models:
    - Network model  (网状模型)
    - Hierarchical model(层次模型)

## 5 Database Languages

### 5.1 Data Definition Languages DDL

用于定义数据库模式的规范符号

!!! example 
    ![](assets/chaper%201%20Introduction/file-20250217120416486.png)

C 语言里的 struct 经过编译后全部变为了代码（有一张符号表）但数据库里不会。

Data dictionary contains **metadata** (元数据，**i.e.** data about data)

- DDL compiler generates a set of table templates stored in a data dictionary (数据字典)
- Database schema
- Integrity constraints (完整性约束) Primary key (ID uniquely identifies instructors, 主键) Referential integrity (references constraint in SQL, 参照完整性) e.g. dept_name value in any instructor tuple must appear in department relation
- Authorization (权限)

### 5.2 Data Manipulation Language DML

Two classes of languages

- Procedural (过程式)– user specifies what data is required and how to get those data **_e.g._** C
- Declarative (nonprocedural，陈述式，非过程式) – user specifies what data is required without specifying how to get those data **_e.g._** SQL

**SQL** is the most widely used query language

### 5.3 SQL Query Language
![](assets/chaper%201%20Introduction/file-20250217120637471.png)

### 5.4 Database Access from Application Program

- Such computations and actions must be written in a **host language**, such as C/C++, Java or Python.
- Application programs generally access databases through one of * Language extensions to allow embedded SQL **_e.g._** 通过预处理器，将 select 语句识别出来，翻译成 C 语言的函数调用。 
- **API** (Application program interface) **_e.g._** ODBC/JDBC which allow SQL queries to be sent to a database.

### 5.5 Database Design数据库设计

- Entity Relationship Model (实体-联系模型)  
    一对一/一对多/多对一/多对多
    ![](assets/chaper%201%20Introduction/file-20250217121108710.png)
图是中立的，可以用关系数据库/面向对象数据库实现

- Normalization Theory规范化理论
    Formalize what designs are bad , and test for them

!!! example
    ![](assets/chaper%201%20Introduction/file-20250217121353152.png)
    这个表存在冗余，department 有重复，应该拆分为两张表

## 6 Database Engine 数据库引擎
![](assets/chaper%201%20Introduction/file-20250217121523133.png)

一个数据库系统是被分为不同的处理各自职责的模型

一个数据库系统的组件可以被分为：
- The storage manager
- the query processor
- the transaction management component

### 6.1 Storage Manager

为了数据持久化，放在硬盘里，但数据处理要进入内存，这之间存在数据鸿沟。  
按块访问内存 (4k/16k), 缓冲管理。

- File manager
- Buffer manager
- Authorization and intergrity manager
- Transaction manger

数据库里面放的是：
- data files 真正的数据
- data dictionary 放的是原子数据
- statistical data 用于数据库的查询处理
- indices