下面的SQL可以快速查找出某一系列表的外键表有哪些。
```sql
with ref_tables as (
    select 
        owner,
        table_name,
        constraint_name,
        r_constraint_name,
        R_OWNER
    from
        dba_constraints
    where
        constraint_type = 'R'
),
main_tables as (
    select
        owner,
        table_name,
        constraint_name 
    from
        dba_constraints
    where 
        constraint_type in ('P', 'U') 
    and table_name like :table_pattern 
    and owner = :table_owner
)
select
    main_tables.owner,
    main_tables.table_name,
    main_tables.constraint_name,
    ref_tables.owner,
    ref_tables.table_name,
    ref_tables.constraint_name
from
    main_tables
left join 
    ref_tables
on
    main_tables.CONSTRAINT_NAME = ref_tables.r_constraint_name
and main_tables.OWNER = ref_tables.R_OWNER
order by 
    main_tables.owner,
    main_tables.table_name,
    main_tables.constraint_name
```
