# HW1
Описание/Пошаговая инструкция выполнения домашнего задания:
создать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере , например postgres2022-, где yyyymmdd год, месяц и день вашего рождения (имя проекта должно быть уникально на уровне GCP)
--создал новый проект в яндекс облаке
далее создать инстанс виртуальной машины Compute Engine с дефолтными параметрами
--создал виртуальную машину 
добавить свой ssh ключ в GCE metadata
--сгенерировал в puttygen ключи (приватный и публичный), добавили в инстанс виртуальной машины
зайти удаленным ssh (первая сессия), не забывайте про ssh-add
--настроил коннект в путти указал логин denis@публичный ip адрес, в авторизации указал ключ 
поставить PostgreSQL
--sudo apt-get -y install postgresql
зайти вторым ssh (вторая сессия)
выполнел 2й коннект к инстансу (использовал сохраненное соединение в pytty)
запустить везде psql из под пользователя postgres
\echo :AUTOCOMMIT
--sudo -u postgres psql
выключить auto commit
--\set AUTOCOMMIT OFF
сделать в первой сессии новую таблицу и наполнить ее данными create table persons(id serial, first_name text, second_name text); insert into persons(first_name, second_name) values('ivan', 'ivanov'); insert into persons(first_name, second_name) values('petr', 'petrov'); commit;

посмотреть текущий уровень изоляции: 
--show transaction isolation level;  
--read committed--

начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции

в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');

сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?
--нет, тк отключен автокоммит и не выполнили коммит после инсерта

завершить первую транзакцию - commit;
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?
--да, тк выполнили коммит в первой сессии после инсерта
завершите транзакцию во второй сессии

начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;
--set transaction isolation level repeatable read;
--show transaction isolation level;
--repeatable read
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');

сделать select * from persons; во второй сессии
видите ли вы новую запись и если да то почему?
--не вижу, тк не выполнен commit
завершить первую транзакцию - commit;

сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?
--не вижу не допускается фантомное чтение
завершить вторую транзакцию
--commit;

сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?
--вижу после коммита во 2й сессии
