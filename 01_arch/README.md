# Домашнее задание к занятию «1.1. Архитектура современных веб-сервисов»

Пришлите ответы на вопросы в личном кабинете на сайте [netology.ru](https://netology.ru).

## Задание «Карта взаимодействия»

### Описание

Вам попало в руки приложение, состоящее из нескольких сервисов, и клиент к нему. Ваша задача — используя Wireshark, построить карту взаимодействия между сервисами в рамках запросов, которые отправляет клиент. Нужно проанализировать ответы.

В каталоге [assets](assets) даны 4 сервера (`server-1/4`) под платформы:

1. *.bin — Linux.
2. *.exe — Windows.
3. i*.bin — macOS.

А также клиент к ним (`client`):

1. *.bin — Linux.
2. *.exe — Windows.
3. i*.bin — macOS.

### Этапы выполнения

1. Скачайте серверы для вашей платформы. Не забудьте проверить любые скачиваемые файлы через VirusTotal.
2. Скачайте каталоги с ключами **[`keys1`](assets/keys1)** и **[`keys2`](assets/keys2)** и разместите их в том же каталоге, что и скачанные в п.1 серверы.
3. Запустите по порядку серверы от 1 до 4. Они стартуют на портах 9001–9004 соответственно.
4. Запустите Wireshark в режиме отслеживания loopback (`Loopback: lo`).
5. Запустите клиента, проверяя, что клиент выводит ответ в том виде, как показано ниже. Часть данных может отличаться.


```json
{
  "transactions": [
    {
      "id": 1,
      "userId": 999,
      "category": "auto",
      "amount": 1000000,
      "created": 1613389415
    }
  ],
  "categoryStats": {
    "auto": 1000000
  }
}
```

**Примечание**. Вы не сможете скачать сами каталоги, если не умеете пользоваться Git, поэтому аккуратно скачайте файлы ключей и положите их в соответствующие каталоги, которые создаёте на своём компьютере. У вас должна получиться структура:

- keys1/
  - public.key
  - private.key
- keys2/
  - public.key
- client-x64.bin (либо другой для вашей платформы)
- server1-x64.bin (либо другой для вашей платформы)
- server2-x64.bin (либо другой для вашей платформы)
- server3-x64.bin (либо другой для вашей платформы)
- server4-x64.bin (либо другой для вашей платформы)

Серверы и клиенты запускайте из командной строки.

### Решение задания

В качестве решения пришлите в формате ниже ответы на вопросы:
1. Каким образом проходит путь запросов от клиента: на какой сервис и через какие сервисы?
2. Какие запросы делаются на каждом этапе, и какие ответы на них приходят?

### Ответ

```text
1.Client --> Server 1 (запрос):
Происходит передача учетных данных пользователя на сервер(авторизация)
PUT /users HTTP/1.1\r\n
Host: localhost:9001\r\n
User-Agent: Go-http-client/1.1\r\n
Content-Type: application/x-www-form-urlencoded\r\n
Accept-Encoding: gzip\r\n
HTML Form URL Encoded: application/x-www-form-urlencoded
Form item: "login" = "user"
Form item: "password" = "111111"
2. Server 1 --> Client (ответ):
Учетные данные верны, получаем ответ 200 OK
HTTP/1.1 200 OK\r\n
Content-Type: application/json\r\n
3. Client --> Server 2 (запрос):
Помимо учетных данных ппроисходит проверка токена безопасности(аутентификация)
GET /api/transactions HTTP/1.1\r\n
Host: localhost:9002\r\n
User-Agent: Go-http-client/1.1\r\n
[truncated]Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsImxvZ2luIjoidXNlciIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNjg4OTI2NjY3LCJleHAiOjE2ODg5MzAyNjd9.dBnOmkvHjTO0ukSNA9aIHdRBzSSb1n8AQS5LkBZE3Jh5WTUiGVU9RruciO6B9VCe75QbK
Accept-Encoding: gzip\r\n
4. Client --> Server 3 (запрос):
Аутентификационны данные верны, доступ получен, попытка получить некоторые данные сервер 3,4
GET /api/transactions HTTP/1.1\r\n
Host: localhost:9003\r\n
User-Agent: Go-http-client/1.1\r\n
X-Userid: 2\r\n
Accept-Encoding: gzip\r\n
5. Client --> Server 4 (запрос):
Аутентификационны данные верны, доступ получен, попытка получить некоторые данные сервер 3,4
GET /api/transactions HTTP/1.1\r\n
Host: localhost:9004\r\n
User-Agent: Go-http-client/1.1\r\n
X-Userid: 2\r\n
Accept-Encoding: gzip\r\n
6. Server 4 --> Server 3 (ответ):
Сверка данных между серверами с данными
HTTP/1.1 200 OK\r\n
Content-Type: application/json\r\n
{"transactions":[{"id":1,"userId":2,"category":"auto","amount":1000000,"created":1688925938},{"id":2,"userId":2,"category":"auto","amount":100000,"created":1688925938},{"id":3,"userId":2,"category":"food","amount":100000,"created":1688925938}],"categoryStats":{"auto":1100000,"food":100000}}
7. Server 3 --> Server 2 (ответ):
Передача данных на сервер аутентификации
HTTP/1.1 200 OK\r\n
Content-Type: application/json\r\n
{"transactions":[{"id":1,"userId":2,"category":"auto","amount":1000000,"created":1688925938},{"id":2,"userId":2,"category":"auto","amount":100000,"created":1688925938},{"id":3,"userId":2,"category":"food","amount":100000,"created":1688925938}],"categoryStats":{"auto":1100000,"food":100000}}
8. Server 2 --> Client (ответ):
Передача искомых данных клиенту
HTTP/1.1 200 OK\r\n
Content-Type: application/json\r\n
{"transactions":[{"id":1,"userId":2,"category":"auto","amount":1000000,"created":1688925938},{"id":2,"userId":2,"category":"auto","amount":100000,"created":1688925938},{"id":3,"userId":2,"category":"food","amount":100000,"created":1688925938}],"categoryStats":{"auto":1100000,"food":100000}}
9. 29	0.075132	::1	::1	TCP	64	61913 → 9001 [RST, ACK] Seq=196 Ack=606 Win=0 Len=0
Закрытие сессии безопасности сервер 1
10. 30	0.075182	::1	::1	TCP	64	61914 → 9002 [RST, ACK] Seq=613 Ack=401 Win=0 Len=0
Закрытие сессии безопасности сервер 2
```

## Задание «Токен»

Это необязательное задание. Его невыполнение не влияет на получение зачёта по домашнему заданию.

### Задача

В рамках того же проекта из каталога [assets](assets) вам даны ключи. Они находятся в каталогах [`keys1`](assets/keys1) и [`keys2`](assets/keys2) соответственно. Ключи предназначены для `server1` и `server2` соответственно.

Используя полученную вами в первом задании схему взаимодействия, запросы и ответы на каждом этапе, попробуйте предположить, для чего используются эти ключи.

<details>
<summary>Несколько подсказок</summary>

1. Попробуйте сравнить содержимое каталогов `keys1` и `keys2`.
2. Попытайтесь подменить один или несколько ключей и посмотреть, на что это повлияет. Не забудьте перезапустить тот сервис, для которого вы поменяли ключ.
3. Возможно, часть передаваемых данных закодирована каким-то алгоритмом. Попробуйте декодировать её и посмотреть, есть ли данные, которые указывают на то, как использовались ключи.
</details>

### Решение задания

В качестве решения пришлите информацию:
1. Для чего и на каком этапе используется, если используется, каждый ключ из каталога `keys1`.
2. Для чего и на каком этапе используется, если используется, ключ из каталога `keys2`.
