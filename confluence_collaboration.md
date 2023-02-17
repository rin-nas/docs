# Confluence — особенности коллективной работы

Confluence — идеальное место для ведения корпоративной базы знаний, хранения, разработки документов и их
обсуждения сотрудниками компании.

В бесплатной версии Конфлюенса кол-во одновремено активных пользователей не может быть > 10, иначе
доступ на редактирование пропадает. Для того, чтобы дать доступ одному пользователю, приходится забирать его у другого пользователя.
Работает это через активацию-деактивацию пользователей.

## Функциональные возможности

### Коммуникации
1. Организуется единое информационное пространство для группы пользователей — нет необходимости пересылать
документы друг-другу по электронной почте во вложении или копировать их на сетевой диск.
2. Решается проблема с несогласованностью обсуждений в почтовой переписке по одной теме: с разорванными
цепочками писем; с трудностью понимания кто, кому и когда ответил; с неудобным чтением обсуждений
снизу-вверх.
3. Возможность совместного обсуждения и комментирования документов двумя способами:
   1. Для общих комментариев — под документом. Сообщения имеют удобное древообразное представление.
Видно, кто кому ответил.
   2. Для частных комментариев — в контексте документа (через выделение части текста). Комментарии
выглядят как мини-задачи, на которые можно переводить в статус «решено». Если автор с решенением не
согласен, он может перевести комментарий обратно в статус «не решено».
1. Возможность совместного редактирования документов, включая одновременное редактирование несколькими
пользователями.
2. Можно организовывать свои рабочие зоны (пространства) под нужны отделов или продуктов.
3. Можно управлять правами доступа к ресурсам. Например, авторы документа могут править документ, а другие
сотрудники только писать комментарии, остальные доступа не имеют.
4. Есть полнотекстовый поиск по документам, комментариям, файлам-вложениям в текстовом формате, в формате
Word, Excel и PDF! Искать можно по заданным критериям, поисковая система работает очень эффективно!

### Редактор документов
1. Интерфейс форматирования текста похож на Word и содержит богатый набор возможностей: создание
параграфов, заголовков, списков, таблиц, вставка картинок, выделение текста и фона цветом, выравнивание,
создание ссылок на другие документы, вставка документов друг в друга.
2. К документу можно прикладывать вложения в виде любых типов файлов (тексты, презентации, таблицы,
картинки, аудио, видео, архивы и прочее). Файлы-вложения можно открывать для редактирования в
соответствующем ПО пакета MS Office сразу из Confluence. При сохранении вложения в MS Office файл
автоматически выгружается в Confluence с новой версией (вручную загружать и выгружать файлы не нужно). Эта
функциональность сравнима с технологией OLE.
3. Возможность создания типовых документов по шаблону
4. Ведётся полная история редактирования документа — когда, кто и что изменил, включая версионность вложений
к документам. Можно сравнить 2 версии документа и получить их отличия в наглядном виде (что именно
изменено, добавлено, удалено).
5. В процессе редактирования документа он периодически сохраняется в виде черновика. По кнопке "Закрыть" все5.
изменения также сохраняются в черновик, чтобы позже, при необходимости, можно было продолжить
редактирование документа.
6. При сохранении документа изменения, одновременно внесённые в документ разными пользователями,
"склеиваются" автоматически. Если возникает конфликт (были правки в одном и том же месте, сделанные
разными пользователями), то показываются отличия текущей редактируемой версии от последней сохранённой (в
наглядном виде). Далее можно продолжить редактирование, перезаписать документ или закрыть его.

### Интеграция, импорт-экспорт
1. Есть возможность импортировать файл Word в документ Confluence и экспортировать документ в файл Word и PDF
2. ~~Для интерактивного прототипирования интерфейсов в документы можно внедрять html и javascript.~~ По
соображениям безопасности (Cross-site scripting attacks) макрос лучше отключить.
3. Есть интеграция с инструментом постановки и контроля задач JIRA . Если на странице в Confluence создать или
вставить ссылку на задачу в JIRA, то ссылка из задачи в JIRA на страницу в Confluence создаётся автоматически.
4. Есть интеграция с инструментом для рисования графических схем и диаграмм https://www.draw.io/. Можно
рисовать диаграммы и схемы не выходя из Confluence. Каждое сохранение диаграммы автоматически сохраняется
отдельной версией с 2-мя файлами: файл с исходным кодом в векторе (xml) и файл с растровым изображением
(png).

### Остальное
1. Все документы хранятся на сервере, в случае поломки компьютера пользователя информация не потеряется (если
на сервере периодически и автоматически делаются резервные копии).
2. Не требуется установка дополнительного ПО на компьютер пользователя, достаточно иметь современный браузер.
3. Есть много плагинов и макросов, расширяющих стандартные возможности Confluence (примеры: "Плагин Scroll Versions — краткое описание возможностей", макрос "трекинг пользователей, смотревших страницу" и др.).

### Ограничения
Ограничение|Решение (workaround)
-----------|--------------------
**Контекстные комментарии**|
Нет возможности оставить контекстные комментарии на названиях разделов и колонках таблиц |Оставьте обычный комментарий с цитированием текста под документом
В режиме редактирования документа контекстные комментарии не видны | Откройте в браузере ещё одну вкладку (лучше на втором мониторе) со страницей в режиме просмотра и смотрите комментарии там
Если при редактировании документа исправить фразу, на которую сделан контекстный комментарий, то после сохранения документа такой комментрий изменит статус на "решённый" | Для минимизации таких случаев старайтесь выделять короткий текст для контекстных комментариев. Пользователь, который оставил контекстный комментарий, получит письмо-уведомление от Сonfluence об этом событии и может проверить актуальность комментария. Если вопрос не решён, напишите новый комментарий.
**Экспорт в Word**|
При экспорте страницы в документ Word нет возможности сделать указанные вложения в виде объектов-файлов (OLE). Создаются ссылки на соответствующее вложения в Confluence, за исключением изображений | Решения пока нет. Вставьте несколько вложений в документ MS Word вручную
При экспорте страницы в документ Word таблицы со многими колонками выглядят плохо, т.к. ширина каждой колонки становится слишком узкой |Для удобного просмотра документа в MS Word на мониторе измените режим просмотра на «веб-документ». Если требуется печать на бумаге, то для изменения ориентации страницы с портретной на альбомную используйте макросы "Scroll Landscape" и "Scroll Portrait" Ещё можно задать относительную ширину столбца (в % от всей ширины таблицы) макросом "Scroll Table Layout", вставив его в нужный столбец и указав величину в его свойстве width
**Разное**|
Для нормальной работы требуется современный браузер (IE11, Google Chrome) | Обновите браузер до последней версии
Название страницы д.б. уникально в рамках пространства | После названия страницы в скобках укажите название родительской страницы или id проекта
Если есть несколько разных вложений на разных страницах, но с одинаковыми названиями, то в режиме редактирования документа при вставке ссылки на вложение в результатах поиска по названию непонятно, от какой страницы вложение | Старайтесь делать названия вложений уникальными
При поиске в режиме редактирования не осуществляется прокрутка страницы | Обновите устаревший браузер Google Chrome на последний
Поисковый механизм Confluence умеет корректно индексировать текстовые файлы только в кодировке UTF8. Русские буквы в других кодировках (например, "windows-1251") не индексируются и в результах поиска они выглядят как `��������`. | Перекодируйте файл в UTF8.