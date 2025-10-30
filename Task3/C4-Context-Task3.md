
# C4 — Диаграмма контекста (Task 3 — Открытие депозитов онлайн по новой версии ADR)

```puml
@startuml
!include ../c4/C4_Context.puml
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контекст (Task 3: UC1–UC7, согласно новой ADR)

' Персоны
Person(Client, "Клиент", "UC1/UC2: подаёт заявку; UC7: получает СМС")
Person(CCAgent, "Менеджер кол-центра", "UC3/UC5: регистрирует и обрабатывает заявки с сайта")
Person(BackOfficeDep, "Менеджер бэк-офиса депозитов", "UC4/UC6: редактирует ставки, оформляет депозит")

' Системы
System(Website, "Сайт", "UC2: создаёт заявку для КЦ")
System(InternetBank, "Интернет-банк", "UC1: подача заявки в ИБ (ASP.NET MVC + MS SQL)")
System(CallCenter, "Система кол-центра", "UC2/UC3/UC5: обращения/заявки")
System(ABS, "АБС", "UC4/UC6/UC7: ставки, оформление депозитов, СМС")
System(SMSGateway, "СМС-шлюз", "UC7: отправка СМС")
System_Ext(Telco, "Телеком-оператор", "UC7: доставка СМС клиенту")

' Потоки по Use Cases
Rel(Client, InternetBank, "UC1: подача заявки", "HTTPS/TLS")
Rel(InternetBank, ABS, "UC6: заявка на депозит (write-only в АБС)", "DB/API (входящая таблица/процедура)")

Rel(Client, Website, "UC2: заполнение формы", "HTTPS/TLS")
Rel(Website, CallCenter, "UC2: создание заявки", "HTTPS/TLS")
Rel(CCAgent, CallCenter, "UC3/UC5: регистрация и обработка заявки", "HTTPS/TLS")

Rel(BackOfficeDep, ABS, "UC4: редактирование ставок; UC6: оформление", "UI АБС")

Rel(ABS, SMSGateway, "UC7: уведомления", "HTTPS")
Rel(SMSGateway, Telco, "UC7: отправка СМС", "SMPP/HTTP")
Rel(Telco, Client, "UC7: получение СМС", "SMS")

@enduml
```
