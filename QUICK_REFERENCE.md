# Quick Reference Card

## Starting the Application

```bash
# Navigate to project
cd /home/marty/code-projects/fun-with-duckdb

# Start server
python3 -m http.server 8765

# Open browser to:
# http://localhost:8765/index.html
```

## Loading Data

| Method | When to Use | Steps |
|--------|-------------|-------|
| **URL** | Public datasets with CORS | Paste URL → Click "Load URL" |
| **Local File** | Any CSV file, CORS-blocked URLs | Click "Choose Local File" → Select file |

## Essential Queries

```sql
-- See structure
DESCRIBE data

-- Count rows
SELECT COUNT(*) FROM data

-- Preview data
SELECT * FROM data LIMIT 10

-- Filter
SELECT * FROM data WHERE column = 'value'

-- Aggregate
SELECT category, COUNT(*) as count
FROM data
GROUP BY category
ORDER BY count DESC

-- Date filtering
SELECT * FROM data
WHERE date_column >= '2024-01-01'
```

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Enter` or `Cmd+Enter` | Run query |

## Common Issues & Solutions

| Problem | Solution |
|---------|----------|
| CORS error | Download file → use "Choose Local File" |
| "duckdb is not defined" | Refresh browser, clear cache |
| No results | Check column names with `DESCRIBE data` |
| Slow browser | Use `LIMIT` to restrict results |

## SQL Cheat Sheet

### Aggregations
```sql
COUNT(*) | SUM(col) | AVG(col) | MIN(col) | MAX(col)
```

### Filtering
```sql
WHERE col = 'value'
WHERE col IN ('a', 'b', 'c')
WHERE col LIKE '%pattern%'
WHERE col > 100
WHERE col BETWEEN 10 AND 20
```

### Grouping
```sql
GROUP BY col1, col2
HAVING COUNT(*) > 10
```

### Sorting
```sql
ORDER BY col ASC    -- ascending
ORDER BY col DESC   -- descending
```

### Joins
```sql
-- Self-join example
SELECT a.*, b.*
FROM data a
JOIN data b ON a.id = b.parent_id
```

### Window Functions
```sql
SELECT
  col,
  ROW_NUMBER() OVER (ORDER BY col) as row_num,
  LAG(col) OVER (ORDER BY date) as previous_value
FROM data
```

## Data Types

DuckDB auto-detects types:
- `INTEGER` - Whole numbers
- `DOUBLE` - Decimal numbers
- `VARCHAR` - Text
- `DATE` - Dates (YYYY-MM-DD)
- `TIMESTAMP` - Date and time

## Column Names with Spaces

```sql
-- Use quotes for column names with spaces
SELECT "Provider Last Name", "ZIP Code"
FROM data
```

## Performance Tips

1. Always use `LIMIT` when exploring
2. Filter early with `WHERE` before aggregating
3. DuckDB handles millions of rows easily
4. Browser struggles displaying 100k+ rows

## Example Workflows

### Explore New Dataset
```sql
-- 1. Check structure
DESCRIBE data;

-- 2. Count rows
SELECT COUNT(*) FROM data;

-- 3. Preview
SELECT * FROM data LIMIT 5;

-- 4. Check for nulls
SELECT COUNT(*) as nulls
FROM data
WHERE column_name IS NULL;
```

### Find Top Values
```sql
SELECT category, COUNT(*) as freq
FROM data
GROUP BY category
ORDER BY freq DESC
LIMIT 10;
```

### Calculate Percentages
```sql
SELECT
  category,
  COUNT(*) as count,
  ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) as percentage
FROM data
GROUP BY category
ORDER BY count DESC;
```

## Getting Help

- Full guide: `USER_GUIDE.md`
- DuckDB SQL docs: https://duckdb.org/docs/
- Example datasets: https://github.com/datasets
