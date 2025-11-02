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

title Банк «Стандарт» — Контейнеры (UC1–UC7) — Target «Сервис депозитов + Kafka»

actor Client as "Клиент" <<Person>>
actor Agent as "Менеджер КЦ" <<Person>>
actor BackOffice as "Бэк-офис" <<Person>>

package "Интернет-банк" as IB {
rectangle "Web\n<ASP.NET MVC 4.5>" as IB_Web
database  "DB\n<MS SQL>" as IB_DB
}

package "Сайт" as Site {
rectangle "SiteApp\n<React + PHP>" as SiteApp
}

package "Система КЦ" as CC {
rectangle "CC App\n<React>" as CC_App
rectangle "CC API\n<Spring Boot>" as CC_API
database  "CC DB\n<PostgreSQL>" as CC_DB
}

package "Сервис депозитов" as Deposits {
rectangle "Deposits API\n<.NET Core>" as Dep_API
database  "Deposits DB\n<MS SQL>" as Dep_DB
}

package "ABS Adapter" as ABSAdapter {
rectangle "Adapter Service\n<NET 6>" as AdapterApp
}

package "АБС" as ABS {
rectangle "ABS UI\n<Delphi>" as ABS_UI
database  "ABS DB\n<Oracle>" as ABS_DB
}

rectangle "Kafka\n<Event Bus>" as Kafka
rectangle "СМС-шлюз\n<Internal>" as SMSGW
rectangle "Телеком-оператор\n<External>" as Telco

Client --> IB_Web : UC1: подача заявки\nHTTPS/TLS
IB_Web --> IB_DB : CRUD
IB_Web --> Dep_API : REST API

SiteApp --> CC_API : UC2: заявка → КЦ\nHTTPS/TLS
CC_App --> CC_API : HTTPS/TLS
CC_API --> CC_DB : SQL
CC_API --> Dep_API : UC2: создание заявки\nREST API

Agent --> CC_App : UC3/UC5: работа оператора\nHTTPS/TLS
BackOffice --> ABS_UI : UC4: ставки/оформление\nUI АБС

Dep_API --> Dep_DB : CRUD
Dep_API --> Kafka : события и команды

ABS --> Kafka : события ставок\n"abs.rates-updated"
Kafka --> Dep_API : обновление ставок
Kafka --> AdapterApp : команды оформления депозита
AdapterApp --> ABS : SOAP/HTTP
ABS --> Kafka : результат открытия
Kafka --> Dep_API : обновление статуса заявки

Dep_API --> SMSGW : UC7: уведомления\nHTTPS
SMSGW --> Telco : UC7: СМС\nSMPP/HTTP
Telco --> Client : UC7: доставка СМС

@enduml
