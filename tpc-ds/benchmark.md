# Benchmark Queries

## Q1 - Relatório de Preços

```sql
select
         l_returnflag,
         l_linestatus,
         sum(l_quantity) as sum_qty,
         sum(l_extendedprice) as sum_base_price,
         sum(l_extendedprice*(1-l_discount)) as sum_disc_price,
         sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge,
         avg(l_quantity) as avg_qty,
         avg(l_extendedprice) as avg_price,
         avg(l_discount) as avg_disc,
         count(*) as count_order
from
         Preparation.lineitem 
where
         l_shipdate <= TO_DATE('1998-12-01', 'YYYY-MM-DD', 1) - INTERVAL '60' DAY
group by
         l_returnflag,
         l_linestatus
order by
         l_returnflag,
         l_linestatus;
```

## Q2 - Fornecedor de Menor Custo

```sql
select 
    s_acctbal,s_name,n_name,p_partkey,p_mfgr,s_address,s_phone,s_comment
from    
    Preparation.part,Preparation.supplier,Preparation.partsupp,Preparation.nation,Preparation.region
where
    p_partkey = ps_partkey
    and s_suppkey = ps_suppkey
    and p_size = 1
    and p_type like '%BRASS'
    and s_nationkey = n_nationkey
    and n_regionkey = r_regionkey
    and r_name = 'EUROPE'
    and ps_supplycost = 
        (select 
            min(ps_supplycost) 
        from 
            Preparation.partsupp, Preparation.supplier, Preparation.nation, Preparation.region 
        where 
            p_partkey = ps_partkey
            and s_suppkey = ps_suppkey
            and s_nationkey = n_nationkey
            and n_regionkey = r_regionkey
            and r_name = 'EUROPE'
        )
order by
    s_acctbal desc,
    n_name,
    s_name,
    p_partkey;   
```