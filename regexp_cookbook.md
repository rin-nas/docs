# Регулярные выражения

## Виды соответствия
* Полное — вся строка соответствует шаблону регулярного выражения, которое всегда начинается с `^` и заканчивается на `$`.
* Частичное — часть строки соответствует шаблону регулярного выражения. Наличие символов `^` и `$` опционально.

## Методы обработки
* Проверка соответствия (валидация): [`RegExp.test()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)
* Поиск и захват подстрок: [`RegExp.exec()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec), [`String.search()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search)
* Поиск и разбиение на подстроки: [`String.split()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)
* Поиск, захват и замена подстрок: [`String.replace()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

## Склад готовых решений
* https://regex101.com/r/eVEGRY/1/ Захват IPv4 + IPv6. В PHP лучше применять готовый валидатор, см. [`filter_var()`](http://php.net/manual/en/function.filter-var.php)
* https://regex101.com/r/JVzBz2/3 Захват тегов с учётом их парности без вложенных таких же тегов (PCRE)
* https://regex101.com/r/CvlwKz/1 Захват тегов с учётом их парности без вложенных таких же тегов (JS)
* https://regex101.com/r/jwH6O2/4 Захват тегов с учётом их парности и вложенности друг в друга (рекурсивно) (PCRE)
* https://regex101.com/r/IVSo1x/1 Захват тегов с учётом их парности и вложенности друг в друга (рекурсивно) (JS) http://jsfiddle.net/rea4sxgn/ -- генератор рег. выражения до заданной глубины рекурсии
* https://jsfiddle.net/zqta1481/14/ Реализация JavaScript метода [`String.match()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match) с учётом рекурсии
* https://regex101.com/r/GtF2QA/8/ Проверка слова на английском языке во множественном числе
* https://regex101.com/r/iB63bg/2/ Проверка регулярного выражения на диалект ECMA 262 (JavaScript) (есть проверка на уникальность флагов)
* https://regex101.com/r/GQ1xKK/14/ Проверка ФИО на русском или английском языке
* https://regex101.com/r/WnauVT/6/ Проверка корректности даты в формате ГГГГ-ММ-ДД без проверки YYYY-02-29 в високосных годах (нужно доделать)
* https://regex101.com/r/CcSugS/10 Детектирование SQL на модификацию данных
* https://regex101.com/r/Q4dsL5/13 Валидация email

* https://regex101.com/r/r2ESLq/2/ Обработка многострочных комментариев в 2 раза эффективнее, чем `/\* .*? \*/`

## Валидация пароля

https://regex101.com/r/MOWCV3/9 

Пароль должен соответствовать следующим требованиям:
1. длина от 8 до 64 символов
2. допустимы только латинские буквы, цифры и знаки пунктуации
3. содержит хотябы одну цифру (0-9)
4. содержит хотябы одну латинскую прописную букву (A-Z)
5. содержит хотябы одну латинскую строчную букву (a-z)
6. содержит хотябы один знак пунктуации: `!"#$%&'()*+,-./:;<=>?@[\]^_``{|}~`
7. не имеют 6 и более подряд совпадающих символов типа "zzzzzz", "000000"
8. не имеет 6 и более подряд "плохих" последовательностей типа "123456", "abcdef", "qwerty"

## Детектирование бинарных данных

Ищем до первого найденного по рег. выражению `~[\x00-\x1f](?<![\x08\x09\x0c\x0a\x0d])~sSX`

[JSON](http://json.org/) не должен определяться как бинарный, поэтому исключаем все специальные символы по спецификации:
* `\b` represents the backspace character (`U+0008`)
* `\t` represents the character tabulation character (U+0009)
* `\f` represents the form feed character (U+000C)
* `\n` represents the line feed character (U+000A)
* `\r` represents the carriage return character (U+000D)


* https://m.habr.com/post/429548/ - Плагин «Rainbow CSV» как альтернатива Excel
* https://m.habr.com/post/343116/ - Как я написал приложение, которое за 15 минут делало то же самое, что и регулярн...
