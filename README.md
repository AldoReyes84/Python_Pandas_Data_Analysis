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
    'Server=(~Server_Name~);'
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

## Resellers Sales

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

<img width="1036" height="955" alt="image" src="https://github.com/user-attachments/assets/94b4f8cd-5b0e-4c8c-bd3f-616af7f0bf8b" />

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

     # Calculate true margin percentage by year and month
    grouped = df.groupby(['Year', 'Month']).agg({
    'GrossMargin': 'sum',
    'SalesAmount': 'sum'
        }).reset_index()
    grouped['MarginPercentage'] = grouped['GrossMargin'] / grouped['SalesAmount'] * 100

    # Pivot table: years as rows, months as columns, margin percentage as values
    pivot_true_margin_pct = grouped.pivot_table(
    values='MarginPercentage',
    index='Year',
    columns='Month',
    aggfunc='first',  # Only one value per group
    ).applymap(lambda x: f"{x:.2f}%" if pd.notnull(x) else "")

    # Calculate grand total margin percentage
    grand_gross_margin = grouped['GrossMargin'].sum()
    grand_sales_amount = grouped['SalesAmount'].sum()
    grand_margin_pct = (grand_gross_margin / grand_sales_amount) * 100

    # Append grand total row
    pivot_true_margin_pct.loc['Grand Total'] = ["" for _ in pivot_true_margin_pct.columns]
    pivot_true_margin_pct.loc['Grand Total', pivot_true_margin_pct.columns[0]] = f"{grand_margin_pct:.2f}%"

    #Gross Margin Pivot
    pivot_gross_margin = pd.pivot_table(
        df,
        values='GrossMargin',
        index='Year',
        columns='Month',
        aggfunc='sum',
        margins=True,
        margins_name='Total'
    ).applymap(lambda x: f"${round(x):,}" if pd.notnull(x) else "")

    print(pivot_true_margin_pct)

<img width="1128" height="767" alt="image" src="https://github.com/user-attachments/assets/7b545678-e99b-4a30-82fe-20b2bbbaeefb" />

    Discount Amount

     pivot_discount = pd.pivot_table(
        df,
        values='DiscountAmount',
        index='Year',
        columns='Month',
        aggfunc='sum',
        margins=True,
        margins_name='Total'
    ).applymap(lambda x: f"${round(x):,}" if pd.notnull(x) else "")
    print(pivot_discount)

<img width="943" height="400" alt="image" src="https://github.com/user-attachments/assets/ddfcd221-1df3-4e9e-a0c5-5481227763b0" />
    
The interpretation of these results are addresserd in the [Data Analysis Project/FactResellersSales_Table](https://github.com/AldoReyes84/Data-Analisys_For-AdventureWorksDW2022_SQL_PowerBI_Python_Excel/tree/main#factresellerssales-table) 

## Internet Sales

We can uses the same code to preform the Internet Sales analysis just by replacing on the Query string the Table Name

     query = "SELECT [SalesAmount], [DueDate], [OrderQuantity], [TotalProductCost], [DiscountAmount] FROM FactInternetSales"

The Results will be the same but this time for the FactInternetSales Table

<img width="824" height="749" alt="image" src="https://github.com/user-attachments/assets/96f5ae92-b13d-4a43-bad8-67ea61c8ad62" />

Order Quantity

<img width="727" height="121" alt="image" src="https://github.com/user-attachments/assets/33376920-2eac-44d3-a07b-964b32a981ba" />

Sales Amount

<img width="1112" height="128" alt="image" src="https://github.com/user-attachments/assets/2676fde7-7f6e-4daa-903c-43a491d0dba0" />

Product Cost

<img width="1105" height="122" alt="image" src="https://github.com/user-attachments/assets/3a569cb6-2059-49b8-991b-294157a7eb1e" />

Gross Margin

<img width="1087" height="122" alt="image" src="https://github.com/user-attachments/assets/9c8af11a-bd57-4ac6-a25b-a1ab1b6aef48" />

Margin Percentage

<img width="819" height="103" alt="image" src="https://github.com/user-attachments/assets/fe05e322-8edb-4ce7-adb7-a47d93ac44ee" />

Discount Amount

<img width="493" height="123" alt="image" src="https://github.com/user-attachments/assets/c68e7a90-ccde-4507-8278-8c41e6ec21af" />
