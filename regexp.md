<h2>
  Регулярные выражения PCRE.
  Обход вложенных тагов с учётом их парности (рекурсивные подмаски).
  Версия 3.0 -- самая скорострельная!
</h2>
<pre>
<?php
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
?>
</pre>
