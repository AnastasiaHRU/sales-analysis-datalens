Анализ продаж интернет-магазина и работы колл-центра

Цель проекта: Объединить разрозненные данные о заказах, клиентах и звонках в единую модель данных, рассчитать ключевые бизнес-метрики и создать интерактивный дашборд для оценки эффективности продаж и работы колл-центра.

Ссылки на проект
[Посмотреть интерактивный дашборд в Yandex DataLens](https://datalens.yandex/or36wde26wl27?_share_link=public) — здесь можно поводить мышкой по графикам и посмотреть точные цифры.
[Посмотреть исходный файл Excel с формулами](https://docs.google.com/spreadsheets/d/13jaLj52ztl3kUvTFrZ_G93Q319xwRoT6/edit?usp=sharing&ouid=114364824286850783319&rtpof=true&sd=true) — здесь находятся мои расчеты прибыли, связи таблиц через ВПР и логические проверки.

Что было сделано:
1. Изначально таблица заказов заканчивалась на статусе оплаты. Я через формулы подтянула в неё данные из других листов (информацию о клиентах, товарах и звонках).
2. Рассчитала чистую прибыль. В формуле учла, что если у заказа статус «Отмена», то прибыль по нему должна быть 0 рублей.
3. Спроектировала схему данных в Yandex DataLens, объединив таблицы по ключам, и разработала интерактивный дашборд.

Работа с SQL
Используемые таблицы:
• orders (order_id, order_date, customer_id, product_id, price, payment_status, profit)
• customers (customer_id, city, age, traffic_source)
• calls (call_id, customer_id, duration_sec, objection_handled)

SQL-запрос для выгрузки ядра клиентов:
SELECT 
    o.customer_id,
    c.city,
    c.traffic_source,
    COUNT(o.order_id) AS total_orders_placed,
    SUM(o.price) AS total_money_spent
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
WHERE o.payment_status = 'Оплачено'
GROUP BY o.customer_id, c.city, c.traffic_source
HAVING COUNT(o.order_id) > 1 
   AND AVG(o.price) > (SELECT AVG(price) FROM orders WHERE payment_status = 'Оплачено')
ORDER BY total_money_spent DESC;

