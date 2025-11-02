# C4 — Диаграмма контекста (Task 4 — Передача ставок в кол-центры)

```plantuml
@startuml
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контекст (Task4: UC1–UC4) — «Передача ставок в кол-центр»

Person(CCAgent, "Менеджер кол-центра", "UC1: просматривает ставки")
Person(PartnerAgent, "Оператор партнёрского КЦ", "UC2/UC4: работает со ставками из файла")
Person(BackOffice, "Менеджер бэк-офиса", "UC3: обновляет ставки")

System(RatesSystem, "Единая система ставок", "Хранение и публикация ставок")
System(CallCenter, "Система кол-центра банка", "UI операторов")
System(PartnerCC, "Система партнёрского КЦ", "Импорт файла ставок")
System(SFTP, "SFTP-сервер", "Передача файлов ставок")

Rel(CCAgent, CallCenter, "UC1: работа в UI", "HTTPS/TLS")
Rel(CallCenter, RatesSystem, "Запрос актуальных ставок", "HTTPS/TLS")

Rel(BackOffice, RatesSystem, "UC3: редактирование ставок", "HTTPS/TLS")

Rel(RatesSystem, SFTP, "UC2: генерация файла ставок", "Автоматически, ежедневно")
Rel(PartnerCC, SFTP, "UC2/UC4: скачивание файла", "SFTP")

@enduml
```
