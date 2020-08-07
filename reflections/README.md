# REFLECTONS

https://docs.dremio.com/acceleration/reflections.html

## Anchor Data Sets

A Data Reflection is always associated with a single dataset, also known as its anchor dataset. The anchor may be a physical dataset or a virtual dataset, so it may contain data from one or more data sources.

## Raw Reflections

A raw reflection includes one or more fields from the anchor dataset, sorted, partitioned and distributed by specific fields.

```sql
ALTER       DATASET <DATASET_PATH> 
CREATE RAW  REFLECTION <REFLECTION_NAME> 
USING       DISPLAY (field1,field2,field3)
            [PARTITION BY (field1)]
            [LOCALSORT BY (field2)]
            [DISTRIBUTE BY (field2)]
```

## Aggregation Reflections

An aggregation reflection includes one or more dimension and measure fields from the anchor dataset, sorted, partitioned and distributed by specified fields.

```sql
ALTER               DATASET <DATASET_PATH> 
CREATE AGGREGATE    REFLECTION <REFLECTION_NAME> 
USING               DIMENSIONS (dimension1,dimension2)
                    MEASURES (measure1,measure2)
                    [PARTITION BY (dimension1)]
                    [LOCALSORT BY (dimension2)]
                    [DISTRIBUTE BY (dimension2)]
```

Dimension candidates:
- Agregate fields (GROUP BY)
- Filtered fields (WHERE)
- Fields in CASE conditions
- Fields in DISTINCT or DISTINCT COUNT conditions

## External Reflections

An external reflection is an un-managed reflection, which allows users to leverage existing datasets and summary tables built in external system as reflections in Dremio.

```sql
ALTER           DATASET <SOURCE_DATASET_PATH> 
CREATE EXTERNAL REFLECTION <REFLECTION_NAME> 
USING           <TARGET_DATASET_PATH>
```

## Drop Reflection

```sql
ALTER DATASET <DATASET_PATH> DROP REFLECTION <REFLECTION_NAME>
```

## Partitioning

- Data Reflections can be partitioned on one or more columns. When specified, Dremio creates multiple files based on partitioning configuration.
- **Low cardinality fields are ideal for partitioning** (e.g. Day-Month-Year). Ideally, the overall cardinality should be less than 10,000 values -- a smaller number of partitions is preferred.
- Dremio optimizes performance by pruning partitions when a query has a filter on a partitioned column.

## Sorting

- Data Reflections can be locally sorted on one or more columns. Sorting ensures that the records are sorted within each node and partition (if any).
- Sorting is especially useful in scenarios with range queries and filters. If sorting is enabled, during query execution, Dremio skips over large blocks of records based on filters on sorted columns.
- Dremio recommends sorting on single fields only.

## Cenário #1

### Definição

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

### Aggregate Reflection

- Dimension
    - o_orderkey (não previsto originalmente, PBI colocou um CASE em uma construção do tipo FILTRO)
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
- Sort
    - 
- Partition
    - o_orderdate

### VDS

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
### Queries

- #1

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
