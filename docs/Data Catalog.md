# Gold Layer Data Catalog – Sales Data Mart (Star Schema)

**Overview**  
The Gold layer contains three business-ready views forming a classic Star Schema for sales analytics:  
- **dim_customers**: Enriched customer dimension  
- **dim_products**: Current/active product dimension  
- **fact_sales**: Transactional sales fact table  

All views are based on transformations and joins from the Silver layer.

## 1. gold.dim_customers – Customer Dimension

| Column Name       | Data Type         | Description / Business Meaning                                      |
|-------------------|-------------------|---------------------------------------------------------------------|
| customer_key      | INT               | Surrogate key (unique warehouse identifier)                         |
| customer_id       | INT / NVARCHAR    | Original customer ID from CRM                                       |
| customer_number   | NVARCHAR(50)      | Customer code/number from ERP/CRM                                   |
| first_name        | NVARCHAR(100)     | Customer's first name                                               |
| last_name         | NVARCHAR(100)     | Customer's last name                                                |
| country           | NVARCHAR(100)     | Customer's country (from ERP location)                              |
| marital_status    | NVARCHAR(50)      | Marital status (Married / Single / n/a)                             |
| gender            | NVARCHAR(50)      | Gender (Male / Female / n/a) – enriched from CRM + ERP fallback     |
| birthdate         | DATE              | Customer's date of birth                                            |
| create_date       | DATETIME / DATE   | Date when the customer record was created/loaded                    |

## 2. gold.dim_products – Product Dimension

| Column Name     | Data Type         | Description / Business Meaning                                      |
|-----------------|-------------------|---------------------------------------------------------------------|
| product_key     | INT               | Surrogate key (unique warehouse identifier)                         |
| product_id      | INT / NVARCHAR    | Original product ID from CRM                                        |
| product_number  | NVARCHAR(50)      | Business product code/number                                        |
| product_name    | NVARCHAR(200)     | Full product name                                                   |
| category_id     | NVARCHAR(50)      | Category code (derived from product key prefix)                     |
| category        | NVARCHAR(100)     | Product category name                                               |
| subcategory     | NVARCHAR(100)     | Product sub-category name                                           |
| maintenance     | NVARCHAR(50)      | Maintenance flag/type (from ERP category)                           |
| cost            | DECIMAL(18,2)     | Product cost                                                        |
| product_line    | NVARCHAR(50)      | Product line (Road / Mountain / Touring / Other Sales / n/a)       |
| start_date      | DATE              | Date when this product version became active                        |

## 3. gold.fact_sales – Sales Fact Table

| Column Name      | Data Type         | Description / Business Meaning                                      |
|------------------|-------------------|---------------------------------------------------------------------|
| order_number     | NVARCHAR(50)      | Unique sales order number                                           |
| product_key      | INT               | Foreign key → dim_products                                          |
| customer_key     | INT               | Foreign key → dim_customers                                         |
| order_date       | DATE              | Date when the order was placed                                      |
| shipping_date    | DATE              | Date when the order was shipped                                     |
| due_date         | DATE              | Payment due date                                                    |
| sales_amount     | DECIMAL(18,2)     | Total sales amount (calculated as quantity × price)                 |
| quantity         | INT               | Number of units sold                                                |
| price            | DECIMAL(18,2)     | Unit selling price                                                  |

**Notes**  
- Sales Amount = Quantity × Price (business rule)  
- All dates are in DATE format for clean reporting  
- Surrogate keys protect from source system changes  
- Data enriched from multiple silver sources (CRM + ERP)
