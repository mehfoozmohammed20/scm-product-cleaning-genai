#SCM Product List Cleaning using GenAI (Python + OpenAI API)

Automates cleaning of messy Supply Chain product master data. Each raw product name is sent to GPT-4o and returned as three structured fields: `main_product`, `product_type`, `SKU`.

---

## Problem

The product master list (1,289 rows) had:

- Inconsistent naming and mixed casing
- SKU info buried in the name (`50g`, `Rs.10`, `6PCS`)
- Spelling errors (`Mashroom`, `Agarwatti`, `Chily`)
- Random spacing
- Brand and variant merged together

Cleaning this by hand is slow, error prone and does not scale.

---

## 🛠 Tech Stack

Python · OpenAI API (GPT-4o) · Pandas · tqdm · Google Colab / Jupyter

---

## How It Works

**Prompt engineering**
1. Find the SKU first (weight, price, count, usually at the end)
2. Take `main_product` from the start of the string (brand or main identity)
3. Whatever is left in the middle is `product_type`
4. Fix spelling and normalise casing
5. Return empty strings for anything not found
6. Few-shot examples in the system prompt cover edge cases (single-word products, no SKU, no type)

**Pipeline**
- Reads raw CSV with Pandas
- Skips empty or invalid names before calling the API
- One product per call, `temperature=0`, `response_format={"type": "json_object"}` for valid, repeatable JSON
- Saves results to CSV every 50 rows
- **Resume:** if the output file exists, continues from the last saved row
- **Error handling:** each call is retried up to 3 times (waiting longer after each failure); if all fail, the row is flagged `API_ERROR` so it can be found and re-run

### Example

| Input | main_product | product_type | SKU |
|---|---|---|---|
| `Oyester Mashroom` | Oyster Mushroom | | |
| `Parle-G Gold Biscuits -10 RS` | Parle-G | Gold Biscuits | 10 RS |
| `Banana Chips 50g` | Banana Chips | Chips | 50g |
| `Dyna -125 gm.` | Dyna | | 125 gm |

---

## 📊 Results

> Fill in after the full run finishes:
> - Rows processed: **1,289**
> - Rows flagged `API_ERROR`: **N**
> - Manual check on a random sample of 100 rows: **N% correct**
> - Time and API cost for the full run: **N min / $N**

---

## How to Run

1. Install dependencies
   ```bash
   pip install pandas openai tqdm
   ```
2. Set your API key as an environment variable (the notebook reads it, nothing is hardcoded)
   ```bash
   export OPENAI_API_KEY="sk-..."      # Windows: set OPENAI_API_KEY=sk-...
   ```
3. Put your CSV in the project folder and update the config at the top of the notebook:
   ```python
   INPUT_FILE = "data/sample_products.csv"
   OUTPUT_FILE = "ProductList_master_cleaned.csv"
   COLUMN_TO_CLEAN = "ProductName"
   ```
4. Run the notebook. If it stops, run it again; it resumes automatically.

---

##  Repository Structure

```
├── SCM_Product_List_Cleaning_using_GenAI.ipynb
├── data/
│   └── sample_products.csv     (small sample, not the full client list)
└── README.md
```

---

## Possible Improvements

- Send products in batches to cut cost and time
- Validate output against a known brand list
- Add a confidence check for ambiguous names

---

## Keywords

Python, OpenAI API, GPT-4o, Prompt Engineering, Few-shot Prompting, JSON Parsing, Data Cleaning, ETL, Supply Chain Analytics, Pandas, Automation
