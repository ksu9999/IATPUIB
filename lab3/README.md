# Практическая работа №3
Солдатенкова Ксения БИСО-03-20

## Цель работы

1.  Закрепить практические навыки использования языка программирования R
    для обработки данных

2.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

3.  Развить пркатические навыки использования функций обработки данных
    пакета dplyr – функцииselect(), filter(), mutate(), arrange(),
    group_by()

## Ход работы

Проанализировать встроенные в пакет nycflights13 наборы данных с помощью
языка R и ответить на вопросы:

``` r
library(nycflights13)
```

``` r
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

1\. Сколько встроенных в пакет nycflights13 датафреймов?

nycflights13::

Ответ: в пакет nycflights13 встроено 5 датафреймов: airlines, airports,
flights, planes, weather

2\. Сколько строк в каждом датафрейме?

``` r
airlines %>% nrow()
```

    [1] 16

``` r
airports %>% nrow()
```

    [1] 1458

``` r
flights %>% nrow()
```

    [1] 336776

``` r
planes %>% nrow()
```

    [1] 3322

``` r
weather %>% nrow()
```

    [1] 26115

3\. Сколько столбцов в каждом датафрейме?

``` r
airlines %>% ncol()
```

    [1] 2

``` r
airports %>% ncol()
```

    [1] 8

``` r
flights %>% ncol()
```

    [1] 19

``` r
planes %>% ncol()
```

    [1] 9

``` r
weather %>% ncol()
```

    [1] 15

4\. Как просмотреть примерный вид датафрейма?

``` r
weather %>% glimpse()
```

    Rows: 26,115
    Columns: 15
    $ origin     <chr> "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EW…
    $ year       <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013,…
    $ month      <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ day        <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ hour       <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, …
    $ temp       <dbl> 39.02, 39.02, 39.02, 39.92, 39.02, 37.94, 39.02, 39.92, 39.…
    $ dewp       <dbl> 26.06, 26.96, 28.04, 28.04, 28.04, 28.04, 28.04, 28.04, 28.…
    $ humid      <dbl> 59.37, 61.63, 64.43, 62.21, 64.43, 67.21, 64.43, 62.21, 62.…
    $ wind_dir   <dbl> 270, 250, 240, 250, 260, 240, 240, 250, 260, 260, 260, 330,…
    $ wind_speed <dbl> 10.35702, 8.05546, 11.50780, 12.65858, 12.65858, 11.50780, …
    $ wind_gust  <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 20.…
    $ precip     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
    $ pressure   <dbl> 1012.0, 1012.3, 1012.5, 1012.2, 1011.9, 1012.4, 1012.2, 101…
    $ visib      <dbl> 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10,…
    $ time_hour  <dttm> 2013-01-01 01:00:00, 2013-01-01 02:00:00, 2013-01-01 03:00…

5\. Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных
(представлено в наборах данных)?

``` r
length(unique(airlines$carrier))
```

    [1] 16

6\. Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
apname <- airports %>% filter(name == 'John F Kennedy Intl') %>% select(faa)
flights %>% filter(month == 5, dest == as.character(apname)) %>% nrow()
```

    [1] 0

7\. Какой самый северный аэропорт?

``` r
maxlat <- max(airports$lat, na.rm = TRUE)
airports %>% select(name,lat) %>% filter(lat==maxlat)
```

    # A tibble: 1 × 2
      name                      lat
      <chr>                   <dbl>
    1 Dillant Hopkins Airport  72.3

8\. Какой аэропорт самый высокогорный (находится выше всех над уровнем
моря)?

``` r
airports %>% arrange(desc(alt)) %>% select(name, alt) %>% top_n(1)
```

    Selecting by alt

    # A tibble: 1 × 2
      name        alt
      <chr>     <dbl>
    1 Telluride  9078

9\. Какие бортовые номера у самых старых самолетов?

``` r
planes %>% select(tailnum, year) %>% filter(year == min(year, na.rm = TRUE))
```

    # A tibble: 1 × 2
      tailnum  year
      <chr>   <int>
    1 N381AA   1956

10\. Какая средняя температура воздуха была в сентябре в аэропорту John
F Kennedy Intl (в градусах Цельсия).

``` r
apname <- airports %>% filter(name == 'John F Kennedy Intl') %>% select(faa)
weather %>% filter(month == 9, origin == as.character(apname)) %>% summarise('temp' = ((temp_mean = mean(temp, 0, na.rm = TRUE))-32)*5/9)
```

    # A tibble: 1 × 1
       temp
      <dbl>
    1  19.4

11\. Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
flights %>% filter(month == 6) %>% group_by(carrier) %>% summarize(n_flights = n()) %>% arrange(desc(n_flights)) %>% top_n(1)
```

    Selecting by n_flights

    # A tibble: 1 × 2
      carrier n_flights
      <chr>       <int>
    1 UA           4975

12\. Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
flights %>% filter(year == 2013) %>% group_by(carrier) %>% filter(dep_delay > 0, arr_delay > 0) %>% summarise('delays' = n()) %>% arrange(desc(delays)) %>% top_n(1)
```

    Selecting by delays

    # A tibble: 1 × 2
      carrier delays
      <chr>    <int>
    1 EV       19183

## Вывод

Проанализировали встроенные в пакет nycflights13 наборы данных с помощью
языка R
