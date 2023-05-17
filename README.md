# Patreon
[Методические указания](https://github.com/init/highload/blob/main/homework_architecture.md)
## 1.1 Тема
**Patreon** - краудфандинговая платформа, где пользователи делятся на *Авторов* и *Покровителей*. Авторы публикуют свой творческий контент/материалы, ограничивая доступ к нему с помощью подписок различных уровней. Покровители платят за получение какого-то уровня доступа к материалам автора на определенный срок. Материал может быть как обычный *текст*, так и *видео* или *аудио* контент, *подкасты*, *изображения*, *комиксы*.
### MVP
1. Профиль автора и покровителя.
2. Создание уровней подписок автором.
3. Публикация постов автором с установкой уровня доступа.
4. Хранение контента с ограничением доступа по наличию необходимой подписки.
5. Оплата помесячной подписки со стороны покровителя.
6. Вывод заработанных средств со сторони автора.
## 1.2 Целевая аудитория
- Месячная аудитория ~450M [^1]
- Распределение аудитории по континетам: [^1]
  Так как точных данных по распределению нет, то оценим на основе поисковых запросам

| Страна                  | Процент | Посетителей в месяц |
| ----------------------- | :-----: | :-----------------: |
| North and South America |   50%   |        225M         |
| Europe                  |  28.5%  |       128.25М       |
| Asia                    |  11.5%  |       51.75М        |
| Other                   |   10%   |         45M         |

- 6М активных покровителей в последнем месяце [^2]
- 250К авторов, 220K из них поддерживает хотя бы 1 покровитель [^3]
- Распределение авторов по основным тематикам контента [^3]

| Жанр                         | Кол-во авторов (мин. 1 покровитель) |
| ---------------------------- | :---------------------------------: |
| Видео                        |                 62K                 |
| Подкасты                     |                 18K                 |
| Музыка                       |                 15K                 |
| Игры                         |                 20K                 |
| Текст                        |                 14K                 |
| Комиксы, рисунки, фотографии |                 40K                 |
| Остальное                    |                 51K                 |

## 2. Расчет нагрузки
### 2.1 Продуктовые метрики
#### 2.1.1 Аудитория [^1]
| Аудитория |   Количество    |
| --------- | :-------------: |
| Месячная  |      450М       |
| Дневная   | 450М / 30 = 15М |
#### 2.1.2 Среднее количество действий пользователя в день
#### 2.1.2.1 Общее
- Авторизация:

Пользователь вынужден авторизовываться на сервисе в нескольких случаях: протухание авторизационной куки, смена пароля или вход с другого устройства. Рекомендавно менять пароль раз в 90 дней [^5]. К этой частоте и привяжем частоту авторизации. Также учтем, что человек заходит минимум с двух уствойств (ПК + телефон).
> (1/90) * 2 = 0.02 авторизаций в день от одного пользователя

- Регистрация:

Число регистрация примем равным числу увеличения аудитории сервиса за последний год [^3]. Так как по увеличению аудтории покровителей данных нет, то примем, что число покровителей увеличилось на такойже процент.
> (210K *0.057 + 6M * 0.057) / 365 ~ 970 регистраций в день на весь сервис

#### 2.1.2.2 Автор
- Публикация поста:

Информации о количестве постов нет, поэтому оценим исходя из частоты платного продвижения постов (как правило, продвижение поста связано с его публикацией, поэтому в общем случаи эти частоты соизмеримы). Из источника [^4] получаем статистику по частоте продвижению (Never равномерно распределим по всем частотам). По полученным данным найдем мат. ожидания частоты продвижения (публикации) поста в разрезе на 1 год:
> (365 * 7.75 + 52 \* 2.5 * 18.05 + 52 * 18.55 + 12 * 2.5 * 21.75 + 12 * 15.15 + 5 * 9.15 + 2.4 * 3.75 + 1 * 5.85)/100 = 70.3475 постов в год
>
> 70.3475/365 ~ 0.2 постов в день от одного автора

- Вывод средств:

Будем считать, что автор выводит средства раз в месяц.
> 1 / 30 = 0.033

#### 2.1.2.2 Покровитель
- Оплата подписки:

Будем считать, что оплата доступна только помесячная. Тогда количество оплат в день будет зависить только от количеств подписок. Из источника [^6] можно получить среднюю цену одной подписки в месяц, а из источника [^3] можно получить месячную выплату от всех покровителей. Тогда из расчета на 6М покровителей, получим количество подписок у одного покровителя.
> Число оплачиваемых подписок у одного покровителя ((25 668 645 / 6M) / 4.5) = 0.95
>
> 0.95 / 30 = 0.032

- Просмотр поста:

Из источника [^1] возьмем, что в среднем в день посещается 2 страницы за 10 минут. Будем считать, что при загрузке страницы автора будут прогружаться последние 5 поста.
> 2*5 = 10

| Тип действия     | Действий в сутки от одного пользователя |
| ---------------- | :-------------------------------------: |
|                  |                  Общее                  |
| Авторизация      |                  0.02                   |
| Регистрация      | 970 пользователей в день на весь сервис |
|                  |                  Автор                  |
| Публикация поста |                   0.2                   |
| Вывод средств    |                  0.033                  |
|                  |               Покровитель               |
| Оплата подписки  |                  0.032                  |
| Просмотр поста   |                   10                    |

#### 2.1.3 Средний размер хранилища пользователя
- Размер профиля (Покровитель)

| Поле                     | Размер (max) | Размер (average) |
| ------------------------ | :----------: | :--------------: |
| Id                       |   4 байта    |     4 байта      |
| Псевдоним (64 символа)   |   64 байт    |     32 байт      |
| Хешированный пароль (bf) |   60 байта   |     60 байт      |
| Почтовый адрес           |  254 байта   |     127 байт     |
> Итог: 223 байт

- Размер профиля (Автор)

| Поле                              | Размер (max) | Размер (average) |
| --------------------------------- | :----------: | :--------------: |
| Описание (1000 символов)          |  1000 байт   |     500 байт     |
| Вложения в описание (Изображения) |    512 Мб    |      20 Мб       |
| Аватар                            |    10 Мб     |       5 Мб       |
| Баланс                            |   4 байта    |     4 байта      |
> Итог: ~ 25 Мб

- Размер подписки

| Поле                    | Размер (max) | Размер (average) |
| ----------------------- | :----------: | :--------------: |
| Id                      |   4 байта    |     4 байта      |
| Уровень                 |    1 байт    |      1 байт      |
| Заголовок (50 символов) |   50 байт    |     25 байт      |
| Описание (500 символов) |   500 байт   |     300 байт     |
| Вложения (Изображения)  |    512 Мб    |       5 Мб       |
| Стоимость               |   4 байта    |     16 байт      |

Пусть среднее число подписок у автора - 5 штук.
> Итог: ~ 25 Мб

- Размер поста

| Поле                           | Размер (max) | Размер (average) |
| ------------------------------ | :----------: | :--------------: |
| Id                             |   4 байта    |     4 байта      |
| Заголовок (100 символов)       |   100 байт   |     50 байт      |
| Основной текст (3000 символов) |  3000 байт   |    1500 байт     |
| Вложения (Изображения)         |    512 МБ    |      20 Мб       |
| Уровень доступа                |    1 байт    |      1 байт      |
> Размер поста: ~ 20 Мб

Информации о количестве постов нет, поэтому посчитаем примерное число постов с 2019 года [^3].
> Число постов: (0.2 * 365) * (136 000 + 185 000 + 212 000 + 220 000) = **54 969 000**
>
> Среднее число постов на 1 автора: 54 969 000 / 220 000 ~ 250
>
> Итог: 250 * 20 Мб ~ 4.88 Гб

| Хранилище   | Средний размер |
| ----------- | :------------: |
| Покрователь |    223 байт    |
| Автор       |    4.93 Гб     |

### 2.2 Технические мерики
#### 2.2.1 Хранилище

- Хранение постов (без вложений):
  > 54 969 000 * (4 + 50 + 1500 + 1) байт = 79.6 Гб

- Хранение изображений:
  > 54 969 000 * 20 Мб + 220 000 * (5 + 25) Мб = 1.029 Пб

#### 2.2.2 Сетевой трафик
Возьмем 3 наиболее частых запроса из продуктовых метрик: Просмотр поста, Публикация поста и Оплата.
За пиковый трафик примем увеличение в 2 раза.
- Просмотр поста:

  > Пиковый: 15M * 2 * 10 * 20 Мб / 86400 с = 542.53 Гбит/с
  >
  > Суточный: 15М * 10 * 20 Мб = 2.794 Пб/сутки

- Публикация поста:
  > Пиковый: 250K * 2 * 0.2 * 20 Мб / 86400 с ~ 0.18 Гбит/с
  >
  > Суточный: 250K * 0.2 * 20 Мб = 976.56 Гб/сутки

- Оплата:
  Примем, что для оплаты нужно отправить данные размером 1 Кб
  > Пиковый: 6M * 2 * 0.032 * 1 Кб / 86400 с = 35.52 Кбит/с
  >
  > Суточный: 6M * 0.032 * 1 Кб ~  0.18 Гб/сутки

| Тип запроса      | Пиковое потребление в теченнии суток | Суммарный суточный |
| ---------------- | ------------------------------------ | ------------------ |
| Проcмотр поста   | 542.53 Гбит/с                        | 2.794 Пб/сутки     |
| Публикация поста | 0.18 Гбит/с                          | 976.56 Гб/сутки    |
| Оплата           | 35.52 Кбит/с                         | 0.18 Гб/сутки      |

#### 2.2.3 RPS
За пиковый трафик примем увеличение в 2 раза.

#### 2.2.3.1 Общее
- Авторизация
  > (6M + 250К) * 0.02 / 86400 ~ 1.44 RPS
- Регистрация
  > 970 / 86400 ~ 0.01 RPS
#### 2.2.3.2 Автор
- Публикация поста:
  > 250K * 0.2 / 86400 ~ 0.6 RPS
- Вывод средств
  > 250K * 0.033 / 86400 ~ 0.0095 RPS
#### 2.2.3.3 Покровитель
- Просмотр поста:
  > 15M * 10 / 86400 ~ 1736 RPS
- Оплата:
  > 6M * 0.032 / 86400 ~ 2.22 RPS

| Тип запроса      |  RPS   | Пиковый RPS |
| ---------------- | :----: | :---------: |
| Авторизация      |  1.44  |    2.88     |
| Регистрация      |  0.01  |    0.02     |
| Публикация поста |  0.6   |     1.2     |
| Вывод средств    | 0.0095 |    0.019    |
| Просмотр поста   |  1736  |    3 472    |
| Оплата           |  2.22  |    4.44     |

## 3. Логическая схема
```mermaid
  erDiagram
    USERS {
      serial id PK
      varchar(256) email UK
      char(60) password
    }
    AUTHORS {
      serial id PK
      integer userID FK
      varchar(64) username
      text about
      uuid avatar FK
      integer balance
    }
    PATROLS {
      serial id PK
      integer userID FK
      varchar(64) username
    }
    POSTS {
      serial id PK
      integer authorID FK
      interger subscriptionID FK
      text content
    }
    SUBSCRIPTION_CARDS {
      serial id PK
      interger authorID FK
      smallint level
      varchar(50) title
      text description
      uuid image
      integer cost
    }
    SESSIONS {
      varchar(60) cookie UK
      interger userID FK
      date expire
    }
    SUBSCRIPTIONS {
      bigserial id PK
      interger patrolID FK
      interger subscriptionID FK
      date expire
    }
    IMAGES {
      uuid label PK
      interger subscriptionID
    }

    AUTHORS ||--o{ POSTS: id
    AUTHORS ||--|| IMAGES: avatar
    USERS ||--o| AUTHORS: id
    USERS ||--o| PATROLS: id
    USERS ||--o{ SESSIONS: id
    AUTHORS ||--o{ SUBSCRIPTION_CARDS: id
    POSTS }o--|| SUBSCRIPTION_CARDS: id
    SUBSCRIPTION_CARDS ||--|| IMAGES: image
    PATROLS ||--o{ SUBSCRIPTIONS: id
    SUBSCRIPTION_CARDS ||--o{ SUBSCRIPTIONS: id
```

### 3.1 Описание
1. Таблица **USER** хранит основные данные всех пользователей сервиса.
2. Таблица **SESSION** служит для хранение всех текущих сессий пользователей из таблицы **USER**.
3. Таблица **PATROLS** хранит данные всех покровителей.
4. Таблица **AUTHORS** хранит данные всех авторов.
5. Таблица **POSTS** хранит все посты и связана с **AUTHORS** и **SUBSCRIPTION_CARDS**. Поле **subscriptionID** может быть **NULL**, что будет значить, что пост доступен без подписи.
6. Таблица **SUBSCRIPTION_CARDS** хранит все подписки. каждая подписка принадлежит какому-то автору (**AUTHORS**).
7. Таблица **SUBSCRIPTIONS** организовывает связь многие ко многим между покровителями и подписками авторов. Также хранит дату окончания подписки.
8. Таблицы **IMAGES** хранит uuid изображения, по которому его можно получить, и уровень подписки, который необходим для получения изображения. Поле **subscriptionID** может быть **NULL**, что будет значить, что изображения доступен без подписи (для аватарок, изображений с описаний и подписок и общедоступных постов).

## 4. Физическая схема
> _PG - PostgreSQL \
> _S3 - S3 объектное хранилище \
> _RD - Redis
```mermaid
  erDiagram
    USERS_PG {
      serial id PK
      varchar(256) email UK
      char(60) password
    }
    AUTHORS_PG {
      serial id PK
      integer userID FK
      varchar(64) username
      text about
      uuid avatar FK
      integer balance
    }
    PATROLS_PG {
      serial id PK
      integer userID FK
      varchar(64) username
    }
    POSTS_PG {
      serial id PK
      integer authorID FK
      interger subscriptionID FK
      text content
    }
    SUBSCRIPTION_CARDS_PG {
      serial id PK
      interger authorID FK
      smallint level
      varchar(50) title
      text description
      uuid image
      integer cost
    }
    SESSIONS_RD {
      varchar(60) cookie UK
      interger userID FK
      date expire
    }
    SUBSCRIPTIONS_PG {
      bigserial id PK
      interger patrolID FK
      interger subscriptionID FK
      date expire
    }
    IMAGES_PG {
      uuid label PK
      interger subscriptionID
    }

    IMAGES_PG ||--|| IMAGES_S3: uuid

    AUTHORS_PG ||--o{ POSTS_PG: id
    AUTHORS_PG ||--|| IMAGES_PG: avatar
    USERS_PG ||--o| AUTHORS_PG: id
    USERS_PG ||--o| PATROLS_PG: id
    USERS_PG ||--o{ SESSIONS_RD: id
    AUTHORS_PG ||--o{ SUBSCRIPTION_CARDS_PG: id
    POSTS_PG }o--|| SUBSCRIPTION_CARDS_PG: id
    SUBSCRIPTION_CARDS_PG ||--|| IMAGES_PG: image
    PATROLS_PG ||--o{ SUBSCRIPTIONS_PG: id
    SUBSCRIPTION_CARDS_PG ||--o{ SUBSCRIPTIONS_PG: id
```
### 4.1 Индексы
1. Индексы на все первичные ключи (по умолчанию).
2. Индексирование поля **username** в таблице **AUTHORS**.
3. Индексирование поля **authorID** в таблице **POSTS**.
4. Индексирование поля **email** в таблице **USERS**.
5. Индексирование поля **patrolID** в таблице **SUBSCRIPTIONS**.
### 4.2 Шардирование
1. **USERS** - шардирование по хэш функции поля **email**.
2. **PATROLS** - шардирование по хэш функции поля **userID**.
3. **POSTS** - шардирование по хэш функции поля **authorID**.
### 4.3 Репликация
Репкликация всей базв данных Postgres по схеме 1 Master - 2 Slave.
## Список литературы
[^1]: [Domain Overview:
patreon.com](https://www.semrush.com/analytics/overview/?q=patreon.com&searchType=domain)

[^2]: [PATREON STATISTICS 2023: USERS, REVENUE & MARKET SHARE](https://earthweb.com/patreon-statistics/)

[^3]: [Patreon Statistics](https://graphtreon.com/patreon-stats)

[^4]: [The first-ever Patreon Creator Census](https://blog.patreon.com/en-GB/the-first-ever-patreon-creator-census)

[^5]: [How Often Should You Change Your Passwords?](https://www.keepersecurity.com/blog/2020/12/21/how-often-should-you-change-your-password-keeper-security/)

[^6]: [Patreon Creators Statistics](https://graphtreon.com/patreon-creators)

[^7]: [](https://influencermarketinghub.com/patreon-stats-revenue-users/)
