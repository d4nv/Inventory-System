# Inventory System (Java + MySQL)

> Fork of [ssjgud12/Inventory-System](https://github.com/ssjgud12/Inventory-System). This was a **team project** (three students) for the Object-Oriented Programming module at ATU Galway, spring 2025.

A console inventory and ordering system written in Java. It stores everything in a MySQL database through JDBC. Users log in and get a different menu for each role: **Admin**, **Manager** or **Customer**.

## Features

- **Login and registration:** users are checked against the `user` table with prepared statements. New sign-ups get the Customer role by default. A duplicate username or email is caught and reported.
- **Role-based dashboards:** after login, a dispatcher sends each user to the Admin, Manager or Customer menu.
- **Admin:** view all users, add, edit and delete products, and view all orders.
- **Manager:** view a stock report (each product and its quantity) and view customer orders.
- **Customer:** browse the product catalogue, add items to a basket, view the basket with line totals, and check out.
- **Transactional checkout:** checkout runs as one database transaction. It creates the order and its line items, reduces stock (the update never takes stock below zero), records a "Stock Out" entry in `inventory_transaction`, and empties the basket.

## Database

The schema has 8 MySQL tables: `user`, `category`, `supplier`, `product`, `orders`, `order_items`, `basket` and `inventory_transaction`. `inventory.sql` creates them and fills them with sample data, including test accounts for each role. There's a screenshot of the schema in `MySQL Workbench 3_29_2025 6_37_27 PM.png`, and the entity design is written up in `inventory system doc.docx`.

## Tech

- Java (IntelliJ IDEA project, configured for JDK 24)
- MySQL and MySQL Connector/J 9.3.0 (JDBC, `MysqlDataSource`)
- Git and GitHub, with a branch per team member merged by pull request

## How to run

1. Install MySQL and run `inventory.sql`. It creates the `inventorysystem` database with sample data:
   ```bash
   mysql -u root -p < inventory.sql
   ```
2. Open the project in IntelliJ IDEA and add **MySQL Connector/J** to the module's libraries.
3. Set your MySQL username and password in `src/main/java/ie/atu/pool/DatabaseUtils.java`. The URL defaults to `jdbc:mysql://localhost:3306/inventorysystem`.
4. Run `ie.atu.standard.MainMenu`, then log in with one of the sample accounts from `inventory.sql` or register a new one.

## Project structure

```
src/main/java/ie/atu/
├── pool/
│   └── DatabaseUtils.java          # MySQL DataSource and connections
└── standard/
    ├── MainMenu.java               # entry point: login / register / exit
    ├── Dashboard.java              # sends each user to their role's menu
    ├── AdminDashboard.java, ManagerDashboard.java, CustomerDashboard.java
    ├── AddProductService.java, EditProductService.java,
    │   DeleteProductService.java, ProductManagementService.java
    ├── ProductService.java         # catalogue and add-to-basket
    ├── BasketService.java          # basket and transactional checkout
    ├── OrderService.java, StockService.java, ViewUsers.java
    └── Account.java, Login.java    # simple model classes
inventory.sql                        # schema and sample data
```

(`TestConnection`, `SelectExample`, `UpdateExample` and `LoginExample` are small JDBC examples from early in the project.)

## My role

There were three of us on the team. A teammate set up the first database schema and the JDBC connection examples. Another added most of the SQL sample data. I wrote most of the Java application:

- the login and account features, and the main menu
- the Admin, Manager and Customer dashboards, plus the role dispatcher
- product management (add, edit, delete) and the product catalogue
- the basket, transactional checkout, order and stock report services, and the user list

## Known limitations

- Passwords are stored and compared as plain text. A real system should hash them, for example with BCrypt.
- The database credentials are hard-coded in `DatabaseUtils`. They should come from a config file or environment variables.
- Customers type their username again to use the basket instead of it being taken from the logged-in session.
- There's a duplicate copy of the source in a root-level `main/` folder next to `src/main/`.
