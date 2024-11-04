# API Interfacing (`TheOddsAPI`) | Historical UFC Odds Data

This program interfaces with `TheOddsAPI` to retrieve head-to-head (`h2h`) closing lines for UFC events across a number of online sportsbooks, covering events from `June 6, 2020` onward – the first date `TheOddsAPI` began archiving UFC `h2h` odds. 

The program allows analysts to collect historical betting odds and save this data to a CSV file for further analysis.

### Configurable Retrieval Periods
By default, the program retrieves only the closing lines for each event. However, it can be configured to pull odds data over the period leading up to each fight, providing insight into betting trends and pre-fight market fluctuations. This expanded data retrieval option allows for more detailed analysis but requires a greater number of API tokens due to the higher number of queries.

---

## Overview of the `main()` Function

The `main()` function orchestrates the workflow for odds retrieval and data saving, completing the following tasks:

1. **Load Event Master List**: Loads an existing `cleaned_event_masterlist.csv` file, which contains a historical list of UFC event dates. These dates are used to cross-reference with `TheOddsAPI` data.

2. **Filter Event Dates by Date Range**: Filters event dates within a specified date range, applying a 12-hour offset to each date to standardize event times across time zones.

3. **Retrieve Historical Odds**: For each filtered event date, the program queries `TheOddsAPI` to retrieve historical `head-to-head` odds data, including details on:

   - **Event Date and Time**: Specific timestamps for each event.
   - **Fighter Odds**: `Head-to-head` betting lines for each fighter.
   - **Bookmaker and Market Data**: Details on the bookmakers providing odds and the types of odds markets available (`h2h`).

4. **Save Odds Data to CSV**: All retrieved odds data is saved into a CSV file, dynamically named based on the specified date range
      - `mma_odds_({start_date} to {end_date}).csv`

---

## Function Descriptions

### 1. `get_event_dates_between(masterlist_df, start_date, end_date)`
- **Description**: Filters the event dates within a specified range and applies a 12-hour offset to account for timezone differences.
- **Returns**: A list of unique event dates and the count of events within the range.

### 2. `get_historical_mma_odds(api_key, snapshot_date)`
- **Description**: Retrieves historical UFC odds for a specified date and time from `TheOddsAPI`, using American odds format.
- **Returns**: JSON response data containing the odds information or `None` if the request fails.

### 3. `save_odds_to_csv(odds_data, file_name)`
- **Description**: Appends structured odds data to a CSV file with `UTF-8` encoding. If the file is empty, it adds a header row.
- **Returns**: None, as it writes directly to a CSV file.

### 4. `get_and_save_odds_for_dates(api_key, event_dates, start_date, end_date, limit=None)`
- **Description**: Iterates over each event date, retrieves odds data using `get_historical_mma_odds()`, and saves the results to a dynamically named CSV file.
- **Returns**: None, as it saves data to a CSV file.

---

## Execution Flow

The program begins by executing the `main()` function, which handles the process of loading event dates, retrieving odds data from `TheOddsAPI`, and saving the output to a CSV file. 

Below is a step-by-step breakdown of the process:

### 1. Loading Event Master List

The program starts by loading a CSV file, `cleaned_event_masterlist.csv`, which contains UFC event dates. The date range for filtering events is also defined, with `start_date` and `end_date` specifying the bounds.

### 2. Filtering Event Dates

The `get_event_dates_between()` function filters the events in the master list to include only those that fall within the specified date range. A 12-hour offset is applied to each event date to standardize the times.

- **Input**: A DataFrame containing event dates, and `start_date`/`end_date` parameters.
- **Output**: A list of unique event dates within the specified range, adjusted by the offset.

### 3. Retrieving Historical Odds Data

The program iterates through each event date in the filtered list, calling `get_historical_mma_odds()` to retrieve the head-to-head odds data from `TheOddsAPI`.

- **Input**: The API key and each event’s adjusted date (snapshot date).
- **Output**: JSON data containing odds information for the specified date, or `None` if no data is available.

#### Example of Retrieved Odds Data

For each event's retrieved odds data, the program appends structured data to a CSV file:

| Timestamp                 | Home Team           | Away Team        | Commence Time          | Bookmaker | Market | Outcome Name          | Odds Price |
|---------------------------|---------------------|------------------|------------------------|-----------|--------|-----------------------|------------|
| 2024-08-24T11:55:39Z      | Albert Odzimkowski  | David Hosek      | 2024-08-24T17:00:00Z   | Bovada    | h2h    | Albert Odzimkowski    | -185       |
| 2024-08-24T11:55:39Z      | Albert Odzimkowski  | David Hosek      | 2024-08-24T17:00:00Z   | Bovada    | h2h    | David Hosek           | 140        |
| 2024-08-24T11:55:39Z      | Albert Odzimkowski  | David Hosek      | 2024-08-24T17:00:00Z   | LowVig.ag | h2h    | Albert Odzimkowski    | -175       |
| 2024-08-24T11:55:39Z      | Albert Odzimkowski  | David Hosek      | 2024-08-24T17:00:00Z   | LowVig.ag | h2h    | David Hosek           | 145        |

### 4. Saving Odds Data to CSV

For each event's retrieved odds data, the program calls `save_odds_to_csv()`, which appends the structured data to a CSV file. If the file is empty, it adds a header row to label the columns.

- **Input**: Odds data dictionary and the filename.
- **Output**: A CSV file containing event date, fighter names, bookmakers, and odds prices -> `mma_odds_({start_date} to {end_date}).csv`

#### Final CSV Data Columns

The CSV file produced by this program includes the following columns:
  
- **Timestamp**: The exact date and time when the odds were recorded.
- **Home Team**: The name of `Fighter A` (e.g., `Albert Odzimkowski`).
- **Away Team**: The name of `Fighter B` (e.g., `David Hosek`).
- **Commence Time**: The date and time of the event.
- **Bookmaker**: The name of the bookmaker providing the odds (e.g., `Bovada`, `LowVig.ag`).
- **Market**: The betting market type (e.g., `h2h` for head-to-head).
- **Outcome Name**: The specific fighter to whom the **Odds Price** applies.
- **Odds Price**: The American odds for the specified fighter; negative values indicate the favorite, and positive values indicate the underdog.


Calculations for implied probability are used internally to support accurate average odds processing but are **not** included in the final CSV output.
