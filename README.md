# DuckDB CSV Explorer 🦆

A powerful, browser-based CSV exploration tool powered by DuckDB WebAssembly. Query massive CSV files with SQL without downloading them entirely - all processing happens in your browser!

## Features

- 🚀 **Instant CSV loading** - Stream remote files without full downloads
- 💪 **Full SQL support** - Leverage DuckDB's complete SQL capabilities
- 🔒 **100% client-side** - Your data never leaves your browser
- ⚡ **Blazing fast** - Query millions of rows in milliseconds
- 🎨 **Beautiful UI** - Modern dark theme with syntax highlighting
- 📁 **Flexible input** - Load from URLs or local files

## Quick Start

1. **Start a local server**:
   ```bash
   python3 -m http.server 8765
   ```

2. **Open in browser**:
   ```
   http://localhost:8765/index.html
   ```

3. **Load data**:
   - **Remote**: Paste a CSV URL and click "Load URL"
   - **Local**: Click "Choose Local File" and select your CSV

4. **Query away**:
   ```sql
   SELECT * FROM data LIMIT 10
   ```

## Example URLs

Try these public datasets:

```
https://raw.githubusercontent.com/datasets/covid-19/main/data/countries-aggregated.csv
https://raw.githubusercontent.com/datasets/gdp/main/data/gdp.csv
https://raw.githubusercontent.com/datasets/population/main/data/population.csv
```

## Sample Queries

**Basic exploration**:
```sql
-- View table structure
DESCRIBE data

-- Count rows
SELECT COUNT(*) FROM data

-- Filter and sort
SELECT * FROM data
WHERE column_name > 100
ORDER BY column_name DESC
LIMIT 20
```

**Aggregations**:
```sql
-- Group and sum
SELECT category, SUM(amount) as total
FROM data
GROUP BY category
ORDER BY total DESC
```

**Advanced analytics**:
```sql
-- Window functions
SELECT
  date,
  value,
  AVG(value) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as moving_avg
FROM data
ORDER BY date
```

## Why DuckDB?

- **No installation required** - Runs entirely in your browser via WebAssembly
- **Handles huge files** - Process gigabytes of data with minimal memory usage
- **Smart data loading** - Only fetches the portions of remote files you actually query
- **Full SQL** - JOIN, GROUP BY, window functions, CTEs, and more
- **Automatic type detection** - Intelligently parses your CSV columns

## CORS Limitations

Some servers block cross-origin requests. If a URL doesn't work:

1. Download the CSV file locally
2. Use the "Choose Local File" option

Common CORS-enabled data sources:
- GitHub (raw.githubusercontent.com)
- Data.gov datasets
- Most public data repositories

## Documentation

- **[USER_GUIDE.md](USER_GUIDE.md)** - Complete usage instructions
- **[CLAUDE.md](CLAUDE.md)** - Technical architecture for AI assistants
- **[DuckDB SQL Docs](https://duckdb.org/docs/sql/introduction)** - Full SQL reference

## Technical Stack

- **DuckDB WASM** v1.28.0 (MVP bundle)
- **Pure JavaScript** - No framework dependencies
- **ES Modules** with import maps
- **Google Fonts** - JetBrains Mono, Space Grotesk

## Browser Support

Works on all modern browsers with WebAssembly support:
- Chrome/Edge 91+
- Firefox 89+
- Safari 15+

## Project Structure

```
.
├── index.html           # Single-page application
├── duckdb-wasm/        # DuckDB WebAssembly files
│   ├── duckdb-browser.mjs
│   ├── duckdb-mvp.wasm
│   └── duckdb-browser-mvp.worker.js
├── README.md           # This file
├── USER_GUIDE.md       # Detailed usage guide
└── CLAUDE.md          # Architecture documentation
```

## Contributing

This is a personal learning project. Feel free to fork and experiment!

## License

MIT

## Acknowledgments

- [DuckDB](https://duckdb.org/) - Amazing in-process SQL database
- [DuckDB WASM](https://www.npmjs.com/package/@duckdb/duckdb-wasm) - WebAssembly port
