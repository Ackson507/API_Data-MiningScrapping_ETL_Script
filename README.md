# Buiding a Data Pipeline 
This project will build a data pipeline that will allow you us to automate our data pipeline for both CSV files and API scraping using Apache Airflow, running the entire process once weekly. We have three objectives we need to do to carry out this project successiful.
- Objective 1: Web-scrapping: Web scraping is a technique used to automatically extract large amounts of data from websites. It involves fetching the web pages and extracting specific information from them, which can then be used for various purposes such as data analysis, market research, and content aggregation. Will use python alonside with webscrapping skills of using libraries such as Pandas or Pyspark, and Beautiful Soup.
- Objective 2: File System: Will have a certain source of file in csv that will be recieving new data at certain period of time. We need to create a trasformation script that will automate data cleaning and trasformation process so that when new data comes in, the funtion in the script will be run through the file and carry out command instruction of cleaning and transforming.
- Objective 3: Deploy Aworflow management tool such as Apche Airflow to automate our data pipeline, we will need to define Directed Acyclic Graphs (DAGs) in Python. Each DAG will correspond to one of our data sources: one for loading and transforming data from a CSV file which is objective 2 and another for scraping data from an API which is Objective 1. Each DAG will consist of four tasks: extracting data, transforming data, loading data, and a final task to notify or log the completion and sent the data collected to our destination database PostgresSQL .

# Objective 1
APIs (Application Programming Interfaces) are owned and managed by the entities that develop and provide them. These entities can be companies, organizations, or individual developers who create APIs to allow other software applications to access their services, data, or functionalities. Some APIs require authentication using API keys, tokens, or Authorization but in this project will pick public and open source API.
![1_tRlxeTqGyGX2yDve3v9Miw](https://github.com/Ackson507/API_Data-MiningScrapping_ETL_Script/assets/84422970/3c01c8c7-c208-4228-8f0b-0909ed880655)

### Data Mining/Scraping:
Web Scraping: This is the process of automatically extracting data from websites. This can include text, images, and other media types from various web pages. In this Project will scrap data from a one of the largets motor vehicle resale company SBT JAPAN. We will have multiple tasks objectives to complete this project as follows;
- Task 1: Web scrapping to fetch data of vehicles available on there website and specifications and patial cleaning upon extracting
- Task 2: We load the data fetched into some variables and convert all thes variables into a dataflame
- Task 3: We will then do full data cleaning and transformation and Export to csv file.
- 
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

### Objective 2
In the ETL_Automation-with-Python repository. We were doing two tasks
- Task 1: Load the dataset into python and write a line of code for cleaning and trasformation.
- Task 2: Wrting a script to automate the Task 1 and defining new funtion with Task 1 steps so that whenever we give this funtion new dataset it automatical cleans it and creates a new dataset appended with last name '_cleand.csv'
- 
### Task 1
Lets open our IDE or Code Editor, for me its Pycharm and paste the script we used in ETL_Automation-with-Python repository
```python
import pandas as pd
import numpy as np


def clean_data(file):
    #1 Read data from CSV file
    df = pd.read_csv("C:/Users/Ackson/Desktop/ETL_files/Sales_July_2019.csv")
    # Set pandas options to show all columns in a single row
    pd.set_option('display.max_columns', None)
    pd.set_option('display.expand_frame_repr', False)

    # 2 Handling Missing Values - By removing observation with missing values
    df.dropna(inplace=True)  # Dropping missing values
    df.drop_duplicates(inplace=True)  # Removing duplicates

    # 3 Changing data types
    df['Order Date'] = pd.to_datetime(df['Order Date'], errors='coerce')
    df['Quantity Ordered'] = pd.to_numeric(df['Quantity Ordered'], errors='coerce')
    df['Order ID'] = pd.to_numeric(df['Order ID'], errors='coerce')
    df['Price Each'] = pd.to_numeric(df['Price Each'], errors='coerce')

    # 4 Extract two coulums and creating new colums
    df['Date'] = pd.to_datetime(df['Order Date'].dt.date)
    df['Time'] = pd.to_datetime(df['Order Date'].dt.date)
    df['Total Sales'] = df['Quantity Ordered'] * df['Price Each']

    # 5 Droping order date and new null
    df.drop(columns=['Order Date'], inplace=True)
    df.dropna(inplace=True)

    # Export cleaned data to CSV file 
    cleaned_file = file.replace('.csv', '_cleaned.csv') #The new dataset will be exported appended with a last name "_cleaned.csv"
    df.to_csv(cleaned_file, index=False) # This line now exports the trasformed dataset to a new dataset csv
    print(f"Data cleaned and saved to {cleaned_file}")

#Example Use
Clean_data("Path of a file needed to be cleaned")
The cleaned dataset name will be exported as "Sales_July_2019_cleaned.csv"

```
![csv file](https://github.com/Ackson507/ETL_Data_Pipeline_with_Python/assets/84422970/4d1b849f-b384-457b-8233-87c91d6645f0)

# Objective 3: Coding Directed Acyclic Graphs (DAGs) and Task dependecies
To automate our data pipeline using Apache Airflow, we will need to define Directed Acyclic Graphs (DAGs) in Python. Each DAG will correspond to one of our data sources: One DAG for loading and transforming data from a CSV file, Follwed by another DAG for scraping data from an API and transforming it and lastly DAG for aggregation and exporting the files to a Postgress Database. Each DAG will consist of two or three tasks with well defined dependencies.
Task 1: Coding a DAG for doing first objective 1
Task 2: Coding a DAG for doing second objective 2
Task 3: Coding a DAG for exporting both files to Postgres Database

![1_M-yvjMW48kSoVpKOx6abmw](https://github.com/Ackson507/Data-pipeline-with-Airflow-Orchestration/assets/84422970/d3b25077-0aa2-4c5d-9efb-2ebb4066c4bf)


Setting up Airflow enviroment on local windows machine.
- Ensure you have Airflow installed and running.
- Create a user ID
- We create a common directory so that the DAG scripts are in accessible locations by Airflow.
- Set up Airflow variables and connections.
- Startup th airflow webserver and scheduler to access the DAG and for monitoring workflow

### 
We open our code editor and begin writing DAGS. After doing some study and research, I have compiled a DAG format with almost all default arguments that is needed and utilized comments to assist myself and a reader to understand the template and lines of code. I will post the full DAG format on another repository of DAGs that i am working on.
```python
# Import necessary modules from Airflow
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.operators.python_operator import PythonOperator
from airflow.operators.dummy_operator import DummyOperator
from airflow.utils.dates import days_ago

# Define the default arguments for the DAG
default_args = {
    'owner': 'airflow',  # Owner of the DAG
    'depends_on_past': False,  # DAG run does not depend on past runs
    'email_on_failure': False,  # Disable email on failure
    'email_on_retry': False,  # Disable email on retry
    'retries': 1,  # Number of retries on failure
}

# Instantiate the DAG
dag = DAG(
    'example_dag',  # Name of the DAG
    default_args=default_args,  # Default arguments
    description='A simple example DAG',  # Description of the DAG
    schedule_interval='@daily',  # Schedule interval (e.g., daily)
    start_date=days_ago(1),  # Start date for the DAG
    catchup=False,  # Whether to perform a catchup run
)

# Define the first task using BashOperator
task1 = BashOperator(
    task_id='print_date',  # Task ID
    bash_command='date',  # Bash command to be executed
    dag=dag,  # Reference to the DAG
)

# Define a simple Python function to be used in a PythonOperator
def my_python_function(**kwargs):
    print("Hello from Python!")

# Define the second task using PythonOperator
task2 = PythonOperator(
    task_id='run_python_function',  # Task ID
    python_callable=my_python_function,  # Python function to be executed
    provide_context=True,  # Provide Airflow context to the Python function
    dag=dag,  # Reference to the DAG
)

# Define the third task using DummyOperator
task3 = DummyOperator(
    task_id='dummy_task',  # Task ID
    dag=dag,  # Reference to the DAG
)

# Set up the task dependencies
task1 >> task2 >> task3
# This specifies that task1 must run before task2 and task2 must run before task3

```

### Setup for Activating Airflow and process
- Ensure your Postgres connection is set up in Airflow:
- Add a connection in Airflow's UI under Admin -> Connections with an ID of your_postgres_conn_id.
Place the Python scripts in your Airflow DAGs directory.
- Verify paths to your data files in the scripts.
- Start Airflow and activate the DAGs in the Airflow UI to monitor and manage the execution.
By following this structure, you ensure that the data extraction, transformation, and loading processes are automated and scheduled to run weekly.


END: We have made this project simplified by not adding all codes but lookout for a full one this is just a detailed format of creating a pipeline.
- Using these scheduling and automation tools such as Apache Airflow to programmatically author, schedule, and monitor workflows. It is particularly useful for complex ETL processes,it can ensure that your data scraping tasks run consistently and reliably. This setup helps in maintaining up-to-date data and allows for efficient data processing workflows, enabling seamless integration of the scraped data into your data pipelines, applications and storage.

Application Cases:
- Market Research: Aggregating data from multiple sources to analyze market trends.
- Financial Analysis: Gathering financial data for investment analysis.
- Content Aggregation: Collecting content from various websites for aggregation platforms.
- Business Intelligence: Feeding data into BI tools for generating insights and reports.



















