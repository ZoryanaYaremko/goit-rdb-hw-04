# goit-rdb-hw-04
-- 1. Створення бази даних
CREATE SCHEMA IF NOT EXISTS LibraryManagement;
use LibraryManagement;

CREATE TABLE IF NOT EXISTS authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(50) NOT NULL
);

CREATE TABLE IF NOT EXISTS genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name  VARCHAR(50) NOT NULL
);

CREATE TABLE IF NOT EXISTS books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(50) NOT NULL,
    publication_year YEAR,
    author_id INT NOT NULL,
    genre_id INT NOT NULL,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE IF NOT EXISTS users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100),
    email VARCHAR(100)
);

CREATE TABLE IF NOT EXISTS borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
![image](https://github.com/user-attachments/assets/464c8204-288e-4bee-8f3c-54d00e849f1c)
![image](https://github.com/user-attachments/assets/cec92250-eb24-4062-bbea-d84c1fac8f85)

-- 2.1 Додавання даних до authors
INSERT INTO authors (author_name) VALUES
('J.K. Rowling'),
('George Orwell'),
('Stephen King');
![image](https://github.com/user-attachments/assets/e499192b-0a24-4495-ba59-43f7a54e0585)

-- 2.2 Додавання даних до genres
INSERT INTO genres (genre_name) VALUES
('Fantasy'),
('Dystopian'),
('Horror');
![image](https://github.com/user-attachments/assets/09cec71e-0565-41f5-8329-4048ed8fc97c)

-- 2.3 Додавання даних до books
INSERT INTO books (title, publication_year, author_id, genre_id) VALUES
('Harry Potter and the Sorcerer''s Stone', 1997, 1, 1),
('1984', 1949, 2, 2),
('The Shining', 1977, 3, 3);
![image](https://github.com/user-attachments/assets/bf6aed63-6d64-436b-8ba6-9a11250a9264)


-- 2.4 Додавання даних до users
INSERT INTO users (username, email) VALUES
('reader1', 'reader1@example.com'),
('booklover', 'booklover@example.com'),
('horrorfan', 'horrorfan@example.com');
![image](https://github.com/user-attachments/assets/871b549b-3fe4-4567-a03c-aee6425f0c65)

-- 2.5 Додавання даних до borrowed_books
INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date) VALUES
(1, 1, '2025-07-01', '2025-07-15'),
(2, 2, '2025-07-02', '2025-07-16'),
(3, 3, '2025-07-03', NULL);
![image](https://github.com/user-attachments/assets/381a1db5-299b-440f-bbf6-b574e8a0de58)

-- 3. JOIN-запит, який об'єднує всі таблиці
USE hw3;

SELECT *
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers s ON o.shipper_id = s.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id;
![image](https://github.com/user-attachments/assets/0935f699-7cd2-4521-9f36-8fbd334e33e3)
![image](https://github.com/user-attachments/assets/35a1257e-f818-462e-be11-76bc9d5fb179)
![image](https://github.com/user-attachments/assets/4d3010b9-fe35-4ffd-af64-fa0bbd474ac7)
![image](https://github.com/user-attachments/assets/7a01d2f9-118e-4bb1-99e8-5fbae2bc58cd)

--  4.1. Визначення кількості отриманих рядків (з оператором COUNT)
USE hw3;
SELECT COUNT(*)
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN products p ON od.product_id = p.id
INNER JOIN categories cat ON p.category_id = cat.id
INNER JOIN employees e ON o.employee_id = e.employee_id
INNER JOIN shippers s ON o.shipper_id = s.id
INNER JOIN suppliers sup ON p.supplier_id = sup.id;
![image](https://github.com/user-attachments/assets/743794b4-0486-4e89-a7dc-92127947fcbd)

-- 4.2. Змінно декілька INNER на LEFT та RIGHT 
USE hw3;
SELECT COUNT(*) AS total_rows
FROM order_details od
LEFT JOIN orders o ON od.order_id = o.id
RIGHT JOIN products p ON od.product_id = p.id
LEFT JOIN categories cat ON p.category_id = cat.id
RIGHT JOIN suppliers sup ON p.supplier_id = sup.id;
![image](https://github.com/user-attachments/assets/6f2898f2-7193-4d6b-bb3d-c601dd51b495)
Для перевірки впливу типів з’єднання я змінила кілька операторів INNER JOIN на LEFT JOIN і RIGHT JOIN у запиті, який об’єднує таблиці order_details, orders, products, categories, suppliers.
При виконанні запиту з міксом LEFT і RIGHT я отримала ту ж кількість рядків, що і при використанні INNER JOIN — 518 рядків.
Це означає, що всі таблиці містять повні відповідності між зовнішніми та первинними ключами, тобто всі замовлення мають пов’язані продукти, клієнтів, постачальників, категорії тощо.
Таким чином, зміна типу JOIN не вплинула на результат, оскільки немає пропущених або "висячих" записів у базі даних.

--  4.3. employee_id > 3 та ≤ 10
SELECT *
FROM order_details od
INNER JOIN orders o ON od.order_id = o.id
INNER JOIN employees e ON o.employee_id = e.employee_id
WHERE e.employee_id > 3 AND e.employee_id <= 10;
![image](https://github.com/user-attachments/assets/5c99e845-fd2b-49fa-939a-78ed41a7413c)

-- 4.4. Групування за категорією: COUNT + AVG(quantity)
SELECT 
    cat.name AS category_name,
    COUNT(*) AS row_count,
    AVG(od.quantity) AS avg_quantity
FROM order_details od
JOIN products p ON od.product_id = p.id
JOIN categories cat ON p.category_id = cat.id
GROUP BY cat.name;
![image](https://github.com/user-attachments/assets/6e851eb4-0a56-4a46-a39b-250fb1a4163d)

-- 4.5. Фільтрація: середня кількість товару > 21
SELECT 
    cat.name AS category_name,
    COUNT(*) AS row_count,
    AVG(od.quantity) AS avg_quantity
FROM order_details od
JOIN products p ON od.product_id = p.id
JOIN categories cat ON p.category_id = cat.id
GROUP BY cat.name
HAVING AVG(od.quantity) > 21;
![image](https://github.com/user-attachments/assets/cf01678f-591b-42a9-9ef5-c7bf83b19fa3)

-- 4.6. Сортування за спаданням кількості рядків
SELECT 
    cat.name AS category_name,
    COUNT(*) AS row_count,
    AVG(od.quantity) AS avg_quantity
FROM order_details od
JOIN products p ON od.product_id = p.id
JOIN categories cat ON p.category_id = cat.id
GROUP BY cat.name
ORDER BY row_count DESC;
![image](https://github.com/user-attachments/assets/a741d517-c0f0-4b1f-a83b-f921c95558fe)

-- 4.7. Вивести чотири рядки, пропустивши перший
SELECT od.order_id, AVG(quantity) AS seredna
FROM order_details AS od
INNER JOIN orders AS o ON od.order_id = o.id
INNER JOIN products AS pr ON od.product_id = pr.id
INNER JOIN customers AS cs ON o.customer_id = cs.id
WHERE od.product_id > 3
GROUP BY od.order_id
HAVING seredna > 10
ORDER BY od.order_id DESC
LIMIT 4 OFFSET 1;
![image](https://github.com/user-attachments/assets/3bb19885-052b-4279-b6ae-d00a7accc24e)










