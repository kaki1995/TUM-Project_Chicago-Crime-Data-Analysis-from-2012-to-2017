# Chicago Crime Data Analysis (2012–2017)

Exploratory analysis of reported crimes in Chicago from 2012 through 2017. The project cleans and enriches a large public-safety dataset, examines temporal and geographic patterns, compares common crime categories and locations, and visualizes arrest and domestic-incident proportions.

The analysis was prepared by **Group Analytikos (Group 22)** as part of the **Advanced Python and Data Science** coursework at the **Technical University of Munich (TUM), Campus Heilbronn**.

**Supervisor:** Batuhan Can

## Table of contents

- [Project goals](#project-goals)
- [Repository contents](#repository-contents)
- [Dataset](#dataset)
- [Requirements](#requirements)
- [Getting started](#getting-started)
- [Analysis workflow](#analysis-workflow)
- [Visualizations and outputs](#visualizations-and-outputs)
- [Findings recorded in the notebook](#findings-recorded-in-the-notebook)
- [Important limitations](#important-limitations)
- [Known issue](#known-issue)
- [Contributors](#contributors)
- [License](#license)

## Project goals

The notebook is intended to answer questions such as:

- Which crime types and location types are reported most often?
- How do reported crime counts vary by year, month, weekday, and hour?
- What proportion of reported incidents resulted in an arrest?
- What proportion of incidents were classified as domestic?
- Where are geographic concentrations of reported incidents?
- Are selected numeric and boolean variables strongly correlated?

The repository currently covers data loading, cleaning, feature extraction, and exploratory data analysis (EDA). The notebook contains headings for model development, evaluation, and tuning, but those sections have not yet been implemented.

## Repository contents

```text
.
├── Group_22_Chicago_Crimes.ipynb  # Complete cleaning and EDA workflow
├── README.md                       # Project documentation
└── .gitignore                      # Excludes CSV data and local Python artifacts
```

The dataset is deliberately not tracked by Git because `*.csv` files are ignored.

## Dataset

The notebook expects a file named:

```text
Chicago_Crimes_2012_to_2017.csv
```

Place it in the repository root, next to `Group_22_Chicago_Crimes.ipynb`.

The input must contain the Chicago crime-record fields used by the notebook:

| Column | Meaning | Used for |
|---|---|---|
| `ID` | Record identifier | Summary and correlation analysis |
| `Case Number` | Police case identifier | Missing-value handling and duplicate checks |
| `Date` | Reported incident date and time | Year, month, day, weekday, and time features |
| `Block` | Partially redacted street block | Block-level coordinate imputation |
| `IUCR` | Illinois Uniform Crime Reporting code | Crime classification context |
| `Primary Type` | High-level crime category | Category distributions and yearly trends |
| `Description` | More specific incident description | Descriptive context |
| `Location Description` | Type of place where the incident occurred | Location-frequency analysis |
| `Arrest` | Whether an arrest was recorded | Arrest distribution |
| `Domestic` | Whether the incident was classified as domestic | Domestic-incident distribution |
| `Beat` | Police beat | Community-area and coordinate imputation |
| `District` | Police district | Missing-value handling and correlation analysis |
| `Ward` | City ward | Missing-value handling and correlation analysis |
| `Community Area` | Chicago community-area number | Missing-value handling and correlation analysis |
| `FBI Code` | FBI crime classification code | Classification context |
| `X Coordinate`, `Y Coordinate` | Projected map coordinates | Geographic imputation |
| `Year` | Dataset-provided incident year | Recreated from `Date` during analysis |
| `Latitude`, `Longitude` | Geographic coordinates | Heatmap generation |
| `Location` | Combined coordinate representation | Geographic imputation |

Additional source columns, such as `Updated On`, are retained but are not central to the current analysis. Some distributions or missing-value counts may differ if a newer or differently filtered version of the data is used.

### Data responsibility

Crime records describe reported incidents, not all crime, guilt, or individual risk. Avoid using this exploratory notebook to profile individuals or communities. Geographic fields may be approximate or partially redacted, and reporting and enforcement practices can introduce systematic bias.

## Requirements

The notebook metadata records Python **3.11.9**. A recent Python 3.10+ environment should also work.

Required packages:

- `jupyter`
- `numpy`
- `pandas`
- `matplotlib`
- `seaborn`
- `folium`

The notebook also uses Python's standard-library `collections` module. `geopy` and `pyproj` appear in a commented alternative geocoding approach and are not required for the active workflow.

## Getting started

1. Clone the repository and enter its directory:

   ```bash
   git clone <repository-url>
   cd TUM-Project_Chicago-Crime-Data-Analysis-from-2012-to-2017
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   ```

   On macOS or Linux:

   ```bash
   source .venv/bin/activate
   ```

   On Windows PowerShell:

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

3. Install the notebook dependencies:

   ```bash
   python -m pip install --upgrade pip
   python -m pip install jupyter numpy pandas matplotlib seaborn folium
   ```

4. Copy `Chicago_Crimes_2012_to_2017.csv` into the repository root.

5. Start Jupyter:

   ```bash
   jupyter notebook Group_22_Chicago_Crimes.ipynb
   ```

6. Run the cells from top to bottom. The cleaning stages depend on dictionaries and data frames created in earlier cells.

To use JupyterLab instead, install `jupyterlab` and run:

```bash
jupyter lab Group_22_Chicago_Crimes.ipynb
```

## Analysis workflow

### 1. Load and inspect the data

The notebook loads the CSV with pandas, then examines its shape, data types, descriptive statistics, and missing values.

### 2. Handle missing values

The current notebook applies the following rules to a copy of the original data:

| Field | Current strategy |
|---|---|
| `Case Number` | Fill missing values with the placeholder `JE999999` |
| `District` | Fill the observed missing value with district `3`, inferred from matching ward/beat records |
| `Ward` | Fill missing values with ward `41`, inferred from the relevant beats |
| `Community Area` | Fill with the most frequent community area for the same police beat |
| `Location Description` | Fill with `OTHER` |
| Coordinate fields | First use the most common coordinate tuple for the same block, then fall back to the most common tuple for the same beat |

Coordinate tuples include `X Coordinate`, `Y Coordinate`, `Latitude`, `Longitude`, and `Location` so the fields remain aligned.

### 3. Engineer time features

`Date` is converted to a pandas datetime, after which the notebook derives:

- `Year`
- `Month`
- `Day`
- `Hour`
- `Minute`
- `Second`
- `DayOfWeek`

### 4. Perform exploratory analysis

The notebook analyzes:

- primary crime-type frequencies;
- the ten most frequent crime types over time;
- the most frequent location descriptions;
- arrest and domestic-incident shares;
- annual, monthly, weekly, and hourly distributions;
- correlations among selected identifier, location, time, and boolean fields; and
- geographic incident density using a Folium heatmap.

## Visualizations and outputs

Running the notebook displays the following outputs inline:

1. Missing-value summaries before and after imputation.
2. A horizontal count plot of all primary crime types.
3. A yearly line chart for the ten most frequent crime types.
4. A count plot of the ten most frequent location descriptions.
5. Pie charts for arrest and domestic classifications.
6. A four-panel view of crime counts by year, month, weekday, and hour.
7. A correlation heatmap for selected columns.
8. An interactive Folium map of incident density.

The active code does not save plots or cleaned data to disk. A commented line shows how the Folium map could be exported:

```python
crime_map.save("crime_heatmap.html")
```

## Findings recorded in the notebook

The notebook's original analysis reports the following descriptive observations for the dataset version used by the team:

- Theft, battery, and criminal damage are the most common primary categories.
- Streets, residences, and apartments are among the most frequent recorded locations.
- Approximately 74.1% of incidents did not have an arrest recorded.
- Approximately 84.9% of incidents were classified as non-domestic.
- Reported incidents generally declined across the covered years, although incomplete 2017 data may distort that comparison.
- July and August had relatively high counts, while February and March had relatively low counts.
- Friday and Saturday had the highest weekday counts in the team's analysis.
- Counts peaked around the late evening and early morning period.
- The selected numeric variables showed mostly weak linear correlations.

These are descriptive results, not causal conclusions. Re-run the notebook against your copy of the data before quoting exact values.

## Important limitations

- **Incomplete period:** The notebook notes that 2017 is incomplete, so year-to-year totals are not directly comparable without normalizing the covered dates.
- **Imputation assumptions:** Filling coordinates from a block or beat assigns a representative location rather than recovering the true incident location.
- **Hard-coded values:** The replacements for district and ward are based on the missing records observed in the team's dataset and may not generalize to another extract.
- **Placeholder case numbers:** Reusing `JE999999` for multiple missing case numbers creates an artificial duplicate identifier.
- **Type consistency:** Filling numeric district and ward columns with quoted strings may change or mix their pandas data types, depending on the pandas version.
- **Performance:** The coordinate preparation uses `DataFrame.iterrows()` and prints large dictionaries. On a full multi-year dataset, these cells can be slow and can generate very large notebook output.
- **Memory use:** The full CSV and several copies/dictionaries are held in memory. A machine with limited RAM may need chunked loading or a filtered sample.
- **Correlation interpretation:** IDs and administrative area codes are labels, not continuous measurements; Pearson correlation on these fields has limited substantive meaning.
- **No predictive model yet:** Model development, evaluation, and tuning sections are placeholders.
- **No automated tests or locked environment:** Results may vary across library and dataset versions.

## Known issue

The Folium cell creates a map in a variable named `map`, but then adds the heat layer to `m`:

```python
map = folium.Map(location=[41.8781, -87.6298], zoom_start=12)
HeatMap(heat_data).add_to(m)
```

Unless `m` already exists in the interactive session, this raises `NameError`. Use a clearly named variable consistently:

```python
crime_map = folium.Map(location=[41.8781, -87.6298], zoom_start=12)
HeatMap(heat_data).add_to(crime_map)
crime_map
```

## Contributors

Group Analytikos (Group 22):

- Yen Vu Thi Ngoc
- Yesenia
- Arpita
- Chan Surendra

This project was completed for the Advanced Python and Data Science coursework at the Technical University of Munich, Campus Heilbronn, under the supervision of Batuhan Can.

## License

No license file is currently included. Unless a license is added, the repository's code and notebook remain under the default copyright of their authors. The source dataset may have separate terms of use that should be reviewed before redistribution.
