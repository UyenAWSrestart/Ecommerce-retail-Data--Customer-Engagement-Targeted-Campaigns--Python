# [Python] RFM Analysis

Author: [Uyen Nguyen]  
Date: February 2025  
Tools Used: Python

---

## 📑 Table of Contents  
I. [Introduction](#i-introduction)  
II. [Dataset Description](#ii-dataset-description)  
III.[Data Preparation](#iii-data-preparation)  
IV.[Visualization](#iv-visualization)  
V. [Final Conclusion & Recommendations](#v-final-conclusion--recommendations)


## I. Introduction
This project performs an RFM (Recency, Frequency, Monetary) analysis for the global retail company SuperStore using Python. 

### The objective:
- Understand user profile and enhance customers' engagement.
- Identify key customer groups for targeted campaigns.
  
### Stakeholders: 
The insights gained will empower the following stakeholders to make informed strategic decisions :
- Data analysts & business analysts
- Marketing and Sales teams


## II. Dataset Description
The attached dataset contains historical customer transactions from December 1, 2010, to September 9, 2011, for an e-commerce retail company.
- Size: 541909 rows, 8 columns
- Format: .xlsx
- Table schema:

| Field       | Description                                                                                                                                                 |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| InvoiceNo   | Invoice number. Nominal, a 6-digit integral number uniquely assigned to each transaction. If this code starts with letter 'C', it indicates a cancellation. |
| StockCode   | Product (item) code. Nominal, a 5-digit integral number uniquely assigned to each distinct product.                                                         |
| Description | Product (item) name. Nominal.                                                                                                                               |
| Quantity    | The quantities of each product (item) per transaction. Numeric.                                                                                             |
| InvoiceDate | Invoice Date and time. Numeric, the day and time when each transaction was generated.                                                                       |
| UnitPrice   | Unit price. Numeric, Product price per unit in sterling.                                                                                                    |
| CustomerID  | Customer number. Nominal, a 5-digit integral number uniquely assigned to each customer.                                                                     |
| Country     | Country name. Nominal, the name of the country where each customer resides.   


## III. Data Preparation

#### 1. Cleaning Data
This part involved checking for missing values, duplicates, and incorrect data types. Appropriate actions were taken, such as imputing missing values, removing duplicates, and correcting data types to ensure data integrity. Additionally, any incorrect or outlier values were identified and handled based on the dataset's context to maintain accuracy and consistency.

```python
#Check for datatype
transactions.dtypes

#Check for missing data in each column
transactions.isna().sum()

#Check for duplicates
print(transactions.duplicated().su

#Check for incorrect values
transactions.describe()
```

**Actions**
```python
#Remove rows had null CustomerID
transactions = transactions.dropna(subset=['CustomerID'])

#Convert CustomerID to datatype 'object' and remove '.0'
transactions['InvoiceDate'] = pd.to_datetime(transactions['InvoiceDate'])
transactions['CustomerID'] = transactions['CustomerID'].astype('object')
transactions['CustomerID'] = transactions['CustomerID'].astype(str).str.replace('.0', '', regex=False)

#Filter data that >0 in Quantity and Unitprice and not cancelled transactions (InvoiceNo not star with C)
transactions = transactions[
    (~transactions['InvoiceNo'].astype(str).str.startswith('C')) &
    (transactions['Quantity'] > 0) &
    (transactions['UnitPrice'] > 0)]
```
#### RFM Calculation and Segmentation
Following data cleaning, the Recency, Frequency, and Monetary values were calculated for each customer. Recency was determined based on the number of days since the last purchase, Frequency measured the total number of transactions, and Monetary value represented the total spending of each customer.
```python
# Calculate Recency
last_purchase_date = transactions.groupby('CustomerID')['InvoiceDate'].max().reset_index(name='LastPurchaseDate')
today = pd.to_datetime('2011-12-31')
last_purchase_date['Recency'] = (today - last_purchase_date['LastPurchaseDate']).dt.days

# Calculate Frequency
frequency = transactions.groupby('CustomerID')['InvoiceDate'].nunique().reset_index(name='Frequency')
frequency['Rank'] = frequency['Frequency'].rank(method='first').astype(int)

# Calculate Monetary
monetary = transactions.assign(Revenue=transactions['Quantity'] * transactions['UnitPrice']).groupby('CustomerID')['Revenue'].sum().reset_index(name='Monetary')

# Merge in 1 df
rfm = last_purchase_date.merge(frequency, on='CustomerID').merge(monetary, on='CustomerID')
rfm.shape
```
Quintiles were used to assign scores to each RFM component, categorizing customers into segments based on their relative RFM values. These scores served as the foundation for customer segmentation and further analysis.
```python
# Quintile
rfm['R_Score'] = pd.qcut(rfm['Recency'], q=5, labels=[5,4,3,2,1])
rfm['F_Score'] = pd.qcut(rfm['Rank'], q=5, labels= [1,2,3,4,5])
rfm['M_Score'] = pd.qcut(rfm['Monetary'], q=5, labels= [1,2,3,4,5])

# Create RFM score
rfm['RFM_Score'] = rfm['R_Score'].astype(str) + rfm['F_Score'].astype(str) + rfm['M_Score'].astype(str)
```
#### Segmentation
```python
def segment_customers(row):
    score = row['RFM_Score']
    if score in ['555', '554', '544', '545', '454', '455', '445']:
        return 'Champions'
    elif score in ['543', '444', '435', '355', '354', '345', '344', '335']:
        return 'Loyal'
    elif score in ['553', '551', '552', '541', '542', '533', '532', '531', '452', '451', '442', '441', '431', '453', '433', '432', '423', '353', '352', '351', '342', '341', '333', '323']:
        return 'Potential Loyalist'
    elif score in ['512', '511', '422', '421', '412', '411', '311']:
        return 'New Customers'
    elif score in ['525', '524', '523', '522', '521', '515', '514', '513', '425', '424', '413', '414', '415', '315', '314', '313']:
        return 'Promising'
    elif score in ['535', '534', '443', '434', '343', '334', '325', '324']:
        return 'Need Attention'
    elif score in ['331', '321', '312', '221', '213', '231', '241', '251']:
        return 'About To Sleep'
    elif score in ['255', '254', '245', '244', '253', '252', '243', '242', '235', '234', '225', '224', '153', '152', '145', '143', '142', '135', '134', '133', '125', '124']:
        return 'At Risk'
    elif score in ['155', '154', '144', '214', '215', '115', '114', '113']:
        return 'Cannot Lose Them'
    elif score in ['332', '322', '233', '232', '223', '222', '132', '123', '122', '212', '211']:
        return 'Hibernating customers'
    elif score in ['111', '112', '121', '131', '141', '151']:
        return 'Lost customers'
    else:
        return 'Unknown'

# Apply the segmentation function to create a new 'Segment' column
rfm['Segment'] = rfm.apply(segment_customers, axis=1)
```
## IV. Visualization

  
 🚀 


## V. Final Conclusion & Recommendations 

The analysis has revealed some inefficiencies in manufacturing performance. The most critical problems are:


These issues directly impact manufacturing efficiency, delivery performance, and cost control, making them a priority for process improvement.

### Recommendations:
