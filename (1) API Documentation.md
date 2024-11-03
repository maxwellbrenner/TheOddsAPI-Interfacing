# API Interfacing (`TheOddsAPI`) | Historical MMA Odds Data

This program retrieves historical odds data for MMA events from `TheOddsAPI`, cleans and processes the odds, calculates implied probabilities, and saves the data into a structured CSV file. 

This tool is valuable for analysts seeking detailed betting data on MMA events, with data formatted for analysis and further processing.

---

## Overview of the `main()` Function

The `main()` function orchestrates the odds retrieval and data processing by following these steps:

1. **Load Masterlist and Define Date Range**: Loads the `event_masterlist` (CSV file) to filter event dates within a specified range. This file is generated using the `UFCStats Scraper`.

2. **Filter Event Dates**: Calls `get_event_dates_between()` to filter events based on `start_date` and `end_date`, applying a 12-hour offset to each date for standardized times.

3. **Retrieve Odds Data**: Calls `get_and_save_odds_for_dates()` to retrieve historical odds data from The Odds API for each filtered event date, saving the data in a CSV file. Each odds entry includes fighter names, odds prices, and bookmakers.

4. **Display and Save Processed Data**: Saves the processed odds data into a new CSV file, structured to facilitate analysis.

By the end of the `main()` function, the CSV file will contain all MMA odds data within the specified date range, formatted for further analysis.

---

## Function Descriptions -- Ordered by Execution in `main()`

### 1. `get_event_dates_between(masterlist_df, start_date, end_date)`
- **Description**: Filters the event dates within a specified range, applies a 12-hour offset to account for timezone differences, and returns a list of unique event dates.
- **Returns**: A list of unique event dates and the count of events within the range.

### 2. `get_historical_mma_odds(api_key, snapshot_date)`
- **Description**: Retrieves historical MMA odds for a specific date and time from The Odds API, using American odds format. This function constructs the URL and sends a request to the API.
- **Returns**: JSON response data containing the odds information or `None` if the request fails.

### 3. `save_odds_to_csv(odds_data, file_name)`
- **Description**: Appends structured odds data to a CSV file with UTF-8 encoding. If the file is empty, it adds a header row.
- **Returns**: None, as it writes directly to a CSV file.

### 4. `get_and_save_odds_for_dates(api_key, event_dates, start_date, end_date, limit=None)`
- **Description**: Iterates over each event date, retrieves odds data using `get_historical_mma_odds()`, and saves the results to a dynamically named CSV file. It also logs remaining and used requests based on API rate limits.
- **Returns**: None, as it saves data to a CSV file.

### 5. `calculate_implied_probability_american_odds(odds)`
- **Description**: Converts American odds to implied probability, providing a probability estimate of the event occurring.
- **Returns**: A float representing implied probability, rounded to two decimal places.

### 6. `parse_odds_data(row, fights_with_odds)`
- **Description**: Parses a row of odds data, structures it by fighter and bookmaker, and stores it in a nested dictionary. Differentiates fighters using the "Outcome Name" field.
- **Returns**: None, as it modifies `fights_with_odds` in place.

### 7. `implied_probability_to_american_odds(probability)`
- **Description**: Converts an implied probability back to American odds format, with positive values for underdogs and negative values for favorites.
- **Returns**: A float representing American odds.

### 8. `calculate_averages_and_best_odds(fights_with_odds)`
- **Description**: Processes odds data for each fighter, calculating average odds, best odds, and the difference in implied probability between them. Updates `fights_with_odds` with these metrics.
- **Returns**: None, as it modifies `fights_with_odds` in place.

### 9. `format_odds_display(fights_with_odds)`
- **Description**: Formats and prints odds and implied probability data for each fighter, displaying average odds, best odds, and the implied probability difference. Useful for quickly assessing bookmaker odds variance.
- **Returns**: None, as it prints directly to the console.

### 10. `load_odds_from_csv(file_name)`
- **Description**: Reads odds data from a CSV file, structures it for processing, and calculates average and best odds with implied probability differences for each fighter.
- **Returns**: A nested dictionary (`fights_with_odds`) containing detailed odds information for each fight event.

---

## Execution Flow

The program begins by executing the `main()` function. This function handles the overall flow, loading event dates, retrieving odds data from The Odds API, processing the data, and saving the output to a CSV file. Below is a step-by-step breakdown of the process:

### 1. Loading Masterlist and Defining Date Range

The program starts by loading a CSV masterlist of MMA events, which contains event dates. The file path is specified in the `main()` function. The date range for filtering events is also defined, with `start_date` and `end_date` specifying the bounds.

### 2. Filtering Event Dates

The `get_event_dates_between()` function filters the events in the masterlist to retain only those that fall within the specified date range. A 12-hour offset is applied to each event date to standardize the event times. 

- **Input**: A DataFrame containing event dates, and `start_date`/`end_date` parameters.
- **Output**: A list of unique event dates within the specified range, adjusted by the offset.

### 3. Retrieving Historical Odds Data

The program then iterates through each event date in the filtered list. For each date, it calls `get_historical_mma_odds()` to fetch the historical odds data from The Odds API.

- **Input**: The API key and each event’s adjusted date (snapshot date).
- **Output**: JSON data containing odds information for the specified date, or `None` if no data is available.

#### Example of Retrieved Odds Data
- **Timestamp**: `2024-08-24T12:00:00Z`
- **Fighter A**: `"Fighter A Name"`
- **Fighter B**: `"Fighter B Name"`
- **Odds**: {"Bookmaker": `"DraftKings"`, "Odds Price": `-150`}

### 4. Saving Odds Data to CSV

For each event's retrieved odds data, the program calls `save_odds_to_csv()`, which appends the structured data to a CSV file. If the file is empty, it adds a header row to label the columns.

- **Input**: Odds data dictionary and the filename.
- **Output**: A CSV file containing event date, fighter names, bookmakers, and odds prices.

### 5. Calculating Implied Probability

The `calculate_implied_probability_american_odds()` function converts American odds into implied probability values, estimating the likelihood of each outcome according to the bookmaker.

- **Input**: American odds value (positive or negative).
- **Output**: A float representing implied probability, rounded to two decimal places.

#### Example of Implied Probability Calculation
- **American Odds**: `+150`
- **Implied Probability**: `0.4` (or `40%`)

### 6. Structuring Odds Data for Each Fighter

The `parse_odds_data()` function organizes the odds data for each event by identifying each fighter’s odds across different bookmakers. It stores this data in a nested dictionary, keyed by event and fighter.

- **Input**: Odds data row from CSV and the main `fights_with_odds` dictionary.
- **Output**: None, as it updates `fights_with_odds` with organized data for each fighter.

#### Example of Parsed Data Structure
- **Event Key**: (`"Fighter A Name"`, `"Fighter B Name"`, `"2024-08-24T12:00:00Z"`)
  - **Fighter A**:
    - Bookmaker: `"DraftKings"`, Odds Price: `-150`
  - **Fighter B**:
    - Bookmaker: `"FanDuel"`, Odds Price: `+130`

### 7. Calculating Average and Best Odds

For each event, the `calculate_averages_and_best_odds()` function calculates the average and best odds for each fighter, based on implied probability. It also computes the `implied probability difference` between the `average` and `best` odds, which helps assess the potential value in the odds variance across bookmakers.

- **Input**: The main `fights_with_odds` dictionary containing all odds data.
- **Output**: None, as it updates `fights_with_odds` in place with calculated averages and implied probability differences.

#### Example of Calculated Averages and Best Odds
- **Fighter A**:
  - Average Odds: `-155`
  - Best Odds: `-150`
  - Implied Probability Difference: `1.5%`
- **Fighter B**:
  - Average Odds: `+115`
  - Best Odds: `+120`
  - Implied Probability Difference: `0.7%`

### 8. Displaying Structured Odds Data

The `format_odds_display()` function prints the odds data for each fighter in a structured format, showing all available odds from different bookmakers, along with the calculated average, best odds, and implied probability differences.

- **Input**: The main `fights_with_odds` dictionary.
- **Output**: None, as it displays the data directly to the console.

## Final CSV Data Columns

The CSV file produced by this program includes the following columns:

- **Timestamp**: The `date` and `time` of the event.
- **Home Team**: The `name` of `Fighter A`.
- **Away Team**: The `name` of `Fighter B`.
- **Bookmaker**: The `name` of the bookmaker providing the odds.
- **Odds Price**: The American odds price for the given fighter.
- **Market**: The betting market (e.g., `h2h` for head-to-head).
- **Implied Probability**: Calculated implied probability from American odds for each fighter.

The data is organized so that each row represents an odds entry for a specific event, including bookmaker information and calculated implied probabilities. The structure enables easy loading into data analysis tools for further examination.
