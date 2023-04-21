# dualboot_postgresql

## Создание и запуск docker контейнер
- docker run --name dbp-pg -e POSTGRES_PASSWORD=password -d postgres
- docker exec -it dbp-pg psql -U postgres

## Создание таблиц

1. Создание таблицы Regions
'''
CREATE TABLE Regions (
id SERIAL PRIMARY KEY,
Name VARCHAR(50) NOT NULL
);
'''

-----------------------------------------------------------------------

2. Создание таблицы Locations
'''
CREATE TABLE Locations (
id SERIAL PRIMARY KEY,
Name VARCHAR(50) NOT NULL,
Address VARCHAR(100) NOT NULL,
Region_id INTEGER,
FOREIGN KEY (Region_id) REFERENCES Regions(id)
);
'''

-----------------------------------------------------------------------

3. Создание таблицы Departments
'''
CREATE TABLE Departments (
id SERIAL PRIMARY KEY,
Name VARCHAR(50) NOT NULL,
Location_id INTEGER,
Manager_id INTEGER,
FOREIGN KEY (Location_id) REFERENCES Locations(id)
);
'''

-----------------------------------------------------------------------

4. Создание таблицы Employees
'''
CREATE TABLE Employees (
id SERIAL PRIMARY KEY,
Name VARCHAR(50) NOT NULL,
Last_name VARCHAR(50) NOT NULL,
Hire_date DATE NOT NULL,
Salary INTEGER NOT NULL,
Email VARCHAR(100) UNIQUE NOT NULL,
Manager_id INTEGER,
Department_id INTEGER,
FOREIGN KEY (Manager_id) REFERENCES Employees(id),
FOREIGN KEY (Department_id) REFERENCES Departments(id)
);
'''

-----------------------------------------------------------------------

5. Добавление внешнего ключа в таблицу Departments
'''
ALTER TABLE Departments ADD FOREIGN KEY (Manager_id) REFERENCES Employees(id);
'''

-----------------------------------------------------------------------



## Выборки:
### Показать работников у которых нет почты или почта не в корпоративном домене (домен dualbootpartners.com)
'''
SELECT * FROM Employees
WHERE Email NOT LIKE '%@dualbootpartners.com'
OR Email IS NULL;
'''
### Получить список работников нанятых в последние 30 дней
'''
SELECT * FROM Employees
WHERE Hire_date >= DATE_TRUNC('day', CURRENT_DATE - INTERVAL '30 days');
'''
### Найти максимальную и минимальную зарплату по каждому департаменту
'''
SELECT Department_id, MIN(Salary) AS MinimalSalary, MAX(Salary) AS MaximumSalary
FROM Employees
GROUP BY Department_id;
'''
### Посчитать количество работников в каждом регионе
'''
SELECT Regions.Name AS Region, COUNT(Employees.id)
FROM Regions
JOIN Locations ON Locations.Region_id = Regions.id
JOIN Departments ON Departments.Location_id = Locations.id
JOIN Employees ON Employees.Department_id = Departments.id
GROUP BY Regions.Name;
'''
### Показать сотрудников у которых фамилия длиннее 10 символов
'''
SELECT * FROM Employees
WHERE LENGTH(Last_name) > 10;
'''
### Показать сотрудников с зарплатой выше средней по всей компании
'''
SELECT * FROM Employees
WHERE Salary > (SELECT AVG(Salary) FROM Employees);
'''