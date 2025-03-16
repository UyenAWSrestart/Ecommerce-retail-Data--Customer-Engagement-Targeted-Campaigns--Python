<img width="1000" alt="Screen Shot 2025-03-16 at 4 45 10 PM" src="https://github.com/user-attachments/assets/de4b133c-9934-498f-906f-80f8b18b8379" />



# Project Title: Ecommerce retail Data - Customer Engagement & Targeted Campaigns | Python


Author: [Uyen Nguyen]  
Date: February 2025  
Tools Used: Python

---

## 📑 Table of Contents  
I. [📌 Background & Overview](#-background--overview)  
II. [📂 Dataset Description](#-dataset-description)  
III.[🚩 Data Preparation](#-data-preparation)  
IV.[📊 Visualization](#-visualization)  
V. [🔎 Recommendations](#-recommendations)


## 📌 Background & Overview

### 📖 What is this project about?
The objective of this project is to analyze customer profiles to help the global retail company SuperStore enhance engagement and identify key customer segments. This involves leveraging Python techniques, specifically the RFM model. The insights gained will enable stakeholders to launch targeted marketing campaigns and improve customer retention.
> RFM Segmentation is a method to classify customers based on their purchasing behaviors using three key metrics:
> - Recency (R): Time since the last purchase. Recent buyers are more likely to purchase again.
> - Frequency (F): Number of purchases within a period. Frequent buyers are more loyal.
> - Monetary Value (M): Total money spent. High spenders are more valuable to the business.

### 👤 Who is this project for?   
The insights gained will empower the following stakeholders to make informed strategic decisions :
- Data analysts & business analysts
- Marketing and Sales teams


## 📂 Dataset Description

### 🌐 Data Descripion
- There are two tables used in this project to support the analysis and generate insights.
  + Table 1: Historical customer transactions  
   The attached dataset contains historical customer transactions from December 1, 2010, to September 9, 2011, for an e-commerce retail company.  
   Size: 541909 rows, 8 columns
  + Table 2: Segmentation
- Format: .xlsx
  
### 🔀 Table schema:

  
<details>
<summary> Table 1: Historical customer transactions</summary>  
     
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

</details>


<details>
<summary> Table 2: Segmentation</summary>  


  | Segment               | RFM Score                                                                                       |
|-----------------------|-------------------------------------------------------------------------------------------------|
| Champions             | 555, 554, 544, 545, 454, 455, 445                                                               |
| Loyal                 | 543, 444, 435, 355, 354, 345, 344, 335                                                          |
| Potential Loyalist    | 553, 551, 552, 541, 542, 533, 532, 531, 452, 451, 442, 441, 431, 453, 433, 432, 423, 353, 352, 351, 342, 341, 333, 323 |
| New Customers         | 512, 511, 422, 421, 412, 411, 311                                                               |
| Promising             | 525, 524, 523, 522, 521, 515, 514, 513, 425, 424, 413, 414, 415, 315, 314, 313                  |
| Need Attention        | 535, 534, 443, 434, 343, 334, 325, 324                                                          |
| About To Sleep        | 331, 321, 312, 221, 213, 231, 241, 251                                                          |
| At Risk               | 255, 254, 245, 244, 253, 252, 243, 242, 235, 234, 225, 224, 153, 152, 145, 143, 142, 135, 134, 133, 125, 124 |
| Cannot Lose Them      | 155, 154, 144, 214, 215, 115, 114, 113                                                          |
| Hibernating customers | 332, 322, 233, 232, 223, 222, 132, 123, 122, 212, 211                                           |
| Lost customers        | 111, 112, 121, 131, 141, 151                                                                    |
  
</details>

## 🚩 Data Preparation

This part involved checking and handling for missing values, duplicates, and incorrect data types to ensure data integrity. Additionally, outlier values were identified and handled based on the dataset's context to maintain accuracy and consistency.

### 1️⃣ Explore Data type and value
```python
# Explore data type
df.info()

# Explore data value
df.describe()
```
<img width="506" alt="Screen Shot 2025-03-09 at 10 34 43 PM" src="https://github.com/user-attachments/assets/6bc2df16-008e-4aa6-9549-22ecd4cbfcbf" />

<img width="608" alt="Screen Shot 2025-03-09 at 10 35 17 PM" src="https://github.com/user-attachments/assets/ff67d99e-6516-4287-a8dc-b0062157ddcd" />

🚀 - We should covert data type of InvoiceNo, StockCode, Description, CustomerID, Country into string.

🚀 - Customer ID and description columns have many missing value.

🚀 - Quantity and unit price columns have negative values.

### 2️⃣ Handle incorrect data type and value
#### Handle incorrect datatype

```python
# Convert data type of InvoiceNo, StockCode, Description, CustomerID, Country into string

column_list = ['InvoiceNo','StockCode','Description','CustomerID','Country']
for c in column_list:
     df[c] = df[c].astype(str)

df.dtypes
```
#### Handle incorrect value (Quantity <0)

```python
# Check reasons for quantity < 0

df["Canceled_order"] = df['InvoiceNo'].apply(lambda x: True if x[0]== 'C' else False)

# Count quantity <0 result from cancelled orders
print(df[(df.Quantity <0)  & (df.Canceled_order == True)].shape)

# Count quantity <0 does not result from cancelled orders (1336 rows)
print(df[(df.Quantity <0)  & (df.Canceled_order == False)].shape)

# Count quantity <0 result from missing CustomerID (1336 rows)
print(df[(df.Quantity <0)  & (df.Canceled_order == False) & (df.CustomerID == 'nan')].shape)

# Check Canceled orders have quantity > 0 (no)
print(df[(df.Quantity >0)  & (df.Canceled_order == True)].shape)
```
🚀 Orders with Quantity < 0 are canceled (9288) or have a missing Customer ID (1336), so they need to be removed.

```python
# Remove orders having Quantity<0 (because they are cancelled or missed Customer ID)
df = df[df.Quantity > 0]
df.head()
```
#### Handle incorrect data type (Price <0)

```python
# Count order with negative Unit Price
print(df[df.UnitPrice < 0].shape)
# Detect order with negative Unit Price
print(df[df.UnitPrice < 0].head())
#remove 2 orders having negative UnitPrice because of being missed Customer ID
df =df[df.UnitPrice > 0]
```
### 3️⃣ Handle missing and duplicate value
#### Check and handle missing value

```python
df =df.replace('nan', None)
# Check columns having missing value

missing_dict = {'Total_missing':df.isnull().sum(),
                'Percent': (df.isnull().sum())/(df.shape[0])*100}
missing_df = pd.DataFrame.from_dict(missing_dict)
print(missing_df)
```
<img width="346" alt="Screen Shot 2025-03-09 at 10 43 16 PM" src="https://github.com/user-attachments/assets/b11a0797-e3f1-45f6-86dc-1ba88bdc2883" />

🚀 Since only CustomerID has missing values and it is a prerequisite for User Segmentation, remove rows with missing CustomerID.

```python
# Remove rows with missing CustomerID
df = df[df.CustomerID.notnull()]
df.info()
```
#### Check and handle duplicated value

```python
# Create df_duplicated
df['is_duplicate'] = df.duplicated(subset=['InvoiceNo', 'StockCode','InvoiceDate','CustomerID'])
df_duplicated = df[df.is_duplicate== True].sort_values(by=['InvoiceNo'])
print(df_duplicated)
```
🚀 Duplicate values are caused by different quantities being entered for the same item --> Remove all duplicated value, keep the observation with the highest quantity.

```python
# Remove all duplicated value, keep the observation with the highest quantity
df =df.sort_values(by=['InvoiceNo', 'Quantity'], ascending=False)
df = df.drop_duplicates(subset=['InvoiceNo', 'StockCode','InvoiceDate','CustomerID'], keep='first')
df.shape
```
### 4️⃣ Calculate RFM
#### Transform segmentation table

```python
# Transform Segmentation table
Segment['RFM_Score']= Segment['RFM Score'].astype(str).str.split(',')
Segment = Segment.explode('RFM_Score').reset_index(drop=True) # Explode the list into separate rows
Segment['RFM_Score'] = Segment.RFM_Score.str.strip() # Strip whitespace from the split values
Segment.head()
```
<img width="453" alt="Screen Shot 2025-03-09 at 10 55 08 PM" src="https://github.com/user-attachments/assets/a5bef6a9-9ce2-4060-8e5d-6f6c9a627c58" />

#### Calculate recency, frequency and monetary value

```python
df.InvoiceDate = pd.to_datetime(df.InvoiceDate )
df['Date'] = df['InvoiceDate'].dt.date
last_date = df['Date'].max()
df['Value'] = df.UnitPrice * df.Quantity

# Create RFM dataframe
RFM = df.groupby('CustomerID').agg(
    Recency = ('Date', lambda x: last_date - x.max()),
    Frequency = ('InvoiceNo','nunique'),
    Monetary = ('Value','sum')).reset_index()

RFM.dtypes
RFM['Recency'] = RFM['Recency'].dt.days.astype('int16')
RFM.head()
```
<img width="366" alt="Screen Shot 2025-03-09 at 10 56 16 PM" src="https://github.com/user-attachments/assets/385de483-184e-40db-99e4-d59eda5b219a" />

#### Remove outliers

```python
Recency_threshold = RFM.Recency.quantile(0.95)
Frequency_threshold = RFM.Frequency.quantile(0.95)
Monetary_threshold = RFM.Monetary.quantile(0.95)

RFM_drop_outlier = RFM[(RFM.Recency <= Recency_threshold) & (RFM.Frequency <= Frequency_threshold) & (RFM.Monetary <= Monetary_threshold)]
RFM_drop_outlier.shape
#RFM = RFM_drop_outlier
```
##### Review data distribution after removing outlier
```python
# Create figure and axis
fig, ax = plt.subplots(figsize=(12, 3))

# Plot histogram
sns.histplot(data=RFM_drop_outlier, x='Recency', bins=10, ax=ax,color='#6256CA')

# Set title and limits
ax.set_title('Distribution of Recency')
ax.set_xlim(left=0)
ax.yaxis.set_visible(False)

# Remove unnecessary spines
for spine in ['left', 'top', 'right']:
    ax.spines[spine].set_visible(False)

# Show customer count for each bin
for container in ax.containers:
    ax.bar_label(container, label_type='edge', padding=2)

# Show plot
plt.show()
```
<img width="881" alt="Screen Shot 2025-03-09 at 10 59 08 PM" src="https://github.com/user-attachments/assets/30a9b694-36e9-48cf-80e1-5e41698f8bad" />

🚀 Overall, about 2600 customers fall into the 0-100 days recency group, making up 60% of the total customer base. This indicates that the company has a strong base of recent customers, which is a positive sign.


```python
# Define bins and labels for Frequency grouping
binsF = [0, 2, 5, 20]
labelsF = ['1-2', '2-5', '5-20']

# Create frequency groups
RFM_drop_outlier['FrequencyGroup'] = pd.cut(RFM_drop_outlier['Frequency'], bins=binsF, labels=labelsF)

# Create figure and axis
fig, ax = plt.subplots(figsize=(8, 3))

# Plot count of FrequencyGroup
sns.countplot(x='FrequencyGroup', data=RFM_drop_outlier, ax=ax, color='#6256CA')

# Set title and remove unnecessary spines
ax.set_title('Distribution of Frequency')
ax.yaxis.set_visible(False)
ax.spines['left'].set_visible(False)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# Show count labels on bars
for container in ax.containers:
    ax.bar_label(container, label_type='edge', padding=2)

plt.show()
```

<img width="631" alt="Screen Shot 2025-03-09 at 11 00 17 PM" src="https://github.com/user-attachments/assets/84cffc47-b5c0-42ce-8ed2-c7903d353da6" />

🚀 - Group 1-2 (2166 customers): Most of customers are one-time or occasional buyers. Opportunity to encourage repeat purchases through engagement strategies.

🚀 - Group 2-5 (1121 customers): Moderate group; target with loyalty programs to increase purchase frequency.

🚀 - Group 5-20 (600 customers): These customers are loyal and make multiple purchases, so focusing on retaining and rewarding them can drive more revenue.


```python
# Define bins and labels for Monetary grouping
binsM = [0, 100, 1000, 10000, np.inf]
labelsM = ['0-100', '100-1k', '1k-10k', '10k+']

# Create Monetary groups
RFM_drop_outlier['MonetaryGroup'] = pd.cut(RFM_drop_outlier['Monetary'], bins=binsM, labels=labelsM)

# Create figure and axis
fig, ax = plt.subplots(figsize=(8, 3))

# Plot count of MonetaryGroup
sns.countplot(x='MonetaryGroup', data=RFM_drop_outlier, ax=ax, color='#6256CA')

# Set title and remove unnecessary spines
ax.set_title('Distribution of Monetary')
ax.yaxis.set_visible(False)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# Show count labels on bars
for container in ax.containers:
    ax.bar_label(container, label_type='edge', padding=2)

# Show plot
plt.show()
```
<img width="591" alt="Screen Shot 2025-03-09 at 11 01 31 PM" src="https://github.com/user-attachments/assets/37762f37-45b0-4047-b965-94eb007a338d" />

🚀 - 60% customers spend under 1000 --> Engage with loyalty programs or personalized offers to increase spend.

🚀 - Group 1000-10000 (1359 customers): this is a significant revenue contributors --> must retain and increase value.


#### Calculate RFM Score

```python
# Calculate RFM score

RFM_drop_outlier['R_score'] = pd.qcut(RFM_drop_outlier.Recency.rank(method='first'), 5, labels=[5,4,3,2,1])
RFM_drop_outlier['F_score'] = pd.qcut(RFM_drop_outlier.Frequency.rank(method='first'), 5, labels=[1,2,3,4,5])
RFM_drop_outlier['M_score'] = pd.qcut(RFM_drop_outlier.Monetary.rank(method='first'), 5, labels=[1,2,3,4,5])
RFM_drop_outlier['RFM_Score'] = RFM_drop_outlier.R_score.astype(str) + RFM_drop_outlier.F_score.astype(str) + RFM_drop_outlier.M_score.astype(str)

RFM_drop_outlier['RFM_Score']=RFM_drop_outlier['RFM_Score'].str.strip()
RFM_drop_outlier.head()
```

```python
# Map RFM Score to segmentation
RFM_final = RFM_drop_outlier.merge(Segment, on='RFM_Score', how='left')
RFM_final.head() 
```

## 📊 Visualization

### 1️⃣ Number customer for each Segmentation

```python
# Calculate percentage of customers in each segment
segment_counts = RFM_final['Segment'].value_counts()
segment_percentage = (segment_counts / segment_counts.sum()) * 100

# Create the count plot
g = sns.catplot(
    x='Segment',
    data=RFM_final,
    kind='count',
    color='#6256CA',
    order=segment_counts.index
)

plt.xlabel('')
plt.ylabel('Number of Customers')
g.fig.suptitle('Number of Customers per Segmentation')

# Annotate each bar with percentage values
for ax in g.axes.flat:
    for p, percentage in zip(ax.patches, segment_percentage):
        ax.text(
            p.get_x() + p.get_width() / 2,
            p.get_height(),
            f'{percentage:.1f}%',  # Show percentage on bars
            ha='center',
            va='bottom'
        )

g.set_xticklabels(rotation=90)
plt.show()
```
<img width="597" alt="Screen Shot 2025-03-09 at 11 09 26 PM" src="https://github.com/user-attachments/assets/5a4cc458-12e0-4420-a7a0-1a38c689420a" />

 🚀 - The company's largest customer segment, "Champions" (17.9%) is a positive indicator, as these customers are highly loyal, engage in frequent transactions, and contribute significantly to sales.
 
 🚀 - However, a substantial portion of the customer base falls within the "Hibernating," "At Risk," and "Lost Customers" groups (36%), presenting a significant challenge. Without a targeted marketing strategy to re-engage these customers, the company's business performance may be severely impacted.

### 2️⃣ Average spending per customer for each Segmentation
  
```python
# Calculate total spending per segment
segment_by_spending = RFM_final.groupby('Segment', as_index=False)['Monetary'].sum()

# Count number of customers per segment
segment_by_usercount = RFM_final.groupby('Segment', as_index=False)['CustomerID'].count()

# Merge both dataframes
segment_add = segment_by_spending.merge(segment_by_usercount, on='Segment', how='left')

# Calculate average spending per customer
segment_add['avg_spend_per_customer'] = segment_add['Monetary'] / segment_add['CustomerID']
segment_add = segment_add.sort_values('avg_spend_per_customer')

# Create bar plot
g = sns.barplot(x='Segment', y='avg_spend_per_customer', data=segment_add, order=segment_add['Segment'], color='#6256CA')

# Add numbers on each bar (rounded to 0 decimal places)
for p in g.patches:
    g.annotate(
        f'{p.get_height():.0f}',  # Rounded to 0 decimal places
        (p.get_x() + p.get_width() / 2, p.get_height()),
        ha='center', va='bottom', fontsize=10, fontweight='bold'
    )

# Add title and labels
g.set_title('Average Spending per Customer by Segment')
plt.xticks(rotation=90)
plt.ylabel('Avg Spend per Customer')

# Show the plot
plt.show()
```
<img width="603" alt="Screen Shot 2025-03-09 at 11 11 36 PM" src="https://github.com/user-attachments/assets/11796f89-2fc6-4a2b-b40b-179b2eff7781" />

 🚀 -The average spending per customer is highest for the "Champions," "Loyal," "At Risk," and "Cannot Lose" segments.
 
 🚀 -The number of customers in the "Cannot Lose Them" segment is the smallest, but their contribution to revenue is extremely high.
 
 🚀 -Although the number of customers in the "Hibernating Customers" and "Lost Customers" segments is significant, their average spending is lower than expected.



## 🔎 Recommendations 

| Segment                                                   | Characteristics                                                                  | Recommendations             |
| --------------------------------------------------------- | -------------------------------------------------------------------------------- | ---------------------------- |
| Champions                                                 | - Highest-value customers with substantial spending<br> - Consistent engagement | - Provide VIP memberships or premium customer service.<br>\- Provide personalized discounts on high-end products, or on some holiday sales.<br>\- Invite them to exclusive brand events, webinars, or private communities. |
| At Risk                                                   | - High-value customers with substantial spending<br> - Decreasing engagement    | - Offer incentives for returning or for engaging with the brand (special discounts).<br>- Have a dedicated customer service line to feedback their questions.                                                             |
| Loyal                                                     | - High-value customers with substantial spending<br>\- Consistent engagement    | -  Provide loyalty discounts, early product launches, or special events.<br>\- Encourage them to refer friends or family in exchange for rewards.                                                                          |
| Cannot Lose Them<br>                                      | - Small group<br>\- High-value customers (High average spending)                | - Offer high-touch personalized services<br>\- Provide retention Incentives with special offers and updates to ensure they stay loyal.                                                                                     |
| Need attention<br>Promossing                              | - Moderate value<br>\- Low current engagement                                   | - Provide special deals on their favorite products or interests.<br>\- Provide a personalized product recommendation list to encourage purchasing                                                                          |
| Potential Loyalists                                       | -Low monetary value than Loyal<br>\- Frequent purchase.                         | - Send them personalized offers to convert them into repeat customers.<br>\- Provide access to premium content or special loyalty benefits to make them feel valued.                                                       |
| About To Sleep<br>Hibernating customers<br>Lost customers | -Low or no recent activity<br>\- Low value (average spending)                   | - Send emails with personalized offers, discounts, or product updates to reignite interest.<br>\-Ask for feedback to understand why they stopped buying and offer tailored solutions.                                      |
| New Customers                                             | -Recent first purchase<br>\- Low value                                          | - Recommend related products or set up follow-up emails.<br>\- Offering a discount or free shipping on their first purchase.                                                                                               |
