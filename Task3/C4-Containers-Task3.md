
# C4 — Диаграмма контейнеров (Task 3 — Открытие депозитов онлайн по новой версии ADR)

```puml
@startuml
!include ../../c4/C4_Container.puml
LAYOUT_WITH_LEGEND()
top to bottom direction

title Банк «Стандарт» — Контейнеры (Task 3: UC1–UC7, согласно новой ADR)

' Персоны (для явной привязки UC)
Person(Client, "Клиент")
Person(CCAgent, "Менеджер кол-центра")
Person(BackOfficeDep, "Менеджер бэк-офиса депозитов")

' Каналы
System_Boundary(IB, "Интернет-банк") {
  Container(IB_Web, "Web", "ASP.NET MVC 4.5", "UC1: форма подачи заявки")
  ContainerDb(IB_DB, "DB", "MS SQL", "UC1: хранение заявки/сессии")
  Rel(IB_Web, IB_DB, "CRUD")
}

System_Boundary(Website, "Сайт") {
  Container(SiteApp, "Site", "PHP + React", "UC2: форма заявки")
}

System_Boundary(CC, "Система кол-центра") {
  Container(CC_App, "CC App", "React", "UC3/UC5: UI менеджера КЦ")
  Container(CC_API, "CC Backend", "Spring Boot", "UC2/UC3/UC5: API обращений")
  ContainerDb(CC_DB, "CC DB", "PostgreSQL", "UC2/UC3/UC5: обращения/заявки")
  Rel(CC_App, CC_API, "HTTPS/TLS")
  Rel(CC_API, CC_DB, "SQL")
  Rel(SiteApp, CC_API, "UC2: заявка → КЦ", "HTTPS/TLS")
}

' Ядро
System_Boundary(ABS, "АБС") {
  Container(ABS_UI, "UI", "Delphi", "UC4/UC6: редактирование ставок и оформление")
  Container(ABS_API, "PL/SQL интерфейсы", "UC6/UC7: входящая таблица/процедуры")
  ContainerDb(ABS_DB, "DB", "Oracle", "Учёт счетов/депозитов, ставки")
  Rel(ABS_API, ABS_DB, "SQL")
}

System(SMSGateway, "СМС-шлюз", "Internal Service")
System_Ext(Telco, "Телеком-оператор", "Внешний провайдер")

' Покрытие UC6: write-only из ИБ в АБС
Rel(IB_Web, ABS_API, "UC6: заявка на депозит (write-only)", "DB/API")

' UC3/UC5: работа КЦ
Rel(CCAgent, CC_App, "UC3/UC5: работа оператора", "HTTPS/TLS")

' UC4/UC6: работа бэк-офиса
Rel(BackOfficeDep, ABS_UI, "UC4/UC6: ставки/оформление", "Клиент АБС")

' UC7: уведомления
Rel(ABS_API, SMSGateway, "UC7: отправка уведомлений", "HTTPS")
Rel(SMSGateway, Telco, "UC7: СМС", "SMPP/HTTP")

@enduml
```
