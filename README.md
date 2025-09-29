# Data Cleaning with AI Support

## Student Information
- Name: Ian Gabriel D. Paulmino
- Course Year: BSCS 4
- Date: 2025-09-29

## Dataset
- Source: https://www.kaggle.com/datasets/ahmedmohamed2003/retail-store-sales-dirty-for-data-cleaning
- Name: Retail Store Sales: Dirty for Data Cleaning

| Column Name       | Description                                                                 | Example Values            |
|-------------------|-----------------------------------------------------------------------------|---------------------------|
| Transaction ID    | A unique identifier for each transaction. Always present and unique.        | TXN_1234567               |
| Customer ID       | A unique identifier for each customer. 25 unique customers.                 | CUST_01                   |
| Category          | The category of the purchased item.                                         | Food, Furniture           |
| Item              | The name of the purchased item. May contain missing values or None.         | Item_1_FOOD, None         |
| Price Per Unit    | The static price of a single unit of the item. May contain missing or None. | 4.00, None                |
| Quantity          | The quantity of the item purchased. May contain missing or None.            | 1, None                   |
| Total Spent       | Total spent, calculated as Quantity * Price Per Unit.                       | 8.00, None                |
| Payment Method    | The method of payment used. May contain missing or invalid values.          | Cash, Credit Card         |
| Location          | The location where the transaction occurred. May contain missing/invalid.   | In-store, Online          |
| Transaction Date  | The date of the transaction. Always present and valid.                      | 2023-01-15                |
| Discount Applied  | Indicates if a discount was applied. May contain missing values.            | True, False, None         |




## Issues Found

### Missing Values
- **Item** had several missing entries.  
- **Price Per Unit**, **Quantity**, and **Total Spent** sometimes missing or inconsistent.  
- **Discount Applied** had missing values.  

### Duplicates
- Potential for duplicate rows, but since **Transaction ID** must be unique, duplicates were checked and removed.  

### Inconsistencies
- **Payment Method** not standardized (e.g., `credit card` vs `Credit Card`, spacing/casing issues).  
- **Location** inconsistencies in casing (`In-store` vs `in-store`).  
- **Discount Applied** had mixed formats: booleans (`True`/`False`), strings, and nulls.  
- Numeric mismatch: **Total Spent** not always equal to **Price Per Unit × Quantity**.  

---

## Cleaning Steps

### Missing Values
- Filled missing **Item** with `"Unknown"`.  
- Recalculated missing numeric values (**Total Spent**, **Price Per Unit**, **Quantity**) using the formula:  
  \[
  \text{Total Spent} = \text{Price Per Unit} \times \text{Quantity}
  \]  
- For still-missing numeric values, filled with the **median** of the column.  
- Filled missing **Discount Applied** with `"Unknown"`.  

### Duplicates
- Checked duplicates using `.duplicated()`.  
- Ensured uniqueness of **Transaction ID** by dropping duplicates.  

### Inconsistencies
- Converted **Transaction Date** to `datetime` type.  
- Standardized **Payment Method** and **Location** casing (e.g., `In-store`, `Credit Card`).  
- Normalized **Discount Applied** → Boolean (`True`/`False`) with `"Unknown"` for missing.  

### Outliers
- Cross-checked **Total Spent** against `Price Per Unit × Quantity` and corrected mismatches.  
- Used **IQR method** to detect numeric outliers in **Price Per Unit**, **Quantity**, and **Total Spent**.  
- Capped extreme outliers to within acceptable IQR bounds.  


## AI prompts used
- Prompt 1: "Generate me the code to a matplotlib for my pandas dataframe. This dataframe contains missing elements, detect these missing elements and graph the their number according to each column of the dataframe"
- Generated code:
# --- PLOT 1: Missing values bar chart (before cleaning) ---
missing_counts = df_before.isnull().sum()

plt.figure(figsize=(8,5))
missing_counts.plot(kind="bar", color="dodgerblue", edgecolor="black")
plt.title("Missing Values per Column (Before Cleaning)")
plt.ylabel("Count of Missing Values")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
plt.show()

- Prompt 2: "Given this dataset, From how I understand it, Cleaning methods can be done be done by enforcing a (Total = Price × Quantity) rule to fill out missing data on each respective fields. I also noticed that there are very large outliers like people buying furniture and just a 10 dollar food, from previous research I say I can use median instead of mean of this. For Duplicates, most systems follow transaction Ids as the unique key therefore they should be the basis of uniqueness. I would also want to enforce data strict formatting for Fields such as Cash and etc. For missing values of items, since the catalog of price lists is not complete guessing would be hard, I can just put unknown instead. Same goes with Discount since it should be difficult unless I have an initial idea of the price catalogue.
Can you point out and validate my understanding of this data so I could build upon a general idea better"? 

## Results
- Rows before: (12575, 11)
	Transaction ID	Customer ID	Category	Item	Price Per Unit	Quantity	Total Spent	Payment Method	Location	Transaction Date	Discount Applied
0	TXN_6867343	CUST_09	Patisserie	Item_10_PAT	18.5	10.0	185.0	Digital Wallet	Online	2024-04-08	True
1	TXN_3731986	CUST_22	Milk Products	Item_17_MILK	29.0	9.0	261.0	Digital Wallet	Online	2023-07-23	True
2	TXN_9303719	CUST_02	Butchers	Item_12_BUT	21.5	2.0	43.0	Credit Card	Online	2022-10-05	False
3	TXN_9458126	CUST_06	Beverages	Item_16_BEV	27.5	9.0	247.5	Credit Card	Online	2022-05-07	NaN
4	TXN_4575373	CUST_05	Food	Item_6_FOOD	12.5	7.0	87.5	Digital Wallet	Online	2022-10-02	False

- Rows after: (12575, 11)
Transaction ID	Customer ID	Category	Item	Price Per Unit	Quantity	Total Spent	Payment Method	Location	Transaction Date	Discount Applied
0	TXN_6867343	CUST_09	Patisserie	Item_10_PAT	18.5	10.0	185.0	Digital Wallet	Online	2024-04-08	True
1	TXN_3731986	CUST_22	Milk Products	Item_17_MILK	29.0	9.0	261.0	Digital Wallet	Online	2023-07-23	True
2	TXN_9303719	CUST_02	Butchers	Item_12_BUT	21.5	2.0	43.0	Credit Card	Online	2022-10-05	False
3	TXN_9458126	CUST_06	Beverages	Item_16_BEV	27.5	9.0	247.5	Credit Card	Online	2022-05-07	Unknown
4	TXN_4575373	CUST_05	Food	Item_6_FOOD	12.5	7.0	87.5	Digital Wallet	Online	2022-10-02	False


Video: link