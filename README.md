# Zomato Data Engineering Project with PySpark

## Overview

This project demonstrates a data engineering workflow using **Apache Spark (PySpark)** and **Spark SQL** in **Databricks**. The Zomato restaurant dataset is loaded, cleaned, organized into logical DataFrames, and queried to explore restaurant ratings, locations, cuisines, costs, and customer votes.

The goal is to practice data preparation, basic data modeling, and analytical querying on restaurant data.

## Tech Stack

- **Language:** Python
- **Data Processing:** Apache Spark, PySpark
- **Querying:** Spark SQL
- **Environment:** Databricks
- **Data Format:** CSV

## Project Workflow

1. **Data Loading**
   - Read the Zomato CSV file into a Spark DataFrame.
   - Use the CSV header and permissive parsing options.
   - Inspect the schema and preview the records.

2. **Data Cleaning**
   - Remove duplicate rows with `dropDuplicates()`.
   - Fill missing values with `"Unknown"`.
   - Standardize the restaurant name column to `Name`.

3. **Data Modeling**
   - Create separate DataFrames for:
     - `restaurants` — restaurant name, location, cuisine, rating, approximate cost, and restaurant type.
     - `locations` — distinct location names.
     - `cuisines` — distinct cuisine values.
     - `ratings` — rating values and vote counts.
   - Register the DataFrames as temporary SQL views.

4. **Spark SQL Analysis**
   - Retrieve restaurant names and locations.
   - Find the top-rated restaurants based on the available rating field.
   - Count restaurants by location.
   - Calculate average ratings by cuisine.
   - Filter locations with more than 10 restaurants.
   - Analyze restaurant counts by location and cuisine.
   - Aggregate votes by location.
   - Filter restaurants with ratings above 4 and more than 500 votes.
   - Calculate average approximate cost by cuisine.

## Example SQL Analysis

### Restaurant count by location

```sql
SELECT location, COUNT(*) AS restaurant_count
FROM cie_df
GROUP BY location;
```

### Average rating by cuisine

```sql
SELECT cuisines,
       AVG(CAST(SPLIT(rate, '/')[0] AS DOUBLE)) AS average_rating
FROM cie_df
WHERE rate LIKE '%/%'
GROUP BY cuisines
ORDER BY average_rating DESC;
```

### Average cost by cuisine

```sql
SELECT cuisines,
       AVG(CAST(REPLACE(`approx_cost(for two people)`, ',', '') AS DOUBLE))
         AS average_cost
FROM cie_df
WHERE `approx_cost(for two people)` <> 'Unknown'
GROUP BY cuisines
ORDER BY average_cost DESC;
```

## Repository Structure

```text
zomato-data-engineering/
├── README.md
├── Fianlcie1 (1).ipynb
└── zomato.csv                 # Add the dataset here if you are permitted to share it
```

You can rename the notebook to `zomato_data_engineering.ipynb` for a cleaner repository.

## How to Run

1. Open the notebook in Databricks.
2. Ensure a Spark session is available.
3. Upload the Zomato CSV dataset to your Databricks workspace or a Unity Catalog volume.
4. Update the CSV path in the notebook to match your file location.
5. Run the notebook cells in order.

The current notebook uses this Databricks path:

```text
/Volumes/workspace/default/milestone/zomato.csv
```

Change this path if your dataset is stored elsewhere.

## Skills Demonstrated

- PySpark DataFrame operations
- CSV ingestion and schema inspection
- Handling duplicates and missing values
- Creating logical DataFrames for data modeling
- Temporary views and Spark SQL
- Aggregations, filtering, sorting, and grouping
- Working with text-formatted ratings and approximate cost values

## Notes and Limitations

- Missing values are replaced with `"Unknown"`; this is a simple cleaning strategy and may not be appropriate for every analytical use case.
- The notebook reads CSV fields as strings initially. Numeric calculations therefore use casting or string transformations where needed.
- The rating and cost analyses depend on the formatting and quality of the source data. Values that cannot be parsed as numbers may need additional validation.
- `monotonically_increasing_id()` generates Spark IDs that are not guaranteed to be consecutive or stable across separate executions. The notebook's modeling is a learning exercise; explicit dimension keys and foreign-key relationships would be needed for a more robust relational model.
- The notebook creates separate DataFrames but does not implement joins between them.
- Dataset size, performance benchmarks, and production deployment were not established in this notebook.

## Future Improvements

- Add stronger validation for rating, vote, and cost fields.
- Standardize column names and data types consistently.
- Build reliable dimension tables with explicit keys and relationships.
- Add automated data-quality checks and reusable transformation functions.
- Save cleaned and modeled data in Delta tables.
- Orchestrate the workflow and add logging and error handling.

## Author

**Basava Reddy**  
CSE — Artificial Intelligence and Machine Learning

---

*This project was developed for hands-on practice with PySpark, Databricks, data cleaning, data modeling, and Spark SQL.*
