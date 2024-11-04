# Odds Integration

This script is designed to integrate historical betting odds with a UFC event dataset by appending odds data to each fight.

---

## Overview of the `main()` Function

The `main()` function orchestrates the workflow for integrating odds data into the UFC masterlist. It includes loading the event and odds data, matching fighter names, calculating average and best odds, identifying rows with missing odds, and saving the cleaned dataset.

The following steps outline each part of the process:

1. **Load Event and Odds Data**: Loads the event dataset `preprocessed_event_masterlist.csv` and betting odds data `mma_odds.csv`, and then filters the events by a specified date range.

2. **Match Fighter Names**: Uses fuzzy matching to align fighter names from the masterlist with those in the odds dataset. If a direct match is unsuccessful, the program applies a relaxed matching strategy to improve accuracy.

3. **Calculate Average and Best Odds**: For each matched fighter, the program calculates the average odds across all bookmakers and identifies the best available odds.

4. **Identify and Handle Missing Odds**: Any fights missing matched odds are flagged and saved in a separate file for review. These rows are removed from the final output.

5. **Save Processed Data**: Exports two files:
   - A file with fights that could not be matched with odds -> `fights_with_nan_odds.csv`
   - A cleaned dataset with appended odds data, dynamically named based on the specified date range -> `event_masterlist_with_odds ({start_date} to {end_date}).csv`

---

## Function Descriptions -- Ordered by Execution in `main()`

### 1. `filter_by_date(df, start_date, end_date)`
- **Description**: Filters the event DataFrame to retain only rows within the specified date range.
- **Returns**: A DataFrame containing only rows within the date range.

### 2. `american_to_probability(odds)`
- **Description**: Converts American odds to implied probability, aiding in calculating average odds.
- **Returns**: The implied probability as a decimal.

### 3. `probability_to_american(prob)`
- **Description**: Converts implied probability back to American-style odds for calculating averages.
- **Returns**: The American odds value as an integer.

### 4. `calculate_avg_odds(odds_list)`
- **Description**: Computes the average American odds from a list by calculating implied probabilities, then averaging and converting back to American odds.
- **Returns**: The average odds value.

### 5. `similar(a, b)`
- **Description**: Calculates a similarity ratio between two strings using fuzzy matching, aiding in name matching.
- **Returns**: The similarity ratio (0 to 1).

### 6. `extract_first_last(name)`
- **Description**: Extracts only the first and last parts of a name for standardized matching.
- **Returns**: A simplified string with just the first and last name parts.

### 7. `is_name_match(fighter_name, outcome_name)`
- **Description**: Compares two names to check if they match, using caching and fuzzy matching for efficiency.
- **Returns**: `True` if names match based on a threshold, otherwise `False`.

### 8. `relaxed_name_match(fighter_name, outcome_name)`
- **Description**: Applies partial matching of first and last names to improve match success for incomplete or altered names.
- **Returns**: `True` if a match is found, otherwise `False`.

### 9. `append_odds_to_fights(masterlist, odds)`
- **Description**: Appends betting odds data to each fight in the masterlist by matching names and dates, calculating average and best odds for each fighter.
- **Returns**: A list of dictionaries where each entry includes fight details and associated odds.

### 10. `calculate_best_avg_odds(odds_df, odds_prices)`
- **Description**: Calculates both the average and best odds for a list of odds prices.
- **Returns**: A tuple containing the average odds and a dictionary of the best odds and bookmaker.

---

## Execution Flow

### 1. Loading Event and Odds Data

The program starts by loading event data from `preprocessed_event_masterlist.csv` and odds data from `mma_odds.csv`. It then filters the event data based on the specified date range, ensuring only relevant fights are processed.

### 2. Matching Fighter Names

The `is_name_match()` and `relaxed_name_match()` functions are used to align fighter names in the masterlist with names in the odds dataset. If exact matching is unsuccessful, relaxed matching improves accuracy by comparing only the first and last names separately.

- **Input**: Fighter names from `preprocessed_event_masterlist.csv` and `mma_odds.csv`.
- **Output**: Matched names for each fight.

### 3. Calculating Average and Best Odds

The `calculate_avg_odds()` function calculates the average odds across all available bookmakers for each fighter. The `calculate_best_avg_odds()` function also identifies the best odds available for each fighter along with the bookmaker offering it.

- **Input**: Lists of odds prices for each fighter in a fight.
- **Output**: Average and best odds for each fighter.

### 4. Handling Missing Odds

Any fights without matched odds are flagged, and the rows are saved to `fights_with_nan_odds.csv` for further review. Rows with missing odds data are excluded from the final dataset.

### 5. Saving the Final Output

After processing, the program saves the cleaned dataset with appended odds data to a dynamically named file based on the date range.

#### Example of Final CSV Output

The final output file `event_masterlist_with_odds ({start_date} to {end_date}).csv` includes columns with fight details and betting odds data:

| Timestamp               | Home Team          | Away Team       | Commence Time         | Bookmaker | Market | Outcome Name       | Odds Price | Fighter A Avg Odds | Fighter A Best Odds | Fighter B Avg Odds | Fighter B Best Odds |
|-------------------------|--------------------|-----------------|-----------------------|-----------|--------|--------------------|------------|--------------------|---------------------------------------|---------------------|---------------------|
| 2024-08-24T11:55:39Z    | Albert Odzimkowski | David Hosek     | 2024-08-24T17:00:00Z  | Bovada    | h2h    | Albert Odzimkowski | -185       | -180               | {'Odds': -185, 'Bookmaker': 'Bovada'} | -140               | {'Odds': -140, 'Bookmaker': 'LowVig.ag'} |

#### Output File Descriptions

- **`fights_with_nan_odds.csv`**: Contains fights that could not be matched with odds data for review.
- **`event_masterlist_with_odds ({start_date} to {end_date}).csv`**: The final cleaned dataset with appended average and best odds for each fight.

This processed dataset is now ready for detailed betting trend analysis and performance evaluation.
