Hello,

I attended for data science training. I have completed the sql block training, which is the first stage of the training in the course. At the end of each block, we have project studies. I would like to share my SQL project with you.

- I will proceed with the project through questions and answers.

Dataset Link: [Brazilian E-Commerce Public Dataset by Olist | Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

**Question 1:** Create ERD by creating the database from the data set in the given link and add it visually. Geolocation table will not be used in the project.

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.001.png)

**Case 1 : Order Analysis**

**Question 1:** Examine the order distribution on a monthly basis. For date data, order\_approved\_at should be used.

select 

to\_char(order\_approved\_at,'YYYY-MM') as payment\_month,

count(o.order\_id) as order\_sayi

from orders o

where to\_char(order\_approved\_at,'YYYY-MM') is not null

group by 1

order by 1

**NOTE** : I did not take 160 records returning **null** because the payment month field is not clear.

**Sample output**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.002.png)

**Question 2 :** Examine the number of orders in the order status breakdown on a monthly basis. Visualize the output of the query with excel. Are there any months with a dramatic decrease or increase? Comment by analysing the data.

- **Query for those whose order status is other than cancelled or unavailable**

select

to\_char(order\_approved\_at,'YYYY-MM') as payment\_month,

order\_status,

count(order\_id) as order\_count

from orders

where order\_status not in ('canceled','unavailable')

and to\_char(order\_approved\_at,'YYYY-MM') is not null

group by 1,2

order by 1,2

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.003.png)

select

to\_char(order\_approved\_at,'YYYY-MM') as payment\_month,

order\_status,

count(order\_id) as order\_count

from orders

where order\_status  in ('canceled','unavailable')

and to\_char(order\_approved\_at,'YYYY-MM') is not null

group by 1,2

order by 1,2

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.004.png)

**My comments**

- In November 2017, there was an extreme increase in the graph. When I examined the data ‘bed\_bath\_table’, ‘furniture\_decor’ products come first. The reason for this is that in the article I found on the internet, I found that Brazil has a strong furniture industry and sales intensified in the fourth quarter of the year, and the volume of furniture production and imports increased in September, October and November. In November, which is the last month in this quarter, sales may have intensified due to reasons such as discounts, campaigns, etc.
  At the same time, black friday sales may have been effective.
- The sudden decline in December may be due to a decrease in the sales of the furniture decor category.
- There may have been an annual salary increase in January, and the increase again in March may be due to the carnival times.
- After May, June and July, especially in July, are the coldest days of the year. This actually manifested itself in the graph. The decrease in tourism and the participation of locals in more home activities may have reduced orders.
- For September 2018, I cannot comment due to insufficient data.
- In the second graph, the high number of orders brought cancellations and inaccessible records compared to other months. In February 2018, there were extra cancellations and inaccessible records.

**Question 3 :** Examine the number of orders by product category. What are the prominent categories on special occasions? For example, New Year, Valentine’s Day…

**NOTE:** I examined special days such as 14 February Valentine’s Day, 12 October Children’s Day, 25 December Christmas. I could not comment on the general average and the average I determined.
\* For 14 February: I analysed the data ten days in advance.
\* For 12 October: I took the data since the beginning of October.
\* For 25 December : I received data from ten days before.

**For the 14th of February**

with general\_ as (

select product\_category\_name\_english,

count(o.order\_id) / NULLIF(DATE\_PART('day',max(o.order\_purchase\_timestamp)-    min(o.order\_purchase\_timestamp)),0) as general

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

group by 1),

ValentinesDay as (

select

product\_category\_name\_english,

count(o.order\_id) / 11.0 as ValentineDay

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join public.product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

where to\_char(o.order\_purchase\_timestamp,'MM-DD') between '02-04' and '02-14'

group by 1)

select g.product\_category\_name\_english,

general,

ValentineDay,

round((ValentineDay/general)::decimal,2) Ratio

from general\_ g

inner join ValentinesDay  s on g.product\_category\_name\_english = s.product\_category\_name\_english

order by  Ratio desc

**Sample output**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.005.png)

**My Comment:**

When I examined the query on the basis of Valentine’s Day, I could not see much effect of Valentine’s Day.
It seems to have some effect on ‘fashio\_female\_clothing’, but the excess of market\_place and christmas\_supplies before it is like the effect of Rio carnival organised in February or March. Also the effect of ‘fashio\_underwer\_beach’ is that the temperature is high in those months.

**For the 12th of October Children’s Day**

with general\_ as (select

product\_category\_name\_english,

count(o.order\_id) / NULLIF(DATE\_PART('day',max(o.order\_purchase\_timestamp)- min(o.order\_purchase\_timestamp)),0) as general

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

group by 1

order by 2 desc),

ChildrenDay as (

select

product\_category\_name\_english,

count(o.order\_id) / 12.0 as childDay

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join public.product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

where to\_char(o.order\_purchase\_timestamp,'MM-DD') between '10-01'and '10-12'

group by 1

order by 2 desc

)

select g.product\_category\_name\_english,

general,

childDay,

round((childDay/general)::decimal,2) Ratio

from general\_ g

inner join ChildrenDay  s on g.product\_category\_name\_english = s.product\_category\_name\_english

order by  Ratio desc

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.006.png)

**My Comment:**

I can clearly see the effect of children’s day in the data. Products such as clothes, toys,computers, console\_games can be bought to children.

**25 December for Christmas Day**

with general\_ as (select

product\_category\_name\_english,

count(o.order\_id) / NULLIF(DATE\_PART('day',max(o.order\_purchase\_timestamp)- min(o.order\_purchase\_timestamp)),0) as general

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

group by 1

order by 2 desc),

NoelDays as (

select

product\_category\_name\_english,

count(o.order\_id) / 11.0 as Noel

from orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

inner join products p on o\_i.product\_id = p.product\_id

inner join public.product\_category\_name\_translation p\_t on p\_t.product\_category\_name  = p.product\_category\_name

where to\_char(o.order\_purchase\_timestamp,'MM-DD') between '12-15'and '12-25'

group by 1

order by 2 desc

)

select g.product\_category\_name\_english,

general,

Noel,

round((Noel/general)::decimal,2) Ratio

from general\_ g

inner join NoelDays  s on g.product\_category\_name\_english = s.product\_category\_name\_english

order by  Ratio desc

**Sample Output:![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.007.png)**

**My Comment:**

- When I checked on Christmas Day, I didn’t see any special products in the first rows.
  Drinks, cds\_dvds-musicals, cds\_dvds-musicals, console\_games,audio products, I can make a comment that people may be having an activity at home at Christmas with their family, close friends with music, food and drink or console games.

**Question 4:** Examine the number of orders on the basis of days of the week (Monday, Thursday, ….) and days of the month (such as the 1st, 2nd of the month). Create and interpret a visual in excel with the output of the query you wrote.

**NOTE:** I took the date field as **order\_purchase\_timestamp** in the question. I made a comparison according to the purchase date.

**Day Basis:**

select

to\_char(order\_purchase\_timestamp,'DAY') as purchase\_day,

count(order\_id) as order\_count

from orders

where to\_char(order\_purchase\_timestamp,'DAY') is not null

group by 1

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.008.png)

**My Comment:**

- According to my research, on the weekend people are looking for relaxation, entertainment, social events, family time, sporting events, concerts, festivals or other cultural events, as well as frequented beaches. It is normal to have fewer orders at the weekend. Unless there is an extreme situation, it is normal for orders to increase on Sunday compared to Saturday.

**On the basis of days of the month:**

select

to\_char(order\_purchase\_timestamp,'DD') as purchase\_day,

count(order\_id) as order\_count

from orders

where to\_char(order\_purchase\_timestamp,'DD') is not null

group by 1

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.009.png)

**My Comments:**

\* Case 1: The increase in the number of black friday and furniture in November in question 2 may have been effective.
\* In some months, orders may have been low because there was no 31st of the month. Slight decreases after 28 may also be related to this.
# <a name="_azms8kpg3ild"></a>**Case 2 : Customer Analysis**
**Question 1:** In which cities do customers shop more? Determine the city of the customer as the city where the customer places the most orders and make the analysis accordingly. For example; Sibel orders from 3 different cities, 3 orders from Çanakkale, 8 orders from Muğla and 10 orders from Istanbul. You should select Sibel’s city as Istanbul, the city where Sibel orders the most, and Sibel’s orders should appear as 21 orders from Istanbul.

with table\_ as(

select

customer\_unique\_id,

customer\_city,

count(customer\_city) order\_count

from customers

group by 1,2

),

table1 as(

select

customer\_unique\_id,

customer\_city,

order\_count,

row\_number() over

` `(partition by customer\_unique\_id order by order\_count desc) rn

from table\_),

table2 as(

select customer\_unique\_id,

` `sum(order\_count) total\_order\_count

` `from table1

` `group by 1

` `order by 1)

select

` `t1.customer\_unique\_id,

` `customer\_city,

` `total\_order\_count

from table1 t1

join table2 t2 on t1.customer\_unique\_id = t2.customer\_unique\_id

where rn =1

order by 3 desc

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.010.png)
# <a name="_asuornjpw5nq"></a>**Case 3: Seller Analysis**
**Question 1 :** Who are the sellers who deliver orders to customers in the fastest way? Bring Top 5. Examine and comment on the number of orders of these sellers and the reviews and ratings of their products.

**NOTE**: Fast sellers I received the difference date until the order is confirmed and delivered to the customer. In other words, I took it as order\_delivered\_customer\_date — order\_approved\_at in the order table. In addition, when the ‘order\_delivered\_customer\_date’ fields were empty, the order did not reach the customer. I analysed 110112 records out of 112650 records. I found the average of the general order numbers from these records, the average was 33 orders.

with OrderInfo as

(select

seller\_id,

count(distinct o.order\_id) order\_count,

avg(order\_delivered\_customer\_date - order\_approved\_at)  as avg\_delivery\_time

from  orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

where order\_delivered\_customer\_date is not null

and order\_approved\_at is not null  and

order\_approved\_at<= order\_delivered\_customer\_date

group by  1

),

--select round(avg(order\_count)) from  OrderInfo

--top 5 query and output are as follows.

top5 as (

select

seller\_id,

count(distinct o.order\_id) order\_count,

avg(order\_delivered\_customer\_date-order\_approved\_at)  as avg\_delivery\_time

from  orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

where order\_delivered\_customer\_date is not null

and order\_approved\_at is not null  and

order\_approved\_at<= order\_delivered\_customer\_date

group by  1

HAVING count(distinct o.order\_id) >= (select round(avg(order\_count)) from  OrderInfo)

)

select \* from top5

order by 3 asc

limit 5;

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.011.png)

**Output and code of the second part of the query:**

with b  as (

select

distinct o.order\_id,

oi.seller\_id,

o\_r.review\_score,

o\_r.review\_comment\_message

from order\_items oi

inner join

(select

seller\_id,

count( distinct o.order\_id) order\_count,

avg(order\_delivered\_customer\_date-order\_approved\_at)  as avg\_delivery\_time

from  orders o

inner join order\_items o\_i

on o.order\_id = o\_i.order\_id

where order\_delivered\_customer\_date is not null

and order\_approved\_at is not null  and

order\_approved\_at<= order\_delivered\_customer\_date

group by  1

HAVING count( distinct o.order\_id) >= 33

order by 3 asc

limit 5

) siparis

on oi.seller\_id = siparis.seller\_id

inner join orders o

on o.order\_id= oi.order\_id

left join order\_reviews o\_r

on o.order\_id = o\_r.order\_id

where o.order\_delivered\_customer\_date is not null

and o.order\_approved\_at is not null  and

o.order\_approved\_at<= o.order\_delivered\_customer\_date

)

select

b.seller\_id,

round(avg(b.review\_score)) avg\_score,

count(b.review\_comment\_message) comment\_message

from b

group by 1

order by 2 desc, 3 desc

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.012.png)

**My Comment:**

- When I added and ran the following query instead of the last part of the query, I examined the comments. There were a total of 73 different comments, 22 of which did not like the product, stating that it was not as in the image, commenting on the return process, missing / different, undelivered, used product or product comments without warranty. In general, all of them stated that the order was fast. Some of the good comments are those who praise the site, like the products and recommend them. In general, the cargo was satisfied with the cargo, the percentage majority liked and recommended the product, the site.

select

distinct b.review\_comment\_message comment\_message,

b.seller\_id,

round(avg(b.review\_score)) avg\_score

from b

where b.review\_comment\_message is not null

group by 1,2

order by 2, 3 desc

**Question 2 :** Which sellers sell more categories of products? Do sellers with more categories also have more orders?

**Note**: I examined on the basis of all orders without looking at order status and order delivery.
select \* from order\_items
where seller\_id= ‘dd7ddc04e1b6c2c614352b383efe2d36’
and order\_id =‘0ddd5a236d9e9023c6b2dc4e0ae35efe’
order by order\_id — There are 2 in the same order in the same category, I distinct the orders because this is a single order.
**Check Output**: ‘dd7ddc04e1b6c2c614352b383efe2d36’ ‘pet\_shop’ 1
As a result of the following query : 3033 records come.

select

seller\_id,

count( distinct p\_t.product\_category\_name\_english) as category\_count,

count(distinct o\_i.order\_id) as order\_count

from order\_items o\_i

inner join products p on o\_i.product\_id = p.product\_id

inner join product\_category\_name\_translation p\_t on p.product\_category\_name = p\_t.product\_category\_name

group by 1

order by 2 desc, 3 desc

limit 11;

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.013.png)

**Are the order counts of sellers with more categories also high?**
\* We cannot make a sharp comment for the question, when I made the ranking in the order count detail, I saw that the order quantities of those whose category is less than the screenshot above are more, as in the screenshot below. So we cannot comment as in the question.

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.014.png)
# <a name="_iplkkbdp55ff"></a>**Case 4 : Payment Analysis**
**Question 1** :In which region do users who pay in more instalments live the most? Interpret this output.

**Note:** According to the query below, I brought the ones equal to and greater than the average number of instalments.
If we want max:
where payment\_installments >= (select max(payment\_installments) from order\_payments). In both, the maximum number of instalments shows 24.

select

distinct customer\_unique\_id,

customer\_city,

payment\_installments

from  customers c

inner join orders o on c.customer\_id = o.customer\_id

inner join order\_payments o\_p on o.order\_id = o\_p.order\_id

where payment\_installments >= (select round(avg(payment\_installments))

`         `from order\_payments)    

order by 3 desc

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.015.png)

**State Query:**

select

distinct customer\_state,

–payment\_installments

from  customers c

inner join orders o on c.customer\_id = o.customer\_id

inner join order\_payments o\_p on o.order\_id = o\_p.order\_id

where payment\_installments >= (select

`            `max(payment\_installments)

`          `from order\_payments)    

order by 1 desc

States: ‘SP’,‘RS’,‘RO’,‘RJ’,‘PR’,‘PB’,‘MG’,‘GO’,‘DF’,‘CE’,‘BA’
Sao Paulo,Rio Grande do Sul,Rondonia,Rio de Janeiro,Paraiba,Minas Gerais,Distrito Federal,Ceara,Bahia

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.016.png)

**My Comment:**

- We can say that people in the coastal states tend to buy more expensive products in instalments.

**Question 2:** Calculate the number of successful orders and total amount of successful payments by payment type. Sort from the most used payment type to the least used payment type.

select

payment\_type,

count(distinct o.order\_id) as order\_number,

`   `sum(payment\_value) as payment\_amount 

from orders o

inner join order\_payments o\_p on o.order\_id= o\_p.order\_id

where order\_status not in ('canceled','unavailable')

group by 1

order by 3 desc

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.017.png)

**Question 3 :** Analyse the orders paid in single payment and instalments by category. In which categories is payment by instalments used the most?

**Single payment:**

select

product\_category\_name\_english,

count(distinct  o\_p.order\_id) as order\_amount

from order\_payments o\_p

inner join order\_items o\_i on o\_p.order\_id = o\_i.order\_id

inner join products p on p.product\_id = o\_i.product\_id

inner join product\_category\_name\_translation p\_c\_n on p.product\_category\_name = p\_c\_n.product\_category\_name

where payment\_installments =1

group by 1

order by 2 desc

limit 10

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.018.png)

**Instalment payments:**

select

product\_category\_name\_english,

count(distinct  o\_p.order\_id) as order\_amount

from order\_payments o\_p

inner join order\_items o\_i on o\_p.order\_id = o\_i.order\_id

inner join products p on p.product\_id = o\_i.product\_id

inner join product\_category\_name\_translation p\_c\_n on p.product\_category\_name = p\_c\_n.product\_category\_name

where payment\_installments > 1

group by 1

order by 2 desc

limit 10

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.019.png)
# <a name="_yzu69av48isx"></a>**Case 5 : RFM Analysis**
Perform RFM analysis using the data set in e\_commerce\_data\_.csv below.
When calculating recency, use the last order date as the basis, not today’s date.

The data set is taken from this link, you can enter and examine the link to recognise the data.

[E-Commerce Data](https://www.kaggle.com/datasets/carrie1/ecommerce-data)

**NOTE:** I removed quantity, unitprice negatives and customer id null ones.
I took max date: InvoiceDate :2011–12–09 instead of today’s date.
select max(invoicedate) from rfm — 2011–12–09
Amount = UnitPrice \* Quantity.
In my Recency and Frequency analyses, I did manual case when because I could not divide the data properly with ntile.

With max\_i\_d as (

select

` `customer\_id,

` `max(invoicedate)::date as max\_invoicedate

from rfm

where customer\_id is not null

and quantity > 0  and unitprice >0

group by 1

),

recency as (

select

` `customer\_id,

` `max\_invoicedate,

` `('2011-12-09' - max\_invoicedate :: date) as recency

from max\_i\_d

) ,

frequency as (

select

customer\_id,

count(distinct invoiceno) as frequency

from rfm

where customer\_id is not null

and quantity > 0  and unitprice >0

group by customer\_id

),

monetary as (

select

customer\_id,

round(sum(unitprice\*quantity)::decimal,2) as monetary

from rfm

where customer\_id is not null

and quantity > 0  and unitprice >0

group by 1

),

scores as (

select

r.customer\_id,

r.recency,

case when r.recency >= 0 and r.recency <= 12 then 1

when r.recency >= 13 and r.recency <= 32 then 2

when r.recency >= 33 and r.recency <= 71  then 3

when r.recency >= 72 and r.recency <= 179  then 4

else 5 end as recency\_score,

f.frequency,

case when f.frequency >= 1 and f.frequency <= 2 then f.frequency

when f.frequency >= 3 and f.frequency <= 4 then 3

when f.frequency >= 5 and f.frequency <= 7  then 4

else 5 end as frequency\_score,

m.monetary,

ntile(5) over (order by monetary) as monetary\_score

from recency as r

left join frequency as f on r.customer\_id = f.customer\_id

left join monetary as m on r.customer\_id = m.customer\_id

),

merge\_mont\_fre as (

select

` `customer\_id,

` `recency\_score,

` `monetary\_score + frequency\_score as mon\_fre\_score

from scores

),

rfm\_score as (

select

customer\_id,

recency\_score,

ntile(5) over (order by mon\_fre\_score) as mon\_fre\_score

from merge\_mont\_fre

)

select \* from rfm\_score

**Sample Output:**

![](Aspose.Words.272498ef-6748-4c01-abbe-d15f2f55b7aa.020.png)

