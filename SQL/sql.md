# SQL

## Select
```sql
select * from Movies 
where title = "The Good, The Bad, The Ugly";
```

```sql
select distinct country from Movies 
```

## Update
```sql
update Movies 
set title = 'The Good, The Bad, The Ugly', releasedate = 19671229  
where title = 'Il buono, il brutto, il cattivo';
```

## Delete
```sql
delete from Movies 
where (releasedate > 19680101 AND releasedate < 19681231)
```

## Insert
```sql
insert into Movies (title, releasedate, country) 
values('Escape from New York', 19810710, 'USA')
```

```sql
insert into Movies 
values('Escape from New York', 19810710, 'USA')
```

## Joins

### Inner Join

```sql
select * from Movies mv 
inner join Director dr
on mv.directorid = dr.id 
```

### Outer Join

#### Left Outer Join
```sql
select * from Movies mv
left outer join ExecProducer epdr
mv.execproducerid = epdr.id
/* There might not be an Executive Producer for every movie*/
```

#### Right Outer Join
```sql
select * from Director dr
right outer join Movie mv
dr.id = mv.directorid
/* Not every movie will have a director attached to it yet  */
```

## Order By
```sql
select * from Movies 
order by releasedate ASC
```

```sql
select * from Movies 
order by releasedate DESC
```

## Top
```sql
select top 10 * from Movies 
```

## Max and Min
```sql
select MIN(releasedate) as EarliestRelease
```

```sql
select MAX(releasedate) as LatestRelease
```