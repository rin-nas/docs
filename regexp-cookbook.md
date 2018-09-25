# Регулярные выражения -- склад готовых решений
## Обход вложенных тагов с учётом их парности (рекурсивные подмаски) (PCRE).

```php
$s = 'Начало.
     [span]правильно[/span]
     ошибка
     [span]правильно [span]правильно[/span] правильно[/span].
     ошибка
     [span]правильно [span]правильно[/span] правильно[/span].
     ошибка
     [span]правильно[/span]
     ошибка
     [span][/span]
     ошибка
     [span][span][/span][/span]
     ошибка
     [span]ошибка[span]ошибка[span]правильно[/span]
     ошибка
     Конец.';
$opentag  = '[span]';
$closetag = '[/span]';
$re_opentag  = preg_quote($opentag, '/');
$re_closetag = preg_quote($closetag, '/');
if (1)
{
#поиск тагов с вложенными тагами
#рег. выражение с раскруткой цикла, атомарной группировкой и запрещением сохранения состояний для возврата
$re = '/
          ' . $re_opentag . '
             [^\[]*+
             (?>
                 (?!' . $re_opentag . '|' . $re_closetag . ').
               | (?R)
             )*
          ' . $re_closetag . '
       /sxiSX';
}
else
{
#поиск тагов без вложенных тагов
$re = '/
          ' . $re_opentag . '
             [^\[]*+
             (?>
                  [^\[]*+
                | (?!' . $re_opentag . '|' . $re_closetag . ').
             )*
          ' . $re_closetag . '
       /sxiSX';
}
echo $re . "\r\n";
$time_start = microtime(true);
for ($i = 0; $i < 5000; $i++) $result = preg_match_all($re, $s, $m);
if ($result === false) echo "Произошла ошибка с кодом " . preg_last_error() . "\r\n";
elseif ($result > 0) print_r($m);
else echo "Совпадений не найдено.\r\n";
echo "\r\n" . (microtime(true) - $time_start) . "\r\n";
```

```
    #IPv4 + IPv6; PHP >= 5.2.0, PCRE 7.0+
    #IPv6 from http://vernon.mauery.com/content/projects/linux/ipv6_regex
    const REMOTE_ADDR = '/^(?:
                             #IPv4
                             (?<IPv4>
                               (?!0+\.)
                               (?<byte>1?\d{1,2}|2(?:[0-4]\d|5[0-5]))
                               (?:\.(?&byte)){3}
                             )
                             #IPv6
                             | (?<smb>[0-9a-f]{1,4}:){1,1} (?<sme>:[0-9a-f]{1,4}){1,6}
                             | (?&smb){1,2} (?&sme){1,5}
                             | (?&smb){1,3} (?&sme){1,4}
                             | (?&smb){1,4} (?&sme){1,3}
                             | (?&smb){1,5} (?&sme){1,2}
                             | (?&smb){1,6} (?&sme){1,1}
                             | ((?&smb){1,7}|:):
                             | :(?&sme){1,7}
                             | (?&smb){6} (?&IPv4)
                             | (?&smb){5} :?+ [0-9a-f]{1,4} :(?&IPv4)
                             | (?&smb){1,1} (?&sme){1,4} :(?&IPv4)
                             | (?&smb){1,2} (?&sme){1,3} :(?&IPv4)
                             | (?&smb){1,3} (?&sme){1,2} :(?&IPv4)
                             | (?&smb){1,4} (?&sme){1,1} :(?&IPv4)
                             | ((?&smb){1,5}|:) :(?&IPv4)
                             | :(?&sme){1,5} :(?&IPv4)
                           )
                          $/sxSX';
```

* https://jsfiddle.net/zqta1481/14/ Реализация метода String.match() с учётом рекурсии
* https://regex101.com/r/GtF2QA/8/ Проверка слова на английском языке во множественном числе
* https://regex101.com/r/iB63bg/2/ Проверка регулярного выражения на диалект ECMA 262 (JavaScript) (есть проверка на уникальность флагов)
* https://regex101.com/r/GQ1xKK/14/ Проверка ФИО на русском или английском языке
* https://regex101.com/r/WnauVT/6/ Проверка корректности даты в формате ГГГГ-ММ-ДД без проверки YYYY-02-29 в високосных годах (нужно доделать)
* https://regex101.com/r/CcSugS/7 Детектирование SQL за модификацию данных
* https://regex101.com/r/Q4dsL5/13 Валидация email
