# Псевдонимы email

Email в верхнем и нижнем регистре считаются одинаковыми: пример: [MIKE77@MAIL.RU](mailto:MIKE77@MAIL.RU) = [mike77@mail.ru](mailto:mike77@mail.ru)

У одного и того же почтового ящика могут быть несколько псевдонимов доменов:

1.  [Яндекс](https://yandex.ru/blog/company/13860): @[yandex.ru](http://yandex.ru) = @[yandex.ua](http://yandex.ua) = @[narod.ru](http://narod.ru) = @[ya.ru](http://ya.ru) = @[yandex.com](http://yandex.com)
2.  [Mail.ru](https://otvet.mail.ru/question/11228482): @[mail.ru](http://mail.ru) = @[inbox.ru](http://inbox.ru) = @[bk.ru](http://bk.ru) = @[list.ru](http://list.ru)

Некоторые почтовые системы позволяют иметь псевдонимы для имени пользователя:

1.  Gmail позволяет в любом месте имени пользователя вставлять точки, которые потом игнорируются ([ivan.petrov@gmail.com](mailto:ivan.petrov@gmail.com) => [ivanpetrov@gmail.com](mailto:vanpetrov@gmail.com)). Поддерживается плюс-адресация, которая позволяет пользователям регистрироваться в различных сервисах ([ivan.petrov+facebook@gmail.com](mailto:ivan.petrov+facebook@gmail.com) = [ivanpetrov@gmail.com](mailto:ivanpetrov@gmail.com))
2.  Яндекс [считает](https://habrahabr.ru/company/yandex/blog/56866/) символы дефиса и точки одинаковыми ([ivan.petrov@yandex.ru](mailto:ivan.petrov@yandex.ru) = [ivan-petrov@yandex.ru](mailto:ivan-petrov@yandex.ru)). Плюс-адресация тоже поддерживается. 

На практике email валидируют и сохраняют в БД в исходном виде без нормализации (приведения к каноническому виду).

# Уникальность email

Если email является идентификатором входа на сайт, то адреса электронной почты должны быть **уникальными** в рамках профилей (учётных записей) пользователей. Для этого создают уникальный [индекс по выражению](https://postgrespro.ru/docs/postgresql/10/indexes-expressional) `lower(email)` (функциональный индекс).

# Уровни валидации email

1.  [синтаксис верный](https://regex101.com/r/Q4dsL5/14) (разрешены обособленные части имён на кириллице)
2.  домен существует ([проверка MX записи](http://php.net/getmxrr), если почтовый домен не существует, то письмо отправить нельзя)
3.  адрес не локальный (решается проверкой существования домена)
4.  домен отсутствует в справочнике [сервисов одноразовой почты](http://lifevinet.ru/inetservices/vremenay-pochta.html) и конкурентов ([hh.ru](http://hh.ru/), [superjob.ru](http://superjob.ru/), [zarplata.ru](http://zarplata.ru/), …)
    

# Ссылки по теме

1.  [https://en.wikipedia.org/wiki/Email_address](https://en.wikipedia.org/wiki/Email_address)
2.  [https://ru.wikipedia.org/wiki/Запись_MX](https://ru.wikipedia.org/wiki/Запись_MX)
3.  [https://ru.wikipedia.org/wiki/IDN](https://ru.wikipedia.org/wiki/IDN) (англ.Internationalized Domain Names — интернационализованные доменные имена)
4.  [https://www.punycoder.com/](https://www.punycoder.com/) — [Punycode](https://en.wikipedia.org/wiki/Punycode) converter (encoder and decoder)  
5.  [https://docs.zendframework.com/zend-validator/validators/email-address/](https://docs.zendframework.com/zend-validator/validators/email-address/)
6.  [https://docs.zendframework.com/zend-validator/validators/hostname/](https://docs.zendframework.com/zend-validator/validators/hostname/)
7.  Исходный код:
    1.  [https://git.rabota.space/rdw/rabota/x/-/blob/develop/App/Orm/Api/V4/Type/EmailType.php](https://git.rabota.space/rdw/rabota/x/-/blob/develop/App/Orm/Api/V4/Type/EmailType.php)
    2.  [https://github.com/zendframework/zend-validator/blob/master/src/EmailAddress.php](https://github.com/zendframework/zend-validator/blob/master/src/EmailAddress.php)
8.  [https://m.habr.com/ru/company/globalsign/blog/481318/](https://m.habr.com/ru/company/globalsign/blog/481318/) — Взлом с помощью Юникода (на примере GitHub)
9.  [Email Address test cases](https://blogs.msdn.microsoft.com/testing123/2009/02/06/email-address-test-cases/)
10. **PostgreSQL domain [email](https://github.com/rin-nas/postgresql-patterns-library/blob/master/domains/email.sql)**
11. Репозитории, собирающие одноразовые емалы:
    1.  [https://github.com/ivolo/disposable-email-domains/blob/master/index.json](https://github.com/ivolo/disposable-email-domains/blob/master/index.json) (**12000!!!**)
    1.  [https://github.com/martenson/disposable-email-domains/blob/master/disposable\_email\_blacklist.conf](https://github.com/martenson/disposable-email-domains/blob/master/disposable_email_blacklist.conf) (2000+ записей)
    2.  [https://gist.github.com/adamloving/4401361#file-temporary-email-address-domains](https://gist.github.com/adamloving/4401361#file-temporary-email-address-domains)
    3.  [https://www.torvpn.com/en/disposable-email](https://www.torvpn.com/en/disposable-email)
    4.  [https://github.com/FGRibreau/mailchecker/blob/ed947043d14d0920fb45c60565ee979d464fa494/list.json](https://github.com/FGRibreau/mailchecker/blob/ed947043d14d0920fb45c60565ee979d464fa494/list.json)

# SQL запросы

Функция нормализации email
```sql
-- https://stackoverflow.com/questions/19230584/emulating-mysqls-substring-index-in-pgsql
-- MySQL dialect
SELECT
  email,
  CASE
    -- https://habrahabr.ru/company/yandex/blog/56866/
    WHEN email RLIKE '@(yandex\.|narod\.ru|ya\.ru)' THEN CONCAT(REPLACE(SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', 1), '+', 1), '-', '.'), '@yandex.com')
    WHEN email RLIKE '@(mail|inbox|bk|list)\.ru' THEN CONCAT(SUBSTRING_INDEX(email, '@', 1), '@mail.ru')
    WHEN email RLIKE '@(rambler|ro|lenta|myrambler|autorambler)\.ru' THEN CONCAT(SUBSTRING_INDEX(email, '@', 1), '@rambler.ru')
    -- http://www.slate.com/blogs/future_tense/2013/08/01/dots_in_gmail_addresses_what_happens_if_you_leave_out_the_period.html
    WHEN email RLIKE '@gmail\.com' THEN CONCAT(REPLACE(SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', 1), '+', 1), '.', ''), '@gmail.com')
    WHEN email RLIKE '@facebook\.com' THEN CONCAT(REPLACE(SUBSTRING_INDEX(email, '@', 1), '.', ''), '@facebook.com')
  ELSE email
  END AS email_normalized
FROM profil
WHERE -- Yandex -- https://yandex.ru/blog/company/13860
         email LIKE '%_@yandex.%' -- @yandex.ru, @yandex.ua, @yandex.com
      OR email LIKE '%_@narod.ru' -- @narod.ru
      OR email LIKE '%_@ya.ru'    -- @ya.ru
      -- MailRu -- https://otvet.mail.ru/question/11228482
      OR email LIKE '%_@mail.ru'
      OR email LIKE '%_@inbox.ru'
      OR email LIKE '%_@bk.ru'
      OR email LIKE '%_@list.ru'
      -- Rambler
      OR email LIKE '%_@rambler.ru'
      OR email LIKE '%_@ro.ru'
      OR email LIKE '%_@lenta.ru'
      OR email LIKE '%_@myrambler.ru'
      OR email LIKE '%_@autorambler.ru'
      -- Google
      OR email LIKE '%_@gmail.com'
      -- Facebook
      OR email LIKE '%_@facebook.com'
LIMIT 300
```
  
Email в "чёрном" списке доменов одноразовой почты

```sql
-- http://lifevinet.ru/inetservices/vremenay-pochta.html
-- PostgreSQL dialect
SELECT COUNT(*)
FROM v3_person
WHERE email ILIKE ANY(ARRAY[
    '%@p33.org',
    '%@mvrht.net',
    '%@mailinator.com',
    '%@yopmail.fr',
    '%@yopmail.net',
    '%@cool.fr.nf',
    '%@jetable.fr.nf',
    '%@nospam.ze.tc',
    '%@nomail.xl.cx',
    '%@mega.zik.dj',
    '%@speed.1s.fr',
    '%@courriel.fr.nf',
    '%@moncourrier.fr.nf',
    '%@monemail.fr.nf',
    '%@monmail.fr.nf',
    '%@tempr.email',
    '%@discard.email',
    '%@discardmail.com',
    '%@discardmail.de',
    '%@spambog.com',
    '%@spambog.de',
    '%@spambog.ru',
    '%@0815.ru',
    '%@hulapla.de',
    '%@teewars.org',
    '%@pfui.ru',
    '%@0815.su',
    '%@sweetxxx.de',
    '%@smddr@mailforspam.com',
    '%@smddr@mfsa.ru',
    '%@sharklasers.com',
    '%@guerrillamail.info',
    '%@grr.la',
    '%@guerrillamail.biz',
    '%@guerrillamail.com',
    '%@guerrillamail.de',
    '%@guerrillamail.net',
    '%@guerrillamail.org',
    '%@guerrillamailblock.com',
    '%@pokemail.net',
    '%@spam4.me',
    '%@dropmail.me'
]);
```
