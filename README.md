# Greater Sydney Bustling Score Analysis

## Project Overview
This project measures how “bustling” different SA2 regions in Greater Sydney are by combining spatial, demographic, transport, business, and public facility data into a single score. The workflow integrates multiple datasets in PostgreSQL/PostGIS, computes standardized indicators, and produces maps and summary visualizations for regional comparison.

## Project Goal
The main goal of this project is to build a data-driven score that reflects how busy or active each SA2 region is. The project also examines whether this score is related to median income across regions.

## Data
The project combines the following datasets:

- **SA2 Boundaries**: geographic boundaries for Statistical Area Level 2 regions
- **Businesses.csv**: number of businesses by industry and SA2
- **Stops.txt**: public transport stops
- **PollingPlaces2019.csv**: 2019 federal election polling place locations
- **Catchments.zip**: future, primary, and secondary school catchment areas
- **Population.csv**: age-group population by SA2
- **Income.csv**: median income by SA2
- **TfNSW_PopUp_Cycleway.shp**: cycleway infrastructure
- **Off-Street-Parking.csv**: off-street parking locations
- **EmergencyManagementFacility.json**: emergency facility locations in NSW

## Tools and Technologies
- Python
- pandas
- GeoPandas
- PostgreSQL / PostGIS
- SQLAlchemy
- psycopg2
- matplotlib

## Project Workflow

### 1. Data Import and Cleaning
Each dataset is imported into Python and cleaned before loading into PostgreSQL/PostGIS.

Main cleaning steps include:
- standardizing column names
- removing irrelevant columns
- dropping missing or invalid records
- converting latitude and longitude into geometry columns
- converting spatial layers into PostGIS-compatible formats
- aligning SA2 codes across datasets

### 2. Database Construction
The cleaned datasets are loaded into PostgreSQL/PostGIS as structured tables.

This stage includes:
- creating tables with primary keys
- defining foreign key relationships where needed
- storing spatial data as `POINT` or `MULTIPOLYGON` geometry
- creating GIST indexes to improve spatial query performance

### 3. Feature Construction
The project builds several region-level indicators for each SA2 area:

- **Zbusiness**: retail businesses per 1,000 people
- **Zstops**: number of transport stops
- **Zpolls**: number of polling places
- **Zschools**: schools per 1,000 young people

These measures are standardized using Z-scores so they can be combined on a common scale.

### 4. Base Score Calculation
A base “bustling” score is computed using:

`Score = sigmoid(Zbusiness + Zstops + Zpolls + Zschools)`

This score summarizes everyday activity, accessibility, and local demand in each region.

### 5. Extended Score Calculation
The model is then expanded by adding more infrastructure-related indicators:

- **Zcycleway**
- **Zparking**
- **Zfacility**

The extended score is computed as:

`Extended Score = sigmoid(Zbusiness + Zstops + Zpolls + Zschools + Zcycleway + Zparking + Zfacility)`

This gives a broader measure of regional activity by incorporating more dimensions of urban services and infrastructure.

### 6. Visualisation and Analysis
The final outputs include:
- ranked SA2 scores
- summary statistics table
- box plot of score distribution
- map overlay of total scores across Greater Sydney
- scatter plot of total score vs median income

## Results

### Main Findings
- The project computes bustling scores for **360 SA2 regions**.
- The final score ranges from **0.038799** to **0.941803**.
- The median score is **0.132386**, which suggests that most regions have relatively low overall bustling scores.
- High-scoring regions include central or highly active areas such as parts of Sydney CBD, while some outer or less service-dense regions score lower.

### Correlation with Income
The project also compares the extended bustling score with median income by SA2.

Key result:
- The correlation coefficient is approximately **-0.01**

This indicates an extremely weak relationship between regional bustling level and median income. In other words, a region being more active or service-rich does not necessarily imply higher median income.

## Interpretation
The score captures several useful signals of regional activity, especially transport access, business presence, school access, and civic infrastructure. However, it is still a simplified measure.

Some limitations include:
- not all aspects of urban activity are captured
- larger regions may mechanically contain more services
- the score does not adjust for land area or service density
- income is influenced by many social and economic factors outside this model

## Repository Contents
- `Code sample_python.ipynb` — full data pipeline, SQL workflow, scoring, and visualisation
- `Report.docx` — written report describing the datasets, rationale, and findings

## Conclusion
This project builds a spatial data pipeline to measure how bustling different Greater Sydney regions are using multi-source data and spatial analysis. It shows how PostgreSQL/PostGIS and Python can be combined to integrate geographic and tabular data, create interpretable indicators, and generate useful regional insights.
