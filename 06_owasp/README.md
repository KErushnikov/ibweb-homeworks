# Домашнее задание к занятию «2.2. OWASP»

Пришлите ответы на вопросы в личном кабинете на сайте [netology.ru](https://netology.ru).

## Описание

Вы рассмотрите проект [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/), входящих в [Vulnerable Web Applications Directory](https://owasp.org/www-project-vulnerable-web-applications-directory/).

Для запуска используйте `docker-compose.yml`:

```yaml
version: '3.7'
services:
  shop:
    image: bkimminich/juice-shop
    ports:
      - 3000:3000
```

Приложение запустится на порту 3000, вам нужно открыть `localhost:3000`.

## Задание «Поиск»

В качестве Cheat Sheet, содержащего примеры возможного вода для проверки подверженности XSS, используйте [XSS Cheat Sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) от OWASP.

### Этапы выполнения

1. Проверьте поле поиска на сайте на подверженность XSS.
2. Проанализируйте, возможно ли ссылку, полученную в результате ввода в строку поиска, передать другому человеку, чтобы получить Reflected XSS. 

### Решение задания

В качестве решения пришлите:
1. Использованные данные для ввода.
2. Пункт из XSS Cheat Sheet, в котором содержится пример для проверки подверженности поля поиска XSS.

Вы, конечно, можете загуглить готовое решение, но это будет не так интересно и продуктивно.

## Задание «Чужая корзина»

Найдите IDOR в корзине. Посмотрите, что лежит в чужих корзинах. 

### Этапы выполнения

1. Положите что-то в свою корзину. 
2. Откройте саму корзину и инструменты разработчика. 
3. Проанализируйте, что лежит в куках и локальном хранилище. Попробуйте изменить данные, обновите страницу. 

![](storage.png)

### Решение задания

В качестве решения пришлите:
1. Наименование параметра, который отвечает за открываемую корзину.
2. Ваши рекомендации по исправлению этой незначительной уязвимости.
