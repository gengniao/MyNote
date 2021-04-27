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

## 3.将查询结果由 bigint 转换为 char
<pre><code>select <font color="#ff69b4">cast</font>(a.product_id <font color="#a52a2a">as</font> <font color="#008b8b">char</font>) as product_id from ba_product</code></pre>

## 4. cast 和 case when 的组合使用
<pre><code><font color="brown">cast</font>(
    <font color="#ff8c00">case</font>
        <font color="fuchsia">when</font> rate_detail.tax_rate  = 0 or isnull(rate_detail.tax_rate) then ifnull((1 - rate_detail.tax_rate) * rate_detail.amount * rate_detail.quantity, 0.00)
        <font color="#e9967a">else</font> ifnull((1 - rate_detail.tax_rate) * rate_detail.amount * rate_detail.quantity, 0.00) <font color="#008b8b">end</font>
        <font color="#ff69b4">as decimal(9, 2)</font>
) as untax_cost_amount
</code></pre>

## 5.