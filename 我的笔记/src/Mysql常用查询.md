<h1 align="center">Mysql 常用查询</h1>

## 1.查询字段值是中文的
<pre><code>
select * from product where <font color="#ff69b4">length(store_category)</font> = <font color="#00008b">char_length(store_category)</font>;
</code></pre>

## 2.分组查询表中某字段相同值数量大于1的记录
```sql
select 
    *
from 
    ba_product
where product_code in (
    select 
        product 
    from 
        ba_product
    group by product_name having count(*) > 1
)
```