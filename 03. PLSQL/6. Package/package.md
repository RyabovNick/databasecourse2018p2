# Package

1. Что такое пакет?
2. Зачем нужен?
3. Синтаксис
4. Почему триггер состоит из двух частей?
5. Что входит в описание (спецификацию)?
6. Что входит в тело пакета?

Почему с персональным компьютером так легко работать? Отчасти потому, что в нем множество полезных функций объединено в один простой в использовании пакет. Программное обеспечение показывает окна (формы), и для достижения желаемого результата не нужно знать, каким образом щелчок мыши передается в компьютер. Сложность "закадровой" деятельности скрыта от пользователя, которому достаточно знать лишь о том, что определенное действие с его стороны приведет к предсказуемым результатам. При разработке приложений PL/SQL обеспечивает похожий эффективный подход, представляя _пакеты_ (packages) для совместного хранения полезных функций, процедур, типов записей и курсоров.

Аналогично процедуре или функции, пакет имеет спецификацию и тело. Однако спецификация и тело пакета создаются отдельно друг от друга. Можно даже заменить одно тело пакеты другим, сохранив совместимость с существующей спецификацией, и пакет по-прежнему будет работать.

Так зачем нужно держать тело отдельно от спецификации? Все очень просто. Пользователи пакета не обязаны знать все детали его реализации, поэтому эти детали скрываются внутри тела (инкапсуляция). Тело хранится, компилируется и обрабатывается внутри базы данных и невидимо пользователям пакета. Для программирования своих задач им просто следует пользоваться спецификацией. Это очень важно, когда вы хотите защитить свой код, сделав его недоступным для хакеров или конкурентов.

Если проводить аналогию с объектно-ориентированным программированием (ООП), то пакет внешне очень напоминает класс, у которого также спецификация и реализация разделены между собой. При создании и использовании пакетов используется один из принципов ООП - _Инкапсуляция_, то есть скрытие реализации, о чем уже говорилось в предыдущем параграфе.

## Преимущества

### Модульность

Пакеты позволяют инкапсулировать логически связанные типы, элементы и подпрограммы в именованный модуль PL/SQL. Каждый пакет легко понять, а интерфейсы между покетами просты, понятны и четко определены. Это помогает разработке приложений.

### Простое проектирование приложения

При разработке приложения все, что нужно - информация интерфейса в спецификации пакета. Можно компилировать спецификацию без тела (самой реализации). Затем можно скомпилировать сохраненные подпрограммы, которые ссылаются на пакет. Не нужно полностью определять тело пакета, пока не будет готово проектирование.

### Скрытие информации

С помощью пакетов можно указать какие типы, элементы и подпрограммы являются общедоступными или частными. Например, если пакет содержит 4 подпрограммы 3 могут быть доступны всем, а одна закрыта. Пакет скрывает реализацию частной подпрограммы. Это упрощает обслуживание и усовершенствование. Скрывая детали реализации от пользователей можно защищать целостность пакета.

### Добавление функциональности

Перменные и курсоры сохраняются в течение всего сеанса. Т.е. они могут использоваться всеми подпрограммами, которые выполняются. Кроме того пакеты позволяют хранить данные по транзакциями, не сохраняя их в БД.

### Лучшая производительность

Когда подпрограмма пакета вызывается в первый раз весь пакет загружается в память.

## Вызов процедур и функций, входящих в состав пакета

Вызов процедур и функций, входящих в состав пакета, также внешне сильно напоминает вызов методов класса. Например, для вызова процедуры increase_prices, входящей в состав пакета prices, нужно выполнить следующий оператор:

```
begin
prices.increase_prices;
end;
```

То есть для обращения к процедурам, переменным и функциям пакета используется нотация очень похожая на использование методов и свойств класса: *имя*пакета.объект*пакета*. Вы уже знакомы с функцей put_line поставляемого ORACLE пакета dbms_output. Если внутри пакета необходимо сослаться на собственный объект пакета, то, естественно, нет необходимости указывать имя пакета перед именем объекта (опять же, как и в методах класса).

В спецификации пакетов можно указывать не только входящие в его состав процедуры и функции, но и переменные, которые в классе назывались бы свойствами класса. Эти переменные являются общими для всех процедур и функций, входящих в состав пакета.

## Создание спецификации пакета

```
CREATE PACKAGE имя_пакета IS
[объявления_переменных_и_типов]
[спецификации_курсоров]
[спецификации_функций_и_процедур]
END [имя_пакета];
```

Все переменные и типы, объявленные в спецификации пакета, доступны его пользователям. Спецификация позволяет узнать, что содержит пакет. Опять же, если проводить аналогию с ООП, спецификация - это интерфейс пакета.

Если в пакете объявить только заголовок, без тела. Это может быть полезно при создании глобальных переменных.

Переменные, присутствующие в спецификации пакета, называются _переменными пакета_ (package variables). Они инициализируются только один раз - при первом обращении к нему. Когда производится вызов какой-либо составляющей пакета, ORACLE загружает пакет в память, где он и остается все то время, пока пользователь соединен с базой данных. При наличии нескольких сеансов переменные пакета и их значения становятся разделяемыми; следует также заметить, что обращение к объектам пакета в последующих сеансах происходит быстрее, поскольку пакет уже присутствует в памяти сервера.

## Создание тела пакета

```
CREATE OR REPLACE PACKAGE BODY имя_пакета IS
[локальные_переменные]
[полные_спецификации_курсоров_пакета]
[полные_спецификации функций_и_процедур_пакета]
BEGIN
[выполняемые_операторы]
[EXCEPTION]
[обработчики_исключений]
END [имя_пакета];
```

_Выполняемая секция_ пакета обычно используется для инициализации локальных переменных пакета или переменных пакета. Она соответствует конструктору класса в ООП. Выполнение этой секции производится один раз при загрузке пакета, а следовательно, ее не стоит использовать для каких-либо повторяющихся действий, поскольку обратиться к ней более одного раза все равно не удастся.

## Пример создания и использования пакета

В приведенном ниже примере будет создан пакет, в котором собраны функции и процедуры, связанные с таким объектом информационной системы, как менеджер. Далее будут приведены пояснения и комментарии к этому примеру.

```
create or replace package manager is
-- переменная, содержащая текущее количество менеджеров

count number;
-- прототип функции, возвращающей информацию
-- о лучшем менеджере фирмы
function best RETURN managers%ROWTYPE;

-- прототип  процедуры, удаляющей  менеджера
procedure del(manr_id number);
END manager;
/

create or replace package body manager is

-- функция возвращает менеджера, получившего наибольшее
-- количество прибыли
function best RETURN managers%ROWTYPE
AS
 m managers%ROWTYPE;
BEGIN
 select * INTO m
 FROM managers WHERE manager_id=1;
  RETURN m;
END;

-- процедура удаления менеджера
procedure del(man_id number)
AS
 mcount number;
BEGIN
 select manager_id INTO mcount FROM managers WHERE manager_id=man_id;
 delete from outgoing where manager_id=man_id;
 delete from incoming where manager_id=man_id;
 delete from managers where manager_id=man_id;
 EXCEPTION
 WHEN NO_DATA_FOUND THEN
 Raise_Application_Error (-20001, `Such manager is not found`);
END;

-- функция возвращает текущее количество менеджеров
-- (функция видна только из методов пакета)
function mCount
RETURN number
AS
 m number;
BEGIN
 select count(*) into m from managers;
  RETURN m;
END;

-- при инициализации пакета в переменную count помещается
-- текущее число менеджеров
BEGIN
  count:=mCount;
END;
```

В спецификации данного пакета указана одна переменная, одна функция и одна процедура. Это то, что видно "снаружи", то есть то, что может использовать пользователь в своей работе с этим пакетом (примеры использования данного пакеты приведены ниже).

Выполняемая секция (execution section) тела пакета содержит одно SQL предложение, цель которого поместить в переменную count текущее количество менеджеров в информационной системе. Данное предложение будет вызвано всего лишь один раз при любом первом обращении к любому из объектов пакета, поэтому вряд ли значение переменной count всегда будет актуально, но в данном случае она использована в учебных целях.

Значение переменной count приравнивается результату выполнения функции managersCount, которая не видна "снаружи", а доступна только из процедур и функций самого пакета.

Тип managers%ROWTYPE в описании прототипа функции bestManager означает, что тип возвращаемого функцией значения зависит от текущей структуры таблицы managers. Данный тип называется _динамической записью_ и помогает создать определенную гибкость в работе с объектами информационной системы.

Если посмотреть на реализацию функции bestManager, то станет понятно, зачем это было сделано. Даже при изменении стуктуры таблицы - удалении или добавлении полей - функция bestManager будет работать правильно.

В реализации процедуры удаления менеджера показан пример использования исключения. Если при вызове процедуры будет указан номер несуществующего менеджера, то при попытке определить номер менеджера в первом операторе процедуры возникнет исключительная ситуация NO_DATA_FOUND, обработчик которой находится в секции исключений. В этом случае работа процедуры прекратится и на экран выдастся сообщение об ошибке `Such manager is not found`.

В качестве первого параметра функции RAISE_APPLICATION_ERROR, содержащего номер ошибки, можно указывать любое число в пределах от -20000 до -20999.

Последующие три оператора DELETE удаляют из базы данных всю информацию, как-то связанную с указанным менеджером. Поскольку между таблицами MANAGERS, INCOMING и OUTGOING существуют связи, удаление информации нужно производить именно в таком порядке: сначала из дочерних таблиц, а затем из родительской.

## Использование пакета

Для того, чтобы вывести на экран текущее количество менеджеров, то есть значение переменной count пакета doManagers, можно поступить следующим образом:

```
begin
dbms_output.put_line(manager.count);
end;
```

Для того, чтобы вывести на экран имя самого удачливого менеджера, можно написать, например, блок такого вида:

```
declare
m managers%ROWTYPE;
begin
m:=manager.best;
dbms_output.put_line(m.name);
end;
```
