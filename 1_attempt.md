# Diagnosis & Planned Decisions (THINK phase)

After loading the dataset (97 rows, 6 columns), I identified six core problems:

Problem 1: Exact Duplicate Row
*   Code/Diagnosis:`raw.duplicated().sum()` revealed 1 entirely duplicated row (Row 96 is a copy of Row 8).
*   Decision: Correct. I will drop this row to prevent redundant data from skewing frequency calculations.

Problem 2: Inconsistent Categorical Strings
*   Code/Diagnosis: `raw["item"].value_counts()` showed fragmented categories due to trailing spaces and lowercase letters (e.g., "oil b ").
*   Decision:Correct. I will standardize strings using `.str.strip().str.title()`.

Problem 3: Missing Observation / Mislabeled Item
*  Code/Diagnosis: After standardizing strings and removing the duplicate, `item.value_counts()` showed exactly 24 observations for Pulse C and Sweetener D (12 weeks × 2 cities = 24). However, Oil B had 25, and Staple A had 23. Filtering by `date == '2026-02-05'` in `City 1` showed two Oil B entries and zero Staple A entries. 
*   Decision: Correct. The Oil B entry at Index 40 has a price of 108.5, which perfectly matches the price trend for Staple A. I will re-label Index 40 as "Staple A" so every item appears its expected 24 times.

Problem 4: String Formatting in Numeric Data
*   Code/Diagnosis: `raw.info()` showed `price` as a string object. Forcing coercion revealed Index 17 contained a comma ("1,250").
*   Decision:Correct / Flag. I will use `.str.replace(",", "")` to remove the comma before converting to numeric. I will *flag* 1250.0 as a likely data entry typo (likely meant to be 125.0), but I will not alter the number without domain verification. 

Problem 5: Missing Price Value
*   Code/Diagnosis: `raw.isna().sum()` showed 1 missing price value (Pulse C, City 2, 2026-01-01).
*   **Decision: Retain. I will leave the value as `NaN`. Dropping the row destroys the record that the store was visited, and inventing a price is data fabrication.

Problem 6: Date Format
*   Code/Diagnosis: `raw.info()` shows the `date` column as a string.
*  Decision:Correct. I will convert it using `pd.to_datetime()` for chronological sorting.
