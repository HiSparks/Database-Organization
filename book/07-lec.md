---
title: "Лекция 7. Оператор выборки данных SELECT (2)"
layout: bookpage
lang: ru
---

# Лекция 7. Оператор выборки данных SELECT (2)

При построении предикатов оператора WHERE в SQL используются следующие операторы:
- операторы сравнения (=, \>, \<, \>=, \<=, \< \>, !=, ! \>, ! \<),
- булевы операторов AND, OR, NOT,
- специальные операторы IN, BETWEEN, IS NULL, LIKE, ALL, ANY, SOME, EXISTS,
- операторы полнотекстового поиска:
	- CONTAINS и CONTAINSTABLE – используются для точного и неточного извлечения слов или фраз из текстовых столбцов,
    - FREETEXT и FREETEXTTABLE – возвращают смысловой контекст фраз, заданных в строке поиска.

Указанные операторы применяют для получения выразительных и мощных предикатов.

## 7.1 Оператор IN (NOT IN)

Оператор IN полностью определяет множество, которому данное значение может принадлежать или не принадлежать.
Пример. Определить из таблицы Student фамилии и стипендию студентов, учащихся в группах с первичными ключами 2 и 3.
Вариант с использованием операции OR (“логическое ИЛИ").

```sql
SELECT name AS 'Ф.И.О.', stip AS "Стипендия" FROM Student
WHERE Id_Groups=2 OR Id_Groups=3
```

Вариант с использованием оператора IN.

```sql
SELECT name AS 'Ф.И.О.', stip AS "Стипендия"
FROM Student
WHERE Id_Groups IN (2, 3)
```


Как видно из примера, IN представляет множество, элементы которого точно перечисляются в круглых скобках и разделяются запятыми. Если значения поля, имя которого указано слева от IN, есть одно из перечисленных в списке значений (требуется точное совпадение), то предикат считается истинным. Если элементы множества имеют символьный тип, то значения в скобках используются в одиночных кавычках.

```sql
SELECT name AS "Ф.И.О.", stip AS "Стипендия" 
FROM Student 
WHERE name IN ("Иванов", "Семенов")
```
Пример. Определить из таблицы фамилии студентов, которые не проживают в городах Харьков и Чугуев (поле city).

```sql
SELECT name AS 'Ф.И.О.' 
FROM Student 
WHERE city NOT IN ('Харьков', 'Чугуев')
```
Пример.	Использование	вложенного	подзапроса	для формирования элементов множества.
```sql
SELECT * FROM Table1
WHERE x NOT IN (SELECT x FROM Table2)
```
По определению значение предиката x NOT IN (S) , где (S)
некоторое множество, равно значению предиката NOT(x IN (S)).

## 7.2 Оператор BETWEEN (NOT BETWEEN)

Оператор	BETWEEN	задает	границы,	в	которые	должно попадать значение, чтобы предикат был истинным.
Синтаксис оператора BETWEEN:
```sql
<имя_поля>	[NOT]	BETWEEN	<начальное_значение>	AND
<конечное_значение>
```
В	качестве	начального	и	конечного	значений	могут использоваться выражения и константы.
Синтаксис предиката BETWEEN в более общем случае:

```sql
<value_expression>	[NOT]	BETWEEN	<low_value_expression>	AND
<high_value_expression>
```
Фактически данный предикат – это сокращенный способ записи следующего выражения:
```sql
((<low_value_expression> <= <value_expression>) AND (<value_expression> <= <high_value_expression>))
Особенности оператора BETWEEN:
```
 
1. В отличии от IN, оператор BETWEEN чувствителен к порядку - начальное_значение в предложении должно быть первым в соответствии с алфавитным или числовым порядком. Пример. Извлечь из таблицы Student фамилии и средний бал студентов, средний бал которых находится в диапазоне между 4 и 5.

	```sql
	SELECT name AS 'Ф.И.О.', sr_ball AS "Средний балл" 
	FROM Student
	WHERE sr_ball BETWEEN 4 AND 5
	```
2. Оператор BETWEEN является включающим, то есть здесь граничные значения (в данном примере это 4 и 5) также делают предикат истинным. SQL непосредственно не поддерживает исключающий BETWEEN. Необходимо сформулировать граничные значения так, чтобы включающая интерпретация была справедлива, либо, при необходимости, исключить граничные значения. Это можно сделать, например, посредством следующего запроса:

	```sql
	SELECT name AS 'Ф.И.О.', sr_ball AS "Средний балл" FROM Student
	WHERE (sr_ball BETWEEN 4 AND 5)
	AND Not sr_ball IN (4, 5)
	```
	
3. Аналогично всем операторам сравнения BETWEEN действует на символьных полях, представленных в двоичном (ASCII) эквиваленте, то есть для выборки можно воспользоваться алфавитным порядком. Следующий запрос выбирает всех студентов, значения поля “name" которых попадают в заданный алфавитный диапазон:
```sql
SELECT *
FROM Student
WHERE name BETWEEN "K" AND "R";
```
Фамилия Romanov будет пропущена, несмотря на то, что BETWEEN является включающим, так как он сравнивает строки неравной длины. Строка "R" короче строки "Romanov", поэтому "R" дополняется пробелами. Пробелы предшествуют символам латинского алфавита (в большинстве реализаций), поэтому Romanov оказался невыбранным. Об этом необходимо помнить при использовании BETWEEN с алфавитными диапазонами. Для включения в результат выполнения запроса сведений о студентах, фамилии которых начинаются на "R", нужно указать следующую букву алфавита ("S") или приписать символ "z" (несколько символов "z", если это необходимо) после второго граничного значения.

## 7.3 Оператор LIKE (NOT LIKE)



Синтаксис оператора LIKE:

```sql
<имя_поля> [NOT] LIKE <образец> (<строка> [NOT] LIKE <подстрока>)
```
 
Оператор LIKE применим только к строковым полям типа CHAR (NCHAR) или VARCHAR (NVARCHAR), поскольку он используется для поиска подстрок в предикате предложения WHERE. Другими словами, он осуществляет просмотр строки для проверки условия - входит ли заданная подстрока в указанную строку. Для гибкой реализации этой цели используются шаблоны (маски)  специальные символы,  которые используются для конструирования образца строки.
Существуют следующие типы шаблонов, используемых с LIKE:
- Символ "подчеркивание" (_) заменяет любой одиночный символ. Например:
	-	```sql 
	… LIKE "б_т" -- образцу "б_т" соответствует "бит" или "бут", но не соответствует "брат";
	```
	-	```sql 
	… LIKE "_ро_" -- любое слово из 4 букв, имеющее в середине две буквы "ро", например, "крот";
	```
	-	```sql
	… LIKE "ов" -- любое слово из 6 букв, оканчивающее на "ов", например, "Петров".
	```
-	Символ	"процент"	(%)	заменяет	последовательность символов произвольной длины, в том числе и нулевой. Например:
	-	```sql 
	… LIKE "И%" -- любая строка, начинающаяся с буквы И, например, "Иванов";
	```
	-	```sql 
	… LIKE "%E%" -- любая строка, содержащая букву E, например, "SELECT";
	```
	-	```sql
	… LIKE "%У%Д" -- образцу "%У%Д" соответствуют "УБД", "СУБД", "РСУБД", но не "УВДУ";
	```
	-	```sql
	… LIKE "%т_о%" -- истинно для любого слова, содержащего шаблон "т_о", например, "Петров".
	```

Пример. Извлечь из таблицы Student информацию обо всех студентах, фамилии которых начинаются с буквы И.

```sql
SELECT *
FROM Student
WHERE name LIKE "И%"
```

-	[] – допускается применение в строке любого отдельного символа из указанных в скобках (допустимо перечисление без пробелов и других разделителей, указание интервала, например, А-С соответствует от А до С).
Например:
	-	```sql
	… LIKE "[cf]%" -- любая строка, начинающаяся на c или f;
	```
	-	```sql
	… LIKE "[s-v]%ing" -- любая строка, начинающаяся на буквы s, t, u, v и оканчивающаяся на ing;
	```
	-	```sql
	… LIKE '[JT]im' -- любая строка из трех букв, начинающаяся на J или T и оканчивающаяся на 'im', например Jim или Tim.
	```

 
-	[^]– любой единичный символ из перечисленных в скобках не должен содержаться в строке (допустимо перечисление без пробелов и других разделителей, а также указание интервала). Например:
	-	… LIKE "S[^f]%" – любая строка, начинающаяся на S, за которой не следует f.
	Разрешено использование конструкции NOT LIKE.
	Пример. Извлечь из таблицы Student информацию обо всех студентах, фамилии которых не начинаются с буквы "И".

	```sql
	SELECT *	FROM Student
	WHERE name NOT LIKE "И%"
	```

LIKE может оказаться полезным при осуществлении поиска имени или другого значения, полное написание которого неизвестно. Предположим, не совсем понятно, как правильно записывается фамилия одного из студентов "Коваленко", "Коленко" или "Коваленков". Можно использовать ту часть, которая известна, и символы шаблона для нахождения всех возможных вариантов:

```sql
SELECT * FROM Student 
WHERE name 
LIKE "К%к%"
```
Символ шаблона (%) в конце строки необходим в тех реализациях SQL, в которых длина поля name превосходит количество букв в имени "Коваленко". В таком случае значение поля name реально хранится как "Коваленко", а за ним следует ряд пробелов. Следовательно, символ "о" не является последним в строке. Символ (%) в шаблоне заменяет все пробелы.

Поиск в строке символов подчеркивания (\_) или процента (%), выполняется с использованием Escape-символа. Escape-символ используется в предикате непосредственно перед символом (%) или (\_) и означает, что следующий за ним символ интерпретируется как обычный символ, а не как символ шаблона.

В SQL строки могут быть равны, но не совпадать с точки зрения оператора LIKE. При проверке равенства строк надо более короткую строку справа заполнить пробелами до длины более длинной. А затем посимвольно сравнивать строки. Таким образом, строки "Иванов" и "Иванов " (с тремя пробелами) окажутся равны. Предикат LIKE пробелов не вставляет, так что выражение `"Иванов" LIKE "Иванов"` даст значение false.

Чтобы обойти эту проблему, можно воспользоваться функциями для обработки строк LTRIM() и RTRIM() (функции TRIM() в MS SQL Server нет), которая удалит ненужные пробелы слева и справа от одного или обоих аргументов `…WHERE RTRIM(name) LIKE "Иванов"`.

Пример. Составить запрос, который выводит список студентов факультета с именем “ФРТСЛА".

Используемый ниже способ решения задачи, основан на правиле создания имен групп, которое используется в ХАИ. Имя группы состоит из трех цифр и необязательной одной буквы. Первая цифра – номер факультета, вторая – номер курса и третья – номер
специальности. Буква используется, если групп на специальности несколько. Таким образом, группы 1 факультета – 1хх, второго – 2хх, третьего – 3хх и т.д. Если существует такое правило, то это значит, что его можно использовать для получения имени группы:
```sql
SELECT s.name AS 'Ф.И.О. Студента' 
FROM groups g, student s
WHERE g.id=s.id_group AND g.name 
LIKE '5%'
```
Однако следующий запрос выполнится неправильно, так как не указано условие связи поля s.name с полем g.name в таблицах Student и Groups.

```sql
SELECT s.name AS 'Ф.И.О. Студента'
FROM Groups g, Student s	WHERE g.name LIKE '5%'
```

Причина неправильности предыдущего запроса станет ясной после анализа следующего запроса.
```sql
SELECT s.name 'ФИО', g.name 'Группа' 
FROM Groups g, Student s 
WHERE g.name 
LIKE '5%'
```
Предикат (g.name LIKE '5%') из таблицы groups выберет две записи – с именами групп "535" и "535a". Оператор SELECT s.name … выберет 4 записи из поля s.name – "Иванов", "Петров", … . При соединении в одну таблицу сначала будет выбрано первое значение из таблицы с меньшим числом записей – это "535", потом к этому значению присоединятся все записи из таблицы с большим числом записей – это "Иванов", "Петров", … . Потом будет выбрана группа "535a" и так далее. Результат представлен на рисунке выше.
Пример. Пусть столбец содержит данные длиной не более 6 символов, а предикат LIKE ищет имена, начинающиеся с "Ива". Для этого обычно можно написать такой код:

```sql
SELECT * FROM student 
WHERE name 
LIKE 'Ива%'
```
Однако этот оператор работает медленнее приводимого ниже
кода:

```sql
SELECT * FROM student
WHERE (name='Ива') OR (name LIKE 'Ива_') OR (name LIKE 'Ива 	') OR (name LIKE 'Ива 	')
```
Завершающие пробелы также представляют собой символы, которые могут иметь точное соответствие.
Предикат с использованием `...name LIKE '%нов'` также будет работать медленнее, чем предикат вида `…WHERE (name LIKE '_нов') OR (name LIKE ' нов') OR (name LIKE ' нов')`.


##	7.4 Оператор IS NULL (IS NOT NULL)

Часто в таблице встречаются записи с незаданными значениями какого-либо из полей, потому что значение поля неизвестно или его просто нет. В таких случаях SQL позволяет указать в поле NULL- значение.
 
1.	Когда значение поля есть NULL это значит, что программа БД специальным образом помечает поле, как не содержащее какого- либо значения для данной строки. Дело обстоит не так в случае простого приписывания полю значения "нуль" или "пробел".
2.	Поскольку NULL не является значением как таковым, он не имеет типа данных.
3.	NULL может размещаться в поле любого типа. Тем не менее, NULL-значение часто используется в SQL.
Для определения неизвестного значения используется оператор
IS с ключевым словом NULL.

Пример. Предположим, в таблице есть поле OCENKA, а экзамена еще не было (или часть студентов экзамен еще не сдала), значит в этом поле будет находиться значение NULL до тех пор, пока оценка не станет известна. Определить все записи таблицы, в которых оценка еще не проставлена.

`SELECT * FROM Student WHERE OCENKA IS NULL`

Совместно с операторами IS NULL могут быть использованы булевы операторы AND, OR и NOT.
Пример. Определить все строки таблицы Student, в которых стипендия (stip) проставлена.

```sql
SELECT * FROM Student
WHERE stip is not NULL
```

##	7.5 Использование выражения CASE

Выражение CASE используется для проверки списка условий и получения одного из нескольких возможных результатов. Функция CASE имеет два формата вызова:
-	простое выражение CASE применяется для сравнения заданного выражения с множеством простых выражений для определения результата,
-	в поисковом выражение CASE для определения результата применяется множество булевых (логических) выражений.
Синтаксис:
Простое выражение CASE: CASE input_expression

```sql
WHEN when_expression 
THEN result_expression [...n ] 
[ ELSE else_result_expression ]
END
```
Поисковое выражение CASE: CASE
```sql
WHEN boolean_expression 
THEN result_expression [ ...n ] 
[ ELSE else_result_expression ]
END
```
Аргументы:
 
`input_expression`

Выражение, полученное при использовании простого формата функции CASE. Аргумент input_expression представляет собой любое допустимое выражение.

`WHEN when_expression`

Простое выражение, с которым сравнивается аргумент input_expression при использовании простого формата функции CASE. Аргумент when_expression представляет собой любое допустимое выражение. Типы данных аргумента input_expression и каждого из выражений when_expression должны быть одинаковыми или неявно приводимыми друг к другу.

`THEN result_expression`

Выражение, возвращаемое, если сравнение выражений input_expression и when_expression дает в результате TRUE или выражение Boolean_expression вычисляется в TRUE. Аргумент result_expression представляет собой любое допустимое выражение.

`ELSE else_result_expression`

Это выражение, возвращаемое, если ни одна из операций сравнения не дает в результате TRUE. Если этот аргумент опущен и ни одна из операций сравнения не дает в результате TRUE, функция CASE возвращает NULL. Аргумент else_result_expression представляет собой любое допустимое выражение. Типы данных аргумента else_result_expression и любого из аргументов result_expression должны быть одинаковыми или неявно приводимыми друг к другу.

`WHEN Boolean_expression`

Это логическое выражение, полученное при использовании поискового формата функции CASE. Аргумент Boolean_expression представляет собой любое допустимое логическое выражение.
Выражение CASE может использоваться в любой инструкции или предложении, которые допускают допустимые выражения. Например, выражение CASE можно использовать в таких инструкциях, как SELECT, UPDATE, DELETE и SET, а также в таких предложениях, как IN, WHERE, ORDER BY и HAVING.
Пример оператора SELECT с простым выражением CASE 
```sql
SELECT	fam 'ФИО', 'Оценка по математике' =

CASE math
WHEN 5 THEN 'отлично' WHEN 4 THEN 'хорошо'
WHEN 3 THEN 'удовлетворительно' WHEN 2 THEN 'неудовлетворительно' ELSE 'неизвестно'
END
FROM Table_1
```

Пример работы оператора SELECT с поисковым выражением CASE
```sql 
SELECT	fam 'ФИО', 'Оценка по математике' = CASE
WHEN math=5 THEN 'отлично' WHEN math=4 THEN 'хорошо'
WHEN math=3 THEN 'удовлетворительно' WHEN math=2 THEN 'неудовлетворительно' ELSE 'неизвестно'
END
FROM Table_1
```


**Контрольные вопросы**

1.	Каковы основные особенности использования предложения
WHERE?
2.	Каковы особенности использования оператора BETWEEN?
3.	Каковы особенности использования оператора IN?
4.	Каковы особенности использования оператора IS NULL?
5.	Каковы особенности использования оператора LIKE?
6.	Каковы особенности использования простого выражения
CASE?
7.	Каковы особенности использования поискового выражения
CASE?
