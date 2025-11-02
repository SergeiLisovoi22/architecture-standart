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

title Банк «Стандарт» — Контекст (UC1–UC7) — MVP «Открытие депозитов онлайн»

actor Client as "Клиент" <<Person>>
actor CCAgent as "Менеджер КЦ" <<Person>>
actor BackOffice as "Менеджер бэк-офиса" <<Person>>

rectangle "Интернет-банк\n<ASP.NET MVC + MS SQL>" as IB
rectangle "Сайт\n<React/PHP>" as Website
rectangle "Система кол-центра\n<Заявки/обращения>" as CCS
rectangle "АБС\n<учёт счетов/депозитов, ставки>" as ABS
rectangle "СМС-шлюз" as SMSGW
rectangle "Телеком-оператор" as Telco

Client --> IB : UC1: подача заявки\nHTTPS/TLS
Client --> Website : UC2: заполнение формы\nHTTPS/TLS
Website --> CCS : UC2: создание заявки\nHTTPS/TLS
CCAgent --> CCS : UC3/UC5: регистрация/обработка\nUI
BackOffice --> ABS : UC4: редактирование ставок\nUI АБС
BackOffice --> ABS : UC6: оформление депозита\nUI АБС
IB ..> ABS : Передача заявок для обработки в АБС\n(регламент/офлайн, MVP)
ABS --> SMSGW : UC7: уведомления\nHTTPS
SMSGW --> Telco : UC7: отправка СМС\nSMPP/HTTP
Telco --> Client : UC7: получение СМС\nSMS
@enduml
