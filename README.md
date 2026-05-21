# Themepark-SQL

# 🎢 Theme Park Database — SQL Assignment

A relational database modelling the operations of a theme park chain, built using MySQL. Covers schema design, data insertion, querying, joins, and DML operations across six related tables.

---

## Schema Overview

The database (`PARK`) consists of six tables:

| Table | Description |
|---|---|
| `THEMEPARK` | Master list of parks worldwide |
| `EMPLOYEE` | Staff records linked to a park |
| `TICKET` | Ticket types and prices per park |
| `ATTRACTION` | Rides and attractions per park |
| `HOURS` | Employee hours worked per attraction |
| `SALES` / `SALES_LINE` | Ticket sales transactions |

### Entity Relationship Summary

```
THEMEPARK ──< EMPLOYEE
THEMEPARK ──< TICKET
THEMEPARK ──< ATTRACTION
THEMEPARK ──< SALES
EMPLOYEE  ──< HOURS >── ATTRACTION
SALES     ──< SALES_LINE >── TICKET
```

---

## Table Definitions

### THEMEPARK
```sql
PARK_CODE   VARCHAR(10)  PRIMARY KEY
PARK_NAME   VARCHAR(35)  NOT NULL
PARK_CITY   VARCHAR(50)  NOT NULL
PARK_COUNTRY CHAR(2)     NOT NULL
```

### EMPLOYEE
```sql
EMP_NUM       NUMERIC(4)   PRIMARY KEY
EMP_TITLE     VARCHAR(4)
EMP_LNAME     VARCHAR(15)  NOT NULL
EMP_FNAME     VARCHAR(15)  NOT NULL
EMP_DOB       DATE         NOT NULL
EMP_HIRE_DATE DATE
EMP_AREA_CODE VARCHAR(4)   NOT NULL
EMP_PHONE     VARCHAR(12)  NOT NULL
PARK_CODE     VARCHAR(10)  FK → THEMEPARK
```

### TICKET
```sql
TICKET_NO    NUMERIC(10)    PRIMARY KEY
TICKET_PRICE NUMERIC(4,2)   NOT NULL  DEFAULT 00.00
TICKET_TYPE  VARCHAR(10)
PARK_CODE    VARCHAR(10)    FK → THEMEPARK
```

### ATTRACTION
```sql
ATTRACT_NO       NUMERIC(10)  PRIMARY KEY
ATTRACT_NAME     VARCHAR(35)
ATTRACT_AGE      NUMERIC(3)   NOT NULL  DEFAULT 0
ATTRACT_CAPACITY NUMERIC(3)   NOT NULL
PARK_CODE        VARCHAR(10)  FK → THEMEPARK
```

### HOURS
```sql
EMP_NUM           NUMERIC(4)    FK → EMPLOYEE
ATTRACT_NO        NUMERIC(10)   FK → ATTRACTION
HOURS_PER_ATTRACT NUMERIC(2)    NOT NULL
HOUR_RATE         NUMERIC(4,2)  NOT NULL
DATE_WORKED       DATE          NOT NULL
PRIMARY KEY (EMP_NUM, ATTRACT_NO, DATE_WORKED)
```

### SALES / SALES_LINE
```sql
-- SALES
TRANSACTION_NO  NUMERIC      PRIMARY KEY
PARK_CODE       VARCHAR(10)  FK → THEMEPARK
SALE_DATE       DATE         NOT NULL

-- SALES_LINE
TRANSACTION_NO  NUMERIC      FK → SALES  (CASCADE DELETE)
LINE_NO         NUMERIC(2)   NOT NULL
TICKET_NO       NUMERIC(10)  FK → TICKET
LINE_QTY        NUMERIC(4)   NOT NULL  DEFAULT 0
LINE_PRICE      NUMERIC(9,2) NOT NULL  DEFAULT 0.00
PRIMARY KEY (TRANSACTION_NO, LINE_NO)
```

---

## Sample Data

**Parks** span 6 countries: France, UK, Nigeria, China, South Africa, and the USA.

**Employees** are distributed across parks in Paris, New York, Durban, and Beijing.

**Tickets** cover Child, Adult, and Senior types for two parks (FR1001, UK3452).

**Attractions** include Carnival, Skydiving, Pirates, UnderSeaWorld, QuadBike, FlyToStars, Hiking, and Cycling.

---

## Queries Demonstrated

### Filtering
```sql
-- Parks whose name ends in 'Land'
SELECT * FROM THEMEPARK WHERE PARK_NAME LIKE '%Land';

-- Tickets priced over £30
SELECT PARK_CODE, TICKET_PRICE, TICKET_TYPE FROM TICKET WHERE TICKET_PRICE > 30;

-- Tickets between £20 and £50
SELECT * FROM TICKET WHERE TICKET_PRICE BETWEEN 20 AND 50;

-- Senior and Child tickets only
SELECT * FROM TICKET WHERE TICKET_TYPE IN ('Senior', 'Child');
```

### Calculated Columns
```sql
-- Simulate a 10% price increase
SELECT PARK_CODE, TICKET_NO, TICKET_TYPE, TICKET_PRICE,
       TICKET_PRICE + ROUND((TICKET_PRICE * 0.1), 2) AS NEW_PRICE
FROM TICKET;
```

### Joins
```sql
-- Parks with their ticket offerings (inner join)
SELECT THEMEPARK.PARK_CODE, PARK_NAME, TICKET_NO, TICKET_TYPE, TICKET_PRICE
FROM THEMEPARK, TICKET
WHERE THEMEPARK.PARK_CODE = TICKET.PARK_CODE;

-- All parks, including those with no attractions (left outer join)
SELECT THEMEPARK.PARK_CODE, PARK_NAME, ATTRACT_NAME
FROM THEMEPARK LEFT JOIN ATTRACTION ON THEMEPARK.PARK_CODE = ATTRACTION.PARK_CODE;
```

### DML Operations
```sql
-- Delete a park
DELETE FROM THEMEPARK WHERE PARK_CODE = 'IN3512';

-- Update attraction capacity
UPDATE ATTRACTION SET ATTRACT_CAPACITY = 42 WHERE ATTRACT_NO = 20044;
```

---

## Using this script

```bash
# Run the full script in MySQL
mysql -u root -p < park_schema.sql
```

Or paste directly into MySQL Workbench and execute section by section.

**Requirements:** MySQL 5.7+ or MariaDB 10.3+
