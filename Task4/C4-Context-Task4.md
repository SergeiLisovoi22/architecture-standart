# C4 — Диаграмма контекста (Task 4 — Передача ставок в кол-центры)

```puml
@startuml
!include ../c4/C4_Context.puml
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контекст (Task4: UC1–UC4)

Person(CCAgent, "Менеджер кол-центра", "UC1: просматривает ставки")
Person(PartnerAgent, "Оператор партнёрского КЦ", "UC2/UC4: работает со ставками по файлу")
Person(BackOfficeDep, "Менеджер бэк-офиса", "UC3: обновляет ставки")

System(RatesSystem, "Единая система ставок", "Хранение и поддержка ставок")
System(CallCenter, "Система кол-центра банка", "UI операторов")
System(PartnerCC, "Система партнёрского КЦ", "Импорт файла ставок")
System(SFTP, "SFTP-сервер", "Передача файлов ставок")

' UC1: просмотр ставок (онлайн)
Rel(CCAgent, CallCenter, "UC1", "Работа в UI")
Rel(CallCenter, RatesSystem, "Запрос ставок", "HTTPS/TLS")

' UC3: обновление ставок
Rel(BackOfficeDep, RatesSystem, "UC3: редактирование ставок", "HTTPS/TLS")

' UC2: выгрузка файла партнёру
Rel(RatesSystem, SFTP, "UC2: генерация файла", "Автоматически, 1 раз/сутки")
Rel(PartnerCC, SFTP, "UC2/UC4: скачивание файла", "SFTP")

@enduml
