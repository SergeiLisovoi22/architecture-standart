
# C4 — Диаграмма контейнеров (Task 4 — Сервис ставок)

```puml
@startuml
!include ../../c4/C4_Container.puml
LAYOUT_WITH_LEGEND()
top to bottom direction

title Банк «Стандарт» — Контейнеры (Task 4: Витрина ставок)

Person(CCAgent, "Оператор кол-центра")
Person(PartnerAgent, "Оператор партнёрского КЦ")

System_Boundary(IB, "Интернет-банк") {
  Container(IB_Web, "Web", "ASP.NET MVC 4.5", "Витрина депозитов")
  ContainerDb(IB_DB, "DB", "MS SQL", "Данные ИБ")
  Rel(IB_Web, IB_DB, "CRUD")
}

System_Boundary(CC, "Система кол-центра (банк)") {
  Container(CC_App, "CC App", "React", "UI операторов")
  Container(CC_API, "CC Backend", "Spring Boot", "API обращений и чтение ставок")
  ContainerDb(CC_DB, "CC DB", "PostgreSQL", "Обращения/справочники")
  Rel(CC_App, CC_API, "HTTPS/TLS")
  Rel(CC_API, CC_DB, "SQL")
}

System_Boundary(Rates, "Сервис ставок") {
  Container(Rates_API, "Rates API", "REST/.NET", "GET /rates")
  Container(Rates_Export, "Export", "Batch", "CSV/XLSX + SFTP")
  ContainerDb(Rates_DB, "Rates DB", "MS SQL", "Витрина ставок")
}

System_Boundary(ABS, "АБС") {
  Container(ABS_UI, "UI", "Delphi", "Рабочие места")
  Container(ABS_PL, "PL/SQL", "Oracle", "Процедуры выгрузки")
  ContainerDb(ABS_DB, "DB", "Oracle", "Учёт счетов/депозитов")
  Rel(ABS_PL, ABS_DB, "SQL")
}

Rel(IB_Web, Rates_API, "Чтение ставок", "HTTPS/TLS")
Rel(CC_API, Rates_API, "Чтение ставок", "HTTPS/TLS")
Rel(Rates_Export, PartnerAgent, "Файлы ставок → система партнёра", "SFTP/PGP")
Rel(ABS_PL, Rates_DB, "Загрузка ставок (регламентно)", "ETL")

@enduml
```
