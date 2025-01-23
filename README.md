# 📊 Exploratory Data Analysis on E-commerce Datasets 📊
## 🌸 Description 🌸
Welcome! This is documentation of my data analysis exploration on e-commerce data sets. 

## 🛠️ Tools and Language 🛠️
<img align="left" alt="Visual Studio Code" width="30px" src="https://upload.wikimedia.org/wikipedia/commons/9/9a/Visual_Studio_Code_1.35_icon.svg" style="padding-right:10px;" />
<img align="left" alt="Google Colab" width="50px" src="https://upload.wikimedia.org/wikipedia/commons/d/d0/Google_Colaboratory_SVG_Logo.svg" style="padding-right:10px;" />
<img align="left" alt="Python" width="30px" src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/110px-Python-logo-notext.svg.png?20100317150552" style="padding-right:10px;" /><br >

## 📜 Script 📜 
[![Open In Collab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1GijLw3HnAQR7mFFMYreMiy8JVoZ4PFp5?usp=sharing)

## 🛒 Dataset Information 🛒
### 🛍️ Data Shape
Columns: 8 <br >
Rows: 4870 
### 🛍️ Columns/Features: 
1. InvoiceNo: A unique number/code for each sale or purchase
2. StockCode: A unique number/code for each Product
3. Description: The name or description of each product
4. Quantity: The number of products purchased in a transaction
5. InvoiceDate: The date the invoice was issued
6. UnitPrice: The price per unit of the product
7. CustomerID: A unique number of each Customer
8. Country: The country where the transaction took place or the location of the customer who made the purchase
   
## 🧼 Data Pre-processing 🧼
### ✏️ 1. Check the missing value
First thing first, we need to check the data information, you can use this code:
```
ecommerce.info()
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/general%20info.png" width="200" /><br >
We can see the indication of missing value through general information. from the general information, it can be seen that the amount of data in each column is the same as the range index, so there is no missing value. However, the data type for the InvoiceDate column is not yet appropriate, so we need to convert it first.
```python
# convert InvoiceDate to datetime format
ecommerce['InvoiceDate'] = pd.to_datetime(ecommerce['InvoiceDate'])
ecommerce.head()
```
### ✏️ 2. Check duplicate value
For checking the duplicate value, here we use this code:
```python
# check for duplicates
ecommerce.duplicated().sum()
```
There is no duplicate value in this dataset.
## 💡 Exploratory Data Analysis 💡
Exploratory aims to uncover insights/patterns from data that can support the process of decision-making in a business or case. There are 3 basic techniques that you can use in the EDA stage:
1. Present the statistical summary of each feature.
   The statistic summary can include mean, median, mode, quartiles, and data distribution type.
2. Present the univariate analysis. Univariate analysis is the activity to analyze each column one-by-one visually.
3. Present the multivariate analysis. Multivariate analysis is the activity of analyzing multiple variables all at once.<br >
Beyond the basic techniques, there are also additional techniques to deep-diving the data, such as filtering techniques, group-by aggregate, and using pivot or melt table

### 🧩 1. Statistical Summary
From the dataset, we can only provide statistical summary of the numerical columns only `Quantity` and `UnitPrice`. It's because the other columns are unique data. Here are the calculation of statistical summary.<br >
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/stat%20summary%20numerical.png" width="250" /><br >
We will also visualize the distribution of those numerical columns.<br >
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/distribution%20of%20numerical.png" width="900" /><br >
The data has a right-skewed distribution (Mean > Median) which the mean of the price is above its median value.

### 👔 2. Product trends: What are the Top 10 products with the most transactions?
We need to perform group by aggregation. Basis group-by is `Description`, while the number of transactions = count `InvoiceNo`. After aggregating, do appropriate sorting and truncation.
```python
# Top 10 products with the most transactions
top10_prod = (ecommerce
            .groupby('Description')
            .agg(num_trx=('InvoiceNo','count'))
            .reset_index() # optional
            .sort_values('num_trx',ascending=False)
            .head(10)
            )
top10_prod
```
and then, visualize it.
```python
# visualize it
plt.figure(figsize=(8,6))
barplot = sns.barplot(data=top10_prod, x='Description', y='num_trx', color='orange')

for bar in barplot.patches:
    barplot.annotate(
        format(bar.get_height()),  # Format angka (2 desimal)
        (bar.get_x() + bar.get_width() / 2, bar.get_height()),  # Posisi anotasi
        ha='center', va='bottom', fontsize=10, color='black'
    )
plt.xticks(rotation=90)
plt.title('Top 10 Products with Most Transaction', fontsize = 14, fontweight='bold')
plt.xlabel('Products')
plt.ylabel('Number of Transaction')
plt.show()
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/top%2010%20product%20most%20transact.png" width="500" /><br >
From the chart above, we can conclude that 10 products that highly popular are: JUMBO BAG RED RETROSPOT, PARTY BUNTING, REGENCY CAKESTAND, WHITE HANGING HEART T-LIGHT HOLDER, TEATIME FAIRY CAKE CASES, SET OF 3 CAKE TINS PANTRY DESIGN, SET OF 4 PANTRY JELLY MOULDS, RED RETROSPOT CHARLOTTE BAG, JAM MAKING SET PRINTED, ASSORTED COLOUR BIRD ORNAMENT.<br >
So, from this insight, here are the recommendations that we can provide:
1. Increase Stock Levels: Ensure these products are well-stocked to meet demand and prevent stockouts.
2. Upselling Opportunities: Bundle these popular products with other less popular or higher-margin items to increase the average transaction value.
3. Expand Marketing Campaigns: Focus on these products in advertisements or promotions to maximize their potential further.

### 👖 3. Product trends: What are the Top 10 products with the LEAST transactions?
To find the top least transaction product, use this code:
```python
# Low 10 products in terms of transactions
low10_prod = (ecommerce
            .groupby('Description')
            .agg(num_trx=('InvoiceNo','count'))
            .reset_index()
            .sort_values('num_trx',ascending=False)
            .tail(10)
            )
low10_prod
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/top%2010%20product%20least%20transact.png" width="250" /><br >
The output is not so interesting to visualize, because all products have a single transaction.<br >
Recommendation: Consider rebranding, bundling, or discontinuing products that consistently underperform.<br >

### 👛 4. Customer Behaviour: Top 10 Customers with the most spending? 
We need to create a new column `total_price = Quantity * UnitPrice`. Then sum it based on group-by CustomerID.
```python
# create total_price column
ecommerce['total_price'] = ecommerce['Quantity'] * ecommerce['UnitPrice']

# group by aggregation
top10_cust = (ecommerce
            .groupby('CustomerID')
            .agg(total_spend=('total_price','sum'))
            .reset_index()
            .sort_values('total_spend',ascending=False)
            .head(10)
            )

top10_cust
```
then, visualize it.
```python
# visualize it
plt.figure(figsize=(10,6))
barplot = sns.barplot(data=top10_cust, x='CustomerID', y='total_spend', color='yellow', order=top10_cust['CustomerID'])

for bar in barplot.patches:
    barplot.annotate(
        format(bar.get_height(), '.2f'),  # Format angka (2 desimal)
        (bar.get_x() + bar.get_width() / 2, bar.get_height()),  # Posisi anotasi
        ha='center', va='bottom', fontsize=10, color='black'
    )
plt.xticks(rotation=45)
plt.title('Top 10 Customer with Highest Spend', fontsize = 14, fontweight='bold')
plt.xlabel('Customer ID')
plt.ylabel('Total Spend')
plt.show()
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/top%2010%20cust.png" width="600" /><br >
Recommendation: Conduct a Loyalty Program --> Reward these high-spending customers with exclusive benefits such as early access to new products, discounts, or personalized offers to retain their loyalty.<br >

### 🧳 5. Customer Behaviour: How many distinct products are sold? How is their price distribution? 
We need to create a sub-dataframe containing `Description` and `UnitPrice` columns only, and ensure no duplication.<br >
```python
prod = ecommerce.groupby('Description').agg(
    unit_price=('UnitPrice','mean')
).reset_index()

prod
```
make sure there is no duplicate value<br >
```python
# number of products
prod.shape[0]
```
```
len(prod)
```
```
prod['Description'].nunique()
```
then, visualize it
```python
# price distribution
plt.figure(figsize=(10,6))
# sns.kdeplot(data=prod, x='UnitPrice')
sns.histplot(data=prod, x='unit_price', kde=True)
plt.title('Distribution of Price Per-product', fontsize = 14, fontweight='bold')
plt.xlabel('Unit Price')
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/price%20distribution.png" width="600" /><br >

The unit price has a right-skewed distribution (Mean > Median) which the mean of the price is above its median value.<br>
A right-skewed price distribution indicates that most products are priced affordably, with only a few premium products priced significantly higher. This may suggest a focus on mass-market products, but premium offerings still play a role in revenue generation.<br >
The large number of distinct products suggests a wide range of offerings, potentially catering to diverse customer needs.

### 🧣 6. Customer Behaviour: From what countries do the customers come? Which country has the most loyal customers?
We need to group by `Country` and `CustomerID`, then take the sum of `total_price`. After that, we draw boxplot side by side countries.
```python
# groupby
country_user = (ecommerce
                .groupby(['Country','CustomerID'])
                .agg(total_spend=('total_price','sum'))
                .reset_index())
country_user.head()
```
```
country_user.Country.value_counts()
```

The top 5 countries with the most customers are: United Kingdom, Germany, France, Switzerland, and Spain<br >
Recommendation: Invest in Key Market: Allocate more resources to the top 5 countries for marketing, logistics, and product availability to further strengthen market share.<br >
Next, we will explore the distribution data of the top 5 countries to gain more insight about the customers in each top 5 countries.
```
# filter to top 5 contries (most CustomerID) only
# using isin technique
top5_countries = ['United Kingdom','Germany','France','Switzerland','Spain']
country_user = country_user[country_user['Country'].isin(top5_countries)]
country_user
```
```
# Clip max spend at 200 so that we can easily compare the boxplots
country_user['total_spend'] = country_user['total_spend'].clip(upper=200)

plt.figure(figsize=(12,6))
sns.boxplot(data=country_user,
            x='Country',
            y='total_spend', color='maroon')
plt.title('Distribution of Customer Spending in Top 5 Countries', fontsize = 14, fontweight='bold')
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/dist%20top%205%20countries.png" width="600" /><br >
Observation:<br >
- Top-Performing Markets: Switzerland and Germany are the strongest performers with high median spends and large spreads.
- **Potential for Premium Segments: The United Kingdom and Switzerland show opportunities to create premium offerings targeting high spenders.
- **Growth Opportunities: Spain requires strategies to improve overall customer engagement and increase spending.<br >
Recommendation:
1. Revitalize the Spanish Market: Implement localized marketing campaigns or introduce budget-friendly product lines to engage more customers and increase spending in Spain.
2. Focus on High-Value Segments:
   - Target high-spending customers in Switzerland and Germany with exclusive promotions or loyalty programs to maximize revenue.
   - Investigate the behavior of high spenders in the United Kingdom to understand their preferences and replicate these success factors in other markets.

### 📈 7. Annual Performances (Time-series): How is the monthly count buyers profile (time series)?
Create the invoice mont column from invoice date, to categorize each transaction to each month.
```
ecommerce['InvoiceMonth'] = ecommerce['InvoiceDate'].dt.to_period('M')
ecommerce.head()
```
Calculate total buyer in each month
```
monthly_data = (ecommerce
                .groupby('InvoiceMonth')
                .agg(num_buyer=('CustomerID','nunique'),
                     total_omzet=('total_price','sum'))
                .reset_index())

monthly_data['InvoiceMonth'] = monthly_data['InvoiceMonth'].astype(str)
monthly_data
```
then, visualize it.
```
# Set a light theme
sns.set_theme(style="whitegrid")

# Plot the line chart
plt.figure(figsize=(14, 8))
sns.lineplot(
    data=monthly_data,
    x='InvoiceMonth',
    y='num_buyer',
    marker='o',
    color='steelblue',
    linewidth=2.5
)

# Add title and labels
plt.title('Monthly Buyer Progression', fontsize=18, fontweight='bold', color='black')
plt.xlabel('Invoice Month', fontsize=14, fontweight='bold', color='gray')
plt.ylabel('Number of Buyers', fontsize=14, fontweight='bold', color='gray')

# Rotate x-axis labels
plt.xticks(rotation=45, fontsize=12, color='black')
plt.yticks(fontsize=12, color='black')

# Tight layout for better spacing
plt.tight_layout()

# Show the plot
plt.show()
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/annual%20buyer%20permonth.png" width="600" /><br >
### 💰 8. Annual Performances (Time-series): How is the monthly revenue profile (time series)?
Same as the total buyers count, here we will create a visualization for total revenue
```
# Set a light theme
sns.set_theme(style="whitegrid")

# Plot the line chart
plt.figure(figsize=(14, 8))
sns.lineplot(
    data=monthly_data,
    x='InvoiceMonth',
    y='total_omzet',
    marker='o',
    color='red',
    linewidth=2.5
)

# Add title and labels
plt.title('Monthly Total Revenue Progression', fontsize=18, fontweight='bold', color='black')
plt.xlabel('Invoice Month', fontsize=14, fontweight='bold', color='gray')
plt.ylabel('Total Omzet', fontsize=14, fontweight='bold', color='gray')

# Rotate x-axis labels
plt.xticks(rotation=45, fontsize=12, color='black')
plt.yticks(fontsize=12, color='black')

# Tight layout for better spacing
plt.tight_layout()

# Show the plot
plt.show()
```
<img src="https://github.com/calistadamara/eda-ecommerce/blob/main/ecommerce/annual%20revenue%20permonth.png" width="600" /><br >
November is the peak month. It's because black market campaign<br >
Recommendation<br >
- Plan Seasonal Campaigns: Replicate the success of the Black Market campaign during other peak seasons (e.g., holidays, festivals) to maintain high transaction volumes.
- Extend Campaign Duration: Explore extending the duration or expanding the scope of such campaigns to sustain momentum.

## 🏮 Recommendation 🏮
Here is the summary of recommendations that we can provide to user/business owner:
* for top 10 popular products:<br >
  Recommendation:
   1. Increase Stock Levels: Ensure these products are well-stocked to meet demand and prevent stockouts.
   2. Upselling Opportunities: Bundle these popular products with other less popular or higher-margin items to increase the average transaction value.
   3. Expand Marketing Campaigns: Focus on these products in advertisements or promotions to maximize their potential further.
* For the top 10 products with the least transaction:
  Management can consider rebranding, bundling, or discontinuing products that consistently underperform.
* For the top 10 customers with the most spending:
  Conduct a Loyalty Program --> Reward these high-spending customers with exclusive benefits such as early access to new products, discounts, or personalized offers to retain their loyalty.
* From the pattern of the top 5 countries with the most spending data, to gain more profit, the Company can consider to:
  1. Revitalize the Spanish Market: Implement localized marketing campaigns or introduce budget-friendly product lines to engage more customers and increase spending in Spain.
  2. Focus on High-Value Segments:
   - Target high-spending customers in Switzerland and Germany with exclusive promotions or loyalty programs to maximize revenue.
   - Investigate the behavior of high spenders in the United Kingdom to understand their preferences and replicate these success factors in other markets.
* And last, from the annual performance the peak month is November, its because of the Black Market Campaign. If the company wants to get even more profits/sales, the Company can consider to:
   1. Plan Seasonal Campaigns: Replicate the success of the Black Market campaign during other peak seasons (e.g., holidays, and festivals) to maintain high transaction volumes.
   2. Extend Campaign Duration: Explore extending the duration or expanding the scope of such campaigns to sustain momentum.
      
## 📓 Closing 📓
That's all for the exploratory data analysis on the e-commerce datasets. Hopefully, it can help you to get more insight and inspiration. If there are any suggestions and critics, don't hesitate to contact me through email. Thank you 🧸



