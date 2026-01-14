# DuckDB CSV Explorer - User Guide

## Quick Start

### Opening the Application

1. **Start a local web server** (required because browsers restrict file:// access):
   ```bash
   cd /home/marty/code-projects/fun-with-duckdb
   python3 -m http.server 8765
   ```

2. **Open in your browser**:
   ```
   http://localhost:8765/index.html
   ```

3. **Wait for initialization**: You'll see "✓ DuckDB ready! Load a CSV to get started."

## Loading Data

### Option 1: Load from URL

**Best for**: Public datasets with CORS enabled (like GitHub, data.gov, etc.)

1. Enter a CSV URL in the text field
2. Click "Load URL"
3. Wait for the confirmation message

**Example URLs that work**:
```
https://raw.githubusercontent.com/datasets/covid-19/main/data/countries-aggregated.csv
https://raw.githubusercontent.com/datasets/gdp/main/data/gdp.csv
```

**Important**: Some servers (like data.cms.gov) block cross-origin requests. If you get a CORS error, download the file and use the local file option instead.

**No download required**: DuckDB streams the data efficiently - even a 100MB+ CSV loads instantly!

### Option 2: Load from Local File

**Best for**: Files on your computer, or URLs that don't allow CORS

1. Click "📂 Choose Local File"
2. Select your CSV/TSV file
3. Wait for the confirmation message

**Supported formats**: .csv, .tsv, .txt

## Running Queries

### Basic Usage

1. Type your SQL query in the "SQL Query" text area
2. Click "Run Query" (or press Ctrl+Enter / Cmd+Enter)
3. Results appear in the table below

### Your Data Table

All loaded data is available in a table named `data`. Every query must reference this table.

### Example Queries

**View first 10 rows**:
```sql
SELECT * FROM data LIMIT 10
```

**Count total rows**:
```sql
SELECT COUNT(*) FROM data
```

**See table structure**:
```sql
DESCRIBE data
```

**Filter data**:
```sql
SELECT * FROM data WHERE Country = 'Afghanistan' LIMIT 100
```

**Aggregate data**:
```sql
SELECT Country, SUM(Confirmed) as total_cases
FROM data
GROUP BY Country
ORDER BY total_cases DESC
LIMIT 10
```

**Complex analysis**:
```sql
SELECT
  Country,
  MAX(Deaths) - MIN(Deaths) as death_increase,
  MAX(Confirmed) as peak_cases
FROM data
WHERE Deaths > 0
GROUP BY Country
ORDER BY death_increase DESC
LIMIT 20
```

## Tips & Tricks

### Keyboard Shortcuts
- **Ctrl+Enter** or **Cmd+Enter**: Run the current query

### Performance
- DuckDB is extremely fast - queries on millions of rows execute in milliseconds
- For large files, use `LIMIT` to preview data before running full queries
- The query execution time is displayed after each run

### Column Names
- If your CSV has spaces in column names, use backticks or quotes:
  ```sql
  SELECT "Provider Last Name" FROM data LIMIT 10
  ```

### Data Types
- DuckDB automatically detects column types (strings, numbers, dates)
- Use `DESCRIBE data` to see detected types

### Multiple Data Sources
- Loading a new CSV replaces the previous data
- To work with multiple files, load one, then export/save results before loading another

## Common Issues

### "CORS blocked" Error
**Problem**: The server hosting the CSV doesn't allow browser access

**Solution**: Download the CSV file and use "Choose Local File" instead

### "Failed to initialize DuckDB"
**Problem**: DuckDB WASM files didn't load properly

**Solution**:
- Clear your browser cache
- Make sure you're accessing via http://localhost (not file://)
- Check that the `duckdb-wasm/` folder exists

### Query Returns No Results
**Problem**: The filter conditions don't match any rows

**Solution**:
- Run `SELECT * FROM data LIMIT 10` to see actual data
- Use `DESCRIBE data` to check column names and types
- Check for case sensitivity in string comparisons

### Browser Runs Slowly
**Problem**: Very large result sets can slow down the browser

**Solution**:
- Always use `LIMIT` to restrict results
- DuckDB can handle huge files, but browsers struggle displaying 100k+ rows

## SQL Reference

DuckDB supports full SQL with many built-in functions. Here are the most useful:

### Aggregations
- `COUNT(*)`, `SUM(column)`, `AVG(column)`, `MIN(column)`, `MAX(column)`

### Filtering
- `WHERE column = value`
- `WHERE column IN ('value1', 'value2')`
- `WHERE column LIKE '%pattern%'`
- `WHERE column > 100`

### Grouping
- `GROUP BY column`
- `HAVING COUNT(*) > 10` (filter after grouping)

### Ordering
- `ORDER BY column ASC` (ascending)
- `ORDER BY column DESC` (descending)

### Dates
- `CAST(column AS DATE)`
- `DATE_TRUNC('month', date_column)`
- `EXTRACT(year FROM date_column)`

### String Functions
- `UPPER(column)`, `LOWER(column)`
- `CONCAT(col1, ' ', col2)`
- `SUBSTRING(column, 1, 10)`

## Advanced Features

### Window Functions
```sql
SELECT
  Country,
  Date,
  Confirmed,
  LAG(Confirmed) OVER (PARTITION BY Country ORDER BY Date) as prev_day
FROM data
```

### CTEs (Common Table Expressions)
```sql
WITH daily_stats AS (
  SELECT Country, Date, Confirmed
  FROM data
  WHERE Confirmed > 0
)
SELECT * FROM daily_stats
ORDER BY Confirmed DESC
LIMIT 100
```

### Subqueries
```sql
SELECT * FROM data
WHERE Country IN (
  SELECT DISTINCT Country
  FROM data
  WHERE Deaths > 1000
)
LIMIT 100
```

## Full SQL Documentation

For complete DuckDB SQL documentation, visit:
https://duckdb.org/docs/sql/introduction

## Troubleshooting Server

If the Python server stops responding:

1. Find the process:
   ```bash
   ps aux | grep "python3 -m http.server"
   ```

2. Kill it:
   ```bash
   kill <process_id>
   ```

3. Restart:
   ```bash
   python3 -m http.server 8765
   ```

## Security Notes

- All data processing happens **in your browser** - no data is sent to external servers
- Local files are read directly by your browser
- Remote URLs are fetched directly by DuckDB via your browser
- This is a client-side only application - completely private

## Technical Details

- **Engine**: DuckDB WebAssembly v1.28.0 (MVP bundle)
- **Data table name**: Always `data`
- **Browser requirements**: Modern browser with WebAssembly support (Chrome, Firefox, Safari, Edge)
- **No installation required**: Pure HTML/CSS/JavaScript application
