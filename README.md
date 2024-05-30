# Buiding a Data Pipeline 
This project will build a data pipeline that will allow you us to automate our data pipeline for both CSV files and API scraping using Apache Airflow, running the entire process once weekly. We have three objectives we need to do to carry out this project successiful.
- Objective 1: Web-scrapping: Web scraping is a technique used to automatically extract large amounts of data from websites. It involves fetching the web pages and extracting specific information from them, which can then be used for various purposes such as data analysis, market research, and content aggregation. Will use python alonside with webscrapping skills of using libraries such as Pandas or Pyspark, and Beautiful Soup.
- Objective 2: File System: Will have a certain source of file in csv that will be recieving new data at certain period of time. We need to create a trasformation script that will automate data cleaning and trasformation process so that when new data comes in, the funtion in the script will be run through the file and carry out command instruction of cleaning and transforming.
- Objective 3: Deploy Aworflow management tool such as Apche Airflow to automate our data pipeline, we will need to define Directed Acyclic Graphs (DAGs) in Python. Each DAG will correspond to one of our data sources: one for loading and transforming data from a CSV file which is objective 2 and another for scraping data from an API which is Objective 1. Each DAG will consist of four tasks: extracting data, transforming data, loading data, and a final task to notify or log the completion and sent the data collected to our destination database PostgresSQL .

![1_tRlxeTqGyGX2yDve3v9Miw](https://github.com/Ackson507/API_Data-MiningScrapping_ETL_Script/assets/84422970/3c01c8c7-c208-4228-8f0b-0909ed880655)

# Objective 1
APIs (Application Programming Interfaces) are owned and managed by the entities that develop and provide them. These entities can be companies, organizations, or individual developers who create APIs to allow other software applications to access their services, data, or functionalities. Some APIs require authentication using API keys, tokens, or Authorization but in this project will pick public and open source API.
### Data Mining/Scraping:
Web Scraping: This is the process of automatically extracting data from websites. This can include text, images, and other media types from various web pages. In this Project will scrap data from a one of the largets motor vehicle resale company SBT JAPAN. We will have multiple tasks objectives to complete this project as follows;
- Task 1: Web scrapping to fetch data of vehicles available on there website and specifications and patial cleaning upon extracting
- Task 2: We load the data fetched into some variables and convert all thes variables into a dataflame
- Task 3: We will then do full data cleaning and transformation
- Task 4: Export to csv file
- Task 5: Upon doing Task 4< at the same time we open a connection to my default database postgres and create a corresponding table awaiting for import of the csv file. Then lastly we excecute a python command to send the exported data and append to the already existing database table called sbt_vehicles.
### Task 1: API Integration and fetching content
APIs (Application Programming Interfaces): These are sets of rules and protocols that allow different software applications to communicate with each other. In this project, APIs are used to fetch data from various online sources. This could be data from social media platforms, financial data providers, weather services, or any other data sources that provide API access.
```python
from bs4 import BeautifulSoup
import requests
import pandas as pd

#Download the page and store it as a "page"

page = requests.get("https://www.sbtjapan.com/used-cars/?steering=all&drive=0&cc_f=0&cc_t=0&mile_f=0&mile_t=0&trans=0&fuel=0&color=0&loadClass=0&engineType=0&location=&port=0&search_box=1&locationIds=0&d_country=68&d_port=53&ship_type=0&FreightChk=yes&currency=2&inspection=no&insurance=1&fav_currency=2&fav_d_country=68&fav_d_port=53&fav_ship_type=0&fav_insurance=2&sort=46&psize=100&p_num=2#listbox")
soup = BeautifulSoup(page.content, 'html.parser')
```
### Task 2: Page/Site Content scrapping 
Firtsly, we begin extracting each individual datapoint on a particular vehicle
```python

#1 We want to craeate a variable to extract the make using find function
model = soup.find('div', class_='caritem_titlearea').text

#2 We want to craeate a variable to extract fuel type
fuel = soup.find('li', class_='specwithicon_fuel').text

#3 We want to craeate a variable to extract trassmission
#4 We want to craeate a variable to extract engine
#5 We want to craeate a variable to extract mileage
trasmission = soup.find('ul', class_='speclist_table').text

#6 We want to craeate a variable to extract number of doors
doors = soup.find('li', class_='specwithicon_doors').text

#7 We want to create a variable to extract drive if its right or left hand drive steering
drive = soup.find('li', class_='specwithicon_steering').text

#8 We want to create a variable to extract actual price of a car
price = soup.find('div', class_='fob_area').text

#9 We want to create a variable to extract total price of a car shipping inclusive
total_price = soup.find('div', class_='totalprices_area').text

```
Secondly, fetching individual datapoint on the entired page.
```python
# Using find_all and loop to extract everythin

#1 We want to craeate a variable to extract the make using find function
model = soup.find_all('div', class_='caritem_titlearea')
all_model = [a.text.replace('\n','').replace(' ','').replace('AddtoFavorites','').replace('Location','').replace('Inventory','').replace('WeeklyRedTagSale','') for a in model]

#2 We want to craeate a variable to extract fuel type
fuel = soup.find_all('li', class_='specwithicon_fuel')
all_fuel =[b.get_text() for b in fuel]

#3 We want to craeate a variable to extract trassmission
#4 We want to craeate a variable to extract engine
#5 We want to craeate a variable to extract mileage
trasmission = soup.find_all('ul', class_='speclist_table')
all_trasmission = [c.text.replace('\n','').replace(' ','') for c in trasmission]

#6 We want to craeate a variable to extract number of doors
doors = soup.find_all('li', class_='specwithicon_doors')
all_doors = [d.text.replace('Doors','').replace(' ','') for d in doors]
#all_total_price

#7 We want to create a variable to extract drive if its right or left hand drive steering
drive = soup.find_all('li', class_='specwithicon_steering')
all_drive = [e.get_text() for e in drive]
#all_drive

#8 We want to create a variable to extract actual price of a car
price = soup.find_all('div', class_='fob_area')
all_price = [f.text.replace('\n','').replace('Vehicle Price:','').replace('USD','').replace(' ','').replace(',','') for f in price]
#all_price

#9 We want to create a variable to extract total price of a car shipping inclusive
total_price = soup.find_all('div', class_='totalprices_area')
all_total_price = [g.text.replace('\n','').replace(' ','').replace('TotalPrice:USD','').replace(',','') for g in total_price]
#all_total_price

df=pd.DataFrame({
    'Model': all_model,
    'Specifications': all_trasmission,
    'Fuel_Type': all_fuel,
    'Doors': all_doors,
    'Drive': all_drive,
    'Actual_Price': all_price,
    'Total_Price': all_total_price
})

```
### Task 3: We will then do full data cleaning and transformation
```python
#1 Changing data types: 'age' to float, 'salary' to int, 'join_date' to datetime
df = df.astype({
    'Actual_Price': 'float',
    'Total_Price': 'float'
})

#2 New column
df['Shipping_cost'] = df['Total_Price']- df['Actual_Price']


#3 Splitting colunms
#3.1
# Split the 'Model' column on 'km'
split_columns = df['Model'].str.split(':', expand=True)
# Assign the split result to new columns
df[['Models', 'Location']] = split_columns

#3.2 
# Split the 'Model' column on 'km'
split_columns = df['Specifications'].str.split('Trans', expand=True)
# Assign the split result to new columns
df[['Specs', 'Transmission']] = split_columns

#3.3
# Split the 'Model' column on 'km'
split_columns = df['Specs'].str.split('km', expand=True)
# Assign the split result to new columns
df[['Milaege', 'Engine']] = split_columns

#4 Replacing
# Replace comand can work for deleting, removing space and adding characters in a column
df['Milaege'] = df['Milaege'].str.replace('Mileage', '').replace(',', '')
df['Engine'] = df['Engine'].str.replace('Engine', '').replace(',', '')

#6 Droping columns
df = df.drop(columns=['Model','Specifications','Specs'])
df.head()

#5 Rearanging columns
# New column order
new_order = ['Models', 'Engine', 'Transmission','Fuel_Type', 'Doors', 'Drive','Milaege','Actual_Price','Shipping_cost','Total_Price']
# Rearrange columns
df = df[new_order]

#7 Postres uses lower case as header names so we change in advance
df= df.rename(columns=lambda x: x.lower())
df.head()
```
Below is the fully extracted and cleaned dataflame;


### Task 4: Export to csv file
```python
df.to_csv('SBT_Vehicles.csv', index=False)
print("DataFrame saved as 'SBT_Vehicles'")
```
![Screenshot (622)](https://github.com/Ackson507/API_Data-Mining_and_ETL_Scripting/assets/84422970/d7ff6581-9fdc-4265-b386-61df68cc856f)


### Task 5: Opening a connection to Postgres database.
We create a corresponding table awaiting for import of the csv file. Then lastly we excecute a python command to send the exported data and append to the already existing database table called sbt_vehicles

Firstly, we create a corresponding table in Postgres
```python
# STEPS FOR CONNECTING and CREATING A DATABASE TABLE

import psycopg2
from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT

# Database connection parameters
db_conn = {
    'dbname': 'destination_db',
    'user': 'postgres',
    'password': '---',
    'host': 'localhost',  # e.g., 'localhost' or an IP address
    'port': '5432'   # default is '5432'
}

connection = psycopg2.connect(**db_conn)
# MUST CODE
connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
cursor = connection.cursor()


# SQL command to create a table
create_table_query = '''
    CREATE TABLE sbt_vehicles (
         models          VARCHAR(50)
        ,engine          VARCHAR(50) NOT NULL
        ,transmission    VARCHAR(50)
        ,fuel_type       VARCHAR(50)
        ,doors           VARCHAR(50) NOT NULL
        ,drive           VARCHAR(50)
        ,mileage         VARCHAR(50)
        ,actual_price    FLOAT
        ,shipping_cost   FLOAT
        ,total_price     FLOAT
     );         
    '''

# Execute the SQL command
cursor.execute(create_table_query)
connection.commit()
print("Table created successfully.")
cursor.close()
connection.close()
print("PostgreSQL connection closed.")

```
Then we export the file.
```pythpn
import pandas as pd
from sqlalchemy import create_engine

# Reading the file
df=pd.read_csv("C:/Users/Ackson/SBT_Vehicles.csv")
df.head()

# Creating a connection URL format for SQLAlchemy's create_engine function
# Format: postgresql://<username>:<password>@<host>:<port>/<database>
engine = create_engine('postgresql://postgres:12345@localhost:5432/destination_db')


# Lets send the dataflame to a database and the already existing  table and append data
df.to_sql('sbt_vehicles', engine, if_exists='append', index=False)

# Final code for the complete excecution and exporting
print("Data has been successfully loaded into the database.")
```
![Database_Table](https://github.com/Ackson507/API_Data-Mining_and_ETL_Scripting/assets/84422970/a8ba8868-87fe-4221-9e6c-dc8b885b2840)

### Objective 2


















END.
- Using these scheduling and automation tools such as Apache Airflow, Cloud-based Scheduling Services and more to programmatically author, schedule, and monitor workflows. It is particularly useful for complex ETL processes. ,it can ensure that your data scraping tasks run consistently and reliably. This setup helps in maintaining up-to-date data and allows for efficient data processing workflows, enabling seamless integration of the scraped data into your data pipelines, applications and storage.

Application Cases:
- Market Research: Aggregating data from multiple sources to analyze market trends.
- Financial Analysis: Gathering financial data for investment analysis.
- Content Aggregation: Collecting content from various websites for aggregation platforms.
- Business Intelligence: Feeding data into BI tools for generating insights and reports.



















