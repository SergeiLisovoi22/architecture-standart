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

title Банк «Стандарт» — Контейнеры (UC1–UC7) — MVP «Открытие депозитов онлайн»

actor Client as "Клиент" <<Person>>
actor Agent as "Менеджер КЦ" <<Person>>
actor BackOffice as "Бэк-офис" <<Person>>

package "Интернет-банк" as IB {
rectangle "Web\n<ASP.NET MVC 4.5>" as IB_Web
database  "DB\n<MS SQL>"           as IB_DB
}

package "Сайт" as Site {
rectangle "SiteApp\n<React + PHP>" as SiteApp
}

package "Система КЦ" as CC {
rectangle "CC App\n<React>"        as CC_App
rectangle "CC API\n<Spring Boot>"  as CC_API
database  "CC DB\n<PostgreSQL>"    as CC_DB
}

package "АБС" as ABS {
rectangle "ABS UI\n<Delphi>"       as ABS_UI
database  "ABS DB\n<Oracle>"       as ABS_DB
}

rectangle "СМС-шлюз\n<Internal Service>" as SMSGW
rectangle "Телеком-оператор\n<External>" as Telco

Client --> IB_Web : UC1: подача заявки\nHTTPS/TLS
IB_Web --> IB_DB  : CRUD
SiteApp --> CC_API : UC2: заявка → КЦ\nHTTPS/TLS
CC_App  --> CC_API : HTTPS/TLS
CC_API  --> CC_DB  : SQL
Agent   --> CC_App : UC3/UC5: работа оператора\nHTTPS/TLS
BackOffice --> ABS_UI : UC4/UC6: ставки/оформление\nКлиент АБС

' В MVP — офлайн/регламентная передача заявок из ИБ в АБС (без новой онлайн-интеграции)
IB_DB ..> ABS_DB : Передача заявок для обработки\n(офлайн/регламент, MVP)

ABS_DB --> SMSGW : UC7: события/триггеры для уведомлений
SMSGW  --> Telco : UC7: СМС\nSMPP/HTTP
Telco  --> Client : UC7: доставка СМС

@enduml
