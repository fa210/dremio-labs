# STARFLAKE

## Cenário #1

- FATO:        
   - orders
- DIMENSÕES:   
   - customer
   - nation
   - region

- Dimension
   - o_orderkey
   - o_custkey
   - c_nationkey
   - r_regionkey
- Measure
   - o_totalprice: COUNT e SUM
   - o_orderpriority: COUNT
   - o_shippriority: COUNT
   - c_acctbal: COUNT e SUM
   - c_mktgsegment: COUNT

```sql
SELECT 
   ORD.o_orderkey, 
   ORD.o_orderstatus, 
   ORD.o_totalprice, 
   ORD.o_orderdate, 
   ORD.o_orderpriority, 
   ORD.o_clerk, 
   ORD.o_shippriority,
   CUS.c_custkey,
   CUS.c_name,
   CUS.c_acctbal,
   CUS.c_mktgsegment,
   NAT.n_nationkey,
   NAT.n_name,
   REG.r_regionkey,
   REG.r_name
FROM
   Preparation.orders ORD
   LEFT OUTER JOIN Preparation.customer CUS ON ORD.o_custkey = CUS.c_custkey
   LEFT OUTER JOIN Preparation.nation NAT ON CUS.c_nationkey = NAT.n_nationkey
   LEFT OUTER JOIN Preparation.region REG ON NAT.n_regionkey = REG.r_regionkey
```
- SQL #1
```sql
SELECT   r_name,
         n_name,
         COUNT(*) as numero_pedidos,
         SUM(o_totalprice) / COUNT(o_totalprice) as ticket_medio
FROM     Business.BI.sf_orders
GROUP BY r_name,
         n_name
ORDER BY r_name,
         n_name
```