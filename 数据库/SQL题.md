
### 顾客登录名（字符串连接）

我们的商店已经上线了，正在创建顾客账户。所有用户都需要登录名，默认登录名是其名称和所在城市的组合。

给出 Customers表 如下：

|   |   |   |   |
|---|---|---|---|
|cust_id|cust_name|cust_contact|cust_city|
|a1|Andy Li|Andy Li|Oak Park|
|a2|Ben Liu|Ben Liu|Oak Park|
|a3|Tony Dai|Tony Dai|Oak Park|
|a4|Tom Chen|Tom Chen|Oak Park|
|a5|An Li|An Li|Oak Park|
|a6|Lee Chen|Lee Chen|Oak Park|
|a7|Hex Liu|Hex Liu|Oak Park|

【问题】编写 SQL 语句，返回顾客 ID（cust_id）、顾客名称（cust_name）和登录名（user_login），其中登录名全部为大写字母，并由顾客联系人的前两个字符（cust_contact）和其所在城市的前三个字符（cust_city）组成。提示：需要使用函数、拼接和别名。

【示例结果】

返回顾客id cust_id，顾客名称cust_name，顾客登录名 user_login

|   |   |   |
|---|---|---|
|cust_id|cust_name|user_login|
|a1|Andy Li|ANOAK|
|a2|Ben Liu|BEOAK|
|a3|Tony Dai|TOOAK|
|a4|Tom Chen|TOOAK|
|a5|An Li|ANOAK|
|a6|Lee Chen|LEOAK|
|a7|Hex Liu|HEOAK|
答案：
```sql
SELECT cust_id, cust_name, UPPER(CONCAT(SUBSTRING(cust_contact, 1, 2), SUBSTRING(cust_city, 1, 3))) AS user_login
FROM Customers
```

### 返回每个顾客不同订单的总金额

我们需要一个顾客 ID 列表，其中包含他们已订购的总金额。

OrderItems表代表订单信息，OrderItems表有订单号：order_num和商品售出价格：item_price、商品数量：quantity。

![](https://uploadfiles.nowcoder.com/images/20241104/0_1730709293453/A4AC046D4A007FA9669242AF79E32B01)  
Orders表订单号：order_num、顾客id：cust_id

![](https://uploadfiles.nowcoder.com/images/20241104/0_1730709309569/72CD304B563DDB362EE01E3B5FEE9288)  

【问题】

编写 SQL语句，返回顾客 ID（Orders 表中的 cust_id），并使用子查询返回total_ordered 以便返回每个顾客的订单总金额，将结果按金额从大到小排序。

【示例结果】返回顾客id cust_id和total_order下单总额

![](https://uploadfiles.nowcoder.com/images/20241104/0_1730709430826/BC8EA45F36B45C653DCBF2C1ADD842D9)  

【示例解析】cust2在Orders里面的订单a0013，a0013的售出价格是2售出数量是1121，总额是2242，最后返回cust2的支付总额是2242。

答案：要注意，一个用户可能会有不同的订单，所以不只要sum每个订单的总金额，还要sum所有订单的总金额
```sql
select o.cust_id,sum(oi.total_ordered) AS total_ordered

FROM Orders o,(

select order_num,sum(item_price*quantity) AS total_ordered

FROM OrderItems

GROUP BY order_num

) AS oi

WHERE o.order_num=oi.order_num

GROUP BY o.cust_id

ORDER BY total_ordered DESC;

```