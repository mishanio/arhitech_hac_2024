| Дано                      | предположения                                                | Нагрузка |
|---------------------------|--------------------------------------------------------------|----------|
| 20 000 заказов в сутки    | время с 12 до 16 и с 18 до 23 в будни, c 12 до 02 в выходные |          |
| 10 официантов на ресторан | Средний заказ 40 минут, 1 официант - 7 заказов               |          |
| 100 рестаранов            | 1000 официантов, 100 администраторов, 2 бухгалтера           |          |

```mermaid
flowchart TD
    OrderDb[(OrderDb)]
    ClientOrderDb[(ClientOrderDb)]
    RestaurantDb[(RestaurantDb)]
    MenuDb[(MenuDb)]
    CommentDb[(CommentDb)]
    client(client)
    waiter(waiter)
    admin(admin)
    client -->|Просмотр меню, Оформить заказ, Вопросы, Жалобы| client-order-api
    client-order-api <-->|Разместить, оплатить заказ| order-service
    client-order-api -->|Формирование заказа клиента| ClientOrderDb
    client-order-api -->|комментарий| comment-service
    waiter -->|Принять заказ, Вопросы| waiter-order-api
    waiter-order-api <-->|Назанчить официанта, работа с заказом| order-service
    waiter-order-api -->|Выход на смену, привязка заказа| restaurant-service
    admin -->|Управление, Поддержка| admin-order-api
    admin-order-api <-->|Настройка столиков и официантов| restaurant-service
    restaurant-service --> RestaurantDb
    order-service -->|Заказ Requested, In Progress, Declined, Payed| OrderDb
    menu-service --> MenuDb
    waiter-order-api -->|Стоимость блюда| menu-service
    admin-order-api --> menu-service
    waiter-order-api -->|комментарий| comment-service
    comment-service --> CommentDb
```


```mermaid
flowchart TD
    OrderDb[(OrderDb)]
    
    Debezium{{Debezium}}
    Kafka{{Kafka}}

    AnalyticsDb[(AnalyticsDb)]
    accountant(accountant)

    order-service --> |Заказ Requested, In Progress, Declined, Payed | OrderDb

    order-service --> |Оплата заказа| billing-service
    OrderDb --> |Заказ Payed| Debezium
    Debezium --> |Событие оплачно | Kafka

    accountant(accountant) --> BI
    BI --> AnalyticsDb
    Kafka --> AnalyticsDb
```