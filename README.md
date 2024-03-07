# sql_academy_solutions
Решение задач по SQL. Тренажер [sql-academy.org](https://sql-academy.org/ru/trainer)









`Задание 1: Вывести имена всех когда-либо обслуживаемых пассажиров авиакомпаний`

```sql
SELECT name from Passenger;
```

`Задание 2: Вывести названия всеx авиакомпаний`
```sql
SELECT name FROM Company;
```

`Задание 3: Вывести все рейсы, совершенные из Москвы`
```sql
SELECT * FROM Trip
WHERE town_from = 'Moscow';
```

`Задание 4: Вывести имена людей, которые заканчиваются на "man"`

```sql
SELECT name FROM Passenger
WHERE name LIKE '%man';
```

`Задание 5: Вывести количество рейсов, совершенных на TU-134`
```sql
SELECT COUNT(*) AS count FROM Trip
WHERE plane = 'TU-134';
```

`Задание 6: Какие компании совершали перелеты на Boeing`
```sql
SELECT DISTINCT name FROM Company
	JOIN Trip ON Company.id = Trip.company
WHERE plane = 'Boeing';
```


`Задание 7: Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)`
```sql
SELECT plane FROM Trip
WHERE town_to = 'Moscow'
GROUP BY plane;
```


`Задание 8: В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?`
```sql
SELECT town_to, TIMEDIFF(time_in, time_out) AS flight_time
FROM Trip
WHERE town_from = 'Paris';
```


`Задание 9: Какие компании организуют перелеты из Владивостока (Vladivostok)?`
```sql
SELECT name
from Company
	inner join Trip on Trip.company = Company.id
where Trip.town_to = 'Vladivostok'
```

`Задание 10: Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г.`
```sql
SELECT *
from Trip
where time_out between '1900-01-01T10:00:00.000Z' and '1900-01-01T14:00:00.000Z'
```


`Задание 11: Вывести пассажиров с самым длинным именем`
```sql
SELECT name FROM Passenger
ORDER BY LENGTH(name) DESC LIMIT 1;
```


`Задание 12: Вывести id и количество пассажиров для всех прошедших полётов`
```sql
SELECT trip,
	COUNT(passenger) as count
FROM Pass_in_trip
GROUP BY trip
```


`Задание 13: Вывести имена людей, у которых есть полный тёзка среди пассажиров`
```sql
SELECT name
FROM Passenger
GROUP BY name
HAVING COUNT(name) > 1
```


`Задание 14: В какие города летал Bruce Willis`
```sql
SELECT town_to
FROM Trip
	INNER JOIN Pass_in_trip ON Trip.id = Pass_in_trip.trip
	INNER JOIN Passenger ON Pass_in_trip.passenger = Passenger.id
WHERE Passenger.name = 'Bruce Willis'
```


`Задание 15: Во сколько Стив Мартин (Steve Martin) прилетел в Лондон (London)`
```sql
SELECT time_in
FROM Trip
	INNER JOIN Pass_in_trip ON Trip.id = Pass_in_trip.trip
	INNER JOIN Passenger ON Pass_in_trip.passenger = Passenger.id
WHERE Passenger.name = 'Steve Martin'
	AND Trip.town_to = 'London'
```


`Задание 16: Вывести отсортированный по количеству перелетов (по убыванию) и имени (по возрастанию) список пассажиров, совершивших хотя бы 1 полет.`
```sql
SELECT COUNT(Pass_in_trip.passenger) as count,
	Passenger.name as name
FROM Pass_in_trip
	INNER join Passenger on Pass_in_trip.passenger = Passenger.id
GROUP By Passenger.name
ORDER by count DESC,
	name
```

`Задание 17: Определить, сколько потратил в 2005 году каждый из членов семьи`
```sql
select member_name,
	status,
	sum(Payments.amount * Payments.unit_price) as costs
from FamilyMembers
	INNER join Payments on Payments.family_member = FamilyMembers.member_id
WHERE YEAR(Payments.date) = 2005
GROUP by status,
	member_name
```

`Задание 18: Узнать, кто старше всех в семьe`
```sql
select member_name
from FamilyMembers
WHERE birthday = (
		select birthday
		from FamilyMembers
		order by birthday
		limit 1
	)
```


`Задание 19: Определить, кто из членов семьи покупал картошку (potato)`
```sql
select status
from FamilyMembers
	INNER JOIN Payments on Payments.family_member = FamilyMembers.member_id
	INNER JOIN Goods on Goods.good_id = Payments.good
where Goods.good_name = 'potato'
group by status
```


`Задание 20: Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму`
```sql
select status,
	member_name,
	sum(Payments.amount * unit_price) as costs
from FamilyMembers
	INNER JOIN Payments on Payments.family_member = FamilyMembers.member_id
	INNER join Goods on Goods.good_id = Payments.good
	INNER join GoodTypes on Goods.type = GoodTypes.good_type_id
WHERE GoodTypes.good_type_name = 'entertainment'
GROUP by status,
	member_nameb
```


`Задание 21: Определить товары, которые покупали более 1 раза`
```sql
SELECT good_name
FROM Goods
	JOIN Payments ON Payments.good = Goods.good_id
GROUP BY good_name
HAVING COUNT(Payments.good) > 1
```


`Задание 22: Найти имена всех матерей (mother)`
```sql
SELECT member_name
from FamilyMembers
where status = 'mother'
```


`Задание 23: Найдите самый дорогой деликатес (delicacies) и выведите его цену`
```sql
SELECT Goods.good_name,
	Payments.unit_price
FROM Payments
	INNER JOIN Goods ON Goods.good_id = Payments.good
where Payments.unit_price = (
		SELECT max(Payments.unit_price)
		FROM Payments
			INNER JOIN Goods ON Goods.good_id = Payments.good
			INNER JOIN GoodTypes ON GoodTypes.good_type_id = Goods.type
		where GoodTypes.good_type_name = 'delicacies'
	)
```


`Задание 24: Определить кто и сколько потратил в июне 2005`
```sql
select FamilyMembers.member_name,
	SUM(Payments.unit_price * Payments.amount) as costs
from FamilyMembers
	INNER JOIN Payments on Payments.family_member = FamilyMembers.member_id
where year(Payments.date) = 2005
	and MONTH(Payments.date) = 6
GROUP by FamilyMembers.member_name
```


`Задание 25: Определить, какие товары имеются в таблице Goods, но не покупались в течение 2005 года`
```sql
SELECT Goods.good_name
FROM Goods
WHERE Goods.good_id != all (
		SELECT good
		from Payments
		where year(Payments.date) = 2005
		GROUP by good
	)
```



`Задание 26: Определить группы товаров, которые не приобретались в 2005 году`
```sql
SELECT good_type_name
FROM GoodTypes
WHERE good_type_id != all (
		SELECT GoodTypes.good_type_id
		from Payments
			JOIN Goods on Goods.good_id = Payments.good
			JOIN GoodTypes on GoodTypes.good_type_id = Goods.type
		where year(Payments.date) = 2005
		GROUP by GoodTypes.good_type_id
	)
```

`Задание 27: Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумму`
```sql
SELECT GoodTypes.good_type_name,
	sum(Payments.unit_price * Payments.amount) AS costs
FROM Payments
	JOIN Goods ON Goods.good_id = Payments.good
	JOIN GoodTypes ON GoodTypes.good_type_id = Goods.type
WHERE YEAR(Payments.date) = 2005
Group by GoodTypes.good_type_name
```


`Задание 28: Сколько рейсов совершили авиакомпании с Ростова (Rostov) в Москву (Moscow) ?`
```sql
with w as(
	select id
	from trip
	WHERE town_to = 'Rostov'
		and town_from = 'Moscow'
)
select count(id) as count
from w
```


`Задание 29: Выведите имена пассажиров улетевших в Москву (Moscow) на самолете TU-134`
```sql
SELECT Passenger.name
FROM Passenger
	JOIN Pass_in_trip ON Passenger.id = Pass_in_trip.passenger
	JOIN Trip ON Trip.id = Pass_in_trip.trip
WHERE Trip.town_to = 'Moscow'
	AND Trip.plane = 'TU-134'
GROUP By Passenger.name
```


`Задание 30: Выведите нагруженность (число пассажиров) каждого рейса (trip). Результат вывести в отсортированном виде по убыванию нагруженности.`
```sql
select Trip.id as trip,
	count(Pass_in_trip.passenger) as count
from Trip
	JOIN Pass_in_trip on Pass_in_trip.trip = Trip.id
GROUP by Trip.id
ORDER by count Desc
```


`Задание 31: Вывести всех членов семьи с фамилией Quincey.`
```sql
select FamilyMembers.*
from FamilyMembers
where FamilyMembers.member_name like '%Quincey'
```


`Задание 32: Вывести средний возраст людей (в годах), хранящихся в базе данных. Результат округлите до целого в меньшую сторону.`
```sql
SELECT floor(avg(2023 - YEAR(birthday))) AS age
FROM FamilyMembers
```


`Задание 33: Найдите среднюю стоимость икры. В базе данных хранятся данные о покупках красной (red caviar) и черной икры (black caviar).`
```sql
select avg(Payments.unit_price) as cost
from Payments
	JOIN Goods on Goods.good_id = Payments.good
where good_name like '%caviar'
```


`Задание 34: Сколько всего 10-ых классов?`
```sql
select count(Class.name) as count
from Class
WHERE Class.name like '10%'
```

`Задание 35: Сколько различных кабинетов школы использовались 2.09.2019 в образовательных целях ?`
```sql
SELECT DISTINCT COUNT(classroom) AS count FROM Schedule
WHERE date LIKE '2019-09-02%';
```


`Задание 36: Выведите информацию об обучающихся живущих на улице Пушкина (ul. Pushkina)?`
```sql
select *
from Student
WHERE Student.address like 'ul. Pushkina%'
```


`Задание 37: Сколько лет самому молодому обучающемуся ?`
```sql
select min(2023 - YEAR(Student.birthday)) as year
from Student
```

`Задание 38: Сколько Анн (Anna) учится в школе ?`
```sql
SELECT COUNT(1) As count
FROM  Student
WHERE first_name LIKE 'Anna';
```


`Задание 39: Сколько обучающихся в 10 B классе ?`
1)
```sql
SELECT COUNT(class) AS count FROM Student_in_class
JOIN Class ON Class.id=class WHERE name LIKE '10 B';
```
2)
```sql
SELECT COUNT(Student.id) as count
FROM Student
	JOIN Student_in_class ON Student_in_class.student = Student.id
	JOIN Class ON Class.id = Student_in_class.class
WHERE Class.name = '10 B'
GROUP BY Class.name
```

`Задание 40: Выведите название предметов, которые преподает Ромашкин П.П. (Romashkin P.P.) ?`
```sql
SELECT name as subjects
FROM Subject
	JOIN Schedule ON Subject.id = Schedule.subject
	JOIN Teacher ON Teacher.id = Schedule.teacher
WHERE Teacher.last_name = 'Romashkin'
	AND Teacher.first_name LIKE 'P%'
	AND Teacher.middle_name LIKE 'P%'
```


`Задание 41: Во сколько начинается 4-ый учебный предмет по расписанию ?`
```sql
select start_pair
from Timepair
where id = 4
```


`Задание 42: Сколько времени обучающийся будет находиться в школе, учась со 2-го по 4-ый уч. предмет ?`
```sql
SELECT DISTINCT TIMEDIFF(
		(
			SELECT end_pair
			FROM Timepair
			WHERE id = 4
		),
(
			SELECT start_pair
			FROM Timepair
			WHERE id = 2
		)
	) as time
from Timepair
```


`Задание 43: Выведите фамилии преподавателей, которые ведут физическую культуру (Physical Culture). Остортируйте преподавателей по фамилии.`
```sql
select last_name
from Teacher
	JOIN Schedule on Schedule.teacher = Teacher.id
	JOIN Subject on Subject.id = Schedule.subject
WHERE Subject.name = 'Physical Culture'
ORDER BY last_name
```


`Задание 44: Найдите максимальный возраст (колич. лет) среди обучающихся 10 классов ?`
```sql
select max(YEAR(Now()) - YEAR(Student.birthday)) as max_year
from Student
	JOIN Student_in_class on Student_in_class.student = Student.id
	JOIN Class on Class.id = Student_in_class.class
where Class.name like '10%'
```

`Задание 45: Какой(ие) кабинет(ы) пользуются самым большим спросом?`
```sql
SELECT classroom
FROM Schedule
GROUP BY classroom
HAVING COUNT(classroom) = (
		SELECT COUNT(classroom) as count
		FROM Schedule
		GROUP BY classroom
		ORDER by count DESC
		LIMIT 1
	)
```



`Задание 46: В каких классах введет занятия преподаватель "Krauze" ?`
```sql
select name
from class
	JOIN Schedule on Schedule.class = Class.id
	JOIN Teacher on Teacher.id = Schedule.teacher
WHERE Teacher.last_name = 'Krauze'
GROUP by name
```



`Задание 47: Сколько занятий провел Krauze 30 августа 2019 г.?`
```sql
SELECT COUNT(teacher) AS count FROM Schedule
JOIN Teacher ON Teacher.id=Schedule.teacher AND last_name = 'Krauze'
WHERE date LIKE '2019-08-30%';
```


`Задание 48: Выведите заполненность классов в порядке убывания`
```sql
select Class.name,
	count(Student_in_class.student) as count
from Class
	JOIN Student_in_class on Student_in_class.class = Class.id
GROUP by Class.name
ORDER by count desc
```


`Задание 49: Какой процент обучающихся учится в 10 A классе ?`
```sql
SELECT COUNT(student) * 100 /(
		SELECT COUNT(student)
		FROM Student_in_class
	) as percent
FROM Student_in_class
	JOIN Class ON Class.id = Student_in_class.class
WHERE Class.name = '10 A'
```


`Задание 50: Какой процент обучающихся родился в 2000 году? Результат округлить до целого в меньшую сторону.`
```sql
SELECT floor(
		COUNT(id) * 100 / (
			SELECT COUNT(id)
			FROM Student
		)
	) AS percent
FROM Student
WHERE YEAR(birthday) = 2000
```
