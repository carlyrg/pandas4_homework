# Pandas
HW-wk4

This assignment used Python, Jupyter Notebook, and the Pandas library to analyze user data for a ficticious video game company. I reviewed data of in-game purchases made by players then aggregated and analyzed it to view trends in the purchasing behavior of players. 

Key findings are noted at the end

```python
import pandas as pd
import json
import numpy as np
```


```python
data = json.load(open('purchase_data.json', 'r'))
heroes = json.dumps(data)
df = pd.read_json(heroes)
#df['Price'] = df['Price'].map('${:.2f}'.format)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



### Total players


```python
players = df['SN'].unique().shape[0]
print("total players: {}".format(players))
```

    total players: 573
    

### Purchasing Analysis


```python
#number of unique items
unique_items = df['Item ID'].unique().shape[0]
unique_items
```




    183




```python
#average purchase price
avg_price = '${}'.format(round(df["Price"].mean(),2))
avg_price
```




    '$2.93'




```python
#total Number purchases
purchase_count = df['Item Name'].count()
purchase_count
```




    780




```python
#total revenue
total_revenue = '${}'.format(round(df["Price"].sum(),2))
total_revenue
```




    '$2286.33'




```python
purch_analysis = pd.DataFrame({
    "Item Count": [unique_items],
    "Average Purchase Price": [avg_price],
    "Total Number of Purchases": [purchase_count],
    "Total Revenue": [total_revenue]
})
purch_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Item Count</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>183</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



### Gender Demographics


```python
#percentage and count of players by gender
gender = df.groupby('Gender')['SN'].nunique().reset_index()
gender = gender.rename(columns={'SN': 'Gender_Count'})
total_players = gender.Gender_Count.sum()
gender['Percentage of Players'] = (gender['Gender_Count']/total_players*100)
gender['Percentage of Players'] = gender['Percentage of Players'].map('{:.2f}%'.format)
gender
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Gender_Count</th>
      <th>Percentage of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>465</td>
      <td>81.15%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>8</td>
      <td>1.40%</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (Gender)


```python
#purchase count, avg purch price, total purch value, normalized total
gender_purchase_analysis = df.groupby('Gender').Price.agg(['count', 'mean', 'sum']).reset_index()
gender_purchase_analysis = gender_purchase_analysis.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
gender_purchase_analysis['Normalized Totals'] = gender_purchase_analysis['Total Purchase Value'] / gender['Gender_Count']
gender_purchase_analysis['Avg Purchase Price'] = gender_purchase_analysis['Avg Purchase Price'].map('${:.2f}'.format)
gender_purchase_analysis['Total Purchase Value'] = gender_purchase_analysis['Total Purchase Value'].map('${:,.2f}'.format)
gender_purchase_analysis['Normalized Totals'] = gender_purchase_analysis['Normalized Totals'].map('${:.2f}'.format)
gender_purchase_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



### Age demographics


```python
#create bins 
bins = [0, 10, 14, 19, 24, 29, 34, 39, 100]
age_group_labels = ['<10', '10-14', '15-19', '20-24', '25-29', '30-34', '35-39','40+']
df['Age Groups'] = pd.cut(df['Age'], bins, labels=age_group_labels)
age_counts = (df.groupby("Age Groups"))['SN'].nunique().reset_index()
age_counts = age_counts.rename(columns={'SN': 'Total Count'})
age_counts
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age Groups</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>20</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>100</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>259</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>87</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>47</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>27</td>
    </tr>
    <tr>
      <th>7</th>
      <td>40+</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
#purchase count, avg purchase price, total purchase value, normalized totals
age_purch_data = df.groupby('Age Groups').Price.agg(['count', 'mean', 'sum']).reset_index()
age_purch_data = age_purch_data.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
age_purch_data['Normalized Totals'] = age_purch_data['Total Purchase Value'] / age_counts['Total Count']
age_purch_data['Avg Purchase Price'] = age_purch_data['Avg Purchase Price'].map('${:.2f}'.format)
age_purch_data['Total Purchase Value'] = age_purch_data['Total Purchase Value'].map('${:.2f}'.format)
age_purch_data['Normalized Totals'] = age_purch_data['Normalized Totals'].map('${:.2f}'.format)

age_purch_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age Groups</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>32</td>
      <td>$3.02</td>
      <td>$96.62</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>31</td>
      <td>$2.70</td>
      <td>$83.79</td>
      <td>$4.19</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>7</th>
      <td>40+</td>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>



### Top Spenders
by purchase value


```python
#SN, Purchase Count, Avg Purch price, Total Purch Value
df_top_cust = df.groupby('SN').Price.agg(['count', 'mean', 'sum']).nlargest(5, "sum").reset_index()
df_top_cust = df_top_cust.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
df_top_cust['Avg Purchase Price'] = df_top_cust['Avg Purchase Price'].map('${:.2f}'.format)
df_top_cust['Total Purchase Value'] = df_top_cust['Total Purchase Value'].map('${:.2f}'.format)
df_top_cust
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Undirrala66</td>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saedue76</td>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mindimnya67</td>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Haellysu29</td>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Eoda93</td>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



### Most Popular item
by purchase count


```python
#Item ID, Item Name, Purchase Count, Item Price
df_top_prod = df.groupby(['Item ID', 'Item Name', 'Price']).Price.agg(['count', 'mean', 'sum']).nlargest(5, "count").reset_index()
df_top_prod = df_top_prod.rename(columns={'count': 'Purchase Count', 'mean': 'Average Price','sum': 'Total Purchase Value'})
df_top_prod['Price'] = df_top_prod['Price'].map('${:.2f}'.format)
df_top_prod['Total Purchase Value'] = df_top_prod['Total Purchase Value'].map('${:.2f}'.format)
del df_top_prod["Average Price"]
df_top_prod
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13</td>
      <td>Serenity</td>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>3</th>
      <td>31</td>
      <td>Trickster</td>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>4</th>
      <td>34</td>
      <td>Retribution Axe</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
  </tbody>
</table>
</div>



### Most Profitable
by purchase value


```python
#Item ID, Item Name, Purchase Count, Item Price, Total Purchase Value
df_top_profit = df.groupby(['Item Name', 'Item ID', 'Price']).Price.agg(['count', 'mean', 'sum']).nlargest(5, "sum").reset_index()
df_top_profit = df_top_profit.rename(columns={'count': 'Purchase Count', 'mean': 'Average Price','sum': 'Total Purchase Value'})
df_top_profit['Total Purchase Value'] = df_top_profit['Total Purchase Value'].map('${:.2f}'.format)
df_top_profit['Price'] = df_top_profit['Price'].map('${:.2f}'.format) # this doesn't work
del df_top_profit['Average Price']
df_top_profit
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Item ID</th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Spectral Diamond Doomblade</td>
      <td>115</td>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Orenmir</td>
      <td>32</td>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Singed Scalpel</td>
      <td>103</td>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>107</td>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>



### observations
1. 15-30 year old males are largely the dominant consumers of this video game.
2. No single product is a revenue driver for them. The most popular item only sold 11 times.
3. Even with 183 items available, no one purchased more than five items so they must be to a very targeted audience
