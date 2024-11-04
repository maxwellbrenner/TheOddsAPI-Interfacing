# UFC Odds Data Integration Pipeline

## Project Functionality

This project provides a three-stage pipeline to interface with UFC data, preprocess betting odds, and append them to detailed fight data. The result is a consolidated dataset that combines event details, fighter statistics, and odds information. 

The project workflow is organized as follows:

1. **API Interfacing**: The first notebook interfaces with `TheOddsAPI` to retrieve `h2h` (head-to-head) UFC closing lines across a number of online sportsbooks
2. **Masterlist Preprocessing**: The second notebook preprocesses `cleaned_event_masterlist.csv` (generated by `UFCStats Scraper`) for odds integration.
3. **Odds Integration**: The final notebook combines odds data and UFC performance statistisc into a structured csv.

---

## Program Workflow

### 1. API Interfacing Program - `(1) API Interfacing.ipynb`

This program interfaces with `TheOddsAPI` to retrieve closing lines for UFC fights, covering events from `June 6, 2020` onward – the first date `TheOddsAPI` began archiving UFC `head-to-head` odds.

It’s also possible to configure the program to retrieve odds data over the period leading up to each fight. This approach provides a detailed view of betting trends and allows for analysis of pre-fight market fluctuation, though it requires significantly more API tokens.

1. **Load Event Master List**: It begins by loading an existing `cleaned_event_masterlist.csv` file. This file contains a historical list of UFC event dates, which are essential for cross-referencing with TheOddsAPI data.
   
2. **Filter Event Dates by Date Range**: The program filters the event dates within a specified date range, which can be customized. A 12-hour offset is applied to each event date to standardize times across time zones.

3. **Retrieve Historical Odds**: For each filtered event date, the program queries TheOddsAPI to retrieve the historical odds data for that date. The odds are retrieved in the "head-to-head" format and include details such as:
   - **Event Date and Time**: Specific timestamps of the events.
   - **Fighter Odds**: Head-to-head betting lines for each fighter.
   - **Bookmaker and Market Data**: Information about the bookmakers providing odds and the types of odds markets available (`h2h`).

4. **Save Odds Data to CSV**: All odds data is saved into a CSV file, dynamically named according to the specified date range
      - `mma_odds_({start_date} to {end_date}).csv`

### Data Collected

The data collected includes:

- **Event Details**: Information about each event, including the event date, home team, and away team (fighters).
- **Betting Odds**: Closing odds for each event, covering the head-to-head market and including details for each fighter's price (odds).
- **Bookmaker Information**: Bookmaker details and the specific market for each betting outcome.

### 2. Masterlist Preprocessing Program - `(2) Preprocessing Masterlist for Odds Integration.ipynb`
This program preprocesses cleaned UFC event data to standardize fighter names and drop nonessential columns, allowing later integration with betting odds data.

1. **Load and Prepare Data**: It begins by loading the `cleaned_event_masterlist.csv` file, which contains UFC event, fight, and fighter details, into a DataFrame.

2. **Standardize Fighter Names**: Using a dictionary of name replacements, the program checks each fighter's name and replaces it if a more standardized or official name is available. This ensures that names match across different data sources and are consistent with official UFC records. If a name replacement affects the winner field, the program updates the winner’s name accordingly to maintain accuracy.

3. **Filter for Essential Columns**: The program reduces the dataset to a focused set of columns, discarding any that are not directly relevant to later analysis.

4. **Save Cleaned Data**: The cleaned DataFrame is saved as a new CSV file, ready for use in subsequent analysis
      - `preprocessed_event_masterlist.csv`

### 3. Odds Integration Program - `(3) Odds Integration.ipynb`

In the final stage, this notebook matches and appends (closing) odds in `mma_odds_({start_date} to {end_date}).csv` 
to each fight in `preprocessed_event_masterlist.csv`. 

1. **Load and Filter Event Data**: Loads `preprocessed_event_masterlist.csv` and filters the data within a specified date range.
   
2. **Match Fighter Names**: For each fight, the program uses fuzzy name matching to align fighter names in the masterlist with those in the odds dataset. If direct matching fails, it applies a relaxed matching approach to compare first and last names separately, increasing the likelihood of a successful match.

3. **Calculate Average and Best Odds**: After matching names, the program calculates both the average (across all bookmakers) and best (highest value) odds for each fighter.

4. **Identify and Handle Missing Data**: The program identifies any fights without matched odds and saves them separately for further inspection. Rows with missing odds are removed from the final cleaned dataset.

5. **Save Processed Data**: The program saves two outputs:
   - A file with rows containing missing odds data
      - `fights_with_nan_odds.csv`
   - A cleaned, complete dataset with average and best odds for each fight, dynamically named based on the date range
      - `event_masterlist_with_odds ({start_date_str} to {end_date_str}).csv`

---

## Requirements

The project requires the following Python libraries:
- **pandas**: The program uses `pandas` extensively for loading, manipulating, and filtering data in DataFrames.
- **requests**: Essential for making HTTP requests to The Odds API to fetch historical MMA odds.
- **csv**: Part of the standard Python library; used to handle CSV file operations for saving odds data and loading data for processing.
- **datetime**: Also part of the Python standard library; used to manipulate and format dates, apply offsets, and filter by date range.
- **statistics**: This module provides functions for statistical calculations, specifically `mean` for calculating average probabilities.
- **difflib**: A standard library module used for calculating similarity ratios between fighter names to facilitate fuzzy matching.

---

## Example CSV Output

The final output file `event_masterlist_with_odds ({start_date_str} to {end_date_str}).csv` includes structured columns with details for each event, fight, and betting odds, as outlined below:

### Event and Fight Information
- **event_name**: Name of the UFC event.
- **event_date**: Date of the event.
- **fighter_a_name**: Name of Fighter A.
- **fighter_b_name**: Name of Fighter B.
- **winner**: Name of the fight’s winner.
- **method_of_victory**: Method of victory (e.g., KO/TKO, Decision).
- **round_of_victory**: Round in which the fight concluded.
- **weightclass**: Weight class of the fight.

### Betting Odds Data
- **Fighter A Avg Odds**: Average odds calculated for Fighter A.
- **Fighter A Best Odds**: Best odds available for Fighter A, with the bookmaker.
- **Fighter B Avg Odds**: Average odds calculated for Fighter B.
- **Fighter B Best Odds**: Best odds available for Fighter B, with the bookmaker.

The dataset is formatted for detailed analysis, modeling, or integration with other sports data sources.
