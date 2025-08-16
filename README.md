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
Append results

        result = pd.concat([monthly, yearly], ignore_index=True)
        result = pd.concat([result, grand_total], ignore_index=True)

Format Results and Print

     # Format columns
    result['OrderQuantity'] = result['OrderQuantity'].apply(lambda x: f"{round(x):,}" if pd.notnull(x) else "")
    result['TotalProductCost'] = result['TotalProductCost'].apply(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    result['DiscountAmount'] = result['DiscountAmount'].apply(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    result['SalesAmount'] = result['SalesAmount'].apply(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    result['GrossMargin'] = result['GrossMargin'].apply(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    result['MarginPercentage'] = result['MarginPercentage'].apply(lambda x: f"{x:.2f}%" if pd.notnull(x) else "")

    print(result)

<img width="856" height="966" alt="image" src="https://github.com/user-attachments/assets/fd7b84b0-366a-4e3c-9367-835a20bf913a" />

#Pivot Tables

OrderQuantity
     pivot_order = pd.pivot_table(
        df,
        values='OrderQuantity',
        index='Year',
        columns='Month',
        aggfunc='sum',
        margins=True,
        margins_name='Total'
    ).applymap(lambda x: f"{round(x):,}" if pd.notnull(x) else "")
    print(pivot_order)

<img width="796" height="443" alt="image" src="https://github.com/user-attachments/assets/724b763e-51ef-4189-b7e6-21a08470d830" />

SalesAmount
     pivot_sales = pd.pivot_table(
        df,
        values='SalesAmount',
        index='Year',
        columns='Month',
        aggfunc='sum',
        margins=True,
        margins_name='Total'
    ).applymap(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    print(pivot_sales)

<img width="1122" height="479" alt="image" src="https://github.com/user-attachments/assets/7a27ebec-1c42-4103-8a88-cc582f06565c" />

ProductCost
     pivot_cost = pd.pivot_table(
        df,
        values='TotalProductCost',
        index='Year',
        columns='Month',
        aggfunc='sum',
        margins=True,
        margins_name='Total'
    ).applymap(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    print(pivot_cost)

<img width="1138" height="442" alt="image" src="https://github.com/user-attachments/assets/27233479-f906-43b6-8054-961d2058088e" />

