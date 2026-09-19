# LSEG Datastream

LSEG Datastream is a platform that provides access to a wide range of financial
data, including futures data. Datastream is available via WRDS. WRDS does not
provide real-time data access to Datastream, but it is still a valuable source
of data for research. The Datastream data in WRDS is updated monthly. The data
is the same as the data in LSEG Workspace (e.g., via Eikon).

## Getting Started with Documentation

Before searching for specific data, it's recommended to familiarize yourself with
Datastream's documentation:

1. Visit the [WRDS Datastream Documentation Page](https://wrds-www.wharton.upenn.edu/pages/support/manuals-and-overviews/thomson-reuters/datastream/)
2. Log in with your WRDS credentials
3. Review the available manuals and overviews to understand:
   - Data coverage and limitations
   - Search functionality
   - Available data types and formats
   - Mnemonic conventions

This documentation will help you navigate the platform more effectively and
understand the data structure before proceeding with specific queries.

## Finding Variables (in Datastream and in WRDS generally)

WRDS provides a powerful variable search tool that allows you to search across all WRDS datasets, including Datastream. This can be particularly helpful when:
- You're unsure which dataset contains your variable of interest
- You want to compare similar variables across different datasets
- You need to find specific mnemonics or variable names

### Using the WRDS Variable Search

1. Navigate to the [WRDS Variable Search Page](https://wrds-www.wharton.upenn.edu/search/?queryTerms=&activeTab=navVariablesSearchTab)
2. Log in with your WRDS credentials
3. Use the search functionality in several ways:
   - Search by keyword (e.g., "treasury futures")
   - Filter by database (e.g., "Datastream")
   - Browse by category
   - Use advanced search options for more specific queries

### Using the Datastream Code Lookup Tool

![Datastream Code Lookup Tool](./../assets/datastream_code_lookup.png)
1. Navigate to the [Datastream Future Contracts Query Form](https://wrds-www.wharton.upenn.edu/pages/get-data/lseg/datastream/futures-data/future-contracts/)
2. Click on "Code Lookup: Datastream Futures"
3. Use the search functionality to find the code for the variable you are interested in

### Search Tips
- Use quotation marks for exact phrase matches
- Try different variations of terms (e.g., "Treasury", "T-Note", "Government Bond")
- Check variable definitions carefully as similar variables may exist across different datasets
- Note the update frequency and coverage period for each variable

The variable search tool is particularly valuable when working with Datastream, as it can help you identify the correct mnemonics and understand what data is available before constructing your queries.

## Treasury Futures Example 1

Search https://wrds-www.wharton.upenn.edu/dynamic-query-form/tr_ds_fut/wrds_cseries_info/

## Treasury Futures Example 2

Let's consider first an example of how to search for Treasury Futures data in
Datastream via WRDS.

To effectively search for Treasury Futures data in Datastream via WRDS, follow
these steps based on the available tools and mnemonic conventions:


### 1. Access Futures Data via WRDS Web Query
WRDS provides a structured interface for retrieving futures data:
- Navigate to the **Thomson/Refinitiv** section in WRDS and select
  **Datastream**.


### 2. Mnemonic Structure for Treasury Futures

Datastream uses standardized codes for futures contracts:
- **Continuous series (front contract):**  
  Format: `[Symbol]CS00`  
  Example: `TYCS00` for 10-Year Treasury Note futures (where `TY` represents the
  underlying asset).
  - `CS` indicates "continuous series" (auto-rolls to the next contract at
    expiration).
  - `00` specifies the rollover timing (1st day of the new month).

- **Individual contracts (live or expired):**  
  Format: `LFUT[Symbol][L/D]`  
  Example: `LFUTTYL` for live 10-Year Treasury contracts or `LFUTTYD` for dead
  (expired) ones.
  - `LFUT` = List Futures  
  - `L` = Live, `D` = Dead  


### 3. Key Treasury Futures Symbols
Common mnemonics for U.S. Treasury futures include:

| **Contract**               | **Base Symbol** | **Example Continuous Series** | **Example Individual Contracts** |
|-----------------------------|-----------------|-------------------------------|-----------------------------------|
| 2-Year Treasury Note        | `TU`            | `TUCS00`                      | `LFUTTUL`, `LFUTTUD`             |
| 5-Year Treasury Note         | `FV`            | `FVCS00`                      | `LFUTFVL`, `LFUTFVD`             |
| 10-Year Treasury Note        | `TY`            | `TYCS00`                      | `LFUTTYL`, `LFUTTYD`             |
| 30-Year Treasury Bond        | `US`            | `USCS00`                      | `LFUTUSL`, `LFUTUSD`             |


### 4. Search and Browse Tips
- **Use WRDS Economic Indicators Filter:** Narrow results by selecting "Futures"
  under asset classes and filtering by keywords like "Treasury" or
  "Bond".
- **Consult Datastream Documentation:** Access mnemonic lists and data
  dictionaries via WRDS's *Thomson/Refinitiv* resources.
- **Verify Coverage:** WRDS includes futures data but excludes fixed-income
  modules like bonds. For granular bond futures, consider cross-referencing with
  CRSP's Treasury data.


### 5. Data Retrieval Example
To download 10-Year Treasury Note futures:
1. **Continuous series:** Use `TYCS00` for auto-rolling front-month data.
2. **Individual contracts:** Use `LFUTTYL` to retrieve all live contracts or
   `LFUTTYD` for expired ones.
3. **Export Options:** Output data in `.csv`, `.xlsx`, or SAS formats via WRDS
   web queries.


## Exploring the Futures Library via Python

The WRDS web interface is useful for quick lookups, but programmatic access via
Python is essential for reproducible research workflows. Connecting to WRDS from
Python lets you run SQL queries against the Datastream library, iterate over
products, and build datasets that feed directly into your analysis pipeline.

**Example repository:**
[`wrds_datastream/`](https://github.com/finm-32800/inclass_examples/tree/main/wrds_datastream)
— a set of 7 progressive scripts that teach you how to browse, search, and
retrieve futures data from the `tr_ds_fut` library.

| Script | What You Learn |
|--------|---------------|
| `01_explore_library.py` | Survey tables, schemas, row counts, and sample rows |
| `02_browse_products.py` | List all available futures products with contract counts and date ranges |
| `03_search_products.py` | Search by keyword (commodities, treasuries, equity indices) |
| `04_contract_metadata.py` | Inspect individual contracts: `contrcode` → `futcode` relationship |
| `05_pull_prices.py` | Fetch settlement prices (complete retrieval workflow) |
| `06_term_structure.py` | Compare term structures: commodity vs. Treasury futures |
| `07_pull_commodities_and_treasuries.py` | Reference: pull 21 commodities + 4 Treasury futures |

The Datastream mnemonics described above (e.g., `TYCS00` for the 10-Year
Treasury continuous series) are useful for identifying products in the WRDS web
interface, but when you query the underlying SQL tables you work with relational
keys instead — primarily `contrcode` (product type) and `futcode` (individual
contract).


## Data Model: Library Structure and Key Relationships

The `tr_ds_fut` library contains four key tables:

| Table | Description | Scale |
|-------|------------|-------|
| `wrds_cseries_info` | Continuous series metadata (auto-rolling front contracts) | Small catalog (~hundreds of rows) |
| `wrds_contract_info` | Individual contract metadata: `contrcode`, `futcode`, delivery dates | ~100k rows |
| `wrds_fut_contract` | Daily prices by `futcode`: settlement, open, high, low, volume | ~30M+ rows |
| `dsfutcalcserval` | Calculated series values (continuous contracts) | ~10M+ rows |

The tables are linked through a hierarchical relationship:

```
contrcode (product type, e.g. Gold = 2020, 10Y Treasury = 458)
    └── wrds_contract_info (one row per contract, has futcode + delivery date)
            └── wrds_fut_contract (daily prices per futcode)
```

**Two-step retrieval pattern.** Because metadata and prices live in separate
tables, retrieving prices is always a two-step process:

1. **Find contracts:** Query `wrds_contract_info` to get the `futcode` values
   for the product and date range you need.
2. **Fetch prices:** Use those `futcode` values to pull daily data from
   `wrds_fut_contract`.

This separation keeps the price table lean (no repeated metadata) and lets you
filter contracts by delivery date, last trade date, or other attributes before
pulling potentially millions of price rows.


## Highlights from the Example Scripts

The sections below show key code patterns from the example repository. Each
snippet is self-contained — you can adapt it directly for your own projects.

### Searching for Products

The `search_products()` function from `03_search_products.py` queries
`wrds_contract_info` for products whose name matches a keyword:

```python
def search_products(db, keyword):
    """Search for futures products by keyword (case-insensitive)."""
    kw = keyword.lower().replace("'", "''")
    query = f"""
    SELECT contrcode, contrname,
           COUNT(*) AS num_contracts,
           MIN(startdate) AS earliest_start,
           MAX(lasttrddate) AS latest_trade
    FROM tr_ds_fut.wrds_contract_info
    WHERE LOWER(contrname) LIKE '%%{kw}%%'
    GROUP BY contrcode, contrname
    ORDER BY num_contracts DESC
    """
    return db.raw_sql(query)
```

A search for `"treasury"` returns results like:

```
Product                                                   Code  Contracts  From          To
10 YEAR US TREASURY NOTE(CBT)                              458       289  1982-03-31  2026-09-19
US TREASURY BOND(CBT)                                      268       343  1977-06-24  2026-09-19
5 YEAR US TREASURY NOTE(CBT)                               510       198  1988-01-04  2026-09-19
2 YEAR US TREASURY NOTE(CBT)                               599       146  1990-07-02  2026-06-30
...
```

The `contrcode` values in this output (458, 268, 510, 599) are the keys you use
in all subsequent queries.


### Two-Step Price Retrieval

From `05_pull_prices.py` — the complete workflow for fetching settlement prices:

```python
# Step 1: Find recent contracts for 10Y Treasury (contrcode=458)
query = f"""
SELECT futcode, contrcode, contrname, contrdate, startdate, lasttrddate
FROM tr_ds_fut.wrds_contract_info
WHERE contrcode = 458
  AND lasttrddate >= '2024-01-01'
ORDER BY contrdate
"""
contracts = db.raw_sql(query)

# Step 2: Pull daily prices for those contracts
futcode_list = ",".join(str(int(f)) for f in contracts["futcode"].tolist())
query = f"""
SELECT futcode, date_, settlement, open_, high, low, volume
FROM tr_ds_fut.wrds_fut_contract
WHERE futcode IN ({futcode_list})
  AND date_ >= '2024-01-01'
ORDER BY futcode, date_
"""
prices = db.raw_sql(query)
```

Step 1 returns the contract-level metadata (which delivery months exist, when
they last traded). Step 2 uses the resulting `futcode` values to pull the actual
daily price series.


### Term Structure Snapshot

From `06_term_structure.py` — a single JOIN query that builds a cross-section of
all active delivery months on a given date:

```python
query = f"""
SELECT ci.futcode, ci.contrdate, ci.lasttrddate,
       fc.settlement, fc.volume
FROM tr_ds_fut.wrds_contract_info ci
JOIN tr_ds_fut.wrds_fut_contract fc
  ON ci.futcode = fc.futcode
WHERE ci.contrcode = 458
  AND fc.date_ = '2025-01-31'
  AND fc.settlement IS NOT NULL
ORDER BY ci.contrdate
"""
term = db.raw_sql(query)
```

This returns one row per delivery month — the building block for analyzing term
structure shape (contango vs. backwardation for commodities, or carry dynamics
for Treasuries).

```{tip}
Script `07_pull_commodities_and_treasuries.py` combines all of these patterns
into a single reference script that pulls 21 commodities and 4 Treasury futures.
Use it as a starting point for building your own research datasets.
```


## Key Limitations
- WRDS updates Datastream data **monthly**, while LSEG Workspace offers
  real-time updates.
- For intraday data or non-U.S. Treasury futures, use LSEG Workspace (access
  varies by institution).

By applying these conventions and tools, you can efficiently locate and extract
Treasury Futures data in Datastream via WRDS.
