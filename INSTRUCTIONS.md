# Complete Instructions for DuckDB CSV Explorer

## 🚀 Getting Started (5 Minutes)

### Step 1: Start the Server

Open a terminal and run:

```bash
cd /home/marty/code-projects/fun-with-duckdb
python3 -m http.server 8765
```

**Keep this terminal running** - don't close it! You should see:
```
Serving HTTP on 0.0.0.0 port 8765 (http://0.0.0.0:8765/) ...
```

### Step 2: Open the Application

Open your web browser and go to:
```
http://localhost:8765/index.html
```

Wait a few seconds for "✓ DuckDB ready! Load a CSV to get started." to appear.

### Step 3: Load Some Data

**Try a remote file first** (easiest):

1. Copy this URL:
   ```
   https://raw.githubusercontent.com/datasets/covid-19/main/data/countries-aggregated.csv
   ```

2. Paste it in the "Enter CSV URL" field

3. Click "Load URL"

4. Wait for "✓ Loaded [number] rows from URL"

### Step 4: Run Your First Query

The default query is already there:
```sql
SELECT * FROM data LIMIT 10
```

Click "Run Query" (or press Ctrl+Enter)

**Congratulations!** You just queried 161,568 rows of COVID-19 data without downloading the entire file.

## 📊 What You Can Do Now

### Explore the Data Structure
```sql
DESCRIBE data
```

This shows all columns and their types.

### Count Rows
```sql
SELECT COUNT(*) FROM data
```

### Filter by Country
```sql
SELECT * FROM data
WHERE Country = 'United States'
ORDER BY Date DESC
LIMIT 20
```

### Find Top Countries by Deaths
```sql
SELECT
  Country,
  MAX(Deaths) as total_deaths
FROM data
GROUP BY Country
ORDER BY total_deaths DESC
LIMIT 10
```

### Calculate Daily Changes
```sql
SELECT
  Country,
  Date,
  Confirmed,
  Confirmed - LAG(Confirmed) OVER (PARTITION BY Country ORDER BY Date) as daily_new
FROM data
WHERE Country = 'Italy'
ORDER BY Date DESC
LIMIT 30
```

## 📁 Loading Your Own Data

### Option A: From a URL

**Requirements**:
- The URL must be publicly accessible
- The server must allow CORS (cross-origin requests)
- Works best with GitHub, Data.gov, and similar services

**Steps**:
1. Find a CSV URL online
2. Paste it in the text field
3. Click "Load URL"

**If you get a CORS error**, use Option B instead.

### Option B: From Your Computer

**Works with any CSV file** - no restrictions!

**Steps**:
1. Click "📂 Choose Local File"
2. Select your .csv or .tsv file
3. Wait for confirmation

**File size**: Can handle files up to several hundred MB (depends on your RAM).

## 🎯 Common Tasks

### Task 1: Analyze Sales Data

Suppose you have a sales.csv with columns: date, product, quantity, revenue

```sql
-- Total revenue by product
SELECT
  product,
  SUM(revenue) as total_revenue,
  SUM(quantity) as total_quantity
FROM data
GROUP BY product
ORDER BY total_revenue DESC;

-- Monthly revenue trend
SELECT
  DATE_TRUNC('month', date) as month,
  SUM(revenue) as monthly_revenue
FROM data
GROUP BY month
ORDER BY month;

-- Top 10 days
SELECT
  date,
  SUM(revenue) as daily_revenue
FROM data
GROUP BY date
ORDER BY daily_revenue DESC
LIMIT 10;
```

### Task 2: Clean and Filter Data

```sql
-- Remove rows with nulls
SELECT * FROM data
WHERE column_name IS NOT NULL;

-- Deduplicate
SELECT DISTINCT * FROM data;

-- Filter by multiple conditions
SELECT * FROM data
WHERE category IN ('A', 'B', 'C')
  AND amount > 100
  AND date >= '2024-01-01';
```

### Task 3: Join Data (Self-Join Example)

```sql
-- Compare consecutive rows
SELECT
  a.date as date1,
  b.date as date2,
  a.value as value1,
  b.value as value2,
  (b.value - a.value) as change
FROM data a
JOIN data b ON b.id = a.id + 1
LIMIT 100;
```

## 💡 Pro Tips

### 1. Always Preview First
Before running complex queries on large datasets:
```sql
SELECT * FROM data LIMIT 5;
```

### 2. Check for Nulls
```sql
SELECT
  COUNT(*) as total_rows,
  COUNT(column_name) as non_null_rows,
  COUNT(*) - COUNT(column_name) as null_rows
FROM data;
```

### 3. Use Aliases for Readability
```sql
SELECT
  very_long_column_name as short_name,
  another_long_name as name2
FROM data;
```

### 4. Format Large Numbers
```sql
SELECT
  category,
  PRINTF('$%,.2f', SUM(amount)) as formatted_total
FROM data
GROUP BY category;
```

### 5. Find Unique Values
```sql
SELECT DISTINCT column_name
FROM data
ORDER BY column_name;
```

## 🔧 Troubleshooting

### Problem: "CORS blocked" Error

**What it means**: The website hosting the CSV doesn't allow browser access.

**Solution**:
1. Download the CSV file to your computer
2. Use "Choose Local File" instead

### Problem: Browser Freezes

**What it means**: You're trying to display too many rows.

**Solution**: Always use `LIMIT`:
```sql
SELECT * FROM data LIMIT 100;
```

### Problem: "Column not found"

**What it means**: The column name is wrong or has spaces.

**Solution**: Check exact names with `DESCRIBE data`, and use quotes for spaces:
```sql
SELECT "Column With Spaces" FROM data;
```

### Problem: Query Returns Nothing

**What it means**: Your filter is too restrictive.

**Solution**: Check what values exist first:
```sql
-- See all unique values
SELECT DISTINCT column_name FROM data;

-- Check case sensitivity
SELECT * FROM data WHERE LOWER(country) = 'usa';
```

### Problem: Dates Look Like Numbers

**What it means**: DuckDB interpreted dates as timestamps.

**Solution**: Convert them:
```sql
SELECT
  CAST(Date / 1000 AS TIMESTAMP) as readable_date,
  *
FROM data;
```

## 📚 Learn More SQL

### Essential SQL Patterns

**Aggregation**:
```sql
SELECT category, COUNT(*), AVG(value), SUM(amount)
FROM data
GROUP BY category;
```

**Subqueries**:
```sql
SELECT * FROM data
WHERE value > (SELECT AVG(value) FROM data);
```

**CTEs (Common Table Expressions)**:
```sql
WITH summary AS (
  SELECT category, COUNT(*) as cnt
  FROM data
  GROUP BY category
)
SELECT * FROM summary WHERE cnt > 100;
```

**Window Functions**:
```sql
SELECT
  *,
  AVG(value) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as moving_avg,
  ROW_NUMBER() OVER (PARTITION BY category ORDER BY value DESC) as rank
FROM data;
```

## 🌐 Example Public Datasets

Try these CORS-enabled datasets:

**COVID-19 Data** (161k rows):
```
https://raw.githubusercontent.com/datasets/covid-19/main/data/countries-aggregated.csv
```

**GDP Data** (11k rows):
```
https://raw.githubusercontent.com/datasets/gdp/main/data/gdp.csv
```

**Population Data** (58k rows):
```
https://raw.githubusercontent.com/datasets/population/main/data/population.csv
```

**S&P 500 Stock Data** (14k rows):
```
https://raw.githubusercontent.com/datasets/s-and-p-500/main/data/data.csv
```

## 🛑 Stopping the Server

When you're done:

1. Go back to the terminal where the server is running
2. Press `Ctrl+C`
3. You'll see: `^C Keyboard interrupt received, exiting.`

To start again later, just run the same command:
```bash
python3 -m http.server 8765
```

## 📖 Additional Resources

- **[USER_GUIDE.md](USER_GUIDE.md)** - Detailed feature documentation
- **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - One-page cheat sheet
- **[DuckDB SQL Reference](https://duckdb.org/docs/sql/introduction)** - Complete SQL documentation
- **[Awesome Public Datasets](https://github.com/awesomedata/awesome-public-datasets)** - More data sources

## 🎉 Next Steps

1. Load your own CSV file
2. Try the example queries on your data
3. Experiment with different SQL functions
4. Share interesting queries with others!

## ⚡ Power User Tips

### Keyboard Shortcut
Press `Ctrl+Enter` (or `Cmd+Enter` on Mac) to run queries without clicking.

### Query Templates

Save these for quick reuse:

```sql
-- Template 1: Time series analysis
SELECT
  DATE_TRUNC('day', timestamp_column) as day,
  COUNT(*) as events,
  AVG(value) as avg_value
FROM data
GROUP BY day
ORDER BY day;

-- Template 2: Top N per category
SELECT * FROM (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY value DESC) as rn
  FROM data
) WHERE rn <= 10;

-- Template 3: Percentage calculations
SELECT
  category,
  COUNT(*) as count,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) as percentage
FROM data
GROUP BY category;
```

---

**Questions?** Check the [USER_GUIDE.md](USER_GUIDE.md) or [DuckDB documentation](https://duckdb.org/docs/).

**Enjoy exploring your data!** 🦆
