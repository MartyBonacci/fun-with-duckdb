# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a browser-based CSV exploration tool using DuckDB WebAssembly. It's a single-page application (SPA) built with vanilla JavaScript - no build tools, no dependencies, just a standalone HTML file.

## Architecture

**Single File Application**: The entire application is contained in `index.html`:
- Lines 1-312: HTML structure with embedded CSS (custom properties for theming)
- Lines 393-644: JavaScript module with DuckDB integration
- External dependencies:
  - DuckDB WASM v1.28.0 (MVP bundle) - loaded from local `duckdb-wasm/` directory
  - Google Fonts (JetBrains Mono, Space Grotesk) from CDN
  - ES Modules with import maps for dependency resolution

**Core Components**:
- `initDuckDB()`: Initializes DuckDB WebAssembly with MVP bundle and configures httpfs extension for HTTP access
- `loadFromUrl()`: Uses DuckDB's `read_csv_auto()` directly on HTTP URLs via httpfs extension
- `loadFromFile()`: Loads CSV from local file upload using `db.registerFileText()`
- `runQuery()`: Executes SQL queries against the loaded data table (named `data`)
- `renderResults()`: Generates HTML table from query results

**Data Flow**:
1. User provides CSV (URL or file upload)
2. For URLs: DuckDB's httpfs extension streams CSV directly from HTTP source
3. For files: Content is registered with DuckDB via `db.registerFileText()`
4. DuckDB creates a table named `data` using `read_csv_auto()`
5. User writes SQL queries against the `data` table
6. Results are rendered in an HTML table with styling

**Key Technical Details**:
- DuckDB connection is stored in module-scoped `conn` variable (exposed globally via window object)
- All CSV data is loaded into a table named `data` (previous table is dropped on new load)
- httpfs extension is installed and loaded during initialization for HTTP file access
- HTTP settings configured: `enable_http_metadata_cache=true`, `http_timeout=30000ms`
- Uses DuckDB's `read_csv_auto()` for automatic CSV parsing and type detection
- Query execution is timed and displays row count + elapsed time
- CORS/network errors are caught and provide helpful fallback messages

**WASM Loading Strategy**:
- Local WASM files stored in `duckdb-wasm/` directory to avoid CORS/ORB (Opaque Response Blocking) issues
- Workers must be same-origin, so CDN URLs cannot be used for worker scripts
- Import map provides dependency resolution for apache-arrow package
- Three files required: `duckdb-browser.mjs`, `duckdb-mvp.wasm`, `duckdb-browser-mvp.worker.js`

**HTTP/CSV Loading Strategy**:
- Uses DuckDB's httpfs extension for native HTTP access to CSV files
- DuckDB can stream data efficiently and only fetch needed portions for queries
- Falls back gracefully when servers block HTTP Range requests
- CORS must be enabled on the server hosting the CSV
- For problematic URLs, users can download and use local file upload

## Development

**Local Testing**: Requires a local HTTP server due to CORS/module restrictions. Run:
```bash
python3 -m http.server 8765
# Then open http://localhost:8765/index.html
```

No build step required - it's a single HTML file with ES modules.

**Testing with Sample Data**:
- Remote CSV: Use a CORS-enabled CSV URL
- Local CSV: Upload any CSV/TSV file via the file input

**Common Queries**:
- `SELECT * FROM data LIMIT 10` - View first 10 rows (default query)
- `DESCRIBE data` - Show table schema
- `SELECT COUNT(*) FROM data` - Count total rows

**Keyboard Shortcuts**:
- Ctrl/Cmd + Enter in the query textarea executes the query

## Styling

Uses CSS custom properties (lines 14-26) for theming. The design features:
- Dark theme with cyan accent color (`--accent: #22d3ee`)
- Gradient backgrounds and hover effects
- Monospace font (JetBrains Mono) for SQL input and table data
- Sans-serif font (Space Grotesk) for UI text
