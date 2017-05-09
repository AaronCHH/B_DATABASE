
# Chapter 4 Advanced SQL for SQLite
<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [Chapter 4 Advanced SQL for SQLite](#chapter-4-advanced-sql-for-sqlite)
  * [4.1 Modifying Data](#41-modifying-data)
    * [Inserting Records](#inserting-records)
    * [Updating Records](#updating-records)
    * [Deleting Records](#deleting-records)
  * [4.2 Data Integrity](#42-data-integrity)
    * [Entity Integrity](#entity-integrity)
    * [Domain Integrity](#domain-integrity)
    * [Storage Classes](#storage-classes)
    * [Views](#views)
    * [Indexes](#indexes)
    * [Triggers](#triggers)
  * [4.3 Transactions](#43-transactions)
  * [Transaction Scopes](#transaction-scopes)
    * [Conflict Resolution](#conflict-resolution)
    * [Database Locks](#database-locks)
    * [Deadlocks](#deadlocks)
    * [Transaction Types](#transaction-types)
  * [4.4 Database Administration](#44-database-administration)
    * [Attaching Databases](#attaching-databases)
    * [Cleaning Databases](#cleaning-databases)
    * [Database Configuration](#database-configuration)
    * [The System Catalog](#the-system-catalog)
    * [Viewing Query Plans](#viewing-query-plans)
  * [4.5 Summary](#45-summary)

<!-- tocstop -->


## 4.1 Modifying Data

### Inserting Records


```python
insert into table (column_list) values (value_list);
```

* Inserting One Row


```python
insert into foods (name, type_id) values ('Cinnamon Bobka', 1);
```


```python
select * from foods where name='Cinnamon Bobka';
```


```python
select max(id) from foods;
```


```python
select last_insert_rowid();
```


```python
insert into foods values(NULL, 1, 'Blueberry Bobka');
select * from foods where name like '%Bobka';
```

* Inserting a Set of Rows


```python
insert into foods
values (null,
       (select id from food_types where name='Bakery'),
       'Blackberry Bobka');
select * from foods where name like '%Bobka';
```


```python
insert into foods
select last_insert_rowid()+1, type_id, name from foods
where name='Chocolate Bobka';
select * from foods where name like '%Bobka';
```

* Inserting Multiple Rows


```python
create table foods2 (id int, type_id int, name text);
insert into foods2 select * from foods;
select count(*) from foods2;
```


```python
create table foods2 as select * from foods;
select count(*) from list;
```


```python
sqlite> select max(id) from foods; max(id)
```


```python
sqlitex insert into foods values (416, 1, 'Chocolate Bobka');
SOL error: PRIMARY KEY must be unique
```


```python
create temp table list as
select f.name food, t.name name,
       (select count(episode_id)
        from foods_episodes where food_id=f.id) episodes
from foods f, food_types t
where f.type_id=t.id;
select * from list;
```

### Updating Records


```python
update foods set name='CHOCOLATE BOBKA'
where name='Chocolate Bobka';
select * from foods where name like 'CHOCOLATE%';
```

### Deleting Records


```python
delete from foods where name='CHOCOLATE BOBKA';
```

## 4.2 Data Integrity


```python
create table contacts (
id integer primary key,
name text not null collate nocase,
phone text not null default 'UNKNOWN',
unique (name,phone) );
```

### Entity Integrity

* Unique Constraints


```python
insert into contacts (name,phone) values ('Jerry','UNKNOWN');
insert into contacts (name) values ('Jerry');
insert into contacts (name,phone) values ('Jerry', '555-1212');
```

* Primary Key Constraints


```python
select rowid, oid,_rowid_,id, name, phone from contacts;
```


```python
create table maxed_out(id integer primary key autoincrement, x text);
insert into maxed_out values (9223372036854775807, 'last one');
select * from sqlite_sequence;
```


```python
drop table maxed_out;
create table maxed_out(id integer primary key autoincrement, x text);
insert into maxed_out values(10, 'works');
select * from sqlite_sequence;
```


```python
insert into maxed_out values(9, 'works');
select * from sqlite_sequence;
```


```python
insert into maxed_out values (9, 'fails');
```


```python
insert into maxed_out values (null, 'should be 11');
select * from maxed_out;
```


```python
select * from sqlite_sequence;
```


```python
create table pkey(x text, y text, primary key(x,y));
insert into pkey values ('x','y');
insert into pkey values ('x','x');
select rowid, x, y from pkey;
```

### Domain Integrity

* Default Values


```python
insert into contacts (name) values ('Jerry');
select * from contacts;
```


```python
create table times ( id int,
  date not null default current_date,
  time not null default current_time,
  timestamp not null default current_timestamp );
insert into times (id) values (1);
insert into times (id) values (2);
select * from times;
```

* NOT NULL Constraints


```python
insert into contacts (phone) values ('555-1212');
```

* Check Constraints


```python
create table contacts
( id integer primary key,
name text not null collate nocase,
phone text not null default 'UNKNOWN',
unique (name,phone),
check (length(phone)>=7) );
```

* Foreign Key Constraints


```python
create table foo
( x integer,
y integer check (y>x),
z integer check (z>abs(y)) );
```


```python
insert into foo values (-2, -1, 2);
insert into foo values (-2, -1, 1);
```


```python
update foo set y=-3 where x=-3;
```


```python
create table foods(
  id integer primary key,
  type_id integer references food_types(id)
  on delete restrict
  deferrable initially deferred,
  name text );
```

* Collations


```python
insert into contacts (name,phone) values ('JERRY','555-1212');
```

### Storage Classes


```python
drop table domain;
create table domain(x);
insert into domain values (3.142);
insert into domain values ('3.142');
insert into domain values (3142);
insert into domain values (x'3142');
insert into domain values (null);
select rowid, x, typeof(x) from domain;
```


```python
select typeof(3.14), typeof('3.14'),
       typeof(314), typeof(x'3142'), typeof(NULL);
```

### Views


```python
create view name as select-stmt;
```


```python
select f.name, ft.name, e.name
from foods f
inner join food_types ft on f.type_id=ft.id
inner join foods_episodes fe on f.id=fe.food_id
inner join episodes e on fe.episode_id=e.id;
```


```python
create view details as
select f.name as fd, ft.name as tp, e.name as ep, e.season as ssn
from foods f
inner join food_types ft on f.type_id=ft.id
inner join foods_episodes fe on f.id=fe.food_id
inner join episodes e on fe.episode_id=e.id;
```


```python
select fd as Food, ep as Episode
        from details where ssn=7 and tp like 'Drinks';
```


```python
drop view name;
```

### Indexes


```python
SELECT * FROM foods WHERE name='JujyFruit';
```


```python
create index [unique] index_name on table_name (columns)
```


```python
create table foo(a text, b text);
create unique index foo_idx on foo(a,b);
insert into foo values ('unique', 'value');
insert into foo values ('unique', 'value2');
insert into foo values ('unique', 'value');
```

* Collations


```python
create index foods_name_idx on foods (name collate nocase);
```


```python
sqlite> .indices foods foods_name_idx
For more information, you can use the .schema shell command as well:
sqlite> .schema foods
CREATE TABLE foods(
    id integer primary key,
    type_id integer, name text );
CREATE INDEX foods_name_idx on foods (name COLLATE NOCASE);
```

* Index Utilization


```python
column {=|>|>=|<=|<}
expression expression
{=|>|>=|<=|<} column
column IN (expression-list)
column IN (subquery)
```


```python
create table foo (a,b,c,d);

create index foo_idx on foo (a,b,c,d);

select * from foo where a=1 and b=2 and d=3;

select * from foo where a>1 and b=2 and c=3 and d=4;

select * from foo where a=1 and b>2 and c=3 and d=4;
```

### Triggers


```python
create [temp|temporary] trigger name
[beforejafter] [insert|delete|update|update of columns] on table action
```

* Update Triggers


```python
create temp table log(x);

create temp trigger foods_update_log update of name on foods
begin
  insert into log values('updated foods: new name=' || new.name);
end;

begin;
update foods set name='JUJYFRUIT' where name='JujyFruit';
select * from log;
rollback;
```


```python
create temp table log(x);

create temp trigger foods_update_log after update of name on foods
begin
  insert into log values('updated foods: new name=' || new.name);
end;

begin;
update foods set name='JUJYFRUIT' where name='JujyFruit';
rollback;
```

* Error Handling


```python
raise(resolution, error_message);
```

* Updatable Views


```python
create view foods_view as
  select f.id fid, f.name fname, t.id tid, t.name tname
  from foods f, food_types t;
```


```python
create trigger on_update_foods_view
instead of update on foods_view
for each row
begin
   update foods set name=new.fname where id=new.fid;
   update food_types set name=new.tname where id=new.tid;
end;
```


```python
.echo on
-- Update the view within a transaction
begin;
update foods_view set fname='Whataburger', tname='Fast Food' where fid=413;
-- Now view the underlying rows in the base tables:
select * from foods f, food_types t where f.type_id=t.id and f.id=413;
-- Roll it back
rollback;
-- Now look at the original record:
select * from foods f, food_types t where f.type_id=t.id and f.id=413;
begin;
update foods_view set fname='Whataburger', tname='Fast Food' where fid=413;
select * from foods f, food_types t where f.type_id=t.id and f.id=413;
rollback;
select * from foods f, food_types t where f.type_id=t.id and f.id=413;
```

## 4.3 Transactions

## Transaction Scopes


```python
begin;
delete from foods;
rollback;
select count(*) from foods;
```

### Conflict Resolution


```python
sqlite> update foods set id=800-id;
SOL error: PRIMARY KEY must be unique
```

### Database Locks


```python
create table test as select * from foods;
create unique index test_idx on test(id);
alter table test add column modified text not null default 'no';
select count(*) from test where modified='no';

update or fail test set id=800-id, modified='yes';

select count(*) from test where modified='yes';

drop table test;

create temp table cast(name text unique on conflict rollback);
insert into cast values ('Jerry');
insert into cast values ('Elaine');
insert into cast values ('Kramer');

begin;
insert into cast values('Jerry');
commit;
```

### Deadlocks


```python
create trigger foods_update_trg
before update of type_id on foods
begin
  select case
     when (select id from food_types where id=new.type_id) is null
     then raise( abort,
                 'Foreign Key Violation: foods.type_id is not in food_types.id')
  end;
end

explain query plan select * from foods where id = 145;
```

### Transaction Types


```python
begin [ deferred | immediate | exclusive ] transaction;
```

## 4.4 Database Administration

### Attaching Databases


```python
attach [databasej/ilenarne as database_name;
```


```python
sqlite> attach database '/tmp/db' as db2; sqlite> select * from db2.foo;
x bar
```


```python
sqlite> select * from main.foods limit 2;
```


```python
sqlite> create temp table foo as select * from food_types limit 3;
sqlite> select * from temp.foo;
```

### Cleaning Databases


```python
reindex collation_name;
reindex table_name|index_name;
```

### Database Configuration

* The Connection Cache Size


```python
sqlite> pragma cache_size;
cache size 2000
sqlite> pragma cache_size=l0000; sqlite> pragma cache_size;
cache size 10000
```

* Getting Database Information


```python
sqlite> pragma database_list;
```


```python
sqlite> create index foods_name_type_idx on foods(name,type_id);
```


```python
sqlite> pragma index_info(foods_name_type_idx);
```


```python
sqlite> pragma index_list(foods);
seq
```


```python
sqlite> pragma table_info(foods);
```

* Synchronous Writes

* Temporary Storage

* Page Size, Encoding, and Autovacuum

* Debugging

### The System Catalog


```python
sqlite> select type, name, rootpage from sqlite_master;
```


```python
sqlite> select sql from sqlite_master where name='foods_update_trg';
```


```python
sqlite> explain query plan select * from foods where id = 145; order
```

### Viewing Query Plans

## 4.5 Summary

SQL may be a simple language to use, but there is quite a bit of it, and it’s taken us two chapters just to introduce the major concepts for SQLite’s implementation of SQL.
But that shouldn’t be too surprising, because it is the sole interface through which to interact with a relational database.
Whether you are a casual user, system administrator, or developer, you have to know SQL if you are going to work with a relational database.
If you are programming with SQLite, then you should be off to a good start on the SQL side of things.
Now you need to know a little about how SQLite goes about executing all of these commands.
This is where Chapter 5 should prove useful.
It will introduce you to the API and show you how it works in relation to the way SQLite functions internally.
