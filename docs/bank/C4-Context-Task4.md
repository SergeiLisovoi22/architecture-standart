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

' Элементы (без внешних puml)
' Персоны/системы
actor   CCAgent      as "Менеджер кол-центра" <<Person>>
actor   PartnerAgent as "Оператор партнёрского КЦ" <<Person>>
actor   BackOffice   as "Менеджер бэк-офиса" <<Person>>

rectangle RatesSystem  as "Единая система ставок\n(внутренняя система банка)" <<System>>
rectangle CallCenter   as "Система кол-центра банка" <<System>>
rectangle PartnerCC    as "Система партнёрского КЦ" <<System>>
rectangle SFTP         as "SFTP-сервер (выгрузка файла ставок)" <<System>>

title Банк «Стандарт» — Контекст (Task4: UC1–UC4) — «Передача ставок в кол-центр»

' UC1: просмотр актуальных ставок (онлайн для КЦ банка)
CCAgent --> CallCenter : UC1: работа в UI
CallCenter --> RatesSystem : Запрос актуальных ставок\nHTTPS/TLS

' UC3: обновление ставок бэк-офисом
BackOffice --> RatesSystem : UC3: редактирование ставок\nUI/API

' UC2/UC4: партнёрский КЦ получает файл по SFTP
RatesSystem --> SFTP : UC2: генерация файла ставок\n(автоматически, раз в сутки)
PartnerCC --> SFTP : UC2/UC4: скачивание файла\nSFTP (защищённо)
PartnerAgent --> PartnerCC : Работа с загруженными ставками

@enduml
