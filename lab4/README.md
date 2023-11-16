# Практическая работа №4
Солдатенкова Ксения БИСО-03-20

## Цель работы

1\. Зекрепить практические навыки использования языка программирования R
для обработки данных

2\. Закрепить знания основных функций обработки данных экосистемы
tidyverse языка R

3\. Закрепить навыки исследования метаданных DNS трафика

## Ход работы

### Подготовка данных

1\. Импортируйте данные DNS

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readr)
```

``` r
header_df <- read.csv('C:/Users/user/Documents/IATPUIB/lab4/header.csv')
```

``` r
header_df
```

              Field       Type
    1           ts       time 
    2          uid      string
    3           id      recor 
    4                       d 
    5        proto       proto
    6     trans_id       count
    7        query      string
    8       qclass       count
    9  qclass_name      string
    10       qtype       count
    11  qtype_name      string
    12       rcode       count
    13  rcode_name      string
    14          QR       bool 
    15          AA       bool 
    16       TC RD  bool bool 
    17          RA       bool 
    18           Z       count
    19     answers      vector
    20        TTLs      vector
    21    rejected       bool 
                                                                                           Description
    1                                                                    Timestamp of the DNS request 
    2                                                                     Unique id of the connection 
    3                                                ID record with orig/resp host/port. See conn.log 
    4                                                                                                 
    5                                                        Protocol of DNS transaction – TCP or UDP 
    6                                       16 bit identifier assigned by DNS client; responses match 
    7                                                                Domain name subject of the query 
    8                                                                Value specifying the query class 
    9                                           Descriptive name of the query class (e.g. C_INTERNET) 
    10                                                                Value specifying the query type 
    11                                                     Name of the query type (e.g. A, AAAA, PTR) 
    12                                                        Response code value in the DNS response 
    13                                 Descriptive name of the response code (e.g. NOERROR, NXDOMAIN) 
    14                                        Was this a query or a response? T = response, F = query 
    15                                    Authoritative Answer. T = server is authoritative for query 
    16 Truncation. T = message was truncated Recursion Desired. T = request recursive lookup of query 
    17                                     Recursion Available. T = server supports recursive queries 
    18                                      Reserved field, should be zero in all queries & responses 
    19                                           List of resource descriptions in answer to the query 
    20                                                               Caching intervals of the answers 
    21                                               Whether the DNS query was rejected by the server 

``` r
dns_log <- read.csv("C:/Users/user/Documents/IATPUIB/lab4/dns.log", sep='\t')
```

``` r
head(dns_log, 10)
```

       X1331901005.510000 CWGtK431H9XuaTN4fi X192.168.202.100 X45658
    1          1331901006 CWGtK431H9XuaTN4fi  192.168.202.100  45658
    2          1331901015  C36a282Jljz7BsbGH   192.168.202.76    137
    3          1331901016  C36a282Jljz7BsbGH   192.168.202.76    137
    4          1331901017  C36a282Jljz7BsbGH   192.168.202.76    137
    5          1331901006  C36a282Jljz7BsbGH   192.168.202.76    137
    6          1331901007  C36a282Jljz7BsbGH   192.168.202.76    137
    7          1331901007  C36a282Jljz7BsbGH   192.168.202.76    137
    8          1331901006 ClEZCt3GLkJdtGGmAa   192.168.202.89    137
    9          1331901007 ClEZCt3GLkJdtGGmAa   192.168.202.89    137
    10         1331901008 ClEZCt3GLkJdtGGmAa   192.168.202.89    137
       X192.168.27.203 X137 udp X33008
    1   192.168.27.203  137 udp  33008
    2  192.168.202.255  137 udp  57402
    3  192.168.202.255  137 udp  57402
    4  192.168.202.255  137 udp  57402
    5  192.168.202.255  137 udp  57398
    6  192.168.202.255  137 udp  57398
    7  192.168.202.255  137 udp  57398
    8  192.168.202.255  137 udp  62187
    9  192.168.202.255  137 udp  62187
    10 192.168.202.255  137 udp  62187
                    X..x00.x00.x00.x00.x00.x00.x00.x00.x00.x00.x00.x00.x00.x00 X1
    1  *\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00  1
    2                                                                 HPE8AA67  1
    3                                                                 HPE8AA67  1
    4                                                                 HPE8AA67  1
    5                                                                     WPAD  1
    6                                                                     WPAD  1
    7                                                                     WPAD  1
    8                                                                   EWREP1  1
    9                                                                   EWREP1  1
    10                                                                  EWREP1  1
       C_INTERNET X33 SRV X0 NOERROR     F   F.1   F.2   F.3 X1.1 X. X..1   F.4
    1  C_INTERNET  33 SRV  0 NOERROR FALSE FALSE FALSE FALSE    1  -    - FALSE
    2  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    3  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    4  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    5  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    6  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    7  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    8  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    9  C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE
    10 C_INTERNET  32  NB  -       - FALSE FALSE  TRUE FALSE    1  -    - FALSE

2\. Добавьте пропущенные данные о структуре данных (назначении столбцов)

``` r
header_df[3, "Field"] <- "id.orig_h"
header_df[3, "Type"] <- "addr"
header_df[4, "Field"] <- "id.resp_h"
header_df[4, "Type"] <- "addr"
header_df[4, "Description"] <- "Response IP"
```

``` r
header_df_n <- header_df %>% add_row(Field = 'id.orig_p', Type = 'port', Description = 'Origin port', .before = 4)
```

``` r
header_df_n <- header_df_n %>% add_row(Field = 'id.resp_p', Type = 'port', Description = 'Response port', .before = 6)
```

``` r
header_df_n
```

              Field       Type
    1           ts       time 
    2          uid      string
    3     id.orig_h       addr
    4     id.orig_p       port
    5     id.resp_h       addr
    6     id.resp_p       port
    7        proto       proto
    8     trans_id       count
    9        query      string
    10      qclass       count
    11 qclass_name      string
    12       qtype       count
    13  qtype_name      string
    14       rcode       count
    15  rcode_name      string
    16          QR       bool 
    17          AA       bool 
    18       TC RD  bool bool 
    19          RA       bool 
    20           Z       count
    21     answers      vector
    22        TTLs      vector
    23    rejected       bool 
                                                                                           Description
    1                                                                    Timestamp of the DNS request 
    2                                                                     Unique id of the connection 
    3                                                ID record with orig/resp host/port. See conn.log 
    4                                                                                      Origin port
    5                                                                                      Response IP
    6                                                                                    Response port
    7                                                        Protocol of DNS transaction – TCP or UDP 
    8                                       16 bit identifier assigned by DNS client; responses match 
    9                                                                Domain name subject of the query 
    10                                                               Value specifying the query class 
    11                                          Descriptive name of the query class (e.g. C_INTERNET) 
    12                                                                Value specifying the query type 
    13                                                     Name of the query type (e.g. A, AAAA, PTR) 
    14                                                        Response code value in the DNS response 
    15                                 Descriptive name of the response code (e.g. NOERROR, NXDOMAIN) 
    16                                        Was this a query or a response? T = response, F = query 
    17                                    Authoritative Answer. T = server is authoritative for query 
    18 Truncation. T = message was truncated Recursion Desired. T = request recursive lookup of query 
    19                                     Recursion Available. T = server supports recursive queries 
    20                                      Reserved field, should be zero in all queries & responses 
    21                                           List of resource descriptions in answer to the query 
    22                                                               Caching intervals of the answers 
    23                                               Whether the DNS query was rejected by the server 

3\. Преобразуйте данные в столбцах в нужный формат

``` r
field_n<-header_df_n[,1]
colnames(dns_log)<-field_n
dns_log$ts <- as.POSIXct(dns_log$ts, origin="1970-01-01")

glimpse(dns_log)
```

    Rows: 427,935
    Columns: 24
    $ `ts `          <dbl> 1331901006, 1331901015, 1331901016, 1331901017, 1331901…
    $ `uid `         <chr> "CWGtK431H9XuaTN4fi", "C36a282Jljz7BsbGH", "C36a282Jljz…
    $ id.orig_h      <chr> "192.168.202.100", "192.168.202.76", "192.168.202.76", …
    $ id.orig_p      <int> 45658, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137…
    $ id.resp_h      <chr> "192.168.27.203", "192.168.202.255", "192.168.202.255",…
    $ id.resp_p      <int> 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, 137, …
    $ `proto `       <chr> "udp", "udp", "udp", "udp", "udp", "udp", "udp", "udp",…
    $ `trans_id `    <int> 33008, 57402, 57402, 57402, 57398, 57398, 57398, 62187,…
    $ `query `       <chr> "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x…
    $ `qclass `      <chr> "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", …
    $ `qclass_name ` <chr> "C_INTERNET", "C_INTERNET", "C_INTERNET", "C_INTERNET",…
    $ `qtype `       <chr> "33", "32", "32", "32", "32", "32", "32", "32", "32", "…
    $ `qtype_name `  <chr> "SRV", "NB", "NB", "NB", "NB", "NB", "NB", "NB", "NB", …
    $ `rcode `       <chr> "0", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", …
    $ `rcode_name `  <chr> "NOERROR", "-", "-", "-", "-", "-", "-", "-", "-", "-",…
    $ `QR `          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
    $ `AA `          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
    $ `TC RD `       <lgl> FALSE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, …
    $ `RA `          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
    $ `Z `           <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 1…
    $ `answers `     <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", …
    $ `TTLs `        <chr> "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", "-", …
    $ `rejected `    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
    $ ts             <dttm> 2012-03-16 16:30:05, 2012-03-16 16:30:15, 2012-03-16 1…

4\. Просмотрите общую структуру данных с помощью функции glimpse()

``` r
glimpse(header_df)
```

    Rows: 21
    Columns: 3
    $ Field       <chr> "ts ", "uid ", "id.orig_h", "id.resp_h", "proto ", "trans_…
    $ Type        <chr> "time ", "string", "addr", "addr", "proto", "count", "stri…
    $ Description <chr> "Timestamp of the DNS request ", "Unique id of the connect…

### Анализ

4\. Сколько участников информационного обмена в сети Доброй Организации?

``` r
unique_part <- union(unique(dns_log$id.orig_h), unique(dns_log$id.resp_h))
unique_part %>% length()
```

    [1] 1359

5\. Какое соотношение участников обмена внутри сети и участников
обращений к внешним ресурсам?

``` r
internal_ip_pattern <- c("192.168.", "10.", "100.([6-9]|1[0-1][0-9]|12[0-7]).", "172.((1[6-9])|(2[0-9])|(3[0-1])).")
internal_ips <- unique_part[grep(paste(internal_ip_pattern, collapse = "|"), unique_part)]
internal <- sum(unique_part %in% internal_ips)
external <- length(unique_part) - internal

ratio <- internal / external
ratio
```

    [1] 15.57317

6\. Найдите топ-10 участников сети, проявляющих наибольшую сетевую
активность.

``` r
top_active <- dns_log %>%
  group_by(id.orig_h) %>%
  summarise(requests = n()) %>%
  arrange(desc(requests)) %>%
  top_n(10, requests)

print(top_active)
```

    # A tibble: 10 × 2
       id.orig_h       requests
       <chr>              <int>
     1 10.10.117.210      75943
     2 192.168.202.93     26522
     3 192.168.202.103    18121
     4 192.168.202.76     16978
     5 192.168.202.97     16176
     6 192.168.202.141    14967
     7 10.10.117.209      14222
     8 192.168.202.110    13372
     9 192.168.203.63     12148
    10 192.168.202.106    10784

7\. Найдите топ-10 доменов, к которым обращаются пользователи сети и
соответственное количество обращений.

``` r
top_domains <- dns_log %>%
  group_by(domain = tolower(`query `)) %>%
  summarise(requests = n()) %>%
  arrange(desc(requests)) %>%
  top_n(10, requests)

print(top_domains)
```

    # A tibble: 10 × 2
       domain                                                               requests
       <chr>                                                                   <int>
     1 "teredo.ipv6.microsoft.com"                                             39273
     2 "tools.google.com"                                                      14057
     3 "www.apple.com"                                                         13390
     4 "time.apple.com"                                                        13109
     5 "safebrowsing.clients.google.com"                                       11658
     6 "wpad"                                                                  11429
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00…    10401
     8 "isatap"                                                                 9712
     9 "44.206.168.192.in-addr.arpa"                                            7248
    10 "hpe8aa67"                                                               6929

8\. Опеределите базовые статистические характеристики (функция summary()
) интервала времени между последовательным обращениями к топ-10 доменам.

``` r
top_domains_filtered <- dns_log %>% 
  filter(tolower(`query `) %in% top_domains$domain) %>%
  arrange(ts)
time_interval <- diff(top_domains_filtered$ts)

summary(time_interval)
```

      Length    Class     Mode 
      137205 difftime  numeric 

9\. Часто вредоносное программное обеспечение использует DNS канал в
качестве канала управления, периодически отправляя запросы на
подконтрольный злоумышленникам DNS сервер. По периодическим запросам на
один и тот же домен можно выявить скрытый DNS канал. Есть ли такие IP
адреса в исследуемом датасете?

``` r
ip_domains <- dns_log %>%
  group_by(ip = id.orig_h, domain = tolower(`query `)) %>%
  summarise(requests = n(), .groups = 'drop') %>%
  filter(requests > 1)

unique_ips_with_period_req <- unique(ip_domains$ip)
unique_ips_with_period_req %>% length()
```

    [1] 240

``` r
head(unique_ips_with_period_req, 10)
```

     [1] "10.10.10.10"     "10.10.117.209"   "10.10.117.210"   "128.244.37.196" 
     [5] "169.254.109.123" "169.254.228.26"  "172.16.42.42"    "192.168.0.199"  
     [9] "192.168.0.3"     "192.168.100.130"

### Обогащение данных

10\. Определите местоположение (страну, город) и организацию-провайдера
для топ-10 доменов. Для этого можно использовать сторонние сервисы,
например
https://check-host.net/ip-info?host=www.apple.com&csrf_token=d6df11b1e07358714e561aefdedc2aa74974c758.

``` r
top_domains
```

    # A tibble: 10 × 2
       domain                                                               requests
       <chr>                                                                   <int>
     1 "teredo.ipv6.microsoft.com"                                             39273
     2 "tools.google.com"                                                      14057
     3 "www.apple.com"                                                         13390
     4 "time.apple.com"                                                        13109
     5 "safebrowsing.clients.google.com"                                       11658
     6 "wpad"                                                                  11429
     7 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00…    10401
     8 "isatap"                                                                 9712
     9 "44.206.168.192.in-addr.arpa"                                            7248
    10 "hpe8aa67"                                                               6929

1.teredo.ipv6.microsoft.com - IP: 20.112.250.133 - Country: USA - City:
Des Moines - Provider organization: Microsoft Corporation

2.tools.google.com - IP: 108.177.127.139 - Country: USA - City: Mountain
View - Provider organization: Google LLC

3.www.apple.com - IP: 2.21.16.234 - Country: Belgium - City: Brussels -
Provider organization: Akamai Technologies

4.time.apple.com - IP: 17.253.28.123 - Country: UK - City: Slough -
Provider organization: Apple Inc.

5.safebrowsing.clients.google.com - IP: 108.177.119.101 - Country: USA -
City: Mountain View - Provider organization: Google LLC

6.44.206.168.192.in-addr.arpa - IP: 44.206.168.192 - Country: USA -
City: Ashburn - Provider organization: Amazon.com
