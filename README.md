
CREATE DATABASE IF NOT EXISTS E_comm;
USE E_comm;

-- Category Table
CREATE TABLE category (
    cat_id INT AUTO_INCREMENT PRIMARY KEY,
    cat_name VARCHAR(30) NOT NULL
);

-- Product Table
CREATE TABLE product (
    pid INT AUTO_INCREMENT PRIMARY KEY,
    pname VARCHAR(40) NOT NULL,
    cat_id INT,
    price DECIMAL(10,2) NOT NULL,
    qty_in_stock INT NOT NULL,
    status VARCHAR(20) DEFAULT 'In Stock',
    FOREIGN KEY (cat_id) REFERENCES category(cat_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);

-- Customers Table
drop table if exists customers;
CREATE TABLE customers (
    cid INT AUTO_INCREMENT PRIMARY KEY,
    cname VARCHAR(30) NOT NULL,
    cphone VARCHAR(30) NOT NULL UNIQUE,
    caddress VARCHAR(100) NOT NULL,
    email VARCHAR(50) NOT NULL UNIQUE
);

-- Orders Table
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    cid INT,
    purchase_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (cid) REFERENCES customers(cid)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Order Details Table
CREATE TABLE order_detail (
    ord_dtl_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    pid INT,
    quantity_purchased INT NOT NULL,
    total DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (pid) REFERENCES product(pid)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);


-- Trigger

DELIMITER $

CREATE TRIGGER trg_after_order_insert
AFTER INSERT ON order_detail
FOR EACH ROW
BEGIN
    UPDATE product
    SET qty_in_stock = qty_in_stock - NEW.quantity_purchased
    WHERE pid = NEW.pid;
    UPDATE product
    SET status = CASE

			WHEN qty_in_stock <= 0 THEN 'Out of Stock'
			ELSE 'In Stock'
	END
    WHERE pid = NEW.pid;
    
END $

DELIMITER ;

-- Stored Procedure
DELIMITER $$

CREATE PROCEDURE place_order(
    IN p_cid INT,
    IN p_pid INT,
    IN p_qty INT
)
BEGIN
    DECLARE total_price DECIMAL(10,2);
    SELECT price * p_qty INTO total_price
    FROM product
    WHERE pid = p_pid;

    INSERT INTO orders (cid) VALUES (p_cid);
    SET @order_id = LAST_INSERT_ID();
    INSERT INTO order_detail (order_id, pid, quantity_purchased, total)
    VALUES (@order_id, p_pid, p_qty, total_price);
END $$

DELIMITER ;

show tables;

show create table category;
show create table product;
show create table customers;
show create table orders;
show create table order_detail;


INSERT INTO category (cat_name) VALUES 
('Electronics'),
 ('Clothing') ;
 select * from category;

INSERT INTO product (pname, cat_id, price, qty_in_stock)
VALUES ('Headphones', 1, 1500.00, 10),
       ('T-Shirt', 2, 500.00, 5);
       
INSERT INTO product (pname, cat_id, price, qty_in_stock)
values
       ('Shirt', 2, 300.25, 1),
       ('Laptop', 1, 45000, 5);
       

select * from product;

INSERT INTO customers (cname, cphone, caddress, email)
VALUES ('Rahul Mehta', '9876543200', 'Delhi', 'rahul@gmail.com');

INSERT INTO customers (cname, cphone, caddress, email)
values 
('Anjali yadav', '7021036390', 'Pune', 'anjali@gmail.com'),
('Jon Jones','7273747678', 'Philedelphia', 'jjones@gmail.com'),
('Alex preira','2265498723', 'Brazil', 'alexp1@gmail.com'),
('Virat Kohli', '7072454560', 'Delhi', 'kohli18@gmail.com');
select * from customers;

call place_order(2, 5, 2);

select * from order_detail;

select * from product;

select * from category;
select * from orders;






