**План тестирования web интернет-магазина**

### Этап 1: Анализ требований (1 неделя)
- Изучение технической документации, макетов, спецификаций.
- Определение критических пользовательских сценариев.
- Выявление потенциальных рисков.
- Определение необходимых инструментов тестирования.
- Согласование плана тестирования с командой разработки.

### Этап 2: Подготовка тестовой документации (2 неделя)
- Разработка тест-плана.
- Составление чек-листов для основных функций.
- Определение тест-кейсов.
- Создание тестовых данных.

### Этап 3: Тестирование альфа-версии (3-5 недели)
- Функциональное тестирование: проверка всех заявленных функций.
- UI/UX тестирование: удобство интерфейса.
- API тестирование (Postman, Swagger): работа с внешними сервисами.
- Интеграционное тестирование.
- Первичное нагрузочное тестирование.

### Этап 4: Регрессионное тестирование (6-8 недели)
- Повторное тестирование после исправления багов.
- Автоматизация тестов для регрессии.
- Подготовка к релизу.

### Этап 5: Тестирование релизной версии (9-12 недели)
- Дымовое тестирование (Smoke Testing) на продакшене.
- Мониторинг производительности.
- Тестирование платежных систем.
- Завершающий отчет о тестировании.

### Этап 6: Поддержка в течение 1 месяца после выпуска
- Мониторинг багов.
- Анализ отзывов пользователей.
- Улучшение тестовой документации.
- Поддержка автоматизированных тестов.

**Метрики качества:**
- Количество критических багов.
- Покрытие тестами.
- Время на исправление ошибок.
- Количество повторных багов.

---

**Развертывание PostgreSQL в Docker**
```bash
docker run --name academy-db -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=academy -p 5432:5432 -d postgres
```

**Скрипт создания базы данных:**
```sql
CREATE TABLE Students (
    s_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    start_year INT NOT NULL
);

CREATE TABLE Courses (
    c_no SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    hours INT NOT NULL
);

CREATE TABLE Exams (
    s_id INT REFERENCES Students(s_id),
    c_no INT REFERENCES Courses(c_no),
    score INT CHECK (score BETWEEN 0 AND 100),
    PRIMARY KEY (s_id, c_no)
);
```

**Добавление данных:**
```sql
INSERT INTO Students (name, start_year) VALUES ('Иван Иванов', 2021), ('Мария Петрова', 2020);
INSERT INTO Courses (title, hours) VALUES ('Математика', 120), ('Физика', 100);
INSERT INTO Exams (s_id, c_no, score) VALUES (1, 1, 85);
```

**Запрос студентов без сданных экзаменов:**
```sql
SELECT s_id, name FROM Students WHERE s_id NOT IN (SELECT DISTINCT s_id FROM Exams);
```

**Запрос студентов с количеством сданных экзаменов:**
```sql
SELECT s.s_id, s.name, COUNT(e.c_no) AS exam_count
FROM Students s
JOIN Exams e ON s.s_id = e.s_id
GROUP BY s.s_id, s.name;
```

**Запрос курсов со средним баллом по экзаменам:**
```sql
SELECT c.c_no, c.title, AVG(e.score) AS avg_score
FROM Courses c
JOIN Exams e ON c.c_no = e.c_no
GROUP BY c.c_no, c.title
ORDER BY avg_score DESC;
```

**Генерация тестовых данных:**
```sql
DO $$
DECLARE i INT := 1;
BEGIN
    WHILE i <= 50 LOOP
        INSERT INTO Students (name, start_year) VALUES ('Student ' || i, 2019 + (i % 5));
        INSERT INTO Courses (title, hours) VALUES ('Course ' || i, 30 + (i * 2));
        INSERT INTO Exams (s_id, c_no, score) VALUES (i, (i % 10) + 1, (RANDOM() * 100)::INT);
        i := i + 1;
    END LOOP;
END $$;
```

