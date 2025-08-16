Go back to [Aldo Reyes Portfolio](https://aldoreyes84.github.io/AldoReyes.github.io/)

Go back to [Data Analysis Project](https://github.com/AldoReyes84/Data-Analisys_For-AdventureWorksDW2022_SQL_PowerBI_Python_Excel/tree/main)

# Python_Pandas_Data_Analysis
Data Analysis for the SQL Server AdventureWorks2022 database with Python and Pandas

Create a Virtual Enviorment 

    python -m venv (envioment_name)

Install pyodbc and pandas Liberies
  
    pip install pandas
    pip install pyodbc

In Terminal
Import Liberies

    import pyodbc
    import pandas as pd
  
Create a connection with SQL Server AdventureWorks2022 Database

    connection_string = (
    'Driver={SQL Server};'
    'Server=DESKTOP-6OVJLPM;'
    'Database=AdventureWorksDW2022;'
    'trusted_connection=yes;'
    )

    try:
        connection = pyodbc.connect(connection_string)
        print("Connection successful!")

    except pyodbc.Error as e:
        print("Error in connection:", e)
    finally:
        connection.close()
        print("Connection closed.")

Define the fields to work with

- Quantity  
- Product Cost  
- Total Discount  
- Sales Amount  
- Gross Margin *Calculated*  
- Margin Percentage *Calculated*

      query = "SELECT [SalesAmount], [DueDate], [OrderQuantity], [TotalProductCost], [DiscountAmount] FROM FactResellerSales"
        df = pd.read_sql(query, connection)
        df['DueDate'] = pd.to_datetime(df['DueDate'])  # Ensure DueDate is datetime
        df['Year'] = df['DueDate'].dt.year
        df['Month'] = df['DueDate'].dt.month
        df['GrossMargin'] = df['SalesAmount'] - df['TotalProductCost']

        Calculate GrossMargin and MarginPercentage for each row
        df['GrossMargin'] = df['SalesAmount'] - df['TotalProductCost']
        df['MarginPercentage'] = (df['GrossMargin'] / df['SalesAmount']) * 100
  

Create monthly and yearly group by aggregations 

     monthly = df.groupby(['Year', 'Month']).agg({
    'SalesAmount': 'sum',
    'OrderQuantity': 'sum',
    'TotalProductCost': 'sum',
    'DiscountAmount': 'sum',
    'GrossMargin': 'sum'
    }).reset_index()
    monthly['MarginPercentage'] = (monthly['GrossMargin'] / monthly['SalesAmount']) * 100

    yearly = df.groupby('Year').agg({
    'SalesAmount': 'sum',
    'OrderQuantity': 'sum',
    'TotalProductCost': 'sum',
    'DiscountAmount': 'sum',
    'GrossMargin': 'sum'
    }).reset_index()
    yearly['Month'] = 'Subtotal'
    yearly['MarginPercentage'] = (yearly['GrossMargin'] / yearly['SalesAmount']) * 100

Create totals for rows and columms 

     grand_total = pd.DataFrame({
    'Year': ['Grand Total'],
    'Month': [''],
    'OrderQuantity': [df['OrderQuantity'].sum()],
    'TotalProductCost': [df['TotalProductCost'].sum()],
    'DiscountAmount': [df['DiscountAmount'].sum()],
    'SalesAmount': [df['SalesAmount'].sum()],
    'GrossMargin': [df['GrossMargin'].sum()],
    'MarginPercentage': [(df['GrossMargin'].sum() / df['SalesAmount'].sum()) * 100]
    })

Lets get Sales by Month, Year and Grand Total

<img width="1126" height="860" alt="image" src="https://github.com/user-attachments/assets/af2189c7-38ce-4781-acd5-9fed2a55ca2e" />

The Results are

<img width="874" height="860" alt="image" src="https://github.com/user-attachments/assets/d1ac4695-9c12-4973-b114-a3084ffe9689" />


