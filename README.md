# Индексы - Семериков Алексей

Задание 1
```sql
 select ROUND(t1.soi*100/t1.sod) '%_of_indexes'
 from (
  select SUM(data_length) sod, SUM(index_length) soi
  from INFORMATION_SCHEMA.TABLES) t1;
```
![](https://github.com/olimp85/index/blob/main/%25.jpg)

Задание 2

```sql
explain analyze
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```

![](https://github.com/olimp85/index/blob/main/explane%20analyse.jpg)

Проблемные места находятся в моменте использования оконных функций OVER и PARTITION BY, в сравнении колонок из разных таблиц,и в момент фильтрации вывода.
Вспоминая лекцию,лектор намекнул на лишнюю таблицу,на мой взгляд это таблица film

* Оптимизация запроса
```sql
create index day_of_payment on payment(payment_date);
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) 
from payment p
join rental r on r.rental_id = p.rental_id 
join customer c ON c.customer_id = p.customer_id 
join inventory i on i.inventory_id = r.inventory_id 
where date(p.payment_date) = '2005-07-30' 
group by concat(c.last_name, ' ', c.first_name);
```
![](https://github.com/olimp85/index/blob/main/explane%20analyze%202.jpg)
