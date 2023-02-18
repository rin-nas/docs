Определение местоположения (города) пользователя по IP-адресу применяется, когда пользователь только зашёл на веб-сайт

| Название БД | Ссылка на скачивание бесплатной (урезанной) БД | URL демо страницы  <br>(IP => location) | PHP класс для чтения БД в бинарном формате из файла | Описание |
| --- | --- | --- | --- | --- |
| Sypex Geo | [скачать](https://sypexgeo.net/) | [демо](https://sypexgeo.net/ru/demo/) | [sypexgeo](https://sypexgeo.net/ru/download/) | Используется бесплатная версия на Rabota.Ru. В бесплатной версии есть несущественные (для нас) ограничения: задержка в актуальности на 2 месяца, обновление БД с периодичностью 2 раза в месяц. |
| MaxMind | [скачать](https://dev.maxmind.com/geoip/geoip2/geolite2/) | [демо](https://dev.maxmind.com/geoip/geoip2/javascript/) | [github](https://github.com/maxmind/MaxMind-DB-Reader-php) | В ветке [R16-6034](https://tasks.rabota.space/browse/R16-6034?focusedCommentId=31466&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-31466) эксперимент подключением этой БД |
| IP2Location | [скачать](https://lite.ip2location.com/database/ip-country-region-city-latitude-longitude-zipcode-timezone) | [демо](https://www.ip2location.com/demo) | [github](https://github.com/chrislim2888/IP2Location-PHP-Module) | [https://www.ip2location.com/free/city-multilingual](https://www.ip2location.com/free/city-multilingual) (чтобы получать название региона на русском языке) |
| IpStack | нет | [демо](https://ipstack.com/) |     |     |

  

Ссылки по теме
==============

How does GeoIP work

* [https://stackoverflow.com/questions/47186210/where-does-raw-geoip-data-come-from](https://stackoverflow.com/questions/47186210/where-does-raw-geoip-data-come-from)
* [https://www.quora.com/How-does-GeoIP-work](https://www.quora.com/How-does-GeoIP-work)
* [https://www.quora.com/How-does-IP-geolocation-service-providers-collect-data-or-how-does-IP-geolocation-databases-are-filled](https://www.quora.com/How-does-IP-geolocation-service-providers-collect-data-or-how-does-IP-geolocation-databases-are-filled)

Сравнение БД

1.  [https://www.trustradius.com/compare-products/ip2location-vs-maxmind](https://www.trustradius.com/compare-products/ip2location-vs-maxmind) (ip2location vs Maxmind) — здесь ip2location лидирует
2.  [https://itnan.ru/post.php?c=1&p=269027](https://itnan.ru/post.php?c=1&p=269027) (Сравнение баз MaxMind и Sypex) — здесь Sypex лидирует

Прочее

* Получить пример IP для нужного города: [https://4it.me/getlistip?q=Казань](https://4it.me/getlistip?q=%D0%9A%D0%B0%D0%B7%D0%B0%D0%BD%D1%8C) (В форме выбрать "RU-Center")
* БД Российских IP адресов регионов от Ru-Center ([nic.ru](http://nic.ru/)): [http://ipgeobase.ru/cgi-bin/AdvSearch.cgi](http://ipgeobase.ru/cgi-bin/AdvSearch.cgi)
