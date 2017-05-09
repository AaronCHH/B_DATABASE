
# Chapter 2 Getting Started
<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [Chapter 2 Getting Started](#chapter-2-getting-started)
  * [2.1 Where to Get SQLite](#21-where-to-get-sqlite)
  * [2.2 SQLite on Windows](#22-sqlite-on-windows)
    * [Getting the Command-Line Program](#getting-the-command-line-program)
    * [Getting the SQLite DLL](#getting-the-sqlite-dll)
    * [Compiling the SQLite Source Code on Windows](#compiling-the-sqlite-source-code-on-windows)
    * [Building the SQLite DLL with Microsoft Visual C](#building-the-sqlite-dll-with-microsoft-visual-c)
    * [Building a Dynamically Linked SQLite Client with Visual C](#building-a-dynamically-linked-sqlite-client-with-visual-c)
    * [Building SQLite with MinGW](#building-sqlite-with-mingw)
  * [2.3 SQLite on Linux, Mac OS X, and Other POSIX Systems](#23-sqlite-on-linux-mac-os-x-and-other-posix-systems)
    * [Binaries and Packages](#binaries-and-packages)
    * [Compiling SQLite from Source](#compiling-sqlite-from-source)
  * [2.4 The Command-Line Program](#24-the-command-line-program)
    * [The CLP in Shell Mode](#the-clp-in-shell-mode)
    * [The CLP in Command-Line Mode](#the-clp-in-command-line-mode)
  * [2.5 Database Administration](#25-database-administration)
    * [Creating a Database](#creating-a-database)
    * [Getting Database Schema Information](#getting-database-schema-information)
    * [Exporting Data](#exporting-data)
    * [Importing Data](#importing-data)
    * [Formatting](#formatting)
    * [Exporting Delimited Data](#exporting-delimited-data)
    * [Performing Unattended Maintenance](#performing-unattended-maintenance)
    * [Backing Up a Database](#backing-up-a-database)
    * [Getting Database File Information](#getting-database-file-information)
  * [2.6 Other SQLite Tools](#26-other-sqlite-tools)
  * [2.7 Summary](#27-summary)

<!-- tocstop -->


## 2.1 Where to Get SQLite

## 2.2 SQLite on Windows

### Getting the Command-Line Program

### Getting the SQLite DLL

### Compiling the SQLite Source Code on Windows

* The Stable Source Distribution

* Anonymous Fossil Source Control

http://www.fossil-scm.org

### Building the SQLite DLL with Microsoft Visual C

### Building a Dynamically Linked SQLite Client with Visual C

### Building SQLite with MinGW

## 2.3 SQLite on Linux, Mac OS X, and Other POSIX Systems

### Binaries and Packages

### Compiling SQLite from Source

## 2.4 The Command-Line Program

### The CLP in Shell Mode

### The CLP in Command-Line Mode

## 2.5 Database Administration

### Creating a Database


```python
sqlite3 test.db
```


```python
sqlite>create table test(id integer primary key, value text);
```


```python
sqlite>insert into test(id, value) values(1, 'eenie');
sqlite>insert into test(id, value) values(2, 'meenie');
sqlite>insert into test(value) values('miny');
sqlite>insert into test(value) values('mo');
```


```python
sqlite>.mode column
sqlite>.headers on
sqlite>select * form test;
```


```python
sqlite>select last_insert_rowid();
```


```python
sqlite>create index test_idx on test (value);
```


```python
sqlite>create view schema as select * from sqlite_master;
```


```python
sqlite>.exit
```

### Getting Database Schema Information


```python
sqlite3 test.db
```


```python
sqlite>.indices test
```


```python
sqlite>.schema
```

### Exporting Data


```python
sqlite>.output file.sql
sqlite>.dump
sqlite>.output stdout
```

### Importing Data


```python
sqlite>.show
sqlite>drop table test;
sqlite>drop view schema;
sqlite>.read file.sql
```

### Formatting


```python
sqlite>.prompt 'sqlite3> ';
```


```python
.output stdout
.separator ,
select * from test;
.output stdout
```


```python
.output file.csv
.mode csv
select * from test;
.output stdout
```

### Exporting Delimited Data


```python
.output text.csv
.separator ,
select * from test where value linke 'm%';
.output stdout
```


```python
create table test2(id integer primary key, value text);
.import text.csv test2
```

### Performing Unattended Maintenance


```python
sqlite3 test.db .dump
sqlite3 test.db .dump > test.sql
sqlite3 test.db "select * from test"
```


```python
sqlite3 test2.db < test.sql
sqlite3 -init test.sql test3.db
sqlite3 -init test.sql test3.db .exit
```

### Backing Up a Database


```python
sqlite3 test.db .dump > test.sql
```


```python
.output file.sql
.dump
.output stdout
.exit
```


```python
sqlite3 test.db < test.sql
```


```python
sqlite3 test.db vacuum
cp test.db test.backup
```

### Getting Database File Information


```python
sqlite3_analyzer test.db
```

## 2.6 Other SQLite Tools

http://sqlitebrowser.org/
http://www.sqliteman.com
https://sourceforge.net/projects/sqliteman/
http://www.sqlabs.com/sqlitemanager.php

## 2.7 Summary

No matter what platform you work on, SQLite is easy to install and build.
Windows, Mac OS X, and Linux users can obtain binaries directly from the SQLite website.
Users of many other operating systems can also obtain binaries using their native—or even third-party—package systems.
The common way to work with SQLite across all platforms is using the SQLite command-line program (CLP).
This program operates as both a command-line tool and an interactive shell.
You can issue queries and do essential database administration tasks such as creating tables, indexes, and views as well as exporting and importing data.
SQLite databases are contained in single operating system files, so doing things like backups are very simple—just copy the file.
For long-term backups, however, it is always best to dump the database in SQL format, because this is portable across SQLite versions.
In the next few chapters, you will be using the CLP to explore SQL and the database aspects of SQLite.
We will start with the basics of using SQLwith SQLite in Chapter 3 and move to more advanced SQL in Chapter 4.



```python

```
