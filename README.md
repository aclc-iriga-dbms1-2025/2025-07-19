# DBMS1: 2025-07-19

## SQL: Structured Query Language
- the standard language for accessing and manipulating databases.

---

## MySQL through XAMPP
You can try SQL by installing MySQL, which is a free open-source database.
The easiest way to get MySQL is by using a software called XAMPP.

### Installing XAMPP on Windows
1. Download XAMPP for Windows from [apachefriends.org/download.html](https://www.apachefriends.org/download.html).
2. Install the downloaded file. When the installation is done, just close the Finish dialog - do not launch the XAMPP Control Panel yet.

### Setting Up MySQL as a Service
1. Open the installed **XAMPP Control Panel** as **Administrator**.
2. Click the Start button next to MySQL to run the MySQL server.
3. Check the box next to MySQL. This makes MySQL start automatically when XAMPP starts.

### Adding MySQL to the Windows Path Environment Variable
1. Find where MySQL is installed. For example, if XAMPP is in Drive C, the path is:
   `C:\xampp\mysql\bin`.
2. Click **Start _(on Windows)_**, search for **"Edit the system environment variables"**, and open it.
3. In the window that appears, click the **"Environment Variables"** button.
4. Under **User variables _(for your username)_**, select the **"Path"** variable and click **Edit**.
5. Click **New**, then paste your MySQL path (e.g., `C:\xampp\mysql\bin`). Click **OK**.
6. Do the same under **System variables**: find the **"Path"** variable, click **Edit**, add the same MySQL path, and click **OK**.

### Accessing MySQL through Windows Command Prompt
1. Open Command Prompt (press **Windows + R**, type `cmd`, then press **Enter**).
2. Type the following command and press Enter:
    ```
    mysql -u root
    ```

---

## Basic SQL Statements for Databases
### 1. CREATE DATABASE
Used to make a new SQL database.

**Syntax:**
```sql
CREATE DATABASE databasename;
```

**Example:**
```sql
CREATE DATABASE mobile_wallet;

CREATE DATABASE money_lending;
```

### 2. DROP DATABASE
Deletes a database permanently. _(Not discussed in class)_

_Be careful! This will delete the entire database and all its data._

**Syntax:**
```sql
DROP DATABASE databasename;
```

**Example:**
```sql
DROP DATABASE mobile_wallet;
```

### 3. SHOW DATABASES
Shows all databases on the server.

**Example:**
```sql
SHOW DATABASES;
```

### 4. USE
Selects which database to work with.

**Syntax:**
```sql
USE databasename;
```

**Example:**
```sql
USE money_lending;
```

### 5. SHOW TABLES
Lists all tables in the selected database.

**Example:**
```sql
use money_lending;

SHOW TABLES;
```

---

## Basic SQL Statements for Tables

### 1. CREATE TABLE
Used to create a new table in a database.

**Syntax:**
```sql
CREATE TABLE table_name (
   column1 datatype,
   column2 datatype,
   column3 datatype,
   ....
);
```

**Examples:**

#### Create the `money_lending`.`offers` table
```sql
CREATE TABLE `offers` (
   `id` int PRIMARY KEY AUTO_INCREMENT,
   `name` varchar(255),
   `interest` double,
   `interval` varchar(255),
   `frequency` int
);
```

#### Create the `money_lending`.`customers` table
```sql
CREATE TABLE `customers` (
   `id` int PRIMARY KEY AUTO_INCREMENT,
   `firstname` varchar(255),
   `lastname` varchar(255)
);
```

#### Create the `money_lending`.`loans` table
This requires a **foreign key** because the loans table
will include information from the `offers` and `customers` tables.

Observe the pattern used below:

```sql
CREATE TABLE `loans` (
   `id` int PRIMARY KEY AUTO_INCREMENT,
   `offer_id` int,
   `customer_id` int,
   `amount` double,
   `date_borrowed` date,
   `date_due` date,
   `installment` double,

    CONSTRAINT fk_loans_offers
        FOREIGN KEY (`offer_id`)
        REFERENCES `offers`(`id`)
        ON DELETE CASCADE
        ON UPDATE CASCADE,

    CONSTRAINT fk_loans_customers
        FOREIGN KEY (`customer_id`)
        REFERENCES `customers`(`id`)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```
- **ON DELETE CASCADE** means that if a record in the offers or customers table is deleted, any related records in the loans table will also be deleted automatically.

- **ON UPDATE CASCADE** means that if the `id` in `offers` or `customers` is updated, the related `offer_id` or `customer_id` in the loans table will also be updated automatically.

### 2. DESCRIBE
Used to show the structure of a table - including column names, data types, and other details.

**Syntax:**
```sql
DESCRIBE table_name;
```

**Examples:**
```sql
DESCRIBE `offers`;


DESCRIBE `customers`;


DESCRIBE `loans`;
```

### 3. INSERT INTO
Adds new data into a table.

**Syntax:**
```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

**Examples:**

#### INSERT OFFERS
```sql
INSERT INTO `offers` (`name`, `interest`, `interval`, `frequency`)
VALUES ("offer1", 16, "daily", 30);


INSERT INTO `offers` (`name`, `interest`, `interval`, `frequency`)
VALUES ("offer2", 20, "weekly", 4);


INSERT INTO `offers` (`name`, `interest`, `interval`, `frequency`)
VALUES ("offer3", 25, "monthly", 6);
```
_**NOTE:** Since the primary key `id` is set to `AUTO_INCREMENT`, there's no need to include it in the INSERT statement - it will be generated automatically._


#### INSERT CUSTOMERS
```sql
INSERT INTO `customers` (`firstname`, `lastname`)
VALUES ("John", "Doe");


INSERT INTO `customers` (`firstname`, `lastname`)
VALUES ("Mary", "Makiling");


INSERT INTO `customers` (`firstname`, `lastname`)
VALUES ("Juan", "Dela Cruz");
```

#### INSERT LOANS
```sql
INSERT INTO `loans` (`offer_id`, `customer_id`, `amount`, `date_borrowed`, `date_due`, `installment`)
VALUES (1, 2, 10000, "2025-07-19", "2025-08-18", 333.33)


INSERT INTO `loans` (`offer_id`, `customer_id`, `amount`, `date_borrowed`, `date_due`, `installment`)
VALUES (2, 3, 50000, "2025-07-19", "2025-08-16", 12500)
```

_**NOTE:** Make sure that the `offer_id` and `customer_id` values exist in their respective parent tables (`offers` and `customers`)._

_Otherwise, you'll get an error like:_
```
Cannot add or update a child row: a foreign key constraint fails.
```

### 4. SELECT
Used to get data from a database.

**Syntax:**
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
**NOTES:**
- _The `WHERE` clause is **optional**. It’s used to filter the results._
- _To select all columns, use * instead of listing column names._

**Examples:**
```sql
SELECT * FROM `offers`;


SELECT * FROM `customers`;


SELECT * FROM `loans`;
```

```sql
SELECT `name`, `interest` FROM `offers`;


SELECT `firstname` FROM `customers`;


SELECT `offer_id`, `customer_id`, `date_due` FROM `loans`;
```

```sql
SELECT * FROM `offers`
WHERE `id` = 1;


SELECT * FROM `customers`
WHERE `firstname` = "Juan";


SELECT * FROM `loans`
WHERE `offer_id` = 1;
```

### 5. UPDATE
Used to change existing data in a table.

**Syntax:**
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```
_**NOTE:** Always use a `WHERE` clause to **avoid updating all rows** in the table by accident._

**Examples:**
```sql
UPDATE `offers`
SET `interest` = 22
WHERE `id` = 3;


UPDATE `customers`
SET `firstname` = "Lisa", `lastname` = "Reyes"
WHERE `id` = 2;


UPDATE `loans`
SET `customer_id` = 2
WHERE `id` = 2;
```

### 6. DELETE
Used to remove data from a table.

**Syntax:**
```sql
DELETE FROM table_name
WHERE condition;
```
_**NOTE:** Always include a `WHERE` clause to **prevent deleting all records** in the table._

**Examples:**
```sql
DELETE FROM `offers`
WHERE `id` = 3;


DELETE FROM `customers`
WHERE `firstname` = "Juan";


DELETE FROM `loans`
WHERE `id` = 2;
```

### 7. DROP TABLE
Used to delete a table and all its data from the database. _(Not discussed in class)_

**Syntax:**
```sql
DROP TABLE table_name;
```

**Example:**
```sql
DROP TABLE loans;
```