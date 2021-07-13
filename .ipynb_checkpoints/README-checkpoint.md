# US Immigration Analysis

#### Project Summary
The goal of this project is to create an ETL pipeline using I94 immigration, city, temperature and demographics data to form a database that is optimized for queries on immigration events. This database can be used to answer questions relating to immigration. We use Spark for ETL jobs and store the results in parquet format for analysis.

The project follows the follow steps:
* Step 1: Scope the Project and Gather Data
* Step 2: Explore and Assess the Data
* Step 3: Define the Data Model
* Step 4: Run ETL to Model the Data
* Step 5: Project Write Up

#### Project Scope
In this project, we will aggregate I94 immigration data by destination city to form our first dimension table. Next we will aggregate city temperature data by city to form the second dimension table. The two datasets will be joined on destination city to form the fact table. The final database is optimized to query on immigration events to determine if temperature affects the selection of destination cities. Spark will be used to process the data.

#### Data sources
1. **I94 Immigration Data**: comes from the U.S. National Tourism and Trade Office and contains various statistics on international visitor arrival in USA and comes from the US National Tourism and Trade Office. The dataset contains data from 2016. [link](https://travel.trade.gov/research/reports/i94/historical/2016.html)
2. **World Temperature Data**: comes from Kaggle and contains average weather temperatures by city. [link](https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data)
3. **U.S. City Demographic Data**: comes from OpenSoft and contains information about the demographics of all US cities such as average age, male and female population. [link](https://public.opendatasoft.com/explore/dataset/us-cities-demographics/export/)

#### Conceptual Data Model
##### Staging Tables
```
stage_immigration_df
    id
    date
    city_code
    state_code
    age
    gender
    visa_type
    count
    
stage_temp_df
    year
    month
    city_code
    city_name
    avg_temperature
    lat
    long
    
stage_demo_df
    city_code
    state_code
    city_name
    median_age
    pct_male_pop
    pct_female_pop
    pct_veterans
    pct_foreign_born
    pct_native_american
    pct_asian
    pct_black
    pct_hispanic_or_latino
    pct_white
    total_pop
```

##### Dimension Tables
```
immigrant_df
    id
    gender
    age
    visa_type
    
city_df
    city_code
    state_code
    city_name
    median_age
    pct_male_pop
    pct_female_pop
    pct_veterans
    pct_foreign_born
    pct_native_american
    pct_asian
    pct_black
    pct_hispanic_or_latino
    pct_white
    total_pop
    lat
    long
    
monthly_city_temp_df
    city_code
    year
    month
    avg_temperature
    
time_df
    date
    dayofweek
    weekofyear
    month
```

##### Fact Table
```
immigration_df
    id
    state_code
    city_code
    date
    count
```

#### Data Dictionary
Dimension Tables
- immigrant_df
    - id: id of immigrant
    - gender: gender of immigrant
    - age: age of immigrant
    - visa_type: immigrant's visa type
    

- city_df
    - city_code: city port code
    - state_code: state code of the city
    - city_name: name of the city
    - median_age: median age of the city
    - pct_male_pop: city's male population in percentage
    - pct_female_pop: city's female population in percentage
    - pct_veterans: city's veteran population in percentage
    - pct_foreign_born: city's foreign born population in percentage
    - pct_native_american: city's native american population in percentage
    - pct_asian: city's asian population in percentage
    - pct_black: city's black population in percentage
    - pct_hispanic_or_latino: city's hispanic or latino population in percentage
    - pct_white: city's white population in percentage
    - total_pop: city's total population
    - lat: latitude of the city
    - long: longitude of the city
    

- monthly_city_temp_df
    - city_code: city port code
    - year: year
    - month: month 
    - avg_temperature: average temperature in city for given month
    

- time_df
    - date: date
    - dayofweek: day of the week
    - weekofyear: week of year
    - month: month
    
Fact Table
- immigration_df
    - id: id
    - state_code: state code of arrival city
    - city_code: city port code of arrival city
    - date: date of arrival
    - count: count of immigrant's entries into the US

#### ETL Steps

1. Clean the data (remove nulls, alter data types, drop duplicates).
2. Load staging tables (staging_immigration_df, staging_temp_df and staging_demo_df).
3. Create dimension tables (immigrant_df, city_df, monthly_city_temp_df and time_df).
4. Create fact table (immigration_df).
5. Write fact and dimension tables to parquet.

#### Project Write Up

- Spark was chosen for this project due to its ability to deal with big data, scale, deal with different data formats and integrate nicely with cloud storage.

- Our database should be updated as frequently as new data is made available. Based on the datasets we work with in this project I would propose a monthly refresh cycle.

**Scenario:** The data was increased by 100x.

**Response:** We can upsize our EC2 instances and add additional Spark nodes.

**Scenario:** The data populates a dashboard that must be updated on a daily basis by 7am every day.

**Response:** Airflow is a fantastic tool for data pipeline automation and scheduling.

**Scenario:** The database needed to be accessed by 100+ people.

**Response:** Hosting our data solution in the cloud would allow for 100+ users to access it.