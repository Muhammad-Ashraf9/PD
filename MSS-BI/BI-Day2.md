# BI - SSAS (SQL Server Analysis Services)

> [!TIP] Relational Model
>
> - Data is stored in tables (rows and columns)
> - every table represents a single entity (e.g. Customers, products, categories)
> - Relationships between tables are defined by primary and foreign keys
> - Data is normalized to reduce redundancy

> [!warning] Dimensional Model
>
> - to transform relational model into dimensional model, we need to understand the business process
> - we need to extract Facts and Dimensions
> - order_details is the most used table to insert
> - facts are the measures that we want to analyze (e.g. sales, quantity)
> - we create a fact table (Fact_Sales)
> - facts qty(agg), total_sales(agg), Discount(agg)
> - dimensions are the attributes that we want to analyze => Sales_man (pk_man, name, address, phone) then link to Fact_Sales with foreign key Fk_man
> - in the query
> - dimension means direction like category, subcategory, product used to describe the product we can denormalize theses tables to create a single table (this will increase redundancy)
> - (Dim_Products) with all the attributes of the product
> - same for customer and Gender then we create a dimension table (Dim_Customer) with all the attributes of the customer
>   > [!error] any Dimensional model should have a dimension for the date (Dim_Date) with all the attributes of the date (year, month, day,...)
>
> > [!warning] to extract information from normalized tables we need to use joins
> >
> > - to get total sales by category: we would need to join the tables (products, categories, order_details)
> > - to get total sales by gender: we would need to join the tables (customers, Gender, order_details)
>
> > [!done] Dimensional Model
> >
> > - is optimized for SELECT queries and easier to extract information with less joins
>
> - ==Star Schema==

![](Pasted%20image%2020240426094136.png)

> [!tip] Data Warehouse
>
> - is a Denormalized database subject oriented, integrated, time-variant, non-volatile collection of data that supports decision making process of an organization (OLAP)

> [!note] Data Warehouse vs OLTP

| Data Warehouse           | OLTP                            |
| ------------------------ | ------------------------------- |
| Subject Oriented         | Application Oriented            |
| Integrated               | Isolated                        |
| Time Variant             | Current Data                    |
| Non-Volatile             | Volatile                        |
| Supports Decision Making | Supports Transaction Processing |

> [!faq] why OLAP?
>
> - Slow-to-execute queries
> - general

> [!example] OLAP Cube
>
> - create a star schema with the fact table and the dimension tables in the data warehouse
> - instead of executing the same query multiple times, we can create a cube to store the results of the query and then we can query the cube to get the results faster

> [!danger] Data Mart
>
> - is a subset of the data warehouse that is designed for a specific business line or team

> [!important] Dependent Data Mart vs Independent Data Mart
>
> - Dependent Data Mart: get data from the data warehouse (Enterprise Data Warehouse)
> - Independent Data Mart: get data from ETL process and is not dependent on the data warehouse (Enterprise Data Warehouse)

> [!tip] Snowflake Schema
>
> - is a normalized form of the star schema
> - the dimension tables are normalized into multiple related tables (smaller dimension tables)

> [!error] Galaxy Schema
>
> - is a combination of star and snowflake schema
> - some dimension tables are normalized and some are denormalized

> [!tip] OLAP Engine
>
> - will transform the snowflake schema into a star schema (runtime)


> [!note] OLAP (Online Analytical Processing)

> [!warning] MOLAP (Multidimensional OLAP)
>
> - Cube is Static:
> - High Performance

> [!done] ROLAP (Relational OLAP)
>
> - Cube is Dynamic:
> - Low Performance

> [!done] HOLAP (Hybrid OLAP)
>
> - Data is stored in a relational database

| Basis                                    | ROLAP                                                                    | MOLAP                                                                          | HOLAP                                                                          |
| ---------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ |
| Storage location for summary aggregation | Relational Database is used as storage location for summary aggregation. | Multidimensional Database is used as storage location for summary aggregation. | Multidimensional Database is used as storage location for summary aggregation. |
| Processing time                          | Processing time of ROLAP is very slow.                                   | Processing time of MOLAP is fast.                                              | Processing time of HOLAP is fast.                                              |
| Storage space requirement                | Large storage space requirement in ROLAP as compare to MOLAP and HOLAP.  | Medium storage space requirement in MOLAP as compare to ROLAP and HOLAP.       | Small storage space requirement in HOLAP as compare to MOLAP and ROLAP.        |
| Storage location for detail data         | Relational database is used as storage location for detail data.         | Multidimensional database is used as storage location for detail data.         | Relational database is used as storage location for detail data.               |
| Latency                                  | Low latency in ROLAP as compare to MOLAP and HOLAP.                      | High latency in MOLAP as compare to ROLAP and HOLAP.                           | Medium latency in HOLAP as compare to MOLAP and ROLAP.                         |
| Query response time                      | Slow query response time in ROLAP as compare to MOLAP and HOLAP.         | Fast query response time in MOLAP as compare to ROLAP and HOLAP.               | Medium query response time in HOLAP as compare to MOLAP and ROLAP.             |

---

# Break

---
