# STARFLAKE

## Cenário #1

- FATO:        
   - orders
- DIMENSÕES:   
   - customer
   - nation
   - region
   - order status
   - order priority
   - ship priority
   - customer market segment

- Dimension
   - c_name
   - n_name
   - r_name
   - o_orderstatus
   - o_orderpriority
   - o_shipprority
   - c_marketsegment
- Measure
   - o_orderkey: COUNT
   - o_totalprice: COUNT e SUM
   - c_acctbal: COUNT e SUM
- Partition
   - o_orderdate


```sql
SELECT 
   ORD.o_orderkey, 
   ORD.o_orderdate,
   ORD.o_orderstatus, 
   ORD.o_orderpriority, 
   ORD.o_shippriority,
   ORD.o_totalprice, 
   CUS.c_name,
   CUS.c_mktgsegment,
   NAT.n_name,
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