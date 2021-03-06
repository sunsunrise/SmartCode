<p align="center">
  <img width="100"src="./doc/Logo.png"/>
</p>

# SmartCode([English Document](./README-EN.md))

> SmartCode = IDataSource -> IBuildTask -> IOutput => Build Everything

## Introduction

![SmartCode](./doc/SmartCode.png)

## SmartCode.Db (代码生成器)

### Demo

![SmartCode](./doc/SmartCode-Db.gif)

### Getting Started

1. Install from .NET Core Global Tool  

  ``` shell
  dotnet tool install --global SmartCode.CLI
  ```

2. 编辑构建配置文件（默认：SmartCode.yml）
3. 命令行执行SmartCode命令
    - SmartCode
    - 等待提示输入配置文件路径（可选：默认程序根目录下SmartCode.yml文件）
    - 回车执行命令
4. 等待任务执行结束
5. 查看输出目录结果

### 构建配置文件

``` yml
Module: SmartSql.Starter
Author: Ahoo Wang
DataSource:
  Name: Db
  Paramters:
    DbName: SmartSqlStarterDB
    DbProvider: SqlServer
    ConnectionString: Data Source=.;Initial Catalog=SmartSqlStarterDB;Integrated Security=True
Language: CSharp
TemplateEngine: Razor 
Output: 
  Type: File
  Path: 'E://SmartSql-Starter'

# 构建任务
Build:
  ClearDir:
    Type: Clear
    Paramters:
      Dirs: '.'
  Solution:
    Type: Project
    Template: Sln.cshtml
    Output:
      Path: '.'
      Name: '{{Project.Module}}'
      Extension: '.sln'
  SmartSqlConfig:
    Type: Project
    Template: SqlMapConfig.cshtml
    Output:
      Path: '{{Project.Module}}.API'
      Name: 'SmartSqlMapConfig'
      Extension: '.xml'
  Entity_Project:
    Type: Project
    Template: Proj.cshtml
    Output:
      Path: '{{Project.Module}}.Entity'
      Name: '{{Project.Module}}.Entity'
      Extension: '.csproj'
  Entity:
    Type: Table
    Module: Entity
    Template: Entity.cshtml
    Output:
      Path: '{{Project.Module}}.{{Build.Module}}'
      Extension: '.cs'
    NamingConverter:
      Table:
        Tokenizer:
          Type: Default
          Paramters:
            IgnorePrefix: 'T_'
            Delimiter: '_'
        Converter:
          Type: Default
          Paramters: { }
      View:
        Tokenizer:
          Type: Default
          Paramters:
            IgnorePrefix: 'V_'
            Delimiter: '_'
        Converter:
          Type: Pascal
      Column:
        Tokenizer:
          Type: Default
          Paramters: 
            Delimiter: '_'
        Converter:
          Type: Pascal
  Repository_Project:
    Type: Project
    Template: Proj-Repository.cshtml
    Output:
      Path: '{{Project.Module}}.Repository'
      Name: '{{Project.Module}}.Repository'
      Extension: '.csproj'
  Repository:
    Type: Table
    Module: Repository
    Template: Repository.cshtml
    Output:
      Path: '{{Project.Module}}.{{Build.Module}}'
      Name: 'I{{OutputName}}Repository'
      Extension: .cs
    NamingConverter:
      Table:
        Tokenizer:
          Type: Default
          Paramters:
            IgnorePrefix: 'T_'
            Delimiter: '_'
        Converter:
          Type: Default
      View:
        Tokenizer:
          Type: Default
          Paramters: 
            IgnorePrefix: 'V_'
            Delimiter: '_'
        Converter:
          Type: Default

  SqlMap:
    Type: Table
    Template: SqlMap-SqlServer.cshtml
    Output:
      Path: '{{Project.Module}}.API/Maps'
      Extension: .xml
    IgnoreTables: null
    NamingConverter:
      Table:
        Tokenizer:
          Type: Default
          Paramters: 
            IgnorePrefix: 'T_'
            Delimiter: '_'
        Converter:
          Type: Default
      View:
        Tokenizer:
          Type: Default
          Paramters: 
            IgnorePrefix: 'V_'
            Delimiter: '_'
        Converter:
          Type: Default
      Column:
        Tokenizer:
          Type: Default
          Paramters: 
            IgnorePrefix: 'T_'
            Delimiter: '_'
        Converter:
          Type: Default
```

| 参数名 | 说明 |
| :--------- | --------:|
| Module | 根模块名 |
| Author | 作者 |
| DataSource | 数据源 |
| Language | 语言：CSharp/Java/.... |
| TemplateEngine | 模板引擎：目前内置：Razor/Handlebars |
| Output | 输出 |
| Build | 任务构建s |

#### DataSource 数据源，Name:Db

> 属性 Name:Db,使用DbSource插件作为数据源

DbSource.Paramters 接受以下三个参数：

| 参数名 | 说明 |
| :--------- | --------:|
| DbName | 数据库名称 |
| DbProvider | 数据驱动提供者:MySql,MariaDB,PostgreSql,SqlServer,Oracle,SQLite |
| ConnectionString | 连接字符串 |

#### Build 任务构建

| 参数名 | 说明 |
| :--------- | --------:|
| Type | 构建类型,Clear:用于清理目录s/文件s,Project:用于构建单文件，如：解决方案文件/项目文件,MultiTemplate:多模板构建多个文件。,Table: 用于构建以数据表为基础的文件，如：Entity,Repository文件|
| Module | 构建模块名 |
| TemplateEngine | 模板引擎，可选，默认使用根模块引擎 |
| Template | 模板文件 |
| Output | 输出 |
| IgnoreNoPKTable | 忽略无主键表 |
| IgnoreView | 忽略视图 |
| IncludeTables | 包括表名s |
| IgnoreTables | 忽略表名s |
| NamingConverter | 命名转换器 |
| Paramters | 自定义构建参数 |

#### NamingConverter 命名转换

| 属性 | 说明 |
| :--------- | --------:|
| 类型 | Table/View/Column |
| Tokenizer | 分词器 |
| Converter | 转换器：Camel/Pascal/None |

##### NamingConverter.Tokenizer 分词器

| 属性 | 说明 |
| :--------- | --------:|
| Type | Default |
| Paramters.IgnorePrefix | 忽略前缀字符 |
| Paramters.Delimiter | 分隔符 |
| Paramters.UppercaseSplit | 使用大写分隔，默认：true |