# C4 — Диаграмма контейнеров (Task 4 — Передача ставок в кол-центры)

```plantuml
@startuml
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контейнеры (Task4: UC1–UC4) — «Передача ставок в кол-центр»

Person(CCAgent, "Менеджер КЦ")
Person(PartnerAgent, "Оператор партнёрского КЦ")
Person(BackOffice, "Бэк-офис")

System_Boundary(RATES, "Единая система ставок") {
  Container(Rates_API, "Rates API", "REST /.NET", "UC1/UC3: получение/обновление ставок")
  ContainerDb(Rates_DB, "Rates DB", "MS SQL", "Хранение ставок")
  Container(Rates_Export, "Export Module", "Batch/Scheduler", "UC2: выгрузка файла")
}

System_Boundary(CC, "Система КЦ банка") {
  Container(CC_App, "CC App", "React", "UI оператора")
  Container(CC_API, "CC Backend", "Spring Boot", "Запрос ставок")
  ContainerDb(CC_DB, "CC DB", "PostgreSQL", "Кэш/справочники")
  Rel(CC_App, CC_API, "HTTPS/TLS")
  Rel(CC_API, CC_DB, "SQL")
  Rel(CC_API, Rates_API, "UC1: GET /rates", "HTTPS/TLS")
}

System(SFTP, "SFTP-сервер", "Хранение файла ставок")
System(PartnerCC, "Система партнёрского КЦ", "Импорт файла ставок")

Rel(BackOffice, Rates_API, "UC3: CRUD ставок", "HTTPS/TLS")
Rel(Rates_API, Rates_DB, "SQL")

Rel(Rates_Export, Rates_DB, "Чтение актуальных ставок", "SQL")
Rel(Rates_Export, SFTP, "UC2: выкладка файла", "SFTP")
Rel(PartnerCC, SFTP, "UC2/UC4: скачивание файла", "SFTP")
Rel(PartnerAgent, PartnerCC, "Работа с загруженными ставками", "")

@enduml
```
