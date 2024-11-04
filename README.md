# UFC Odds Data Integration Pipeline

## Project Functionality

This project provides a three-stage pipeline to interface with UFC data, preprocess betting odds, and append them to detailed fight data. The result is a consolidated dataset that combines event details, fighter statistics, and odds information. 

The project workflow is organized as follows:

1. **API Interfacing**: The first notebook interfaces with `TheOddsAPI` to retrieve event, fight, and fighter odds information.
2. **Odds Preprocessing**: The second notebook preprocesses raw odds data by structuring, filtering, and calculating implied probabilities.
3. **Appending Odds Data**: The final notebook integrates odds into the UFC data, appending calculated average and best odds for each fight.

The output is an enriched dataset that enables analysis and insight into UFC events with corresponding betting odds.

---

## Program Workflow

### 1. API Interfacing Program - `(1) API Interfacing.ipynb`

This initial program interfaces with the UFC API to gather core data for each UFC event, fight, and fighter. It collects essential details such as:
- **Event Details**: name, date, location.
- **Fight Information**: winner, method of victory, round and time of victory, weight class, title fight status, and links to fight-specific stats.
- **Fighter Statistics**: attributes like height, reach, and date of birth for each fighter in the event.

The program begins by loading an existing `cleaned_event_masterlist.csv` file, which contains UFC event dates. It then filters these dates based on a specified date range. After identifying the relevant event dates, the program requests historical betting odds for each date from The Odds API. These odds are compiled into a CSV file, named dynamically based on the date range, which is used for further integration with UFC event data in subsequent notebooks.

### 2. Odds Preprocessing Program - `(2) Odds Preprocessing.ipynb`

This program processes raw betting odds data to align it with the UFC event data, making it suitable for appending. The key preprocessing steps include:

#### Filtering Odds Data by Date Range
The program filters odds data to match the event date range from `cleaned_event_masterlist.csv`, ensuring that only relevant betting data is retained.

#### Converting Odds Formats
American-style odds are converted to implied probabilities, enabling accurate calculation of average and best odds across multiple bookmakers.

#### Structuring Odds Data
Odds data is restructured by:
- **Event Date**: Ensuring each odds entry aligns with the event date.
- **Fighter Names**: Standardizing and matching names to ensure consistency with the UFC dataset.

The processed odds data is prepared for integration with the UFC event data in the following notebook.

### 3. Appending Odds Data Program - `(3) Appending Odds Data.ipynb`

In the final stage, this notebook matches and appends odds data to each fight in `cleaned_event_masterlist.csv`. It performs the following key functions:

#### Matching Odds to Fights
The program matches betting odds to each fight record based on:
- **Event Date**: Ensuring odds data corresponds with the event date.
- **Fighter Names**: Using fuzzy matching for consistent name matching.

#### Calculating Average and Best Odds
For each fight:
- The program calculates average odds by averaging implied probabilities from multiple bookmakers.
- It identifies the best available odds and their associated bookmakers.

#### Final Output
The program appends the calculated average and best odds to each fight and saves the final output as `event_masterlist_with_odds.csv`. This file includes:
- **Event and Fight Details**: Basic details such as event name, fight outcome, and weight class.
- **Betting Odds Data**: Average and best odds for each fighter with associated bookmaker details.

This consolidated dataset is analysis-ready for sports modeling, statistical analysis, or integration with other datasets.

---

## Requirements

The project requires the following Python libraries:
- `requests`: For interfacing with the UFC API.
- `BeautifulSoup` from `bs4`: For parsing HTML content from the API.
- `pandas`: For data handling, cleaning, and preprocessing.
- `datetime`: For managing date formats in event and odds data.

---

## Example CSV Output

The final output file, `event_masterlist_with_odds.csv`, includes structured columns with details for each event, fight, and betting odds, as outlined below:

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
