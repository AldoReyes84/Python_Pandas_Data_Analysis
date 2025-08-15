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

    
  

<img width="857" height="827" alt="image" src="https://github.com/user-attachments/assets/ab14ad11-9d7a-4d41-ba3e-d68953160c32" />

Lets get Sales by Month, Year and Grand Total

<img width="1126" height="860" alt="image" src="https://github.com/user-attachments/assets/af2189c7-38ce-4781-acd5-9fed2a55ca2e" />

The Results are

<img width="874" height="860" alt="image" src="https://github.com/user-attachments/assets/d1ac4695-9c12-4973-b114-a3084ffe9689" />


