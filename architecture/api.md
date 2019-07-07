# Паттерны API и межсерверного взаимодействия

## Микросеврисная архитектура

Микросервисная архитектура — вариант сервис-ориентированной архитектуры программного обеспечения, ориентированный на взаимодействие насколько это возможно небольших, слабо связанных и легко изменяемых модулей — микросервисов, получивший распространение в середине 2010-х годов в связи с развитием практик гибкой разработки и DevOps.

Если в традиционных вариантах сервис-ориентированной архитектуры модули могут быть сами по себе достаточно сложными программными системами, а взаимодействие между ними зачастую полагается на стандартизованные тяжеловесные протоколы (такие, как SOAP, XML-RPC), в микросервисной архитектуре системы выстраиваются из компонентов, выполняющих относительно элементарные функции, и взаимодействующие с использованием экономичных сетевых коммуникационных протоколов. За счёт повышения гранулярности модулей архитектура нацелена на уменьшение степени зацепления и увеличение связности, что позволяет проще добавлять и изменять функции в системе в любое время.

## Преимущества микросевисов

- **Независимые релизы** - Выдача компонентов по мере готовности благодаря декомпозиции решения и отделения различных процессов. Раздельные команды, циклы разработки, тестирование, деплоя. И конечно раделенные продуктовые команды - это важно, потому что большой командой, работающей над старым монолитом, сложно было управлять. Такая команда вынуждена была работать по строгому процессу, а хотелось больше творчества и независимости. Это могли позволить себе только небольшие команды.

- **Гибкость развертывания и масштабирования** - Хотелось бы комбинировать сервисы так, как это необходимо нам, а не так, как заставляет код. Можно гораздо гибче масштабировать приложение, точечено балансируя число интсансов сервисов, которые не вывозят нагрузку.

- **Технологическая независимость** - Можно выбирать инструмент под задачу. Тут заюзаем Go с хранением в памяти тут оставим java с полноценной субд. Гораздо легче внедрить новый инструмент.

   

  ## Недостатки микросервисов

  - Издержки на межсетевое взаимодействие
  - Трудности с распределенными транзакциями
  - Сложность эксплуатации
  - Риски получения зоопарка технологий
  - При неверном применении подхода, получим распределенный монолит.

## MQ vs. HTTP

I was going through my old files and came across my notes on MQ and thought I’d share some reasons to use MQ vs. HTTP:

- If your consumer processes at a fixed rate (i.e. can’t handle floods to the HTTP server [bursts]) then using **MQ provides the flexibility** for the service to buffer the other requests vs. bogging it down.
- **Time independent processing** and **messaging exchange patterns** — if the thread is performing a fire-and-forget, then MQ is better suited for that pattern vs. HTTP.
- **Long-lived processes are better suited for MQ** as you can send a request and have a seperate thread listening for responses (note WS-Addressing allows HTTP to process in this manner but requires both endpoints to support that capability).
- **Loose coupling** where one process can continue to do work even if the other process is not available vs. HTTP having to retry.
- **Request prioritization** where more important messages can jump to the front of the queue.
- **XA transactions** – MQ is fully XA compliant – HTTP is not.
- **Fault tolerance** – MQ messages survive server or network failures – HTTP does not.
- MQ provides for **‘assured’ delivery** of messages once and only once, http does not.
- MQ provides the ability to do **message segmentation** and message grouping for large messages – HTTP does not have that ability as it treats each transaction seperately.
- MQ provides a **pub/sub interface** where-as HTTP is point-to-point.

https://solace.com/blog/products-tech/experience-awesomeness-event-driven-microservices



***Дополнительно:***

-  [Просто о микросервисах](https://habr.com/ru/company/raiffeisenbank/blog/346380/)
- [Смерть микросервисного безумия в 2018 году](https://habr.com/ru/company/flant/blog/347518/)
- [Переход от монолита к микросервисам: история и практика](https://habr.com/ru/company/raiffeisenbank/blog/458404/)
- [Микросервисы — за и против](http://devopsru.com/news/2016-05-10-microservice-trade-offs.html)