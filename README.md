# Яндекс.Директ

## Содержание

* ### [1. Тема, целевая аудитория](#1)
* ### [2. Расчет нагрузки](#2)
* ### [3. Глобальная балансировка](#3)

## 1 Тема и целевая аудитория<a name="1"></a>

### 1.1 Тема
[Яндекс.Директ](https://direct.yandex.ru/) - система контекстной рекламы Яндекса

### 1.2 MVP

- Создание рекламных объявлений
- Показ рекламных объявлений
- Просмотр аналитики рекламной кампании
- Использование пульта управления рекламой: жалобы на рекламу

### 1.3 Целевая аудитория

Среднемесячная аудитория рекламной сети Яндекса - 91.5 млн. человек [^1]

## 2 Расчет нагрузки<a name="2"></a>

### 2.1 Расчеты

Дневная аудитория - 63.9 млн. человек

В виду недоступности статистики Яндекса
Для расчета количества показываемых в день объявлений воспользуемся статистикой схожего сервиса - Google Ads [^2]

Средняя цена за клик - $1.5
Годовая выручка сервиса = $200 млрд.
Получим количество кликов = 200 млрд. / 1.5 = 133 млрд. кликов
При ctr = 3.17%, рассчитаем количество показов: 133 млрд. / 0.03 = 4 443 триллионов показов / год, что равняется приблизительно 12 млрд. показов в день

Месячная аудитория Google Ads - 2.5 млрд. человек[^3], что в 27 раз больше месячной аудитории Яндекса.
Примем количество показов в день пропорциональным месячной аудитории. Получим приблизительное значение показов в день равное 450 млн.
RPS = 5208

Примем средний размер рекламного объявления равным 0.5МБ.
Тогда сетевой трафик составит 20 Гбит/c
Трафик в сутки = 215 ТБ

В 2015 году Яндекс опубликовал количесво рекламодателей [^4]: 394 000 человек. Попробуем найти приблизительное число рекламодателей в 2023 году, исходия из рекламной выручки. Рекламная выручка Яндекса в 2015 году составила 60 млрд. рублей[^4], в 2023 году - 182.5 млрд. рублей[^5].
Число рекламодателей в 2023 году найдём следующим образом: 394 000 * 182.5 / 60 = 1 198 416 рекламодателей.

Допустим, каждый рекламодатель в среднем запрашивает статистику своих рекламных раз в 20 минут.
Получим RPS = 1000

Из статистики модерации Яндекс рекламы[^6]:
Пульт управления рекламой используют 103 млн. раза в месяц => 40 RPS

Модерацию проходит 43 млн. объявлений в месяц => 16 RPS
При принятом среднем размере рекламног объявления в 0.5МБ суммарный объём составляет 20.5 ТБ в месяц
Общее число объявлений - 4.5 млрд, общий объём - 2145 ТБ

### 2.2 Продуктовые требования

| Метрика                             | Значение   |
| ----------------------------------- | ---------- |
| Месячная аудитория (MAU)            | 91.5 млн.  |
| Дневная аудитория (DAU)             | 63.9 млн.  |
| Кол-во рекламодателей               | 1.2 млн.   |
| Общее число объявлений              | 4.5 млрд.  |
| Подборов объявлений в день          | 450 млн.   |
| Новых объявлений в месяц            | 103 млн.   |
| Занимаемый всеми объявлениями объем | 2145 ТБ.   |

### 2.3 Технические требования

Примем пиковый RPS равным увеличенному в два раза среднему

| Запрос                              | RPS (средний)   | RPS (пиковый) |
| ----------------------------------- | --------------- | ------------  |
| Создание объявлений                 | 16              | 32            |
| Просмотр статистики                 | 1000            | 2000          |
| Использование пульта управления     | 40              | 80            |
| Подбор объявлений                   | 5208            | 10416         |

Примем пиковый трафик равным увеличенному в два раза среднему

| Запрос                              | Потребляемый трафик (средний) | Потребляемый трафик (пиковый) |
| ----------------------------------- | ----------------------------- | ----------------------------- |
| Создание объявлений                 | 8 Мбит/c                      | 16 Мбит/c                     |
| Подбор объявлений                   | 20 Гбит/с                     | 40 Гбит/c                     |

### 3 Глобальная балансировка<a name="3"></a>

Так как сервис расчитан на Российскую аудиторию, расположим единственный ЦОД в самой густонаселённой части страны - в Москве [^7]. 
Тогда согласно сервису "Looking.house"[^8] задержка сети от ЦОДа до разных городов России будет выглядеть следующим образом:

| Город          | Задержка |
| -------------- | -------- |
| Москва         | 2 мс     |
| Казань         | 25 мс    |
| Краснодар      | 38 мс    |
| Новосибирск    | 43 мс    |
| Красноярск     | 53 мс    |
| Хабаровск      | 102 мс   |
| Владивосток    | 113 мс   |

Как видно из таблицы, самая большая задержка - из Владивостока (113 мс)
Данные сервиса "Не Looking Glass" [^9] выдают похожие результаты

Так как задержка сети не является критичным параметром для сервиса рекламы, данную задержку можно считать приемлемой

### 4 Локальная балансировка<a name="4"></a>

Весь входящий в датацентр трафик будет 

## Источники
[^1]: [Статистика по аудитории Яндекса](https://radar.yandex.ru/)
[^2]: [Общая статистика Google Ads](https://www.digitalthirdcoast.com/blog/google-ads-statistics)
[^3]: [Статистика по аудитории Google Ads](https://prodima.vn/en/what-is-the-reach-of-the-google-display-network/)
[^4]: [Финансовый отчёт Яндекса 2015](https://yastatic.net/s3/ir-docs/financials/2015/4Q2015_RUS.pdf)
[^5]: [Финансовый отчёт Янедкса 2023](https://yastatic.net/s3/ir-docs/financials/2023/2Q2023_RUS.pdf)
[^6]: [Отчёт Яндекса по модерации рекламы](https://ya.ru/project/admoderation/2023#screen01)
[^7]: [Плотность населения РФ](https://ru.wikipedia.org/wiki/%D0%9F%D0%BB%D0%BE%D1%82%D0%BD%D0%BE%D1%81%D1%82%D1%8C_%D0%BD%D0%B0%D1%81%D0%B5%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F_%D1%81%D1%83%D0%B1%D1%8A%D0%B5%D0%BA%D1%82%D0%BE%D0%B2_%D0%A0%D0%BE%D1%81%D1%81%D0%B8%D0%B9%D1%81%D0%BA%D0%BE%D0%B9_%D0%A4%D0%B5%D0%B4%D0%B5%D1%80%D0%B0%D1%86%D0%B8%D0%B8)
[^8]: [Looking.house](https://looking.house/point.php?id=651)
[^9]: [Не Looking Glass RuVDS](https://lg.ruvds.com/)
