# MyInves n8n Workflow

This repository contains the n8n workflow for **[MyInves](https://github.com/AgostaGiorgio/MyInves)**, a platform designed to track and manage personal net worth. The workflow automates the collection of asset prices and exchange rates, ensuring the system always has up-to-date valuations while maintaining a historical snapshot of the portfolio's performance.

## Overview

The workflow performs two primary functions:
1.  **Portfolio Snapshotting:** Before updating with new data, it calculates and saves a "snapshot" of the current asset values and total portfolio value into history tables.
2.  **Automated Data Fetching:** It retrieves the latest prices for specific assets and exchange rates from external APIs and stores them in the database.

## Workflow Logic

### 1. Snapshot Phase (History Preservation)
To ensure a clean and readable history, the workflow first processes the existing "latest" data:
- **Calculate Snapshot:** Runs a complex SQL query to determine the current value in EUR of all assets by combining the latest quantities (`asset_readings`), the latest prices (`asset_prices`), and the latest exchange rates (`exchange_rates`).
- **Save Asset History:** Records the individual value of each asset in the `assets_history` table.
- **Save Portfolio History:** Aggregates all asset values to save a single total portfolio value entry in the `portfolio_history` table.

### 2. Update Phase (Data Fetching)
Once the snapshot is secured, the workflow fetches fresh data:
- **Asset Prices:** Fetches the real-time price for **IWDA** (via EODHD API) and **Gold** (via MetalPriceAPI).
- **Exchange Rates:** Fetches current rates for **AED** (United Arab Emirates Dirham) and **USD** (US Dollar) against the **EUR**.
- **Database Update:** Maps the fetched prices back to their respective internal IDs and inserts new records into the `asset_prices` and `exchange_rates` tables.

## Tracked Assets & Currencies
- **IWDA.AS:** iShares Core MSCI World UCITS ETF.
- **XAU (Gold):** Calculated per gram from market rates.
- **USD:** US Dollar to Euro exchange rate.
- **AED:** UAE Dirham to Euro exchange rate.

## Database Schema Requirements
The workflow interacts with a PostgreSQL database (`MYINVES_Postgres`) and expects the following tables:
- `assets`: Stores asset metadata (ID, Name, Currency).
- `asset_prices`: Stores historical price points for assets.
- `exchange_rates`: Stores historical exchange rates to EUR.
- `asset_readings`: Stores the quantity of assets held at specific times.
- `assets_history`: Stores the calculated value of assets at snapshot time.
- `portfolio_history`: Stores the total value of the entire portfolio over time.

## Configuration
- **Triggers:** The workflow is configured to run automatically via Schedule Triggers.
- **Credentials:** Requires configured credentials for:
    - **PostgreSQL:** Access to the `MYINVES` database.
    - **HTTP Request (EODHD):** API token for fetching stock/ETF data.
    - **HTTP Request (MetalPriceAPI):** API key for fetching precious metal and currency rates.

---
*Part of the MyInves Net Worth Tracker project.*
