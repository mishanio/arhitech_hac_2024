| Дано                      | предположения                                                | Нагрузка |
|---------------------------|--------------------------------------------------------------|----------|
| 20 000 заказов            | время с 12 до 16 и с 18 до 23 в будни, c 12 до 02 в выходные |          |
| 10 официантов на ресторан | Средний заказ 40 минут, 1 официант - 7 заказов               |          |
| 100 рестаранов            | 1000 официантов, 100 администраторов, 2 бухгалтера           |          |

```mermaid
flowchart TD
    OrderDb[(OrderDb)]
    Debezium{{Debezium}}
    Kafka{{Kafka}}

    client(client)
    waiter(waiter)
    admin(admin)

    client -->|Просмотр меню, Оформить заказ, Вопросы, Жалобы| client-order-api
    client-order-api <-->|Разместить, оплатить заказ| order-service

    waiter -->  |Принять заказ, Вопросы| waiter-order-api
    waiter-order-api <--> |Назанчить офианта, работа с заказом| order-service
    admin -->  |Управление, Поддержка| admin-order-api
    admin-order-api <-->|Настройка столиков и тд| order-service
    order-service --> |Заказ Requested, In Progress, Declined, Payed | OrderDb


    order-service --> |Оплата заказа| billing-service
    OrderDb --> |Заказ Payed| Debezium
    Debezium --> |Событие оплачно | Kafka

```
    RestaurantDb[(RestaurantDb)]
    restaraunt-service