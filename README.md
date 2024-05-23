# API_Data Mining and ETL_Process
This is the process of automatically extracting data from websites. 
![1_tRlxeTqGyGX2yDve3v9Miw](https://github.com/Ackson507/API_Data-MiningScrapping_ETL_Script/assets/84422970/3c01c8c7-c208-4228-8f0b-0909ed880655)

# Overview
API integration involves connecting to external systems or services via APIs to retrieve data. APIs (Application Programming Interfaces) provide a way for different software systems to communicate and share data in a structured format, often using HTTP requests and responses.APIs (Application Programming Interfaces) are owned and managed by the entities that develop and provide them. These entities can be companies, organizations, or individual developers who create APIs to allow other software applications to access their services, data, or functionalities. Some APIs require authentication using API keys, tokens, or OAuth but in this project will pick plublic and open source API.

### Data Mining/Scraping:
Web Scraping: This is the process of automatically extracting data from websites. This can include text, images, and other media types from various web pages. In this Project will scrap data from a one of the largets motor vehicle resale company SBT JAPAN. We will have three tasks objectives;
- Task 1: Web scrapping to fetch data of vehicles available on there website and specifications and patial cleaning upon extracting
- Task 2: We load the data fetched into some variables and convert all thes variables into a dataflame
- Task 3: We will then do full data cleaning and transformation
- Task 4: Export to csv file
- Task 5: Upon doing Task 4< at the same time we open a connection to my default database postgres and create a corresponding table awaiting for import of the csv file. Then lastly we excecute a python command to send the exported data and append to the already existing database table called sbt_vehicles.
- 
### API Integration:
APIs (Application Programming Interfaces): These are sets of rules and protocols that allow different software applications to communicate with each other. In this project, APIs are used to fetch data from various online sources. This could be data from social media platforms, financial data providers, weather services, or any other data sources that provide API access.
### ETL Process:
Extraction: This is the process of gathering raw data from various sources. In this context, it includes data scraped from websites or obtained via APIs.
Transformation: This involves cleaning, organizing, and structuring the raw data into a usable format. Transformation might include filtering out unnecessary information, correcting errors, and converting data types.
Loading: This is the process of storing the transformed data into a destination database or data warehouse where it can be accessed and analyzed by end-users or other applications.
