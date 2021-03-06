---
title: "Лекция 5: Оператор выборки данных SELECT"
layout: bookpage
lang: ru
navigation_weight: 5
---

# 1. SELECT – общие сведения
- Задача - извлечение данных из таблиц согласно поставленному условию

- Производительность SELECT - одна из важнейших характеристик СУБД

- Синтаксис:

```sql
SELECT [ ALL | DISTINCT ] определение_полей

FROM определение таблиц

[ WHERE условие ]

[ GROUP  BY  условие_группировки [ HAVING условие ] ]

[ ORDER BY  условие_сортировки ]
```

- SELECT – набор полей, которые требуется получить в результате запроса (ALL - повторяющиеся строки присутствуют, DISTINCT - отсутствуют).

- FROM – набор целевых таблиц

- WHERE –условие извлечения данных

- GROUP BY – условие группировки данных.

- HAVING – условие внутри группировки

- ORDER BY – поля и порядок сортировки.
-_Предложение SELECT оператора SELECT_ -  набор полей, формирующих результирующую таблицу. Могут быть заданы:

1. поля таблиц;

2. простейшее выражение или SQL-выражение;

3. агрегатная функция, где в качестве аргумента, используется поле или SQL-выражение.

- “*\**” – все поля всех таблиц, которые участвуют в запросе;

- *имя\_поля* – поле с именем “*имя\_поля*”;

- *имя\_таблицы.имя_поля* – обозначает поле с именем “*имя\_поля*” таблицы “*имя\_таблицы*” (или *псевдоним.имя\_поля*);
4. *SQL-выражение* – SQL-выражения, куда входят поля таблиц.

- арифметические операции “+”, “-”, “\*”, “/”, в качестве аргументов - поля таблиц БД или константы.

- SQL-функция, в качестве аргумента - поле таблицы, константа или их комбинация с помощью арифметических операций.

- комбинации с помощью арифметических операций полей таблицы и SQL-функций

```sql
SIN(a + b)

COS(a) + SIN(b) + a + 1 – вычисляется на сервере фунциями СУБД
```

# Предложение FROM оператора SELECT

- Одна таблица: `FROM имя_таблицы [псевдоним]`

- Несколько: `FROM имя_таблицы1 [ псевдоним1], имя_таблицы2 [ псевдоним2]`, ...

- Псевдоним действует в рамках SQL-оператора SELECT

- `SELECT S.name FROM Student S`

- `SELECT name FROM Student (обе нотации верны)`

-  `SELECT name, name FROM Groups, Student (ошибка!!!)`

- `SELECT Student.name, Groups.name FROM Groups, Student (длинная запись)`

- `SELECT s.name, g.name FROM Groups g, Student s`


```sql

SELECT name 'Ф.И.О.', sr_ball * 100 / 5 'Успеваемость(%)' FROM Student

усп = sr_ball * 100 / 5 – формульное выражение

```

Задание имен столбцов:

```sql

name 'Ф.И.О.'

sr_ball * 100 / 5 'Успеваемость(%)'

```

![](../assets/images/5_01.jpg)

- DISTINCT удаляет из результирующей таблицы повторяющиеся записи.

- ALL (по умолчанию), оставляет результирующую таблицу в том виде, в котором она получилась после склейки значений полей:

```sql
SELECT DISTINCT name AS 'Ф.И.О.',

sr_ball * 100 / 5 AS 'Успеваемость (%)‘  FROM Student
```

Вывод: DISTINCT  *удалит однофамильцев с одинаковой успеваемостью*
- WHERE  - условие извлечения данных из таблиц(ы): определяет *предикат* (истинный либо ложный) для каждой строки таблицы.

- WHERE извлекает те строки из таблицы, для которых предикат = ”*истина*”.

- WHERE задает *связь между таблицами* (условия связи) + дополнительные условия (условия выборки)

```sql
SELECT s.name AS 'Ф.И.О. студента',

g.name AS 'Имя группы'

FROM Groups g, Student s

WHERE g.id = s.id_group
```

(!!!) отсутствие этого условия дает право СУБД склеить таблицы в порядке создания записей, а не в порядке принадлежности студентов к группе

![](../assets/images/5_02.svg)

- Реляционные операции сравнения:


- =  - “равно”,

- < >  - “не равно” (или !=),

- \>  - “больше”,

- <  - “меньше”,

- \> =  - “больше или равно”,

- < =  - “меньше или равно”,

- ! >  - “ не меньше”,

- ! <  - “ не больше”.
- Пример 1


```sql
SELECT name AS 'Ф.И.О.',

sr_ball * 100 / 5 AS 'Успеваемость (%)'

FROM Student

WHERE name = ‘Иванов’
```

- Пример 2

```sql
SELECT name AS 'Ф.И.О.',

stip AS 'Стипендия'

FROM Student

WHERE stip >0;
```

- Булевы операторы в SQL условиях:

AND - (A  AND  B) возвращает истину, если они оба истинны

OR - (A  OR  B) возвращает истину, если хотя бы один из них истинен

NOT – (NOT  A) изменяет значение А на противоположное

```sql
SELECT name AS 'Ф.И.О.', sr_ball AS ‘Средний балл’, Id_Group

FROM Student

WHERE sr_ball >=3 And Id_Group=5
```

Вывод списка студентов факультета с именем “РТСЛА”:

```sql
SELECT f.name AS 'Факультет', s.name AS 'Ф.И.О. Студента'

FROM Facility f, Groups g, Student s

WHERE f.id=g.id_facility

AND g.id=s.id_groups AND f.name='РТСЛА‘
```

- f.name='РТСЛА' – выбор факультета РТСЛА

- g.id=s.id_groups – выбор студентов группы

- f.id=g.id_facility – выбор групп факультета
(pic)

Вывод списка студентов факультета с именем “РТСЛА” (вариант 2):

```sql
SELECT s.name AS 'Ф.И.О. Студента'

FROM Groups g, Student s

WHERE g.id=s.id_groups AND g.id_facility=5
```
![](../assets/images/5_03.svg)

Нет задачи вывести вместе с именем студента еще и название его факультета

![](../assets/images/5_03.svg)