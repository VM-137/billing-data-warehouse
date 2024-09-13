# Cloud Service Provider Data Warehouse

## Project Overview

This project focuses on designing a **data warehouse** for a cloud service provider using their historical billing data. The dataset is provided in the form of a CSV file (`cloud-billing-dataset.csv`), containing billing information from the past decade. The goal is to organize this data into a **star schema** that can efficiently support queries related to billing trends, customer insights, and more.

## Dataset Description

The billing data contains the following fields:

- **customerid**: Unique ID of the customer
- **category**: Customer type (e.g., Individual, Company)
- **country**: Country of the customer
- **industry**: Industry or domain of the customer (e.g., Legal, Engineering)
- **month**: The billing month in `YYYY-MM` format (e.g., 2009-01)
- **billedamount**: The amount billed for the given month in USD

### Example Data

Here are five example rows from the CSV file:

| customerid | category  | country | industry   | month   | billedamount |
|------------|-----------|---------|------------|---------|--------------|
| 101        | Individual| USA     | Legal      | 2020-05 | 150          |
| 102        | Company   | Canada  | Engineering| 2020-06 | 500          |
| 103        | Individual| India   | IT         | 2020-07 | 100          |
| 104        | Company   | UK      | Finance    | 2020-08 | 750          |
| 105        | Individual| Australia| Education | 2020-09 | 200          |

## Star Schema Design

To support the complex queries related to billing, the data is organized into a **star schema** with one fact table and two dimension tables.

### Fact Table: FactBilling

The **FactBilling** table stores the billing information for each customer for each month. The table includes foreign keys referencing customer and month details from the respective dimension tables.

| Field Name   | Details                                          |
|--------------|--------------------------------------------------|
| rowid        | Primary key, unique identifier for each bill     |
| customerid   | Foreign key, references customer ID              |
| monthid      | Foreign key, references month ID                 |
| billedamount | Amount charged in USD                            |

### Dimension Table: DimCustomer

The **DimCustomer** table contains all relevant information about each customer.

| Field Name | Details                                  |
|------------|------------------------------------------|
| customerid | Primary key, unique ID for each customer |
| category   | Customer type (e.g., Individual, Company)|
| country    | Country of the customer                  |
| industry   | Industry of the customer (e.g., Legal)   |

### Dimension Table: DimMonth

The **DimMonth** table stores details about each billing month, including derived information such as the year, month name, and quarter.

| Field Name  | Details                                |
|-------------|----------------------------------------|
| monthid     | Primary key, unique ID for each month  |
| year        | Year of the billing                    |
| month       | Month number (1–12)                    |
| monthname   | Name of the month (e.g., January)      |
| quarter     | Quarter number (1–4)                   |
| quartername | Quarter name (e.g., Q1, Q2)            |

## Database Schema Creation

### Step 1: Start PostgreSQL Server

Make sure the **PostgreSQL** server is running. If you're running PostgreSQL locally or on a cloud platform, ensure the server is up and ready.

### Step 2: Create the Database

#### SQL Schema Breakdown

The star-schema.sql file contains SQL commands to create the three tables:

* FactBilling: The fact table storing billing information.
* DimCustomer: The dimension table storing customer details.
* DimMonth: The dimension table storing month-related details.

```sql
BEGIN;

CREATE TABLE public."FactBilling"
(
    rowid integer NOT NULL,
    customerid integer NOT NULL,
    monthid integer NOT NULL,
    billedamount integer NOT NULL,
    PRIMARY KEY (rowid)
);

CREATE TABLE public."DimMonth"
(
    monthid integer NOT NULL,
    year integer NOT NULL,
    month integer NOT NULL,
    monthname "char" NOT NULL,
    quarter integer NOT NULL,
    quartername "char" NOT NULL,
    PRIMARY KEY (monthid)
);

CREATE TABLE public."DimCustomer"
(
    customerid integer NOT NULL,
    category "char" NOT NULL,
    country "char" NOT NULL,
    industry "char" NOT NULL,
    PRIMARY KEY (customerid)
);

ALTER TABLE public."FactBilling"
    ADD FOREIGN KEY (customerid)
    REFERENCES public."DimCustomer" (customerid)
    NOT VALID;

ALTER TABLE public."FactBilling"
    ADD FOREIGN KEY (monthid)
    REFERENCES public."DimMonth" (monthid)
    NOT VALID;

END;
```
* FactBilling: Contains billing data and foreign keys to customer and month.
* DimMonth: Stores month, year, and quarter information.
* DimCustomer: Contains customer details such as country, category, and industry.


#### Querying the Data Warehouse

Once the star schema is created and the database populated, you can run queries such as:

* Average billing per customer
* Total billing by country
* Top 10 customers by billing amount
* Billing by industry, category, and country
* Billing trends over time (monthly, quarterly, yearly)

#### Example Query: Average Billing Per Customer
```sql
SELECT customerid, AVG(billedamount) as avg_billing
FROM public."FactBilling"
GROUP BY customerid;
```

#### Project Structure
```bash
.
├── data/
│   └── cloud-billing-dataset.csv  # Billing data
├── erd-diagram.png                # ERD diagram for the star schema
├── star-schema.sql                # SQL file for schema creation
├── README.md                      # Project documentation
└── requirements.txt               # Dependencies (if applicable)
```

### Conclusion

This project demonstrates the design of a data warehouse for cloud billing data using a star schema. The schema allows for efficient querying of billing information, providing insights into customer behavior, billing trends, and more.


This version of the `README.md` provides a clear and simple explanation of the project, including the purpose, star schema design, database setup steps, and SQL schema script.
