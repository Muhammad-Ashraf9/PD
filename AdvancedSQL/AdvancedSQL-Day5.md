### table variable

> [!tip] table variable
>
> - is used to store data temporarily (batch based)
> - `declare @table_variable_name table (column1 datatype, column2 datatype, ...)`
>
> ```sql
> declare @table_variable_name table (column1 datatype, column2 datatype, ...)
> ```

> [!bug] local temporary table
>
> - session based tabel
> - is used to store data temporarily
> - global temporary table but only for the session (connection)
> - `create table #table_name (column1 datatype, column2 datatype, ...)`
>
> ```sql
> create table #table_name (column1 datatype, column2 datatype, ...)
> ```

> [!note] global temporary table
>
> - is used to share data between different sessions
> - `create table ##table_name (column1 datatype, column2 datatype, ...)`
>
> ```sql
> create table ##table_name (column1 datatype, column2 datatype, ...)
> ```

---

### table variable sequence

> [!note] Sequence
>
> - like identity but shared between different tables
> - is used to generate a sequence of numbers
> - `create sequence sequence_name as datatype start with start_value increment by increment_value`
> - is saved in sequences
> - `next value for sequence_name` to get the next value
> - can call `next value for sequence_name` from different tables

```sql
create sequence mySequence
start with 1
increment by 1
MinValue 1
MaxValue 100
cycle; -- default


------- use sequence

create table #temp (id int, name varchar(50))

insert into #temp (id, name) values (next value for mySequence, 'Muhammad') -- 1
insert into #temp (id, name) values (next value for mySequence, 'Ashraf') -- 2
insert into #temp (id, name) values (next value for mySequence, 'Ali') -- 3
insert into #temp (id, name) values (next value for mySequence, 'Ahmed') -- 4
```

---

#### High Availability

> [!note] High Availability
>
> - is the ability of a system to operate continuously without failing for a long time
>   > [!done] 5 methods to achieve high availability
>   >
>   > 1. failover clustering
>   > 2. AlwaysOn
>   > 3. peer-to-peer replication
>   > 4. Database mirroring
>   > 5. ==ship transaction log==

> [!note] ship transaction log
>
> - 1 primary server and multiple secondary servers
> - primary server sends the transaction log to secondary servers
> - useing jobs:
> - Job Full backup, Job Transaction log
>   -used shared storage to store the backup files
> - secondary servers has copy jobs to copy the backup files from shared storage to local storage
> - secondary servers have restore jobs to restore the backup files
> - the less the time between the backup and restore the more the synchronization between the primary and secondary servers
> - in case of failover, the secondary server becomes the primary server
> - this used to have replicates of the primary server
