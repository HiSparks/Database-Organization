---
title: "Лекция 14: Хранимые процедуры и триггеры"
layout: bookpage
lang: ru
navigation_weight: 12
--- 

# Лекция 14: Хранимые процедуры и триггеры

1. Хранимые процедуры MS SQL
2. Триггеры

Хранимая процедура в SQL Server — это группа из одной или нескольких инструкций Transact-SQL. Процедуры аналогичны конструкциям в других языках программирования:

- обрабатывают входные параметры и возвращают вызывающей программе значения в виде выходных параметров;
- содержат программные инструкции, которые выполняют операции в базе данных, включая вызов других процедур;
- возвращают значение состояния вызывающей программе - сведения об успешном или неуспешном завершении (и причины последнего).

Преимущества хранимых процедур:

- **Снижение сетевого трафика** между клиентами и сервером: команды выполняются как один пакет кода. Это позволяет существенно сократить сетевой трафик между сервером и клиентом - по сети отправляется *только вызов на выполнение* процедуры.

- **Большая безопасность**  Многие пользователи и клиентские программы могут выполнять операции с объектами БД посредством процедур, даже если у них нет прямых разрешений на доступ к базовым объектам. Процедура проверяет, какие из процессов и действий могут выполняться, и защищает базовые объекты базы данных. Злоумышленники не могут видеть имена объектов таблиц и баз данных, внедрять свои инструкции Transact-SQL или выполнять поиск важных данных.

- **Повторное использование кода**Если какой-то код многократно используется в операции базы данных, то отличным решением будет произвести его инкапсуляцию в процедуры. Это устранит необходимость излишнего копирования того же кода, снизит уровень несогласованности кода и позволит осуществлять доступ к коду любым пользователям или приложениям, имеющим необходимые разрешения.

- **Легкое обслуживание** Если клиентские приложения вызывают процедуры, а операции БД остаются на уровне данных, то для внесения изменений в основную базу данных будет достаточно обновить только процедуры. Уровень приложения остается незатронутым изменениями в схемах баз данных, связях или процессах.

- **производительность** По умолчанию компиляция процедуры и создание плана выполнения производится при ее первом запуске. Поскольку обработчику запросов не нужно создавать новый план, обычно обработка процедуры занимает меньше времени.

Типы хранимых процедур:


- **Пользовательские процедуры** Могут быть созданы в пользовательской БД или любых системных БД, за исключением базы данных Resource . Процедура может быть разработана либо на языке Transact-SQL , либо как ссылка на метод Microsoft .NET Framework среды CLR.

- **Временные процедуры (ВП)** Вид пользовательских процедур. Существует два вида ВП: локальные и глобальные. Отличаются именами, видимостью и доступностью. Имена локальных временных процедур начинаются с #; они видны только текущему соединению пользователя и удаляются при закрытии соединения. Имена глобальных ВП начинаются с ##; видны любому пользователю и удаляются после окончания последнего сеанса.

- **System** Системные процедуры включены в SQL Server. Физически они хранятся во внутренней скрытой базе данных **Resource** . 

Используются для планирования предупреждений и заданий. Поскольку названия системных процедур начинаются с префикса sp_, этот префикс не рекомендуется использовать при создании пользовательских процедур. 


Создание хранимой процедуры (SQL Server Management Studio):

- **Хранимые процедуры -> Создать хранимую процедуру.**
- **Запрос  ->  Указать значения для параметров шаблона.**
- В окне **Задание значений для параметров шаблона** ввести значения:

| Параметр | Зачение | 
|---|-----------|
| Автор | Ваше имя  |  
| Дата создания | Сегодняшняя дата  | 
| Описание| Возвращает данные о сотрудниках.​       | 
|Procedure_name| HumanResources.uspGetEmployeesTest​|  
|@Param1| @LastName| 
|@Datatype_For_Param1|nvarchar(50)| 
|Default_Value_For_Param1|NULL| 
|@Param2| @LastName| 
|@Datatype_For_Param2| nvarchar(50)| 
|Default_Value_For_Param2| @NULL| 

- В **редакторе запросов** заменить инструкцию SELECT следующей инструкцией:

```sql
Transact-SQL
SELECT FirstName, LastName, Department 
FROM HumanResources.vEmployeeDepartmentHistory 
WHERE FirstName = @FirstName AND LastName = @LastName AND EndDate IS NULL; 
```

- Проверка синтаксиса: **Синтаксический анализ -> Запрос.**
- Создать процедуру: **Запрос -> Выполнить.** Процедура создается как объект в базе данных.

Создание хранимой процедуры (**в редакторе запросов**):
- **Файл -> Создать запрос**
- Вставить код в окно запроса, **-> Выполнить.**

```sql
USE AdventureWorks2012; 
GO CREATE PROCEDURE HumanResources.uspGetEmployeesTest2
@LastName nvarchar(50), 
@FirstName nvarchar(50) 
AS SET NOCOUNT ON; 
SELECT FirstName, LastName, Department 
FROM HumanResources.vEmployeeDepartmentHistory 
WHERE FirstName = @FirstName AND LastName = @LastName AND EndDate IS NULL; 
GO 
```
Выполнить процедуру:

```sql
EXECUTE HumanResources.uspGetEmployeesTest2 N'Ackerman', N'Pilar'; 

EXEC HumanResources.uspGetEmployeesTest2 @LastName = N'Ackerman', @FirstName = N'Pilar'; 

EXECUTE HumanResources.uspGetEmployeesTest2 @FirstName = N'Pilar', @LastName = N'Ackerman';  
```
Работа с параметрами:

- Параметры используются для обмена данными между хранимой процедурой или функцией и вызвавшим ее приложением или инструментом.
- Входные параметры позволяют передать значение в хранимую процедуру или функцию.
- Выходные параметры позволяют возвратить из хранимой процедуры значение . Определяемые пользователем функции не могут задавать выходные параметры.
- Каждая хранимая процедура возвращает целочисленный код возврата. Если хранимая процедура не задает значение кода возврата явно, возвращается значение 0.

```sql
-- Create a procedure that takes one input parameter and returns one output parameter and a return code. 
CREATE PROCEDURE SampleProcedure @EmployeeIDParm INT, @MaxTotal INT OUTPUT AS 
-- Declare and initialize a variable to hold @@ERROR. DECLARE @ErrorSave INT SET @ErrorSave = 0 
-- Do a SELECT using the input parameter. 
SELECT FirstName, LastName, JobTitle FROM HumanResources.vEmployee WHERE EmployeeID = @EmployeeIDParm 
-- Save any nonzero @@ERROR value. 
IF (@@ERROR <> 0) SET @ErrorSave = @@ERROR 
-- Set a value in the output parameter. 
SELECT @MaxTotal = MAX(TotalDue) 
FROM Sales.SalesOrderHeader; 
IF (@@ERROR <> 0) SET @ErrorSave = @@ERROR 
-- Returns 0 if neither SELECT statement had an error; otherwise, returns the last error. 
RETURN @ErrorSave 
GO
```
Существует два способа возврата данных из процедуры вызывающей программе: параметры вывода и коды возврата. 

**1. Возврат данных с помощью выходного параметра**

Процедура может возвращать текущее значение параметра при указании ключевого слова OUTPUT для параметра в определении процедуры. 

Чтобы сохранить значение параметра в переменной, которая может быть использована в вызываемой программе, при выполнении процедуры вызываемая программа должна использовать ключевое слово OUTPUT.

*Пример объявления процедуры:*
Параметр @SalesPerson получает входное значение, указанное вызывающей программой. Инструкция SELECT использует значение, переданное входному параметру для получения верного значения SalesYTD . Инструкция SELECT также присваивает это значение выходному параметру @SalesYTD, который возвращает значение вызывающей программе при завершении процедуры.

```sql
USE AdventureWorks2012; 
GO 
CREATE PROCEDURE Sales.uspGetEmployeeSalesYTD 
@SalesPerson nvarchar(50), 
@SalesYTD money OUTPUT 
AS 
SET NOCOUNT   ON; 
SELECT @SalesYTD = SalesYTD 
FROM Sales.SalesPerson AS sp 
JOIN HumanResources.vEmployee AS e ON e.BusinessEntityID = sp.BusinessEntityID 
WHERE LastName = @SalesPerson; 
RETURN 
GO 
```
*Пример вызова процедуры:*

Вызывается процедура, которая была создана в первом примере и сохраняет выходное значение, возвращенное вызванной процедурой в переменной @SalesYTD , являющейся локальной в вызывающей программе.

```sql
convert(varchar(10),@SalesYTDBySalesPerson); GO 
-- Declare the variable to receive the output value of the procedure. 
DECLARE @SalesYTDBySalesPerson money; 
-- Execute the procedure specifying a last name for the input parameter 
-- and saving the output value in the variable @SalesYTDBySalesPerson 
EXECUTE 
Sales.uspGetEmployeeSalesYTD 
    N'Blythe', @SalesYTD = @SalesYTDBySalesPerson OUTPUT;
 -- Display the value returned by the procedure. 
PRINT 'Year-to-date sales for this employee is ' + convert(varchar(10),@SalesYTDBySalesPerson); 
GO 

```
**2. Возврат данных с использованием кода возврата**

Процедура может возвращать целочисленное значение, называемое кодом возврата, чтобы указать состояние выполнения процедуры. 

Код возврата для процедуры указывается при помощи инструкции RETURN. Как и выходные параметры, при выполнении процедуры код возврата необходимо сохранить в переменной, чтобы использовать это значение в вызывающей программе. 

Например, переменная @result типа данных int используется для хранения кода возврата из процедуры my_proc, например:

```sql
DECLARE @result int; 
EXECUTE @result = my_proc;
```

**Триггер** - поименованный объект БД, который ассоциирован с таблицей и активируемый при наступлении определенного события, связанного с этой таблицей. 

Эти события в основном соответствуют инструкциям Transact-SQL, которые начинаются с ключевых слов CREATE, ALTER, DROP, GRANT или UPDATE.

Используйте триггеры DDL в случаях:

- Предотвращать внесение определенных изменений в схему базы данных.
- Настроить выполнение в базе данных некоторых действий в ответ на изменения в схеме базы данных.
- Записывать изменения или события схемы базы данных.

# 2. Триггеры

DDL – триггеры:

Trigger on a CREATE, ALTER, DROP, GRANT, DENY, REVOKE or UPDATE statement

DML – триггеры:

Trigger on an INSERT, UPDATE, or DELETE statement to a table or view


Триггеры DLL срабатывают в ответ на событие Transact-SQL, обработанное текущей базой данных или текущим сервером. Область триггера зависит от события. 

Например, триггер DDL, созданный для срабатывания на событие CREATE TABLE, может срабатывать каждый раз, когда в базе данных или в экземпляре сервера возникает событие CREATE_TABLE. 

*Пример:*

```sql
CREATE TRIGGER safety 
ON DATABASE 
FOR DROP_TABLE, ALTER_TABLE 
AS PRINT 'You must disable Trigger "safety" to drop or alter tables!' 
ROLLBACK;
```
**Определение инструкции или группы инструкций Transact-SQL**

Триггеры DDL могут срабатывать после выполнения одной или нескольких определенных инструкций Transact-SQL . Списки инструкций Transact-SQL, которые можно задать для запуска триггера DDL:

```sql
CREATE/ALTER/DROP 
DATABASE/CERTIFICATE/INDEX/MESSAGE/
    PROCEDURE/QUEUE/ROLE/RULE/TABLE/VIEW/XML
    ```


Выбор предопределенной группы инструкций DDL для запуска триггера DDL

- Триггер DDL может срабатывать после любого события Transact-SQL из заданной группы схожих событий. Например, чтобы триггер DDL срабатывал после выполнения любой инструкции CREATE TABLE, ALTER TABLE или DROP TABLE, можно указать FOR DDL_TABLE_EVENTS в инструкции CREATE TRIGGER. 

**Создание триггера DDL (для таблицы или представления)**

```sql
CREATE [ OR ALTER ] TRIGGER [ schema_name . ]trigger_name 
ON { table | view } 
[ WITH <dml_trigger_option> [ ,...n ] ] 
{ FOR | AFTER | INSTEAD OF } 
{ [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] } 
AS { sql_statement [ ; ] [ ,...n ] | EXTERNAL NAME <method specifier [ ; ] > }
```

**Создание триггера DDL (для БД)**

```sql
CREATE [ OR ALTER ] TRIGGER trigger_name 
ON { ALL SERVER | DATABASE } 
[ WITH <ddl_trigger_option> [ ,...n ] ] 
{ FOR | AFTER } { event_type | event_group } [ ,...n ] 
AS { sql_statement [ ; ] [ ,...n ] | EXTERNAL NAME < method specifier > [ ; ] }
```
**Аргументы:**

OR ALTER: изменяет триггер только в том случае, если он уже существует.

*schema_name* - Имя схемы, которой принадлежит триггер DML. Действие триггеров DML ограничивается областью схемы таблицы или представления, для которых они созданы.

*имя_триггера* - Имя триггера (не может начинаться с символов # или ##).

*table | view* - Таблица или представление, в которых выполняется триггер DML, иногда указывается как таблица триггера или представление триггера.

DATABASE - Применяет область действия триггера DDL к текущей базе данных. 

**Изменение триггеров DDL**

- Если необходимо изменить определение триггера DDL, можно удалить и вновь создать триггер или переопределить существующий триггер одной инструкцией.

- Если изменилось имя объекта, на который ссылается триггер DDL, текст триггера необходимо соответствующим образом изменить. Перед переименованием объекта  надо определить, не повлияет ли изменение на работу каких-либо триггеров.

*Синтаксис:*

```SQL 
-- SQL Server Syntax Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger) 
ALTER TRIGGER schema_name.trigger_name 
ON ( table | view ) 
[ WITH <dml_trigger_option> [ ,...n ] ] 
( FOR | AFTER | INSTEAD OF ) 
{ [ DELETE ] [ , ] [ INSERT ] [ , ] [ UPDATE ] } 
AS { sql_statement [ ; ] [ ...n ] | EXTERNAL NAME <method specifier> [ ; ] }
```


*Правила:*

- AFTER - Указывает, что этот триггер запускается только после того, как запускающая его инструкция SQL успешно выполнена. Все ссылочные каскадные действия и проверки ограничений также должны быть успешно выполнены перед запуском триггера. По умолчанию.

- INSTEAD OF - Указывает, что триггер DML выполняется вместо запускающей инструкции SQL, переопределяя таким образом действия запускающих инструкций. 

- На каждую инструкцию INSERT, UPDATE или DELETE в таблице или представлении может быть определено не более одного триггера INSTEAD OF. 

**Отключение и удаление триггеров**

- Если триггер DDL больше не нужен, он может быть отключен или удален.

- Отключение триггера DDL не приводит к его удалению, Триггер все еще существует как объект в текущей базе данных. Однако этот триггер не будет срабатывать при выполнении инструкций Transact-SQL , на которые он был запрограммирован. Отключенные триггеры DDL можно повторно включать. 

- При удалении триггера DDL он удаляется из текущей базы данных. Удаление триггера DDL никоим образом не влияет на объекты или данные, на которые распространялась область действия триггера.

**Отключение триггера**

```sql
DISABLE TRIGGER { [ schema_name . ] trigger_name [ ,...n ] | ALL } 
ON { object_name | DATABASE | ALL SERVER } [ ; ]
```
Замечания:
Триггеры включаются по умолчанию при создании. Отключение триггера не сбрасывает его. Триггер все еще существует как объект в текущей базе данных. Однако триггер не запускается, когда инструкции Transact-SQL, для которых он запрограммирован, выполняются. 
Триггеры можно повторно включить с помощью ENABLE TRIGGER. 

*Пример:*

```sql
CREATE TRIGGER safety 
ON DATABASE 
FOR DROP_TABLE, ALTER_TABLE 
AS PRINT 'You must disable Trigger "safety" to drop or alter tables!' ROLLBACK; 
GO 
DISABLE TRIGGER safety ON DATABASE; 
GO
```

**Удаление триггеров:**

```sql
DROP TRIGGER - Удаляет триггер DML или DDL из текущей базы данных.

Trigger on an INSERT, UPDATE, or DELETE statement to a table or view (DML Trigger) 
DROP TRIGGER [ IF EXISTS ] [schema_name.]trigger_name [ ,...n ] [ ; ] 

Trigger on a CREATE, ALTER, DROP, GRANT, DENY, REVOKE or UPDATE statement (DDL Trigger) 

DROP TRIGGER [ IF EXISTS ] trigger_name [ ,...n ] ON { DATABASE | ALL SERVER } [ ; ] 
```
**Удаление триггеров – замечания:**

- Триггер DML может быть удален напрямую или в результате удаления таблицы триггера. При удалении таблицы удаляются все связанные с ней триггеры.

- С помощью инструкции DROP TRIGGER сразу несколько триггеров DDL можно удалить только в том случае, если при их создании были использованы одинаковые предложения ON.

- Чтобы переименовать триггер, используйте инструкции DROP TRIGGER и CREATE TRIGGER. Чтобы изменить определение триггера, используйте инструкцию ALTER TRIGGER.



