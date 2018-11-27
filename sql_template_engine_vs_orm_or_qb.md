
# Сравнение SQL шаблонизатора vs ORM или QueryBuilder

## Введение
Принцип [KISS](https://ru.wikipedia.org/wiki/KISS_(%D0%BF%D1%80%D0%B8%D0%BD%D1%86%D0%B8%D0%BF)) утверждает, что большинство систем работают лучше всего, если они остаются простыми, а не усложняются. В области проектирования IT решений простота должна быть одной из ключевых целей, и следует избегать ненужной сложности.

* Описание принципов работы [SQL шаблонизатора](https://github.com/rin-nas/sql-template-engine/blob/master/README.md)
* По [ORM](https://ru.wikipedia.org/wiki/ORM) ознакомьтесь [с этой статьёй на Хабре](https://m.habr.com/company/pgdayrussia/blog/328690/).
* По QueryBuilder ознакомьтесь с разделом документации [Laravel](http://laravel.su/docs/5.5/queries) и/или [Doctrine](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/query-builder.html).

## Сокращения
* ORM — Object-Relational Mapping
* QB — QueryBuilder

## Таблица сравнения
Критерий | SQL шаблонизатор | ORM или QueryBuilder
---------|------------------------|---------------------
Порог вхождения программиста перед началом работы| Меньше. Достаточно изучить десять простых типов меток-заменителей и одно правило условного блока| Больше. Нужно изучить десятки методов с их параметрами
Перегенерация моделей каждый раз при изменении схемы БД| Не нужно | Нужно. Это занимает лишнее время, в логике генератора могут быть ошибки.
Скорость генерации SQL | Быстрее. Простой парсер без использования регулярных выражений работает очень быстро. Парсер на базе рег. выражений работает не медленнее ORM или QB | Медленнее. Ещё один слой абстакции с дополнительной логикой, которая может содержать ошибки
Размер исходного кода | 5K - 10К | 50К и более | 
Быстрое создание нового запроса | Да. Только расстановка меток-заменителей в SQL запросе | Нет. Необходимо переписывать SQL запрос после его создания в набор методов ORM/QB, это лишние затраты времени
Быстрые правки в логике запроса | Да. В любом месте можно дописать сколько угодно сложный SQL код. | Нет. При усложнении логики возможностей ORM уже не хватает и тогда нужно переписывать обратно в SQL. Запрос в QB может стать слабо читабельным и трудноподдерживаемым.
Оптимальность генерации sql | Да. Выполяется именно тот запрос, который задумал разработчик. | Нет. Вместо 1 sql запроса их может быть десятки, что плохо сказывается на общем времени выполнения
Результат в виде вложенных объектов | Да. За 1 SQL запрос можно получить результат сколь угодно сложной структуры, возвращая [JSON](https://ru.wikipedia.org/wiki/JSON) и затем декодируя его. По сути это аналог объектов| Да. Но на каждый объект будет как минимум 1 SQL запрос

## Примеры кода

### SQL
```
SELECT MAX("v3_response"."id")
FROM "v3_response"
         JOIN "v3_resume" ON ("v3_resume"."id" = "v3_response"."resume_id")
         JOIN "v3_vacancy" ON ("v3_vacancy"."id" = "v3_response"."vacancy_id")
         JOIN "v3_company" ON (COALESCE("v3_vacancy"."company_id", "v3_vacancy"."real_employer_id") = "v3_company"."id")
         JOIN "v3_person" AS "resumePerson" ON ("v3_resume"."person_id" = "resumePerson"."id")
         JOIN "v3_person" AS "vacancyPerson" ON ("v3_resume"."person_id" = "vacancyPerson"."id")
WHERE (("v3_response"."modified_date" >= '2018-11-26 00:00:00') AND
       ("v3_response"."modified_date" < '2018-11-27 00:00:00') AND
       ("v3_response"."has_new_for_worker" IS TRUE) AND
       ("v3_response"."is_deleted_by_worker" IS FALSE) AND
       ("v3_response"."at_employer_side" IS FALSE) AND
       ("v3_response"."status" = 1) AND ("v3_company"."is_approve" IS TRUE) AND
       ("v3_company"."is_spam" IS FALSE) AND
       ("v3_company"."is_outstaffing" IS FALSE) AND
        NOT EXISTS(SELECT 1
                   FROM "v3_company_colored_tag"
                   WHERE (("v3_company_colored_tag"."status" = 1) AND
                          ("v3_company_colored_tag"."colored_tag_id" = 148) AND
                          ("v3_company_colored_tag"."company_id" = "v3_company"."id"))) AND
       ("resumePerson"."is_spammer" IS FALSE) AND
       ("vacancyPerson"."is_spammer" IS FALSE) AND
       NOT EXISTS(SELECT 1
                 FROM "v3_person_company_black"
                 WHERE (("v3_person_company_black"."company_id" = "v3_company"."id") AND
                        ("v3_person_company_black"."person_id" = "v3_resume"."person_id"))))
GROUP BY "v3_resume"."person_id"
```

### ORM

SQL код выше был сгенерирован вот этим кодом на PHP:

```
$responseTable = DAO::v3_response()->getTable();
$resumeTable   = DAO::v3_resume()->getTable();
$vacancyTable  = DAO::v3_vacancy()->getTable();
$employerTable = DAO::v3_company()->getTable();
$personalBlackTable = DAO::v3_personEmployerBlack()->getTable();
$personTable   = DAO::v3_person()->getTable();

$query = OSQL::select()->
    from($responseTable)->
    join(
      $resumeTable,
      Expression::eq(
        DBField::create('id', $resumeTable),
        DBField::create('resume_id', $responseTable)
      )
    )->
    join(
      $vacancyTable,
      Expression::eq(
        DBField::create('id', $vacancyTable),
        DBField::create('vacancy_id', $responseTable)
      )
    )->
    // надо left, если нужны отклики по вакансиям без компании - маловероятно
    join(
      $employerTable,
      Expression::eq(
        DAO::v3_vacancy()->getCoalescedEmployerIdExpression(),
        DBField::create('id', $employerTable)
      )
    )->
    join(
      $personTable,
      Expression::eq(
        DBField::create('person_id', $resumeTable),
        DBField::create('id', 'resumePerson')
      ),
      'resumePerson'
    )->
    join(
      $personTable,
      Expression::eq(
        DBField::create('person_id', $resumeTable),
        DBField::create('id', 'vacancyPerson')
      ),
      'vacancyPerson'
    )->
    get(SQLFunction::create('MAX', DBField::create('id', $responseTable)))->
    where(
      Expression::andBlock(
                  Expression::gtEq(
                      DBField::create('modified_date', $responseTable),
                      $start
                  ),
                  Expression::lt(
                      DBField::create('modified_date', $responseTable),
                      $stop
                  ),
        Expression::isTrue(
          DBField::create('has_new_for_worker', $responseTable)
        ),
        Expression::isFalse(
          DBField::create('is_deleted_by_worker', $responseTable)
        ),
        Expression::isFalse(
          DBField::create('at_employer_side', $responseTable)
        ),
        Expression::eq(
          DBField::create('status', $responseTable),
          DBValue::create(v3_Response::STATUS_INVITED)
        ),
        Expression::isTrue(
          DBField::create('is_approve', $employerTable)
        ),
        Expression::isFalse(
          DBField::create('is_spam', $employerTable)
        ),
        Expression::isFalse(
          DBField::create('is_outstaffing', $employerTable)
        ),
        Expression::notExists(
          OSQL::select()->
            get(DBValue::create(1))->
            from(DAO::v3_CompanyColoredTag()->getTable())->
            where(
              Expression::andBlock(
                Expression::eq(
                  DBField::create('status', DAO::v3_CompanyColoredTag()->getTable()),
                  DBValue::create(v3_CompanyColoredTag::STATUS_ADDED)
                ),
                Expression::eq(
                  DBField::create('colored_tag_id', DAO::v3_CompanyColoredTag()->getTable()),
                  DBValue::create(v3_ColoredTag::OUTSTAFFING)
                ),
                Expression::eq(
                  DBField::create('company_id', DAO::v3_CompanyColoredTag()->getTable()),
                  DBField::create('id', $employerTable)
                )
              )
            )
        ),
        Expression::isFalse(
          DBField::create('is_spammer', 'resumePerson')
        ),
        Expression::isFalse(
          DBField::create('is_spammer', 'vacancyPerson')
        ),
        Expression::notExists(
          OSQL::select()->
            from($personalBlackTable)->
            get(DBValue::create(1))->
            where(
              Expression::andBlock(
                Expression::eq(
                  DBField::create('company_id', $personalBlackTable),
                  DBField::create('id', $employerTable)
                ),
                Expression::eq(
                  DBField::create('person_id', $personalBlackTable),
                  DBField::create('person_id', $resumeTable)
                )
              )
            )
        )
      )
    )->
    groupBy(
      DBField::create('person_id', $resumeTable)
    );
```
