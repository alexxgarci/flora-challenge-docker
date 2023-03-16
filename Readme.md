# 🐳 Docker + PHP 8.1 + MySQL + Nginx + Symfony 6.1 Boilerplate

## Description

This is a complete stack for running Symfony 6.1 into Docker containers using docker-compose tool with [docker-sync library](https://docker-sync.readthedocs.io/en/latest/).

It is composed by 4 containers:

- `nginx`, acting as the webserver.
- `php`, the PHP-FPM container with the 8.0 version of PHP.
- `db` which is the MySQL database container with a **MySQL 8.0** image.
- `symfony_docker_app_sync` to sync files using library `docker-sync `.

## Installation

1. 😀 Clone this rep.

2. Create the file `./.docker/.env.nginx.local` using `./.docker/.env.nginx` as template. The value of the variable `NGINX_BACKEND_DOMAIN` is the `server_name` used in NGINX.

3. Go inside folder `./docker` and run `docker-sync-stack start` to start containers.

4. You should work inside the `php` container. This project is configured to work with [Remote Container](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension for Visual Studio Code, so you could run `Reopen in container` command after open the project.

5. Inside the `php` container, run `composer install` to install dependencies from `/var/www/symfony` folder.

6. Use the following value for the DATABASE_URL environment variable:

```
DATABASE_URL=mysql://app_user:helloworld@db:3306/app_db?serverVersion=8.0.23
```

You could change the name, user and password of the database in the `env` file at the root of the project.


## To launch the project
### Exercise 1

- *Example 1* (Product A added + Product C added + Voucher S added + Product A added + Voucher V added + Product B added):

    + at `./docker` folder:
    ``` $ docker-compose run php bin/console flora:calculate:cart --example=1 ```
    + or inside the `php` container:
    ``` $ bin/console flora:calculate:cart --example=1 ```
- *Example 2* (Product A added + Voucher S added + Product A added + Voucher V added + Product B added + Voucher R added + Product C added + Product C added + Product C added):

    + at `./docker` folder:
    ``` $ docker-compose run php bin/console flora:calculate:cart --example=2 ```
    + or inside the `php` container:
    ``` $ bin/console flora:calculate:cart --example=2 ```
- Tests:

    ``` $ docker-compose run php vendor/phpunit/phpunit/phpunit --testdox src/AddToCartCommandTest.php ```

#### Expected result

    ========================Flora Challenge Begin======================
    
    +-------------------- Cart ------------+---------+
    | id                                   | price   |
    +--------------------------------------+---------+
    | A                                    | order 1 |
    | B                                    | order 2 |
    +----------------- total cart: 2€ -----+---------+
    
    ========================Flora Challenge End========================

### Exercise 2

1. 
    ```
    SELECT 
        YEAR(order_date) AS year, 
        MONTH(order_date) AS month, 
        SUM(total_amount) AS revenue 
    FROM 
        Orders 
    WHERE 
        YEAR(order_date) = 2022 
    GROUP BY 
        YEAR(order_date), 
        MONTH(order_date);
    ```

2. 
    ```
    SELECT 
        Products.name, 
        SUM(Order_Items.quantity * Order_Items.unit_price) AS revenue 
    FROM 
        Products 
    INNER JOIN 
        Order_Items 
    ON 
        Products.id = Order_Items.product_id 
    INNER JOIN 
        Orders 
    ON 
        Order_Items.order_id = Orders.id 
    WHERE 
        YEAR(Orders.order_date) = 2021 
    GROUP BY 
        Products.id 
    ORDER BY 
        revenue DESC 
    LIMIT 
        5;
    ```

3. 
    ```
    SELECT 
        Customers.name, 
        SUM(Orders.total_amount) AS total_spent 
    FROM 
        Customers 
    INNER JOIN 
        Orders 
    ON 
        Customers.id = Orders.customer_id 
    WHERE 
        YEAR(Orders.order_date) = 2020 
    GROUP BY 
        Customers.id 
    ORDER BY 
        total_spent DESC 
    LIMIT 
        10;
    ```

4. 
    ```
    SELECT 
        Customers.name, 
        AVG(Orders.total_amount) AS avg_order_value 
    FROM 
        Customers 
    INNER JOIN 
        Orders 
    ON 
        Customers.id = Orders.customer_id 
    WHERE 
        YEAR(Orders.order_date) = 2021 
    GROUP BY 
        Customers.id;
    ```


5. 
    ```
    SELECT 
        SUM(total_amount) AS revenue 
    FROM 
        Orders 
    INNER JOIN 
        Order_Items 
    ON 
        Orders.id = Order_Items.order_id 
    INNER JOIN 
        Products 
    ON 
        Order_Items.product_id = Products.id 
    WHERE 
        Products.name LIKE '%blue%';
    ```

