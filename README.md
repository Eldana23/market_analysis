This repository contains my implementation of the Market Basket Analysis assignment using Python and pandas. The workflow follows the brief’s requirements - loading transactions, canonicalizing schema, cleaning, pricing items with a fixed RNG seed, computing itemset statistics (pairs and triples), and producing exploratory visualizations.

---

## Repository Layout

```
.
├── main.ipynb                 # Notebook with all steps (Parts A–E)
├── csv_files/                 # Folder containing all CSV datasets
│   ├── groceries.csv          # Raw dataset (input)
│   ├── transactions_clean.csv # Cleaned transactions (output, Part B)
│   ├── product_prices.csv     # Random prices per item (output, Part C)
│   └── transactions_priced.csv# Cleaned transactions + Basket_Total (output, Part C)
└── README.md                  # Project description and instructions
```

### Dependencies

The notebook imports the following:
- `numpy`, `pandas`
- `matplotlib`, `seaborn`
- `collections.Counter`, `itertools.combinations`, `ast`

Install with:
```bash
pip install numpy pandas matplotlib seaborn plotly
```

---

## How to Run

### Google Colab
1. Open `main.ipynb` in Colab.
2. Upload `groceries.csv` if not already present.
3. Run all cells from top to bottom.

---

## Parameters 

These control preprocessing, thresholds, and pricing:

```
rng_seed = 42
top_n_items = 15
min_count_pairs = 20
min_count_triples = 5
top_k = 10
price_min, price_max = 0.50, 15.00


groc_path = 'groceries.csv'
clean_path = 'transactions_clean.csv'
prices_path = 'product_prices.csv'
transaction_path = 'transactions_priced.csv'
```

- **Determinism:** Prices are drawn with `np.random.default_rng(rng_seed)` to ensure reproducibility.
- **File paths:** The notebook expects the following files in the working directory:

  - Input: `groceries.csv`
  - Outputs: `transactions_clean.csv`, `product_prices.csv`, `transactions_priced.csv`

---

## Data Processing Pipeline 

### Part A — Load & EDA
- Load `groceries.csv`.
- Canonicalize schema:
  - `TransactionID` (unique integer index per basket)
  - `Items` (list of strings per basket)
- Data dictionary:
  - `TransactionID` — `int` — unique identifier for the transaction
  - `Items` — `list[str]` — list of item names in the basket
- Basic EDA:
  - Number of transactions and unique products
  - Basket size distribution (min/median/95th percentile)
  - Top 20 products by frequency

### Part B — Standardize & Clean
- Standardize item names: lowercase, trimmed, spaces replaced by underscores.
- Remove empty/invalid items.
- Drop baskets with fewer than 2 items.
- Add `Basket_Size` and reindex `TransactionID`.
- Save cleaned table to `transactions_clean.csv`.

### Part C — Pricing
- Build product-level price map with a fixed RNG seed in the range `[0.50, 15.00]`.
- Compute `Basket_Total` as the sum of item prices in each basket (quantity = 1).
- Save `product_prices.csv` and `transactions_priced.csv`.

### Part D — Itemset Counting
- Count **pairs** and **triples** per basket using `Counter` over `itertools.combinations` of **unique** items in each basket.
- Apply configurable minimum support counts:
  - `min_count_pairs = 20`
  - `min_count_triples = 5` (I chose 5 because the max count for triples is 8.)
- Sort itemsets by support count (desc) and deterministically break ties (alphabetical).
- Report support **count** and **fraction**.

### Part E — Visualizations
- Bar chart of top 15 items by frequency.
- Bar chart of top-`k` pairs by support.
- Bar chart of top-`k` triples by support.
- Heatmap of co-occurrences for the 25 most frequent items.
- Histograms of basket size and basket total.
---

## Data Dictionary

For `transactions_priced.csv`:

| Column          | Type        | Description                                                |
|-----------------|-------------|------------------------------------------------------------|
| TransactionID   | int         | Unique transaction identifier                              |
| Items           | list[str]   | Cleaned list of items in the basket                        |
| Basket_Size     | int         | Number of items in the basket (after cleaning)             |
| Basket_Total    | float       | Sum of item prices for the basket (quantity = 1 per item) |

For `product_prices.csv`:

| Column | Type  | Description                       |
|--------|-------|-----------------------------------|
| item   | str   | Item name (standardized)          |
| price  | float | Random unit price (seeded range)  |

---


## Declaration of Original Work

I hereby declare that the work presented in the submitted report and the accompanying
code is entirely my own. No portion of this submission has been copied,
reproduced, or directly generated/refined using the responses or outputs of any
AI tools (including, but not limited to, ChatGPT, Copilot, Gemini, DeepSeek,
or other automated systems). Any external sources, datasets, or tools that have
been used are properly cited and referenced. I understand that any breach of this
declaration may result in submission cancellation or significant mark deduction.
