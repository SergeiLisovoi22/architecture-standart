@startuml
skinparam defaultTextAlignment center
skinparam packageStyle rectangle
skinparam shadowing false
skinparam ArrowColor #333333
skinparam NodeBorderColor #666666
skinparam RectangleBorderColor #666666
skinparam ActorBorderColor #333333
skinparam DatabaseBorderColor #666666
skinparam ArrowThickness 1

title Банк «Стандарт» — Контекст (UC1–UC7) — Target «Сервис депозитов + Kafka»

actor Client as "Клиент" <<Person>>
actor CCAgent as "Менеджер КЦ" <<Person>>
actor BackOffice as "Менеджер бэк-офиса" <<Person>>

rectangle "Интернет-банк\n<ASP.NET MVC + MS SQL>" as IB
rectangle "Сайт\n<React/PHP>" as Website
rectangle "Система кол-центра\n<Заявки/обращения>" as CCS
rectangle "Сервис депозитов\n<.NET Core + MS SQL>" as Deposits
rectangle "ABS Adapter\n<.NET 6>" as ABSAdapter
rectangle "Kafka\n<Event Bus>" as Kafka
rectangle "АБС\n<учёт счетов/депозитов, ставки>" as ABS
rectangle "СМС-шлюз" as SMSGW
rectangle "Телеком-оператор" as Telco

' --- Акторы и фронты
Client --> IB : UC1: подача заявки\nHTTPS/TLS
Client --> Website : UC2: заполнение формы\nHTTPS/TLS
Website --> CCS : UC2: заявка из сайта\nHTTPS/TLS
CCAgent --> CCS : UC3/UC5: регистрация/обработка\nUI
BackOffice --> ABS : UC4: редактирование ставок\nUI АБС

' --- Новый сервис и интеграции
IB --> Deposits : Создание и просмотр заявок\nREST API
CCS --> Deposits : Создание заявок от КЦ\nREST API
Deposits --> Kafka : Публикация событий\n"deposits.*"
ABS --> Kafka : Публикация событий\n"abs.rates-updated"
Kafka --> Deposits : Обновление ставок и статусов
Kafka --> ABSAdapter : Команды оформления депозита
ABSAdapter --> ABS : Вызовы АБС\nSOAP/HTTP
ABS --> Kafka : События открытия/ошибок
Deposits --> SMSGW : UC7: уведомления\nHTTPS
SMSGW --> Telco : UC7: отправка СМС\nSMPP/HTTP
Telco --> Client : UC7: получение СМС\nSMS

@enduml
