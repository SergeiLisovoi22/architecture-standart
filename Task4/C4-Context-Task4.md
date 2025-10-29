
# C4 — Диаграмма контекста (Task 4 — Передача ставок)

```puml
@startuml
!include ../../c4/C4_Context.puml
LAYOUT_WITH_LEGEND()

title Банк «Стандарт» — Контекст (Task 4: Ставки для кол-центров)

Person(Client, "Клиент", "Получает консультации")
Person(CCAgent, "Оператор кол-центра", "Консультирует по ставкам")
Person(PartnerAgent, "Оператор партнёрского КЦ", "Консультирует по ставкам")
Person(BackOfficeDep, "Бэк-офис депозитов", "Поддерживает ставки в АБС")

System(InternetBank, "Интернет-банк", "Витрина депозитов")
System(CallCenter, "Система кол-центра (банк)", "Рабочие места операторов")
System(PartnerCC, "Система партнёрского КЦ", "Внешняя система партнёра")
System(RatesService, "Сервис ставок (витрина)", "MS SQL + API/Export")
System(ABS, "АБС", "Источник данных ставок")

Rel(BackOfficeDep, ABS, "Обновляет ставки", "UI АБС")
Rel(ABS, RatesService, "Загрузка ставок (регламентно)", "ETL/процедуры")
Rel(InternetBank, RatesService, "Чтение актуальных ставок", "HTTPS/TLS")
Rel(CallCenter, RatesService, "Чтение актуальных ставок", "HTTPS/TLS")
Rel(RatesService, PartnerCC, "Экспорт файлов ставок", "SFTP/CSV|XLSX")

Rel(Client, CCAgent, "Консультация", "Телефон")
Rel(Client, PartnerAgent, "Консультация", "Телефон")

@enduml
```
