
# C4 — Диаграмма контекста (Task 3 — Открытие депозитов онлайн, покрытие UC1–UC5)

```puml
@startuml
!include ../../c4/C4_Context.pumlя
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контекст (Task 3: UC1–UC5)

' Персоны
Person(Client, "Клиент", "UC1/UC2: подача заявки; UC5: получает СМС")
Person(CCAgent, "Оператор кол-центра", "UC3: обрабатывает заявки с сайта")
Person(BackOfficeDep, "Бэк-офис депозитов", "UC4: оформляет депозит в АБС")

' Системы
System(Website, "Сайт", "UC2: заявка → кол-центр")
System(InternetBank, "Интернет-банк", "UC1: подача заявки на депозит")
System(CallCenter, "Система кол-центра", "UC2/UC3: получение и обработка заявок с сайта")
System(ABS, "АБС", "UC4/UC5: оформление депозита и уведомления")
System(SMSGateway, "СМС-шлюз", "UC5: отправка СМС")
System_Ext(Telco, "Телеком-оператор", "UC5: доставка СМС клиенту")

' Потоки по Use Cases
Rel(Client, InternetBank, "UC1: подача заявки", "HTTPS/TLS")
Rel(InternetBank, ABS, "UC1: заявка на депозит (write-only, MVP)", "DB/API (входящая таблица/процедура)")

Rel(Client, Website, "UC2: заполнение формы", "HTTPS/TLS")
Rel(Website, CallCenter, "UC2: создание заявки", "HTTPS/TLS")
Rel(CCAgent, CallCenter, "UC3: обработка заявки", "HTTPS/TLS")

Rel(BackOfficeDep, ABS, "UC4: оформление депозита", "Клиентский UI АБС")

Rel(ABS, SMSGateway, "UC5: уведомления", "HTTPS")
Rel(SMSGateway, Telco, "UC5: отправка СМС", "SMPP/HTTP")
Rel(Telco, Client, "UC5: получение СМС", "SMS")

@enduml
```
