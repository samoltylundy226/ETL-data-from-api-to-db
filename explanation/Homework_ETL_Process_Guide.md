# Homework Process Guide: ETL from API to PostgreSQL

## 1. Goal of This Homework
Build a simple **ETL (Extract, Transform, Load)** pipeline in Python that fetches product data from an API, cleans the nested `rating` column, and loads the data into a PostgreSQL database table (`tbl_products`).

```text
[FakeStore API]  --->  1. EXTRACT (requests)  --->  2. TRANSFORM (pandas)  --->  3. LOAD (psycopg2 / PostgreSQL)
```

---

## 2. Project Folders
* **`dataset/`**: Stores a backup of the raw data fetched from the API (`raw_products.csv`).
* **`notebooks/`**: Contains the Python ETL code (`elt_data_from_api_to_db.ipynb`).
* **`results/`**: Stores the final transformed CSV (`transformed_products.csv`) or database result screenshots.
* **`scripts/`**: Contains SQL commands (`sql_script.sql`) to check or clear the table in PostgreSQL.

---

## 3. Step-by-Step Process

### Step 1: Import Libraries
Install and import the 3 main libraries:
* **`requests`**: Fetches data from the web API.
* **`pandas`**: Stores and transforms data as a table (`DataFrame`).
* **`psycopg2`**: Connects Python to your PostgreSQL database.

### Step 2: Extract (Fetch Data from API)
1. Send a `GET` request to `https://fakestoreapi.com/products/`.
2. Check if `response.status_code == 200` (200 means success).
3. Convert the JSON response into a Pandas DataFrame (`df = pd.DataFrame(data)`).
4. *(Optional)* Save a raw backup: `df.to_csv('../dataset/raw_products.csv', index=False)`.

### Step 3: Transform (Unpack Nested JSON)
* **Problem:** The `rating` column contains a dictionary (`{'rate': 3.9, 'count': 120}`), which cannot be stored directly as numeric columns in SQL.
* **Solution:** Split `rating` into two flat columns:
  * `rating_rate` (e.g., `3.9`)
  * `rating_count` (e.g., `120`)
* *(Optional)* Save cleaned data: `df.drop(columns=['rating']).to_csv('../results/transformed_products.csv', index=False)`.

### Step 4: Connect to PostgreSQL
Set up `db_config` with your database `host`, `database`, `user`, and `password`.

### Step 5: Load (Create Table & Insert Data)
1. Connect to PostgreSQL using `psycopg2.connect(**db_config)`.
2. Run `CREATE TABLE IF NOT EXISTS tbl_products (...)` to create the table.
3. Loop through the DataFrame (`df.iterrows()`) and `INSERT` each product into `tbl_products`.
4. Call `conn.commit()` to save the changes and `conn.close()` to close the connection.

### Step 6: Verify in DBeaver (`scripts/sql_script.sql`)
Run this SQL query in PostgreSQL to confirm your 20 products are loaded:
```sql
SELECT * FROM public.tbl_products;
```
