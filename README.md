# Customer Segmentation 🛒🛍️

Customer segmentation of an online retailer using unsupervised learning, built as a series of four notebooks: from raw transactions to customer clusters.

Customer segmentation divides customers into groups with similar purchasing behavior, so that marketing, sales and product decisions can be tailored to each group. This project segments customers of a UK-based online gift shop using the public **Online Retail** dataset.

## Project roadmap

| # | Notebook | Status |
|---|----------|--------|
| 1 | **Data cleaning** (`customer_segmentation_data_cleaning.ipynb`) | ✅ Available |
| 2 | EDA and simple RFM analysis | 🚧 Coming soon |
| 3 | Product categorization (~20 categories and ~40 subcategories) | 🚧 Coming soon |
| 4 | Customer clustering | 🚧 Coming soon |

Each notebook takes the output of the previous one: the cleaned dataset produced in notebook 1 (`data/cleaned_data.csv`) is the input of the rest. New notebooks will be added to this repository progressively.

## Notebook 1: Data cleaning

The raw data contains inconsistencies that would distort any customer-level analysis. This notebook detects them, decides whether to fix or remove each one, and prints how many rows are affected at every step.

| Step | Problem | Decision |
|------|---------|----------|
| Duplicates | Repeated rows | **Kept**: they are different units of the same product within an invoice, not recording errors |
| Missing `CustomerID` | ~25% of the rows (135,080) | **Removed**: the customer cannot be recovered from other lines of the same invoice, and segmentation needs it |
| Text | Inconsistent case and spaces | Normalized `InvoiceNo`, `StockCode`, `Country` (and `Description`, after the inspection below) |
| Non-product lines | Postage, discounts, bank charges, manual adjustments, commissions | **Removed**, detected by stock codes that do not follow the product pattern and by reviewing descriptions with lowercase letters |
| Descriptions per code | One `StockCode` with several descriptions | Unified with the latest description |
| Codes per description | One description with several `StockCode` (variants) | New `ProductCode` column with the most frequent code. `StockCode` is preserved |
| Prices | Zero or symbolic prices | **Removed** instead of imputed, to avoid anticipating the EDA |
| Quantities | Zero quantities | No records |
| Country | `unspecified` country, not recoverable | Removed. Added a `Continent` feature |
| Dates | Lines of one invoice with different timestamps | One timestamp per invoice |
| Purchases and returns | Returns without a previous purchase, or larger than what was purchased | Invoice lines aggregated; returns matched to purchases of the same customer, product and price, and then deleted or reduced so a customer never returns more than they hold |

### A note on the dataset

Some features of the data are **not described in the official UCI documentation**. For example, most product descriptions are uppercase, while several non-product records (`Discount`, `Manual`, `Bank Charges`, `Next Day Carriage`, `CRUK Commission`, `High Resolution Image`) are written in mixed case. This is a useful hint, but only a heuristic (a few real products also contain lowercase characters), so the candidates are reviewed manually in the notebook.

### Output: `data/cleaned_data.csv`

One row per invoice line.

| Column | Description |
|---|---|
| `InvoiceNo` | Invoice number. Returns have an invoice starting with "c" |
| `InvoiceDate` | Date and time of the invoice |
| `CustomerID` | Customer identifier |
| `Country`, `Continent` | Customer's country and continent |
| `StockCode` | Original product code |
| `ProductCode` | Product key: most frequent `StockCode` of the description |
| `Description` | Product description |
| `UnitPrice` | Price per unit |
| `Quantity` | Units in the line: positive for purchases, negative for returns |

## Getting started

### Prerequisites

The project was developed with Python 3.14. Install the dependencies with:

```
pip install -r requirements.txt
```

### Usage

Clone this repository:

```
git clone https://github.com/marcela-cl/customer-segmentation.git
```

Navigate to the project directory:

```
cd customer-segmentation
```

Run the notebooks in order:

```
# First: Data Cleaning
jupyter notebook customer_segmentation_data_cleaning.ipynb
```

The first time it runs, the notebook downloads the dataset from the UCI repository, converts it to CSV and stores it in `data/raw_data.csv`. The following runs load that file from disk. The `data/` folder is not tracked by git.

## Repository structure

```
customer-segmentation/
├── customer_segmentation_data_cleaning.ipynb   # Notebook 1: data cleaning
├── data/                                       # Created when running the notebook (not tracked)
│   ├── raw_data.csv
│   └── cleaned_data.csv
├── requirements.txt
└── README.md
```

## Data source and acknowledgements

Dataset: [Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail), UCI Machine Learning Repository. It contains all the transactions between 01/12/2010 and 09/12/2011 of a UK-based, registered non-store online retail company that mainly sells unique all-occasion gifts. Many customers of the company are wholesalers. Please check the UCI page for the terms of use of the dataset.

- Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33
- Dua, D. and Graff, C. (2019). [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science.
