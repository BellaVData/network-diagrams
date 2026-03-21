# Проект: Сетевое взаимодействие в системе заказов

Этот проект описывает процесс транзакции заказа с точки зрения сетевого стека (модели TCP/IP).

## Контекстная диаграмма (Уровень А-0)
Здесь показано высокоуровневое взаимодействие системы с внешними сущностями.

*(Примечание: Загрузи файл context.svg в репозиторий, чтобы схема появилась здесь)*
![Контекстная диаграмма](context.svg)

---

## Декомпозиция процесса (Уровень А0)

<a href="decomposition.svg" target="_blank">
  <img src="decomposition.svg" alt="Открыть в полном размере" style="width: 100%; min-width: 1000px;">
</a>

### Описание этапов:
1. **DNS**: Преобразование URL в IP.
2. **TCP/TLS**: Установка защищенного соединения.
3. **HTTP**: Передача полезной нагрузки (JSON).

## DNS: Преобразование URL в IP
Процесс получения IP-адреса через систему доменных имен, смоделированный в нотации BPMN.

<a href="dns_process.svg" target="_blank">
  <img src="dns_process.svg" alt="Процесс DNS (BPMN)" style="width: 100%; min-width: 1000px;">
</a>

```mermaid
sequenceDiagram
    autonumber
    participant C as Браузер (Клиент)
    participant S as Сервер (Система заказов)
    
    Note over C, S: 1. Установка TCP соединения (3-way handshake)
    C->>S: SYN
    S-->>C: SYN-ACK
    C->>S: ACK
    
    Note over C, S: 2. Установка TLS соединения
    C->>S: ClientHello
    S-->>C: ServerHello
    S-->>C: Certificate
    S-->>C: ServerHelloDone
    
    Note over C: Проверка сертификата
    
    C->>S: ClientKeyExchange
    C->>S: ChangeCipherSpec
    C->>S: Finished
    
    S-->>C: ChangeCipherSpec
    S-->>C: Finished
    
    Note over C, S: 3. Зашифрованный обмен
    C->>S: HTTP Request (JSON)
    S-->>C: HTTP Response (JSON)
```
