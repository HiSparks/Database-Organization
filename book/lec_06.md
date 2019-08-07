---
title: "Лекция 6: Оператор выборки данных SELECT – ЧАСТЬ 2"
layout: bookpage
lang: ru
navigation_weight: 6
---

# 1. IN, BETWEEN, LIKE, IS NULL
Операторы при построении предикатов:

- сравнения (=, >, <, >=, <=, < >, !=, ! >, ! <);

- AND, OR, NOT;

- специальные операторы IN, BETWEEN, IS NULL, LIKE;

- специальные операторы ALL, ANY, SOME, EXISTS (для подзапросов)

- CONTAINS и CONTAINSTABLE – точное и неточное извлечение слов из текстовых столбцов,

- FREETEXT и FREETEXTTABLE – возвращают смысловой контекст заданных фраз

- IN - определяет множество, которому данное значение может принадлежать или не принадлежать

```sql
SELECT name AS 'Ф.И.О.', stip AS ‘Стипендия’

FROM Student

WHERE Id_Groups=2 OR Id_Groups=3
```

```sql
SELECT name AS 'Ф.И.О.', stip AS ‘Стипендия’

FROM Student

WHERE Id_Groups IN (2, 3)
```

```sql
SELECT name AS 'Ф.И.О.', stip AS 'Стипендия' FROM Student

WHERE name IN ('Иванов', 'Семенов')
```

```sql
SELECT name AS 'Ф.И.О.' FROM Student

WHERE city NOT IN ('Харьков', 'Чугуев')
```

- BETWEEN - задает границы, в которые должно попадать значение, чтобы предикат был истинным.

- Синтаксис:

```sql
<имя_поля> [NOT] BETWEEN
 <начальное_значение> AND <конечное_значение>
```

BETWEEN  *является включающим*: граничные значения возвращают истину

```sql
SELECT name AS 'Ф.И.О.', sr_ball AS ‘Средний балл’

FROM Student

WHERE sr_ball BETWEEN 4 AND 5
```

- Исключение граничных значений:

```sql
SELECT name AS 'Ф.И.О.', sr_ball AS ‘Средний балл’

FROM Student

WHERE (sr_ball BETWEEN 4 AND 5)

AND Not  sr_ball IN (4, 5)
```

```sql
SELECT name AS 'Ф.И.О.', sr_ball AS ‘Средний балл’

FROM Student

WHERE (sr_ball BETWEEN 4 AND 5)

AND sr_ball  Not IN (4, 5)
```

- LIKE - поиска подстрок

- Синтаксис оператора LIKE:

```sql
<имя_поля> [NOT] LIKE <образец>
```

- Применим только к строковым полям типа CHAR (NCHAR) или VARCHAR (NVARCHAR)

Маски:

«\_» - любой одиночный символ

… LIKE ‘_ро_’ – любое слово из 4 букв, имеющее в середине буквы ‘ро’, например, ’крот’;
Маски:

«%» - последовательность символов произвольной длины (в т.ч. нулевой)

… LIKE ‘%У%Д’ – образцу ‘%У%Д’ соответствуют ‘УБД’, ‘СУБД‘, ‘РСУБД’, но не ‘УВДУ’;

```SQL
SELECT*

FROM Student

WHERE name LIKE ‘И%’
```

Маски:

- [] – любой символ из указанных в скобках (перечисление без пробелов и разделителей, например, А-С соответствует от А до С).

- … LIKE ‘[cf]%’ –строка, начинающаяся на c  или f;

- … LIKE ‘[s-v]%ing’ –строка, начинающаяся на буквы s, t, u, v  и оканчивающаяся на ing;

- … LIKE '[JT]im' – любая строка из трех букв, начинающаяся на J или T и оканчивающаяся на 'im', например Jim или Tim.

- Задача: Пусть столбец содержит данные длиной не более 6 символов. Найти имена, начинающиеся с ‘Ива‘.

```sql
SELECT * FROM student  WHERE name LIKE 'Ива%'
```

Однако этот оператор работает медленнее приводимого ниже кода:

```sql
SELECT * FROM student

WHERE (name='Ива') OR (name LIKE 'Ива_')

OR (name LIKE 'Ива__') OR (name LIKE 'Ива___')
```

- NULL  не является значением как таковым, он не имеет типа данных

- NULL может размещаться в поле любого типа

- Совместно IS NULL могут быть использованы  операторы AND, OR и NOT

```sql
SELECT * FROM Student

WHERE stip is not NULL
```

## 2. CASE
- Цель: проверка списка условий и получение одного из нескольких возможных результатов

- 2 формата вызова:

простое выражение  CASE- для сравнения заданного выражения с множеством простых выражений для определения результата

```sql
CASE input_expression

WHEN when_expression  THEN result_expression [...n ]

[ ELSE else_result_expression  ]

END
```

поисковое выражение  CASE – в определении результата применяется множество логических выражений

```sql
CASE

WHEN boolean_expression THEN result_expression [ ...n ]

[ ELSE else_result_expression  ]

END
```

Аргументы:

- *input\_expression*  - выражение, полученное при использовании простого формата функции CASE (любое допустимое выражение)

- WHEN *when\_expression* - простое выражение, с которым сравнивается аргумент input\_expression (любое допустимое выражение). Типы данных аргумента input\_expression и when\_expression  должны быть одинаковыми.

- THEN *result\_expression*  - выражение, возвращаемое, если сравнение input\_expression  и when\_expression дает TRUE или Boolean\_expression  вычисляется в TRUE.

Пример:

```sql
SELECT fam 'ФИО', 'Оценка по математике' =

CASE math

WHEN 5 THEN 'отлично'

WHEN 4 THEN 'хорошо'

WHEN 3 THEN 'удовлетворительно'

WHEN 2 THEN 'неудовлетворительно'

ELSE 'неизвестно'

END

FROM Table_1
```

