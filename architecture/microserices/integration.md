# Протоколы межсервисного взаимодействия

### MQ vs. HTTP

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

## Синхронные

RPC

REST

## Асинхронные



