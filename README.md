# Assignment 2: Cleaned Datasets (EDA)

**Roll no.:** 23L-0549
**Version:** v1.0
**Date published:** 17 October 2026

## Summary: source, changes, version

| | Netflix (`23L-0549_A2_Q1.csv`) | Supermarket (`23L-0549_A2_Q2.csv`) |
|---|---|---|
| **Original source** | Kaggle dataset "Netflix Movies and TV Shows" (shivamb): https://www.kaggle.com/datasets/shivamb/netflix-shows | Instructor-provided file `supermarket_Sales.csv` |
| **What I changed** | Fixed 3 runtimes stored in `rating`; stripped spaces and converted `date_added` to dates; split `duration` into `duration_min` and `n_seasons`; filled blank director, cast and country with "Unknown" and added `has_director` and `has_cast`; flagged near-duplicates and outliers instead of removing them; added derived columns (`year_added`, `gap_years`, `rating_group`, `n_genres`, `n_countries`, `primary_country`). | No missing values, duplicates or invalid values were found, so nothing was removed or imputed. Converted `Date` and `Time` from text and added `hour`, `weekday`, `month`, `time_of_day` and `is_weekend`; flagged 9 high totals as `is_total_outlier`; renamed columns to snake_case. Redundant columns were kept and documented. |
| **Version** | v1.0 | v1.0 |
| **Date** | 17 October 2026 | 17 October 2026 |

Full details of each cleaning decision are in the sections below.

This repository contains the two cleaned datasets produced for Assignment 2 (Exploratory Data Analysis). Each dataset was cleaned in a Jupyter notebook, and every cleaning decision is documented below. The accompanying write-up explains the analysis and insights.

## Files

| File | Rows | Columns | Description |
|------|------|---------|-------------|
| `23L-0549_A2_Q1.csv` | 8,807 | 27 | Cleaned Netflix Movies and TV Shows catalogue |
| `23L-0549_A2_Q2.csv` | 1,000 | 24 | Cleaned supermarket sales transactions |

---

## Dataset 1: Netflix Movies and TV Shows

**Source:** Kaggle dataset "Netflix Movies and TV Shows" (shivamb): https://www.kaggle.com/datasets/shivamb/netflix-shows
**Content:** 8,807 titles (6,131 Movies and 2,676 TV Shows). One row is one title.

### Main columns

| Column | Description |
|--------|-------------|
| `show_id` | Identifier only; not an analytical feature |
| `type` | Movie or TV Show |
| `title` | Title name; not an analytical feature |
| `director`, `cast`, `country` | May hold several values in one cell. Blanks are filled with "Unknown" |
| `date_added` | Date added to Netflix (converted from text to date) |
| `release_year` | Year of release |
| `rating` | Original maturity rating |
| `duration` | Original duration text |
| `listed_in` | Genres (may hold several values) |
| `description` | Text description; not an analytical feature |

### Derived columns

| Column | Description |
|--------|-------------|
| `duration_min` | Runtime in minutes (Movies only) |
| `n_seasons` | Number of seasons (TV Shows only) |
| `has_director`, `has_cast` | Whether the original value was present |
| `year_added` | Year extracted from `date_added` |
| `gap_years` | `year_added` minus `release_year` |
| `rating_group` | Grouped maturity rating (e.g. Kids, Teens, Adults) |
| `n_genres`, `n_countries` | Counts derived from `listed_in` and `country` |
| `primary_country` | First listed country |
| `is_near_duplicate` | Flags near-duplicate title pairs |


### Cleaning summary

| Problem | Decision |
|---------|----------|
| Runtimes of 3 movies (74, 84, 66 min) sit in `rating`; their `duration` is blank | Moved the value to `duration`; left `rating` blank |
| `date_added` is text and 88 values start with a space | Stripped spaces and converted to dates; the 10 truly missing stay blank |
| `duration` mixes minutes (Movies) and seasons (TV Shows) | Split into `duration_min` and `n_seasons` |
| `director` is blank for 91% of TV Shows; `cast` and `country` about 9% blank | Filled with "Unknown", added `has_director` and `has_cast`; derived counts stay missing |
| 5 near-duplicate title pairs (case or punctuation differ) | Kept both rows and flagged `is_near_duplicate` |
| 709 duration outliers (IQR) and 14 titles added before their release year | Kept all and flagged them |

### Notes for users

- Analyse Movies and TV Shows separately. Never mix minutes with seasons.
- `gap_years` is just `year_added` minus `release_year`; `rating` and `rating_group` overlap.
- Leave "Unknown" country (831) and "Unknown" rating (7) out of rankings.
- Ten titles have no `date_added`, and 2021 only runs to September.
- Near-duplicates are flagged, not dropped, and outliers were kept, so use medians or the flag columns.

---

## Dataset 2: Supermarket Sales

**Source:** Instructor-provided file `supermarket_Sales.csv`.
**Content:** 1,000 transactions from three branches (A in Yangon, B in Mandalay, C in Naypyitaw) between 1 January and 30 March 2019. One row is one customer transaction (one invoice) for one product line. "Sales" means the `total` column, which includes 5% tax.

### Main columns

| Column | Description |
|--------|-------------|
| `invoice_id` | Identifier only |
| `branch`, `city` | Branch and its city (one-to-one) |
| `customer_type` | Member or Normal |
| `product_line` | Product category |
| `unit_price` | Price per unit |
| `quantity` | Units purchased |
| `tax_5pct` | 5% tax on `cogs` |
| `total` | `cogs` plus tax |
| `date`, `time` | Converted from text (month/day/year) |
| `payment` | Payment method |
| `cogs` | Unit price x quantity (pre-tax sales, not a real cost) |
| `gross_margin_pct` | Constant 4.7619 |
| `gross_income` | Equal to `tax_5pct` |
| `rating` | Customer rating (4.0 to 10.0) |

### Derived columns

`hour`, `weekday`, `month`, `time_of_day`, `is_weekend`, `is_total_outlier`.

### Cleaning summary

| Problem | Decision |
|---------|----------|
| Missing values, duplicates, invalid values or label variants | None found, so nothing was removed or imputed |
| `Date` and `Time` stored as text | Converted; added `hour`, `weekday`, `month`, `time_of_day`, `is_weekend` |
| `cogs`, `tax_5pct`, `total`, `gross_income` can be recomputed from price x quantity; margin is constant | Kept and documented as redundant |
| `Branch` and `City` are one-to-one | Kept both |
| 9 high `total` values (all quantity 10) | Kept and flagged `is_total_outlier` |
| Column names with spaces and symbols | Renamed to snake_case |

### Notes for users

- The only independent numeric inputs are `unit_price` and `quantity`.
- `total`, `tax_5pct`, `cogs` and `gross_income` are all derived from them. Do not use one to predict another (leakage). `gross_margin_pct` is constant and `city` duplicates `branch`.
- `rating` shows no relationship with any other column.
- The data covers only 1 Jan to 30 Mar 2019.

---


## License and attribution

- Netflix data: derived from the Kaggle dataset "Netflix Movies and TV Shows" (shivamb), https://www.kaggle.com/datasets/shivamb/netflix-shows. Please check the original dataset's license on Kaggle.
- Supermarket data: derived from the instructor-provided file `supermarket_Sales.csv`.

## Author

Roll no. 23L-0549
