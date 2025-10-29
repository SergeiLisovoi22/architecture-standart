
---

###  **C4-Containers-Task4.md**

```md
# C4 — Диаграмма контейнеров (Task 4 — Передача ставок в кол-центры)

```puml
@startuml
!include ../../c4/C4_Container.puml
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контейнеры (Task4: UC1–UC4)

Person(CCAgent, "Менеджер КЦ")
Person(PartnerAgent, "Оператор партнёрского КЦ")
Person(BackOfficeDep, "Бэк-офис")

System_Boundary(Rates, "Единая система ставок") {
  Container(Rates_API, "Rates API", "REST/.NET", "UC1/UC3: обновление и получение ставок")
  ContainerDb(Rates_DB, "Rates DB", "MS SQL", "Хранение ставок")
  Container(Rates_Export, "Export Module", "Batch", "UC2: формирование и выгрузка файла")
}

System_Boundary(CC, "Система КЦ банка") {
  Container(CC_App, "CC App", "React", "UI оператора")
  Container(CC_API, "CC Backend", "Spring Boot", "Запрос ставок")
  ContainerDb(CC_DB, "CC DB", "PostgreSQL", "Кэш ставок / справочники")
  Rel(CC_App, CC_API, "HTTPS/TLS")
  Rel(CC_API, CC_DB, "SQL")
  Rel(CC_API, Rates_API, "UC1: GET /rates")
}

System(SFTP, "SFTP-сервер", "Хранение файла ставок")
System(PartnerCC, "Система партнёрского КЦ", "Импорт файла ставок")

' UC2/UC4: файл
Rel(Rates_Export, SFTP, "UC2: SFTP-выгрузка (ежедневно)")
Rel(PartnerCC, SFTP, "UC2/UC4: скачивание файла", "SFTP")

' UC3: обновление ставок
Rel(BackOfficeDep, Rates_API, "UC3: обновление ставок", "HTTPS/TLS")

@enduml
