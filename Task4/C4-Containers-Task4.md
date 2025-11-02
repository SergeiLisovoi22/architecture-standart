@startuml
' ==== Минимальные стили/элементы (встроены) ====
skinparam defaultTextAlignment center
skinparam packageStyle rectangle
skinparam shadowing false
skinparam ArrowColor #333333
skinparam NodeBorderColor #666666
skinparam RectangleBorderColor #666666
skinparam ActorBorderColor #333333
skinparam DatabaseBorderColor #666666
skinparam ArrowThickness 1

' Условные типы контейнеров
' rectangle — Container, database — ContainerDb

title Банк «Стандарт» — Контейнеры (Task4: UC1–UC4) — «Передача ставок в кол-центр»

' Персоны
actor   CCAgent      as "Менеджер КЦ" <<Person>>
actor   PartnerAgent as "Оператор партнёрского КЦ" <<Person>>
actor   BackOffice   as "Бэк-офис" <<Person>>

' Единая система ставок
package "Единая система ставок" as RATES {
rectangle Rates_API    as "Rates API\n<REST /.NET>"     <<Container>>
database  Rates_DB     as "Rates DB\n<MS SQL>"          <<ContainerDb>>
rectangle Rates_Export as "Export Module\n<Batch/Scheduler>" <<Container>>
}

' Система кол-центра банка
package "Система КЦ банка" as CC {
rectangle CC_App  as "CC App\n<React>"         <<Container>>
rectangle CC_API  as "CC Backend\n<Spring Boot>" <<Container>>
database  CC_DB   as "CC DB\n<PostgreSQL>"     <<ContainerDb>>
}

' Партнёрский контур
rectangle SFTP as "SFTP-сервер\n(хранилище файла ставок)" <<Container>>
rectangle PartnerCC as "Система партнёрского КЦ\n<Импорт файла>" <<Container>>

' --- Связи по UC ---

' UC3: бэк-офис обновляет ставки
BackOffice --> Rates_API : UC3: CRUD ставок\nHTTPS/TLS
Rates_API  --> Rates_DB  : SQL

' UC1: КЦ банка читает ставки онлайн
CCAgent  --> CC_App     : Работа оператора\nHTTPS/TLS
CC_App   --> CC_API     : Запрос ставок\nHTTPS/TLS
CC_API   --> Rates_API  : UC1: GET /rates\nHTTPS/TLS
CC_API   --> CC_DB      : Кэш/справочники\nSQL

' UC2: выгрузка файла партнёру (ежедневно, автоматически)
Rates_Export --> Rates_DB : Чтение актуальных ставок\nSQL
Rates_Export --> SFTP     : UC2: выкладка файла\nSFTP (защищённо)

' UC4: подключение партнёрского КЦ
PartnerCC --> SFTP       : Загрузка файла ставок\nSFTP
PartnerAgent --> PartnerCC : Работа с загруженными ставками

@enduml
