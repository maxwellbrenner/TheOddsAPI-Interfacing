# API Interfacing (TheOddsAPI) | Historical MMA Odds Data

This program retrieves historical odds data for MMA events from TheOddsAPI, cleans and processes the odds, calculates implied probabilities, and saves the data into a structured CSV file. 

This tool is valuable for analysts seeking detailed betting data on MMA events, with data formatted for analysis and further processing.

---

## Overview of the `main()` Function

The `main()` function orchestrates the odds retrieval and data processing by following these steps:

1. **Load Masterlist and Define Date Range**: Loads the event masterlist (CSV file) to filter event dates within a specified range.

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

