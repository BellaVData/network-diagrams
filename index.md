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
    participant OS as ОС (TCP/IP стек)
    participant S as Сервер

    Note over C, OS: 1. Подготовка соединения
    C->>OS: Создать сокет
    C->>OS: Инициировать TCP соединение (IP, порт 443)

    Note over OS, S: 2. TCP 3-way handshake
    OS->>S: SYN (seq=x)
    S-->>OS: SYN-ACK (seq=y, ack=x+1)
    OS->>S: ACK (ack=y+1)

    Note over OS, C: TCP соединение установлено
    OS-->>C: Соединение готово

    Note over C, S: 3. TLS Handshake (ClientHello)
    C->>S: ClientHello (SNI, cipher suites, client random)

    Note over S: Выбор параметров безопасности
    S-->>C: ServerHello (chosen cipher, server random)
    S-->>C: Certificate (публичный ключ сервера)
    S-->>C: ServerHelloDone

    Note over C: Проверка сертификата
    Note over C: - Проверка CA
    Note over C: - Проверка домена
    Note over C: - Проверка срока действия

    Note over C: Генерация pre-master secret
    C->>S: ClientKeyExchange (зашифрован pre-master)

    Note over C, S: Вычисление session keys
    Note over C, S: (client random + server random + pre-master)

    C->>S: ChangeCipherSpec
    C->>S: Finished (зашифрован)

    S-->>C: ChangeCipherSpec
    S-->>C: Finished (зашифрован)

    Note over C, S: 4. Защищённый канал установлен

    Note over C: Формирование HTTP запроса
    C->>S: HTTPS Request (encrypted, JSON)

    Note over S: Обработка запроса
    S-->>C: HTTPS Response (encrypted, JSON)

    Note over C: Расшифровка ответа
```
