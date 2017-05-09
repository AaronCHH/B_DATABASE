
# Chapter 3 SQL for SQLite
<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [Chapter 3 SQL for SQLite](#chapter-3-sql-for-sqlite)
  * [3.1 The Example Database](#31-the-example-database)
    * [Installation](#installation)
    * [Running the Examples](#running-the-examples)
  * [3.2 Syntax](#32-syntax)
    * [Commands](#commands)
    * [Literals](#literals)
    * [Keywords and Identifiers](#keywords-and-identifiers)
    * [Comments](#comments)
  * [3.3 Creating a Database](#33-creating-a-database)
    * [Creating Tables](#creating-tables)
    * [Altering Tables](#altering-tables)
  * [3.4 Querying the Database](#34-querying-the-database)
    * [Relational Operations](#relational-operations)
    * [Select and the Operational Pipeline](#select-and-the-operational-pipeline)
    * [Filtering](#filtering)
    * [Limiting and Ordering](#limiting-and-ordering)
    * [Functions and Aggregates](#functions-and-aggregates)
    * [Grouping](#grouping)
    * [Removing Duplicates](#removing-duplicates)
    * [Names and Aliases](#names-and-aliases)
    * [Subqueries](#subqueries)
    * [Compound Queries](#compound-queries)
    * [Conditional Results](#conditional-results)
    * [Handling Null in SQLite](#handling-null-in-sqlite)
    * [Summary](#summary)

<!-- tocstop -->


## 3.1 The Example Database


```python
# %load ch03/ch03.sql
create table episodes (
  id integer primary key,
  season int,
  name text );

create table foods(
  id integer primary key,
  type_id integer,
  name text );

create table food_types(
  id integer primary key,
  name text );

create table foods_episodes(
  food_id integer,
  episode_id integer );
```


```python
create table episodes ( id integer primary key, season int, name text );
create table foods( id integer primary key, type_id integer, name text );
create table food_types( id integer primary key, name text );
create table foods_episodes( food_id integer, episode_id integer );
```

### Installation


```python
sqlite3 foods.db < foods.sql
```

### Running the Examples


```python
sqlite3 foods.db < foods.sql
```


```python
.echo on .mode column .headers on .nullvalue NULL
```


```python
select *
from foods
where name='JujyFruit'
and type_id=9;
```


```python
select f.name name, types.name type
from foods f
inner join (
  select *
  from food_types
  where id=6) types
on f.type_id=types.id;
```

## 3.2 Syntax


```python
select burger
from kitchen
where patties=2
and toppings='jalopenos'
and condiment != 'mayo'
limit 1;
```

### Commands


```python
select id, name from foods;
insert into foods values (null, 'Whataburger');
delete from foods where id=413;
```

### Literals


```python
'Derry'
'Newman'
'DujyFruit'
```


```python
'Kenny''s chicken'
```


```python
3.142
6.0221415E23
```


```python
x'Ol'
X'Offf'
x'OFOEFF'
X'OfOeffab'
```

### Keywords and Identifiers


```python
SELECT * from foo;
SeLeCt * FrOm F00;
```

### Comments

Comments in SQLite are denoted by two consecutive hyphens (--),
which comment the remaining line, or by the multiline C-style notation (/* */),
which can span multiple lines. Here’s an example:
```sql
-- This is a comment on one line
/* This is a comment spanning
two lines */
```

## 3.3 Creating a Database

### Creating Tables


```python
create [temp] table table_name (column_definitions [, constraints]);
```


```python
create [temp|temporary] table ... ;
```


```python
create table contacts ( id integer primary key,
                       name text not null collate nocase,
                       phone text not null default 'UNKNOWN',
                       unique (name,phone) );
```

### Altering Tables


```python
alter table table { rename to name | add column column_def }
```


```python
alter table contacts
        add column email text not null default '' collate nocase;
```


```python
.schema contacts
```


```python
create table contacts ( id integer primary key,
                        name text not null collate nocase,
                        phone text not null default 'UNKNOWN',
                        email text not null default '' collate nocase,
                        unique (name,phone) );
```

## 3.4 Querying the Database

### Relational Operations

### Select and the Operational Pipeline


```python
select [distinct] heading
from tables
where predicate
group by columns
having predicate
order by columns
limit count, offset;
```


```python
select heading from tables where predicate;
```


```python
sqlite> select id, name from foodjtypes;
```


```python
select * from foodjtypes;
```

### Filtering


```python
select * from dogs where color='purple' and grin='toothy';
```


```python

```

* Values

* Operators


```python
x = count(episodes.name) y = count(foods.name) z = y/x * 11
```

* Binary Operators


```python
x>5
1<2
```


```python
sqlite> SELECT l>2;
l>2
```


```python
sqlite> SELECT l<2;
l<2
```


```python
sqlite> SELECT 1=2;
1=2
```


```python
sqlite> SELECT -1 AND l;
-1 AND 1 1
```

* Logical Operators


```python
(x > 5) AND (x != 3)
(y < 2) OR (y > 4) AND NOT (y = 0)
(color='purple') AND (grin='toothy')
```


```python
sqlite> select * from foods where name='3ujyFruit' and type_id=9;
```

* The LIKE and GLOB Operators


```python
select id, name from foods where name like 'J%';
```


```python
select id, name from foods where name like '%ac%P%';
```


```python
select id, name from foods
        where name like '%ac%P%' and name not like '%Sch%'
```


```python
sqlite> select id, name from foods
...> where name glob 'Pine*';
```

### Limiting and Ordering


```python
select * from food_types order by id limit 1 offset 1;
```


```python
select * from foods where name like 'B%'
        order by type_id desc, name limit 10;
```


```python
select * from foods where name like 'B%'
order by type_id desc, name limit 1 offset 2;
```


```python
select * from foods where name like 'B%'
        order by type_id desc, name limit 2,1;
```

### Functions and Aggregates


```python
select upper('hello newman'), length('hello newman'), abs(-12);
```


```python
select id, upper(name), length(name) from foods
        where type_id=1 limit 10;
```


```python
select id, upper(name), length(name) from foods
        where length(name) < 5 limit 5;
```


```python
select count(*) from foods where type_id=1;
```


```python
select avg(length(name)) from foods;
```


```python
select type_id from foods group by type_id;
```

### Grouping


```python
select type_id, count(*) from foods group by type_id;
```


```python
select type_id, count(*) from foods
        group by type_id having count(*) < 20;
```


```python
select type_id, count(*) from foods;
```

### Removing Duplicates


```python
select distinct type_id from foods;
```


```python
select foods.name, food_types.name
        from foods, food_types
        where foods.type_id=food_types.id limit 10;
```

* Inner Joins


```python
Select *
From foods inner join food_types on foods.id = food_types.id;
```

* Cross Joins


```python
select * from foods, food_types;
```

* Outer Joins


```python
select *
from foods left outer join foods_episodes on foods.id=foods_episodes.food_id;
```

* Natural Joins

* Preferred Syntax


```python
select * from foods, food_types where foods.id=food_types.food_id;
```


```python
select * from foods inner join food_types on foods.id=food_types.food_id;
select * from foods left outer join food_types on foods.id=food_types.food_id;
select * from foods cross join food_types;
```

### Names and Aliases


```python
select foods.name, food_types.name
from foods, food_types
where foods.type_id = food_types.id
limit 10;
```


```python
select f.name, t.name
from foods f, food_types t
where f.type_id = t.id
limit 10;
```


```python
select f.name as food, e1.name, e1.season, e2.name, e2.season
from episodes e1, foods_episodes fe1, foods f,
     episodes e2, foods_episodes fe2
where
  -- Get foods in season 4
  (e1.id = fe1.episode_id and e1.season = 4) and fe1.food_id = f.id
  -- Link foods with all other epsisodes
  and (fe1.food_id = fe2.food_id)
  -- Link with their respective episodes and filter out e1's season
  and (fe2.episode_id = e2.id AND e2.season != e1.season)
order by f.name;
```

### Subqueries


```python
sqlite> select 1 in (l,2,3);
```


```python
sqlite> select 2 in (3,4,5);
```


```python
sqlite> select count(*)
...> from foods
...> where type_id in (l,2);
```


```python
select count(*)
from foods
where type_id in
 (select id
  from food_types
  where name='Bakery' or name='Cereal');
```

### Compound Queries


```python
select * from foods f
order by (select count(type_id)
from foods where type_id=f.type_id) desc;
```


```python
select f.name, types.name from foods f
inner join (select * from food_types where id=6) types
on f.type_id=types.id;
```


```python
select f.*, top_foods.count from foods f
inner join
  (select food_id, count(food_id) as count from foods_episodes
     group by food_id
     order by count(food_id) desc limit 1) top_foods
  on f.id=top_foods.food_id
union
select f.*, bottom_foods.count from foods f
inner join
  (select food_id, count(food_id) as count from foods_episodes
     group by food_id
     order by count(food_id) limit 1) bottom_foods
  on f.id=bottom_foods.food_id
order by top_foods.count desc;
```


```python
select f.* from foods f
inner join
  (select food_id, count(food_id) as count
     from foods_episodes
     group by food_id
     order by count(food_id) desc limit 10) top_foods
  on f.id=top_foods.food_id
intersect
select f.* from foods f
  inner join foods_episodes fe on f.id = fe.food_id
  inner join episodes e on fe.episode_id = e.id
  where e.season between 3 and 5
order by f.name;
```


```python
select f.* from foods f
inner join
  (select food_id, count(food_id) as count from foods_episodes
     group by food_id
     order by count(food_id) desc limit 10) top_foods
  on f.id=top_foods.food_id
except
select f.* from foods f
  inner join foods_episodes fe on f.id = fe.food_id
  inner join episodes e on fe.episode_id = e.id
  where e.season between 3 and 5
order by f.name;
```

### Conditional Results


```python
select name || case type_id
                 when 7  then ' is a drink'
                 when 8  then ' is a fruit'
                 when 9  then ' is junkfood'
                 when 13 then ' is seafood'
                 else null
               end description
from foods
where description is not null
order by name
limit 10;
```


```python
case
    when count(*) > 4 then 'Very High'
    when count(*) = 4 then 'High'
    when count(*) in (2,3) then 'Moderate'
    else 'Low'
end
```


```python
select name,( select
                case
                    when count(*) > 4 then 'Very High'
                    when count(*) = 4 then 'High'
                    when count(*) in (2,3) then 'Moderate'
                    else 'Low'
                end
            )
            from foods_episodes
            where food_id=f.id) frequency
from foods f
where frequency like '%High';

### Joining Tables
```

### Handling Null in SQLite


```python
select *
from mytable
where myvalue = null;
```


```python
sqlite> select NULL is NULL;
```


```python
sqlite> select nullif(l,l); null
sqlite> select nullif(l,2);
```

### Summary

Congratulations, youhave learned the select command for SQLite’s implementation of SQL.
Not only have you learned how the command works, but you’ve learned some relational theory in the process.
You should now be comfortable with using select statements to queryyour data, join, aggregate, summarize, and dissect it for various uses.
We’ll continue the discussion of SQL in the next chapter, where we’ll build on your knowledge of select by introducing the other members of DML, as well as DDL and other helpful SQL constructs in SQLite.




```python

```
