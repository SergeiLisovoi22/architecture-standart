
# C4 — Диаграмма контейнеров (Task 3 — Открытие депозитов онлайн, покрытие UC1–UC5)

```puml
@startuml
!include ../../c4/C4_Container.puml
LAYOUT_WITH_LEGEND()
top to bottom direction

title Банк «Стандарт» — Контейнеры (Task 3: UC1–UC5)

' Персоны (для наглядности связей)
Person(Client, "Клиент")
Person(CCAgent, "Оператор кол-центра")
Person(BackOfficeDep, "Бэк-офис депозитов")

' Каналы
System_Boundary(IB, "Интернет-банк") {
  Container(IB_Web, "Web", "ASP.NET MVC 4.5", "UC1: подача заявки")
  ContainerDb(IB_DB, "DB", "MS SQL", "UC1: хранение заявок/сессий")
  Rel(IB_Web, IB_DB, "CRUD")
}

System_Boundary(Website, "Сайт") {
  Container(SiteApp, "Site", "PHP + React", "UC2: форма заявки")
}

System_Boundary(CC, "Система кол-центра") {
  Container(CC_App, "CC App", "React", "UC3: UI операторов")
  Container(CC_API, "CC Backend", "Spring Boot", "UC2/UC3: API обращений")
  ContainerDb(CC_DB, "CC DB", "PostgreSQL", "UC2/UC3: обращения")
  Rel(CC_App, CC_API, "HTTPS/TLS")
  Rel(CC_API, CC_DB, "SQL")
  Rel(SiteApp, CC_API, "UC2: заявка → КЦ", "HTTPS/TLS")
}

System_Boundary(ABS, "АБС") {
  Container(ABS_UI, "UI", "Delphi", "UC4: оформление")
  Container(ABS_API, "PL/SQL интерфейсы", "UC1/UC5: входящая таблица/процедуры")
  ContainerDb(ABS_DB, "DB", "Oracle", "Учёт счетов/депозитов")
  Rel(ABS_API, ABS_DB, "SQL")
}

System(SMSGateway, "СМС-шлюз", "Internal Service")
System_Ext(Telco, "Телеком-оператор", "Внешний провайдер")

' Покрытие UC1: write-only из ИБ в АБС
Rel(IB_Web, ABS_API, "UC1: заявка на депозит (write-only, MVP)", "DB/API")

' UC3 взаимодействие оператора
Rel(CCAgent, CC_App, "UC3: работа оператора", "HTTPS/TLS")

' UC4 оформление бэк-офисом
Rel(BackOfficeDep, ABS_UI, "UC4: оформление депозита", "Клиент АБС")

' UC5 уведомления
Rel(ABS_API, SMSGateway, "UC5: отправка уведомлений", "HTTPS")
Rel(SMSGateway, Telco, "UC5: СМС", "SMPP/HTTP")

@enduml
```
