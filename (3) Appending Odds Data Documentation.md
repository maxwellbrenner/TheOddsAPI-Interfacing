# Preprocessing `event_masterlist_with_odds` for UFC Odds Data

This script processes a UFC event dataset (`preprocessed_event_masterlist.csv`) along with an odds dataset (`mma_odds.csv`) to integrate betting odds data into the event records. It standardizes fighter names, calculates average and best odds per fighter, and identifies any missing values in the odds data. This final dataset is streamlined for further analysis or integration with other data sources.

---

## Overview of the `main()` Function

The `main()` function orchestrates the data processing steps, including loading the UFC event and odds datasets, appending betting odds to event records, calculating average and best odds, and saving the cleaned data to a CSV file. Below is a breakdown of each step:

1. **Load Dataset**: Loads CSV files containing UFC event data and betting odds.

2. **Filter by Date Range**: Filters the event data to include only records within a specific date range, providing a streamlined dataset for analysis.

3. **Append Odds Data**: Calls `append_odds_to_fights()` to match odds data to each fighter in the event dataset and calculate average and best odds.

4. **Identify Missing Odds Values**: Searches for rows with missing average or best odds to ensure data completeness, and saves these rows to a separate file if needed.

5. **Save Cleaned Data**: Exports the cleaned and processed dataset, containing event details and matched betting odds, to a CSV file.

By the end of the `main()` function, a refined UFC event dataset with betting odds data is saved in CSV format.

---

## Function Descriptions -- Ordered by Execution in `main()`

### 1. `filter_by_date(df, start_date, end_date)`
- **Description**: Filters the DataFrame to include only events within a specified date range.
- **Returns**: A filtered DataFrame containing only events within the given date range.

### 2. `american_to_probability(odds)`
- **Description**: Converts American-style odds to implied probability. Positive and negative odds are handled differently to calculate the probability that reflects a bookmaker’s estimated likelihood of an outcome.
- **Returns**: Implied probability as a float, where 1 indicates 100% probability.

### 3. `probability_to_american(prob)`
- **Description**: Converts implied probability back into American odds format.
- **Returns**: American odds as an integer, with positive odds for underdogs and negative odds for favorites.

### 4. `calculate_avg_odds(odds_list)`
- **Description**: Computes the average odds from a list of American odds values, converting each to implied probability, averaging, and converting back to American odds.
- **Returns**: The average American odds as an integer.

### 5. `append_odds_to_fights(masterlist, odds)`
- **Description**: Matches betting odds to each fight event based on fighter names and event date. This function calculates both the average and best odds for each fighter.
- **Returns**: A list of dictionaries containing fight event data with appended odds details.

### 6. `calculate_best_avg_odds(odds_df, odds_prices)`
- **Description**: Helper function to calculate both average and best odds for a given list of odds prices. Uses `calculate_avg_odds` and identifies the highest odds as the "best odds."
- **Returns**: A tuple containing the average odds and best odds details (price and bookmaker).

---

## Execution Flow

The program starts by executing the `main()` function, which loads the UFC event and odds datasets, filters events by date, appends odds data, identifies missing values, and saves the processed data. Here’s a detailed step-by-step breakdown:

### 1. Loading the Datasets

The program loads CSV files containing UFC event data and betting odds, including columns for event details, fighter names, and odds from multiple bookmakers. The file paths are specified in the `main()` function. The datasets are loaded using Pandas and are ready for further processing.

### 2. Filtering Events by Date Range

The `filter_by_date()` function selects events within a specified date range (from June 6, 2020, to August 24, 2024), refining the dataset to include only recent events.

- **Input**: The full DataFrame and start and end dates for filtering.
- **Output**: A filtered DataFrame containing only the events within the date range.

### 3. Appending Odds Data to Fights

The `append_odds_to_fights()` function integrates odds data for each event by matching fighter names and event dates:
- For each fight, the function identifies odds for both fighters, calculates average odds based on implied probabilities, and identifies the best odds available from the listed bookmakers.
- If a fighter has missing odds, the program uses a relaxed matching approach to try to find similar fighter names.
- The function updates each event record with the calculated average odds, best odds, and the associated bookmaker.

- **Input**: Each row of the event DataFrame with odds information from multiple bookmakers.
- **Output**: A list of dictionaries where each event includes betting odds data for both fighters.

#### Example of Odds Integration
For a given fight with **fighter_a_name**: "John Smith" and **fighter_b_name**: "Jane Doe", the program searches the odds dataset for matching odds entries and appends both average and best odds with bookmaker details.

### 4. Identifying Rows with Missing Odds Values

The program checks each event for missing average or best odds for either fighter. Rows with missing odds values are saved to a separate CSV file for future review.

- **Input**: The DataFrame containing events with appended odds data.
- **Output**: A CSV file containing events with missing odds values and a count of affected rows.

### 5. Saving the Processed Data to CSV

After integrating odds and identifying missing values, the program saves the cleaned dataset to a new CSV file for further use. The file path for saving is specified in `main()`.

---

## Example CSV Output

The cleaned CSV file contains columns with event information, fighter details, and betting odds, as outlined below:

#### Event and Fight Information
- **event_name**: The name of the UFC event.
- **event_date**: The date of the event.
- **fighter_a_name**: The name of the first fighter.
- **fighter_b_name**: The name of the second fighter.

#### Betting Odds Data
- **Fighter A Odds**: A list of dictionaries, each containing bookmaker and odds price for Fighter A.
- **Fighter A Avg Odds**: The average odds calculated from all available odds for Fighter A.
- **Fighter A Best Odds**: The best odds available for Fighter A, along with the bookmaker name.
- **Fighter B Odds**: A list of dictionaries, each containing bookmaker and odds price for Fighter B.
- **Fighter B Avg Odds**: The average odds calculated from all available odds for Fighter B.
- **Fighter B Best Odds**: The best odds available for Fighter B, along with the bookmaker name.

By structuring the dataset with consistent fighter names, event details, and betting odds data, the CSV file is ready for further analysis, integration with other datasets, or statistical modeling.

---

## Prerequisites

This script requires:
- `preprocessed_event_masterlist.csv` and `mma_odds.csv` files, generated by the UFCStats Scraper and API Interfacing program.
- Python libraries: `pandas` for data handling and `difflib` for name matching.

This setup ensures accurate odds matching, calculation, and analysis of UFC fight data.

