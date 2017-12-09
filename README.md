Observations 
Observation 1: Percentages of Males that are playing the game are far more than the females 
Observation 2 : 
An Age Group of 20 -24 use the game and are actively making purchases 
Betrayal, Whisper of Grieving Windows and Arcnae Gem are  the most popular even though they are not the most profitable ( The price can be increased in order to make more revenue out of the more popular items)

```python
# import modules required to open the file and perform analysis 

import os
import pandas as pd 
import json 
import numpy as np
from IPython.display import display



# Adjusting the view for results displayed below 

pd.set_option('display.height', 1000)
pd.set_option('display.max_rows', 500)
pd.set_option('display.max_columns', 500)
pd.set_option('display.width', 1000)


with open("/Users/pooja/Documents/Pandas HW_ House of Pymoli/purchase_data.json") as data_file:
    data = json.load(data_file)
    df = pd.DataFrame(data)
    
    df.columns = [c.replace(' ', '_') for c in df.columns]
    #print (df.head())
    
    display(df.head()) 
    
    
    
```

    height has been deprecated.
    



<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item_ID</th>
      <th>Item_Name</th>
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



```python
# Player Count

print("The total number of players are:"+ str(df.SN.nunique()))

```

    The total number of players are:573



```python
# Number of unique items, average purchase price , total number of purchases and total revenue 

unique_items=df.Item_Name.nunique()
Avg_purchase_price=df['Price'].mean()
total_purchases=df.Item_ID.count()
revenue=df['Price'].sum()

df_result = pd.DataFrame({'Unique_Items': [unique_items], 'Average_Purchase_Price': [Avg_purchase_price],
                          'Total_Number_Purchases':[total_purchases],'Total_Revenue':[revenue]})

print(df_result)


```

       Average_Purchase_Price  Total_Number_Purchases  Total_Revenue  Unique_Items
    0                2.931192                     780        2286.33           179



```python
#Gender Demographics 

# created a new Data Frame that groups by Screen Name(SN) and Gender (This is to get unique screen names and count gender acordingly)

counts = df.groupby(['SN','Gender'], as_index=False).size().unstack(fill_value=0).reset_index()

# Retrieve the number of rows where the male column has a value 
male_count=counts[(counts['Male']>0)].count()

MC=(male_count['Male'])

# Retrieve the number of rows where the Female column has a value 

female_count=counts[(counts['Female']>0)].count()

FC=(female_count['Female'])

# Retrieve the total of unique screen names and added them ( this is to know the remainder which makes the others / Non Disclosed gender count)

Total=df.SN.nunique()

Known=(MC+FC)

Others=Total-Known

# Calculate the percentages of gender Types 

MP=(MC/Total)*100
FP=(FC/Total)*100
OP=(Others/Total)*100

# creating a list to re name the index so that they are more descriptive 

new_index=['Total','Percentage']

# Creating a Data Frame to display the results 

gender_result=pd.DataFrame({'Male':[MC,MP],'Female':[FC,FP],'Others/Non-Disclosed':[Others,OP]},index=new_index)

display (gender_result)




```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Female</th>
      <th>Male</th>
      <th>Others/Non-Disclosed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Total</th>
      <td>100.00</td>
      <td>465.00</td>
      <td>8.00</td>
    </tr>
    <tr>
      <th>Percentage</th>
      <td>17.45</td>
      <td>81.15</td>
      <td>1.40</td>
    </tr>
  </tbody>
</table>
</div>



```python
#Purchasing Analysis by Gender 

#Purchase Count
#Average Purchase Price
#Total Purchase Value
#Normalized Totals 

#gender_ana = df.groupby(['Gender'], as_index=False).size().unstack(fill_value=0).reset_index()

gender_ana=df.groupby(['Gender'], as_index=False)['Price'].sum()
# Total Purchase value by gender 

Avg_Purchase_Female=gender_ana.iloc[0,1]/FC
Avg_Purchase_Male=gender_ana.iloc[1,1]/MC
Avg_Purchase_Other=gender_ana.iloc[2,1]/Others


Avg_Price= [Avg_Purchase_Female,Avg_Purchase_Male,Avg_Purchase_Other]

gender_ana['Normalized_Total']=Avg_Price


gender_ana1= df.groupby(['Gender'],as_index=False)['Item_ID'].count()


gender_ana['Purchase_count']=gender_ana1['Item_ID']

gender_ana['Average_Price']=gender_ana['Price']/gender_ana['Purchase_count']

gender_ana





```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Price</th>
      <th>Normalized_Total</th>
      <th>Purchase_count</th>
      <th>Average_Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>382.91</td>
      <td>3.83</td>
      <td>136</td>
      <td>2.82</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>1,867.68</td>
      <td>4.02</td>
      <td>633</td>
      <td>2.95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>35.74</td>
      <td>4.47</td>
      <td>11</td>
      <td>3.25</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create bins to categorize the age 

bins = [0,10,14,19,24,29,34,40,100]


# Create the names for the four bins
group_names = ['>10','10-14', '15-19', '20-24','25-29','30-34','35-40','<40']

#pd.cut(df["Age"], bins, labels=group_names)

df["Age Group"] = pd.cut(df["Age"],bins, labels=group_names)




age_ana= df.groupby(['Age Group'],as_index=False)['Item_ID'].count()

age_ana1= df.groupby(['Age Group'],as_index=False)['Price'].sum()


#age_ana1['Avg_Price_Age']=(age_ana1['Price']/age_ana1['Price'].sum())*100

#age_ana['Price1']=df.groupby(['Age Group'],as_index=False)['Price'].sum()


result_age_demographics = pd.merge(age_ana, age_ana1, on='Age Group')


pd.options.display.float_format = '{:20,.2f}'.format

result_age_demographics['Avg_Purchase']=result_age_demographics['Price']/result_age_demographics['Item_ID']

result_age_demographics['Normalized_Total']=result_age_demographics['Item_ID'].sum()/result_age_demographics['Item_ID']

result_age_demographics






```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age Group</th>
      <th>Item_ID</th>
      <th>Price</th>
      <th>Avg_Purchase</th>
      <th>Normalized_Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&gt;10</td>
      <td>32</td>
      <td>96.62</td>
      <td>3.02</td>
      <td>24.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>31</td>
      <td>83.79</td>
      <td>2.70</td>
      <td>25.16</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>133</td>
      <td>386.42</td>
      <td>2.91</td>
      <td>5.86</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>336</td>
      <td>978.77</td>
      <td>2.91</td>
      <td>2.32</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>125</td>
      <td>370.33</td>
      <td>2.96</td>
      <td>6.24</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>64</td>
      <td>197.25</td>
      <td>3.08</td>
      <td>12.19</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-40</td>
      <td>56</td>
      <td>164.51</td>
      <td>2.94</td>
      <td>13.93</td>
    </tr>
    <tr>
      <th>7</th>
      <td>&lt;40</td>
      <td>3</td>
      <td>8.64</td>
      <td>2.88</td>
      <td>260.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_ana=df.groupby(['SN'],as_index=False)['Price'].sum()

top_ana2=top_ana.sort_values('Price',ascending=False).head()

top_ana1= df.groupby(['SN'],as_index=False)['Item_ID'].count()

#top_ana1

top_ana3 = pd.merge(top_ana2, top_ana1, on='SN')

top_ana3['Avg_Price_Top5']=top_ana3['Price']/top_ana3['Item_ID']

top_ana3


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Price</th>
      <th>Item_ID</th>
      <th>Avg_Price_Top5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Undirrala66</td>
      <td>17.06</td>
      <td>5</td>
      <td>3.41</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saedue76</td>
      <td>13.56</td>
      <td>4</td>
      <td>3.39</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mindimnya67</td>
      <td>12.74</td>
      <td>4</td>
      <td>3.18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Haellysu29</td>
      <td>12.73</td>
      <td>3</td>
      <td>4.24</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Eoda93</td>
      <td>11.58</td>
      <td>3</td>
      <td>3.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
# most popular items  


df1=df.groupby(['Item_Name','Item_ID','Price']).count()



df1=df1.sort_values('Age',ascending=False)



df1=df1.rename(columns = {'Age':'Purchase_Count'}).head()

df1 = df1.drop(['Gender','SN'],1)

df1=df1.reset_index()

df1['Total Purchase Value']=df1['Purchase_Count']*df1['Price']

display(df1)



```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item_Name</th>
      <th>Item_ID</th>
      <th>Price</th>
      <th>Purchase_Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>39</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arcane Gem</td>
      <td>84</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Trickster</td>
      <td>31</td>
      <td>2.07</td>
      <td>9</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Serenity</td>
      <td>13</td>
      <td>1.49</td>
      <td>9</td>
      <td>13.41</td>
    </tr>
  </tbody>
</table>
</div>



```python
# Most profitable Items 

df1=df.groupby(['Item_Name','Item_ID','Price']).count()



#df1=df1.sort_values('Age',ascending=False)



df1=df1.rename(columns = {'Age':'Purchase_Count'})

df1 = df1.drop(['Gender','SN'],1)

df1=df1.reset_index()

df1['Total Purchase Value']=df1['Purchase_Count']*df1['Price']

#print(df1)

df2=df1.sort_values('Total Purchase Value',ascending=False).head()

display(df2)







```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item_Name</th>
      <th>Item_ID</th>
      <th>Price</th>
      <th>Purchase_Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>115</th>
      <td>Retribution Axe</td>
      <td>34</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>135</th>
      <td>Spectral Diamond Doomblade</td>
      <td>115</td>
      <td>4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Orenmir</td>
      <td>32</td>
      <td>4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>127</th>
      <td>Singed Scalpel</td>
      <td>103</td>
      <td>4.87</td>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>137</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>107</td>
      <td>3.61</td>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>

