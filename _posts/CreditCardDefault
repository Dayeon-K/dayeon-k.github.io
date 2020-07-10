---
title: "Credit Card Default- Logit, Random Forest"
date: 2020-07-11 08:26:28 -0400
categories: MachineLearning Binary LogisticRegression RandomForest
---


# 1. Load Data

<i>Credit Card default data <br>
Source : https://www.kaggle.com/uciml/default-of-credit-card-clients-dataset

Description of Independent Variables
- ID: ID of each client
- LIMIT_BAL: Amount of given credit in NT dollars (includes individual and family/supplementary credit
- SEX: Gender (1=male, 2=female)
- EDUCATION: (1=graduate school, 2=university, 3=high school, 4=others, 5=unknown, 6=unknown)
- MARRIAGE: Marital status (1=married, 2=single, 3=others)
- AGE: Age in years
- PAY_0: Repayment status in September, 2005 (-1=pay duly, 1=payment delay for one month, 2=payment delay for two months, … 8=payment delay for eight months, 9=payment delay for nine months and above)
- PAY_2: Repayment status in August, 2005 (scale same as above)
- PAY_3: Repayment status in July, 2005 (scale same as above)
- PAY_4: Repayment status in June, 2005 (scale same as above)
- PAY_5: Repayment status in May, 2005 (scale same as above)
- PAY_6: Repayment status in April, 2005 (scale same as above)
- BILL_AMT1: Amount of bill statement in September, 2005 (NT dollar)
- BILL_AMT2: Amount of bill statement in August, 2005 (NT dollar)
- BILL_AMT3: Amount of bill statement in July, 2005 (NT dollar)
- BILL_AMT4: Amount of bill statement in June, 2005 (NT dollar)
- BILL_AMT5: Amount of bill statement in May, 2005 (NT dollar)
- BILL_AMT6: Amount of bill statement in April, 2005 (NT dollar)
- PAY_AMT1: Amount of previous payment in September, 2005 (NT dollar)
- PAY_AMT2: Amount of previous payment in August, 2005 (NT dollar)
- PAY_AMT3: Amount of previous payment in July, 2005 (NT dollar)
- PAY_AMT4: Amount of previous payment in June, 2005 (NT dollar)
- PAY_AMT5: Amount of previous payment in May, 2005 (NT dollar)
- PAY_AMT6: Amount of previous payment in April, 2005 (NT dollar)
---
Description of Dependent Variable
- default.payment.next.month: Default payment (1=yes, 0=no)


```python
import pandas as pd
pd.options.mode.chained_assignment = None
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
sns.set_style("whitegrid")
```


```python
data = pd.read_csv("UCI_Credit_card.csv")
data.head(10)
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
      <th>ID</th>
      <th>LIMIT_BAL</th>
      <th>SEX</th>
      <th>EDUCATION</th>
      <th>MARRIAGE</th>
      <th>AGE</th>
      <th>PAY_0</th>
      <th>PAY_2</th>
      <th>PAY_3</th>
      <th>PAY_4</th>
      <th>...</th>
      <th>BILL_AMT4</th>
      <th>BILL_AMT5</th>
      <th>BILL_AMT6</th>
      <th>PAY_AMT1</th>
      <th>PAY_AMT2</th>
      <th>PAY_AMT3</th>
      <th>PAY_AMT4</th>
      <th>PAY_AMT5</th>
      <th>PAY_AMT6</th>
      <th>default.payment.next.month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>20000.0</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>24</td>
      <td>2</td>
      <td>2</td>
      <td>-1</td>
      <td>-1</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>689.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>120000.0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>26</td>
      <td>-1</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>3272.0</td>
      <td>3455.0</td>
      <td>3261.0</td>
      <td>0.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>0.0</td>
      <td>2000.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>90000.0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>34</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>14331.0</td>
      <td>14948.0</td>
      <td>15549.0</td>
      <td>1518.0</td>
      <td>1500.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>5000.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>50000.0</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>37</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>28314.0</td>
      <td>28959.0</td>
      <td>29547.0</td>
      <td>2000.0</td>
      <td>2019.0</td>
      <td>1200.0</td>
      <td>1100.0</td>
      <td>1069.0</td>
      <td>1000.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>50000.0</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>57</td>
      <td>-1</td>
      <td>0</td>
      <td>-1</td>
      <td>0</td>
      <td>...</td>
      <td>20940.0</td>
      <td>19146.0</td>
      <td>19131.0</td>
      <td>2000.0</td>
      <td>36681.0</td>
      <td>10000.0</td>
      <td>9000.0</td>
      <td>689.0</td>
      <td>679.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>50000.0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>37</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>19394.0</td>
      <td>19619.0</td>
      <td>20024.0</td>
      <td>2500.0</td>
      <td>1815.0</td>
      <td>657.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>800.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>500000.0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>29</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>542653.0</td>
      <td>483003.0</td>
      <td>473944.0</td>
      <td>55000.0</td>
      <td>40000.0</td>
      <td>38000.0</td>
      <td>20239.0</td>
      <td>13750.0</td>
      <td>13770.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8</td>
      <td>100000.0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>23</td>
      <td>0</td>
      <td>-1</td>
      <td>-1</td>
      <td>0</td>
      <td>...</td>
      <td>221.0</td>
      <td>-159.0</td>
      <td>567.0</td>
      <td>380.0</td>
      <td>601.0</td>
      <td>0.0</td>
      <td>581.0</td>
      <td>1687.0</td>
      <td>1542.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>140000.0</td>
      <td>2</td>
      <td>3</td>
      <td>1</td>
      <td>28</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>...</td>
      <td>12211.0</td>
      <td>11793.0</td>
      <td>3719.0</td>
      <td>3329.0</td>
      <td>0.0</td>
      <td>432.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>20000.0</td>
      <td>1</td>
      <td>3</td>
      <td>2</td>
      <td>35</td>
      <td>-2</td>
      <td>-2</td>
      <td>-2</td>
      <td>-2</td>
      <td>...</td>
      <td>0.0</td>
      <td>13007.0</td>
      <td>13912.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>13007.0</td>
      <td>1122.0</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>10 rows × 25 columns</p>
</div>




```python
data.describe()
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
      <th>ID</th>
      <th>LIMIT_BAL</th>
      <th>SEX</th>
      <th>EDUCATION</th>
      <th>MARRIAGE</th>
      <th>AGE</th>
      <th>PAY_0</th>
      <th>PAY_2</th>
      <th>PAY_3</th>
      <th>PAY_4</th>
      <th>...</th>
      <th>BILL_AMT4</th>
      <th>BILL_AMT5</th>
      <th>BILL_AMT6</th>
      <th>PAY_AMT1</th>
      <th>PAY_AMT2</th>
      <th>PAY_AMT3</th>
      <th>PAY_AMT4</th>
      <th>PAY_AMT5</th>
      <th>PAY_AMT6</th>
      <th>default.payment.next.month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>...</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>3.000000e+04</td>
      <td>30000.00000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>15000.500000</td>
      <td>167484.322667</td>
      <td>1.603733</td>
      <td>1.853133</td>
      <td>1.551867</td>
      <td>35.485500</td>
      <td>-0.016700</td>
      <td>-0.133767</td>
      <td>-0.166200</td>
      <td>-0.220667</td>
      <td>...</td>
      <td>43262.948967</td>
      <td>40311.400967</td>
      <td>38871.760400</td>
      <td>5663.580500</td>
      <td>5.921163e+03</td>
      <td>5225.68150</td>
      <td>4826.076867</td>
      <td>4799.387633</td>
      <td>5215.502567</td>
      <td>0.221200</td>
    </tr>
    <tr>
      <th>std</th>
      <td>8660.398374</td>
      <td>129747.661567</td>
      <td>0.489129</td>
      <td>0.790349</td>
      <td>0.521970</td>
      <td>9.217904</td>
      <td>1.123802</td>
      <td>1.197186</td>
      <td>1.196868</td>
      <td>1.169139</td>
      <td>...</td>
      <td>64332.856134</td>
      <td>60797.155770</td>
      <td>59554.107537</td>
      <td>16563.280354</td>
      <td>2.304087e+04</td>
      <td>17606.96147</td>
      <td>15666.159744</td>
      <td>15278.305679</td>
      <td>17777.465775</td>
      <td>0.415062</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>10000.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>21.000000</td>
      <td>-2.000000</td>
      <td>-2.000000</td>
      <td>-2.000000</td>
      <td>-2.000000</td>
      <td>...</td>
      <td>-170000.000000</td>
      <td>-81334.000000</td>
      <td>-339603.000000</td>
      <td>0.000000</td>
      <td>0.000000e+00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7500.750000</td>
      <td>50000.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>28.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>...</td>
      <td>2326.750000</td>
      <td>1763.000000</td>
      <td>1256.000000</td>
      <td>1000.000000</td>
      <td>8.330000e+02</td>
      <td>390.00000</td>
      <td>296.000000</td>
      <td>252.500000</td>
      <td>117.750000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>15000.500000</td>
      <td>140000.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>34.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>19052.000000</td>
      <td>18104.500000</td>
      <td>17071.000000</td>
      <td>2100.000000</td>
      <td>2.009000e+03</td>
      <td>1800.00000</td>
      <td>1500.000000</td>
      <td>1500.000000</td>
      <td>1500.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>22500.250000</td>
      <td>240000.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>41.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>54506.000000</td>
      <td>50190.500000</td>
      <td>49198.250000</td>
      <td>5006.000000</td>
      <td>5.000000e+03</td>
      <td>4505.00000</td>
      <td>4013.250000</td>
      <td>4031.500000</td>
      <td>4000.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>30000.000000</td>
      <td>1000000.000000</td>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>3.000000</td>
      <td>79.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>...</td>
      <td>891586.000000</td>
      <td>927171.000000</td>
      <td>961664.000000</td>
      <td>873552.000000</td>
      <td>1.684259e+06</td>
      <td>896040.00000</td>
      <td>621000.000000</td>
      <td>426529.000000</td>
      <td>528666.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
<p>8 rows × 25 columns</p>
</div>




```python
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 30000 entries, 0 to 29999
    Data columns (total 25 columns):
     #   Column                      Non-Null Count  Dtype  
    ---  ------                      --------------  -----  
     0   ID                          30000 non-null  int64  
     1   LIMIT_BAL                   30000 non-null  float64
     2   SEX                         30000 non-null  int64  
     3   EDUCATION                   30000 non-null  int64  
     4   MARRIAGE                    30000 non-null  int64  
     5   AGE                         30000 non-null  int64  
     6   PAY_0                       30000 non-null  int64  
     7   PAY_2                       30000 non-null  int64  
     8   PAY_3                       30000 non-null  int64  
     9   PAY_4                       30000 non-null  int64  
     10  PAY_5                       30000 non-null  int64  
     11  PAY_6                       30000 non-null  int64  
     12  BILL_AMT1                   30000 non-null  float64
     13  BILL_AMT2                   30000 non-null  float64
     14  BILL_AMT3                   30000 non-null  float64
     15  BILL_AMT4                   30000 non-null  float64
     16  BILL_AMT5                   30000 non-null  float64
     17  BILL_AMT6                   30000 non-null  float64
     18  PAY_AMT1                    30000 non-null  float64
     19  PAY_AMT2                    30000 non-null  float64
     20  PAY_AMT3                    30000 non-null  float64
     21  PAY_AMT4                    30000 non-null  float64
     22  PAY_AMT5                    30000 non-null  float64
     23  PAY_AMT6                    30000 non-null  float64
     24  default.payment.next.month  30000 non-null  int64  
    dtypes: float64(13), int64(12)
    memory usage: 5.7 MB
    

> Can see that there are no NaN values, but there are some errors

# 2. EDA and Preprocessing

## 2-1. Dependent Variable


```python
sns.catplot(x="default.payment.next.month",kind="count",data=data,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c194c76d8>




![png](Default_files/Default_10_1.png)


The labels are quite imbalanced. We might consider oversampling but because oversampling changes the data structure and might increase bias, we will leave the data for now.

## 2-2. Predictors

### i) Limit Balance


```python
#for modifying the data
data_2 = data.copy()
```


```python
sns.distplot(a=data["LIMIT_BAL"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c19e8e518>




![png](Default_files/Default_15_1.png)


Skewed data -> Should Scale the data

<b> Plot Balance Limit by Dafault


```python
g = sns.FacetGrid(data,hue="default.payment.next.month",palette ="pastel",height=5,aspect=2)
g. map(sns.distplot,"LIMIT_BAL").add_legend()
```




    <seaborn.axisgrid.FacetGrid at 0x12c1b8dc4e0>




![png](Default_files/Default_18_1.png)


The overall distribution isn't very different for labels 0 and 1. However, for people with limit balance less than 100000, the probability of default increases.

### ii) Education


```python
sns.catplot(x="EDUCATION",kind="count",data=data,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c1ba0bba8>




![png](Default_files/Default_21_1.png)


0 values should not be included. 5 and 6 are also the same. Let's check how many 0,5 and 6 values there are.


```python
#0 Values
print('0 Values:{}, 5 values:{}, 6 values:{}'.format(len(data[data['EDUCATION'] == 0]),len(data[data['EDUCATION'] == 5]),len(data[data['EDUCATION'] == 6])))
```

    0 Values:14, 5 values:280, 6 values:51
    

Since there are approximately 1% of unknown values, replace them with the <i>mode, which is 2 


```python
data_2['EDUCATION'].replace(5,2,inplace=True)
```


```python
data_2['EDUCATION'].replace(6,2,inplace=True)
```


```python
data_2['EDUCATION'].replace(0,2,inplace=True)
```


```python
#Plot the modified data
sns.catplot(x="EDUCATION",kind="count",data=data_2,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c1b905e10>




![png](Default_files/Default_28_1.png)


<b> Plot Education Level by Default


```python
sns.countplot(x = 'EDUCATION', hue = 'default.payment.next.month', data = data_2, palette = 'pastel')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1f5ff898>




![png](Default_files/Default_30_1.png)


The proportion of default doesn't change significantly regarding education level.

### iii) Age


```python
sns.distplot(a=data["AGE"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1ce02550>




![png](Default_files/Default_33_1.png)


The distribution of age looks sensible, so leave it.

<b> Plot Age by Default


```python
sns.countplot(x = 'AGE', hue = 'default.payment.next.month', data = data_2, palette = 'pastel')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1e45ffd0>




![png](Default_files/Default_36_1.png)


The age variable doesn't seem to be significant in terms of default. 

### iv) Marriage

The marriage variable has both 0(unknown) and 3(other). 


```python
sns.catplot(x="MARRIAGE",kind="count",data=data,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c19881240>




![png](Default_files/Default_40_1.png)



```python
print('0 values:{}, 3 values:{}'.format(len(data[data['MARRIAGE'] == 0]),len(data[data['MARRIAGE'] == 3])))
```

    0 values:54, 3 values:323
    

Since there aren't many 0 or 3 values,<br>
consider unknown values as other. Replace 0 with 3.


```python
data_2['MARRIAGE'].loc[(data['MARRIAGE']==0)] = 3
```

<b> Plot Marriage Status by Default


```python
sns.countplot(x = 'MARRIAGE', hue = 'default.payment.next.month', data = data_2, palette = 'pastel')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1e1480f0>




![png](Default_files/Default_45_1.png)


There doesn't seem to be much significance in the case of marriage.

### v) Pay

The pay variables should take on values from -1 to 8. However, the data summary shows that there are values smaller than -1. <br>
Consider values smaller than 0 as duely payed and change all values smaller than 0 to 0.


```python
len(data[data['PAY_0'] <= 0])
```




    23182




```python
sns.catplot(x="PAY_0",kind="count",data=data,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c1ce70ac8>




![png](Default_files/Default_50_1.png)



```python
data_2['PAY_0'].loc[(data['PAY_0']<=0)] = 0
data_2['PAY_2'].loc[(data['PAY_2']<=0)] = 0
data_2['PAY_3'].loc[(data['PAY_3']<=0)] = 0
data_2['PAY_4'].loc[(data['PAY_4']<=0)] = 0
data_2['PAY_5'].loc[(data['PAY_5']<=0)] = 0
data_2['PAY_6'].loc[(data['PAY_6']<=0)] = 0
```

<b> Plot PAY_0 by default


```python
sns.countplot(x = 'PAY_0', hue = 'default.payment.next.month', data = data_2, palette = 'pastel')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1e109518>




![png](Default_files/Default_53_1.png)


If someone couldn't pay their bills the month before, by common sense, it seems highly likely that the person would default again.<br>
In this sense, 'PAY_0' variable intuitively seems very important.
<b> The graph above shows this.

### vi) Bill Amount

Bill amount should be positive values. However in the data, there are some negative values.


```python
sns.distplot(a=data["BILL_AMT1"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1dcbdf60>




![png](Default_files/Default_57_1.png)



```python
data_2['BILL_AMT1'].loc[(data['BILL_AMT1']<0)] = 0
data_2['BILL_AMT2'].loc[(data['BILL_AMT2']<0)] = 0
data_2['BILL_AMT3'].loc[(data['BILL_AMT3']<0)] = 0
data_2['BILL_AMT4'].loc[(data['BILL_AMT4']<0)] = 0
data_2['BILL_AMT5'].loc[(data['BILL_AMT5']<0)] = 0
data_2['BILL_AMT6'].loc[(data['BILL_AMT6']<0)] = 0
```

<b> Plot Bill Amount by Default


```python
g = sns.FacetGrid(data_2,hue="default.payment.next.month",palette ="pastel",height=5,aspect=2)
g. map(sns.distplot,"BILL_AMT1").add_legend()
```




    <seaborn.axisgrid.FacetGrid at 0x12c1f6cba20>




![png](Default_files/Default_60_1.png)


Bill_AMT variables needs further investigation.

### vii) New Var - Delayed

I thought that if a person ever failed to pay their bills, it's highly likely that the person might fail again.<br>
For this reason I made a new binary variable that tells if the person <b>defaulted in the past(=1) or not(=0).


```python
delayed = data_2['PAY_0'] +data_2['PAY_2'] + data_2['PAY_3'] +data_2['PAY_4'] + data_2['PAY_5'] + data_2['PAY_6']
delayed[delayed >0] = 1
```


```python
data_2['del'] = delayed
```


```python
sns.catplot(x="del",kind="count",data=data_2,palette="pastel")
```




    <seaborn.axisgrid.FacetGrid at 0x12c1ce70e10>




![png](Default_files/Default_66_1.png)


### vii) New Var - Mean Amount Delayed

I also thought that the amount delayed (or failed to pay) on average would affect the probability of default. <br>
I made a new variable by <b>averaging the delayed amount each month.


```python
# The  distribution of amount delayed
sns.distplot(data_2['BILL_AMT1']-data_2['PAY_AMT1'])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x12c1fb06860>




![png](Default_files/Default_69_1.png)


You can see that the amount delayed on September is mostly near 0


```python
left = (data['BILL_AMT1']-data['PAY_AMT1']+data['BILL_AMT2']-data['PAY_AMT2']+data['BILL_AMT3']-data['PAY_AMT3']+
data['BILL_AMT4']-data['PAY_AMT4']+data['BILL_AMT5']-data['PAY_AMT5']+data['BILL_AMT6']-data['PAY_AMT6'])/6
```

<b> Plot the Distribution of the new variable.


```python
data_2['left'] = left
```


```python
g = sns.FacetGrid(data_2,hue="default.payment.next.month",palette ="pastel",height=5,aspect=2)
g. map(sns.distplot,"left").add_legend()
g.set(xlim=(-50000,400000))
```




    <seaborn.axisgrid.FacetGrid at 0x12c0eb8d978>




![png](Default_files/Default_74_1.png)


The variable looks very significant in terms of default ratio. 

## 2-3. Modifying Data


```python
from sklearn.model_selection import train_test_split
import warnings
warnings.filterwarnings('ignore')

```


```python
data_2.describe()
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
      <th>ID</th>
      <th>LIMIT_BAL</th>
      <th>SEX</th>
      <th>EDUCATION</th>
      <th>MARRIAGE</th>
      <th>AGE</th>
      <th>PAY_0</th>
      <th>PAY_2</th>
      <th>PAY_3</th>
      <th>PAY_4</th>
      <th>...</th>
      <th>BILL_AMT6</th>
      <th>PAY_AMT1</th>
      <th>PAY_AMT2</th>
      <th>PAY_AMT3</th>
      <th>PAY_AMT4</th>
      <th>PAY_AMT5</th>
      <th>PAY_AMT6</th>
      <th>default.payment.next.month</th>
      <th>del</th>
      <th>left</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>...</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>3.000000e+04</td>
      <td>30000.00000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>15000.500000</td>
      <td>167484.322667</td>
      <td>1.603733</td>
      <td>1.819267</td>
      <td>1.557267</td>
      <td>35.485500</td>
      <td>0.356767</td>
      <td>0.320033</td>
      <td>0.304067</td>
      <td>0.258767</td>
      <td>...</td>
      <td>38942.268767</td>
      <td>5663.580500</td>
      <td>5.921163e+03</td>
      <td>5225.68150</td>
      <td>4826.076867</td>
      <td>4799.387633</td>
      <td>5215.502567</td>
      <td>0.221200</td>
      <td>0.335633</td>
      <td>39701.713106</td>
    </tr>
    <tr>
      <th>std</th>
      <td>8660.398374</td>
      <td>129747.661567</td>
      <td>0.489129</td>
      <td>0.707450</td>
      <td>0.521405</td>
      <td>9.217904</td>
      <td>0.760594</td>
      <td>0.801727</td>
      <td>0.790589</td>
      <td>0.761113</td>
      <td>...</td>
      <td>59445.970807</td>
      <td>16563.280354</td>
      <td>2.304087e+04</td>
      <td>17606.96147</td>
      <td>15666.159744</td>
      <td>15278.305679</td>
      <td>17777.465775</td>
      <td>0.415062</td>
      <td>0.472219</td>
      <td>60527.510499</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>10000.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>21.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000e+00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-445252.333333</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7500.750000</td>
      <td>50000.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>1256.000000</td>
      <td>1000.000000</td>
      <td>8.330000e+02</td>
      <td>390.00000</td>
      <td>296.000000</td>
      <td>252.500000</td>
      <td>117.750000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>753.458333</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>15000.500000</td>
      <td>140000.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>34.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>17071.000000</td>
      <td>2100.000000</td>
      <td>2.009000e+03</td>
      <td>1800.00000</td>
      <td>1500.000000</td>
      <td>1500.000000</td>
      <td>1500.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>16987.166667</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>22500.250000</td>
      <td>240000.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>41.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>49198.250000</td>
      <td>5006.000000</td>
      <td>5.000000e+03</td>
      <td>4505.00000</td>
      <td>4013.250000</td>
      <td>4031.500000</td>
      <td>4000.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>50952.958333</td>
    </tr>
    <tr>
      <th>max</th>
      <td>30000.000000</td>
      <td>1000000.000000</td>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>3.000000</td>
      <td>79.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>...</td>
      <td>961664.000000</td>
      <td>873552.000000</td>
      <td>1.684259e+06</td>
      <td>896040.00000</td>
      <td>621000.000000</td>
      <td>426529.000000</td>
      <td>528666.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>686013.333333</td>
    </tr>
  </tbody>
</table>
<p>8 rows × 27 columns</p>
</div>



### i) One Hot Encoding Cat. Var

Because Education and Marriage data are categorical, we will encode this into dummy variables.


```python
from sklearn.preprocessing import OneHotEncoder
encoder = OneHotEncoder()
```


```python
encoded = pd.DataFrame(encoder.fit_transform(data_2[['EDUCATION','MARRIAGE']]).toarray())
```


```python
data_2.drop(['ID','EDUCATION','MARRIAGE'],axis=1,inplace=True)
data_2 = data_2.join(encoded)
data_2.describe()
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
      <th>LIMIT_BAL</th>
      <th>SEX</th>
      <th>AGE</th>
      <th>PAY_0</th>
      <th>PAY_2</th>
      <th>PAY_3</th>
      <th>PAY_4</th>
      <th>PAY_5</th>
      <th>PAY_6</th>
      <th>BILL_AMT1</th>
      <th>...</th>
      <th>default.payment.next.month</th>
      <th>del</th>
      <th>left</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.00000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>...</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
      <td>30000.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>167484.322667</td>
      <td>1.603733</td>
      <td>35.485500</td>
      <td>0.356767</td>
      <td>0.320033</td>
      <td>0.304067</td>
      <td>0.258767</td>
      <td>0.22150</td>
      <td>0.226567</td>
      <td>51223.330900</td>
      <td>...</td>
      <td>0.221200</td>
      <td>0.335633</td>
      <td>39701.713106</td>
      <td>0.352833</td>
      <td>0.479167</td>
      <td>0.163900</td>
      <td>0.004100</td>
      <td>0.455300</td>
      <td>0.532133</td>
      <td>0.012567</td>
    </tr>
    <tr>
      <th>std</th>
      <td>129747.661567</td>
      <td>0.489129</td>
      <td>9.217904</td>
      <td>0.760594</td>
      <td>0.801727</td>
      <td>0.790589</td>
      <td>0.761113</td>
      <td>0.71772</td>
      <td>0.715438</td>
      <td>73635.860576</td>
      <td>...</td>
      <td>0.415062</td>
      <td>0.472219</td>
      <td>60527.510499</td>
      <td>0.477859</td>
      <td>0.499574</td>
      <td>0.370191</td>
      <td>0.063901</td>
      <td>0.498006</td>
      <td>0.498975</td>
      <td>0.111396</td>
    </tr>
    <tr>
      <th>min</th>
      <td>10000.000000</td>
      <td>1.000000</td>
      <td>21.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>-165580.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-445252.333333</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>50000.000000</td>
      <td>1.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>3558.750000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>753.458333</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>140000.000000</td>
      <td>2.000000</td>
      <td>34.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>22381.500000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>16987.166667</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>240000.000000</td>
      <td>2.000000</td>
      <td>41.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>67091.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>50952.958333</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1000000.000000</td>
      <td>2.000000</td>
      <td>79.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.000000</td>
      <td>8.00000</td>
      <td>8.000000</td>
      <td>964511.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>686013.333333</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
<p>8 rows × 31 columns</p>
</div>



### ii) Divide X,y


```python
test_data  = pd.DataFrame(data_2['default.payment.next.month'])
train_data = data_2.drop(['default.payment.next.month'],axis=1)
```


```python
train_data
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
      <th>LIMIT_BAL</th>
      <th>SEX</th>
      <th>AGE</th>
      <th>PAY_0</th>
      <th>PAY_2</th>
      <th>PAY_3</th>
      <th>PAY_4</th>
      <th>PAY_5</th>
      <th>PAY_6</th>
      <th>BILL_AMT1</th>
      <th>...</th>
      <th>PAY_AMT6</th>
      <th>del</th>
      <th>left</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20000.0</td>
      <td>2</td>
      <td>24</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3913.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1</td>
      <td>1169.166667</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>120000.0</td>
      <td>2</td>
      <td>26</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>2682.0</td>
      <td>...</td>
      <td>2000.0</td>
      <td>1</td>
      <td>2012.833333</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>90000.0</td>
      <td>2</td>
      <td>34</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>29239.0</td>
      <td>...</td>
      <td>5000.0</td>
      <td>0</td>
      <td>15105.833333</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>50000.0</td>
      <td>2</td>
      <td>37</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>46990.0</td>
      <td>...</td>
      <td>1000.0</td>
      <td>0</td>
      <td>37157.666667</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>50000.0</td>
      <td>1</td>
      <td>57</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>8617.0</td>
      <td>...</td>
      <td>679.0</td>
      <td>0</td>
      <td>8381.666667</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>29995</th>
      <td>220000.0</td>
      <td>1</td>
      <td>39</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>188948.0</td>
      <td>...</td>
      <td>1000.0</td>
      <td>0</td>
      <td>113799.833333</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>29996</th>
      <td>150000.0</td>
      <td>1</td>
      <td>43</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1683.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0</td>
      <td>1115.333333</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>29997</th>
      <td>30000.0</td>
      <td>1</td>
      <td>37</td>
      <td>4</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>3565.0</td>
      <td>...</td>
      <td>3100.0</td>
      <td>1</td>
      <td>6532.666667</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>29998</th>
      <td>80000.0</td>
      <td>1</td>
      <td>41</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-1645.0</td>
      <td>...</td>
      <td>1804.0</td>
      <td>1</td>
      <td>19905.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>29999</th>
      <td>50000.0</td>
      <td>1</td>
      <td>46</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>47929.0</td>
      <td>...</td>
      <td>1000.0</td>
      <td>0</td>
      <td>37094.333333</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>30000 rows × 30 columns</p>
</div>




```python
test_data
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
      <th>default.payment.next.month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>29995</th>
      <td>0</td>
    </tr>
    <tr>
      <th>29996</th>
      <td>0</td>
    </tr>
    <tr>
      <th>29997</th>
      <td>1</td>
    </tr>
    <tr>
      <th>29998</th>
      <td>1</td>
    </tr>
    <tr>
      <th>29999</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>30000 rows × 1 columns</p>
</div>



### iii) Standard Scaler


```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_scaled = scaler.fit_transform(train_data)
```


```python
X_train_scaled,X_test_scaled,y_train,y_test = train_test_split(X_scaled,test_data,test_size=0.2)
```

### iv) SMOTE

Over-Sample data using SMOTE


```python
from imblearn.over_sampling import SMOTE
smote = SMOTE()
X_train_over, y_train_over= smote.fit_sample(X_train_scaled,y_train)
X_train_over.shape, y_train_over.shape, pd.Series(y_train_over).value_counts()
```




    ((37392, 30), (37392,), 1    18696
     0    18696
     dtype: int64)



# 3. Modeling

 Logistic Regression and Random Forest were used in the modeling. <br>
The reason I chose only one black box model is to show an example and roughly compare the difference between regression and a blackbox model. 


```python
from sklearn.model_selection import KFold
from sklearn.metrics import accuracy_score,precision_score,recall_score,roc_auc_score
from sklearn.metrics import f1_score, confusion_matrix, precision_recall_curve, roc_curve
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import GridSearchCV

# a function for checking the results
def get_clf_eval(y_test,pred):
    confusion = confusion_matrix(y_test,pred)
    accuracy = accuracy_score(y_test,pred)
    precision = precision_score(y_test,pred)
    recall = recall_score(y_test,pred)
    f1 = f1_score(y_test,pred)
    print('confusion matrix')
    print(confusion)
    print('accuracy:{}, preicison:{}, recall:{},f1 score:{}'.format(accuracy,precision,recall,f1))

```

<b> Before Modeling, check the overall performance of Logistic Regression and Random Forest using 5-fold CV


```python
kfold = KFold(n_splits = 5)
cv_accuracy =[] 
lr_clf = LogisticRegression()
rf_clf = RandomForestClassifier()
```

<b> i) Logistic Regression


```python
n_iter = 0
for train_index,test_index in kfold.split(train_data):
    X_train, X_test = train_data.iloc[train_index],train_data.iloc[test_index]
    y_train, y_test = test_data.iloc[train_index],test_data.iloc[test_index]
    
    lr_clf.fit(X_train,y_train)
    pred = lr_clf.predict(X_test)
    n_iter+=1
    
    train_size = X_train.shape[0]
    test_size = X_test.shape[0]
    
    print('Train Size:',train_size,'Test Size',test_size)
    get_clf_eval(y_test,pred)

    
```

    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4687    0]
     [1313    0]]
    accuracy:0.7811666666666667, preicison:0.0, recall:0.0,f1 score:0.0
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4620    0]
     [1380    0]]
    accuracy:0.77, preicison:0.0, recall:0.0,f1 score:0.0
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4543    0]
     [1457    0]]
    accuracy:0.7571666666666667, preicison:0.0, recall:0.0,f1 score:0.0
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4780    0]
     [1218    2]]
    accuracy:0.797, preicison:1.0, recall:0.001639344262295082,f1 score:0.0032733224222585926
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4732    2]
     [1265    1]]
    accuracy:0.7888333333333334, preicison:0.3333333333333333, recall:0.0007898894154818325,f1 score:0.0015760441292356185
    

The overall performance of data (non-scaled, not oversampled-"Raw") is very bad. <br>
By observing the Confusion Matrix, it seems that the prediction was rather conservative, and thus we might need to <i>change the prediction threshold.

<b> ii) Random Forest


```python
n_iter = 0
for train_index,test_index in kfold.split(train_data):
    X_train, X_test = train_data.iloc[train_index],train_data.iloc[test_index]
    y_train, y_test = test_data.iloc[train_index],test_data.iloc[test_index]
    
    rf_clf.fit(X_train,y_train)
    pred = rf_clf.predict(X_test)
    n_iter+=1
    
    train_size = X_train.shape[0]
    test_size = X_test.shape[0]
    
    print('Train Size:',train_size,'Test Size',test_size)
 
    get_clf_eval(y_test,pred)

    
```

    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4383  304]
     [ 867  446]]
    accuracy:0.8048333333333333, preicison:0.5946666666666667, recall:0.3396801218583397,f1 score:0.4323800290838585
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4323  297]
     [ 897  483]]
    accuracy:0.801, preicison:0.6192307692307693, recall:0.35,f1 score:0.4472222222222222
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4261  282]
     [ 845  612]]
    accuracy:0.8121666666666667, preicison:0.6845637583892618, recall:0.4200411805078929,f1 score:0.5206295193534667
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4548  232]
     [ 759  461]]
    accuracy:0.8348333333333333, preicison:0.6652236652236653, recall:0.3778688524590164,f1 score:0.4819654992158912
    Train Size: 24000 Test Size 6000
    confusion matrix
    [[4518  216]
     [ 812  454]]
    accuracy:0.8286666666666667, preicison:0.6776119402985075, recall:0.358609794628752,f1 score:0.46900826446280997
    

The results of Random Forest seems better than Logistic Regression.

## 1) Logistic Regression

### i) Changing the threshold


```python
from sklearn.preprocessing import Binarizer
lr_clf = LogisticRegression()
lr_clf.fit(X_train,y_train)
pred_proba = lr_clf.predict_proba(X_test)
```


```python
thresholds = [0.30,0.40,0.50,0.60,0.70,0.80]

def get_eval_by_threshold(y_test,pred_proba_c1,thresholds):
    for custom_threshold in thresholds:
        binarizer = Binarizer(threshold=custom_threshold).fit(pred_proba_c1)
        custom_predict = binarizer.transform(pred_proba_c1)
        print("threshold:",custom_threshold)
        get_clf_eval(y_test,custom_predict)      
```

<b> With Raw Data


```python
get_eval_by_threshold(y_test,pred_proba[:,1].reshape(-1,1),thresholds)
```

    threshold: 0.3
    confusion matrix
    [[3005 1729]
     [ 493  773]]
    accuracy:0.6296666666666667, preicison:0.30895283772981613, recall:0.6105845181674565,f1 score:0.4102972399150743
    threshold: 0.4
    confusion matrix
    [[3998  736]
     [ 856  410]]
    accuracy:0.7346666666666667, preicison:0.35776614310645727, recall:0.32385466034755134,f1 score:0.33996683250414594
    threshold: 0.5
    confusion matrix
    [[4732    2]
     [1265    1]]
    accuracy:0.7888333333333334, preicison:0.3333333333333333, recall:0.0007898894154818325,f1 score:0.0015760441292356185
    threshold: 0.6
    confusion matrix
    [[4734    0]
     [1266    0]]
    accuracy:0.789, preicison:0.0, recall:0.0,f1 score:0.0
    threshold: 0.7
    confusion matrix
    [[4734    0]
     [1266    0]]
    accuracy:0.789, preicison:0.0, recall:0.0,f1 score:0.0
    threshold: 0.8
    confusion matrix
    [[4734    0]
     [1266    0]]
    accuracy:0.789, preicison:0.0, recall:0.0,f1 score:0.0
    


```python
#visualize
from sklearn.metrics import precision_recall_curve
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
%matplotlib inline
```


```python
def precision_recall_curve_plot(y_test,pred_proba_c1):
    precisions, recalls, thresholds = precision_recall_curve(y_test,pred_proba_c1)
    
    plt.figure(figsize=(8,6))
    thresholds_boundary = thresholds.shape[0]
    plt.plot(thresholds,precisions[0:thresholds_boundary],linestyle='--',label='precision')
    plt.plot(thresholds,recalls[0:thresholds_boundary],label='recall')
    
    #x-axis ticker 
    start,end = plt.xlim()
    plt.xticks(np.round(np.arange(start,end,0.1),2))
    
    #label,legend, grid
    plt.xlabel('Threshold value');plt.ylabel('Precision and Recall')
    plt.legend();plt.grid()
    plt.show()
    
precision_recall_curve_plot(y_test,lr_clf.predict_proba(X_test)[:,1])
```


![png](Default_files/Default_112_0.png)


Because the default case is a case where both precision and recall matters, we will select 0.40 as the threshold. However, both precision and recall are smaller than 0.4, which shows a very bad performance.

<b> With Scaled Data


```python
lr_clf_scaled = LogisticRegression()
lr_clf_scaled.fit(X_train_scaled,y_train)
pred_proba_scaled = lr_clf_scaled.predict_proba(X_test_scaled)
```


```python
get_eval_by_threshold(y_test,pred_proba_scaled[:,1].reshape(-1,1),thresholds)
```

    threshold: 0.3
    confusion matrix
    [[3976  675]
     [ 626  723]]
    accuracy:0.7831666666666667, preicison:0.5171673819742489, recall:0.535952557449963,f1 score:0.5263924281033855
    threshold: 0.4
    confusion matrix
    [[4294  357]
     [ 782  567]]
    accuracy:0.8101666666666667, preicison:0.6136363636363636, recall:0.42031134173461826,f1 score:0.49890013198416194
    threshold: 0.5
    confusion matrix
    [[4426  225]
     [ 889  460]]
    accuracy:0.8143333333333334, preicison:0.6715328467153284, recall:0.34099332839140106,f1 score:0.45231071779744353
    threshold: 0.6
    confusion matrix
    [[4540  111]
     [1074  275]]
    accuracy:0.8025, preicison:0.7124352331606217, recall:0.2038547071905115,f1 score:0.31700288184438047
    threshold: 0.7
    confusion matrix
    [[4600   51]
     [1232  117]]
    accuracy:0.7861666666666667, preicison:0.6964285714285714, recall:0.08673091178650852,f1 score:0.15425181278839814
    threshold: 0.8
    confusion matrix
    [[4631   20]
     [1297   52]]
    accuracy:0.7805, preicison:0.7222222222222222, recall:0.0385470719051149,f1 score:0.07318789584799437
    


```python
precision_recall_curve_plot(y_test,lr_clf_scaled.predict_proba(X_test_scaled)[:,1])
```


![png](Default_files/Default_117_0.png)



```python
get_eval_by_threshold(y_test,pred_proba_scaled[:,1].reshape(-1,1),thresholds=[0.30])
```

    threshold: 0.3
    confusion matrix
    [[3976  675]
     [ 626  723]]
    accuracy:0.7831666666666667, preicison:0.5171673819742489, recall:0.535952557449963,f1 score:0.5263924281033855
    

The best threshold would be 0.30. The performance improved in terms of both precision and recall, with an F1 score of 0.52.

<b> With Over Sampled Data


```python
lr_clf_over = LogisticRegression()
lr_clf_over.fit(X_train_over,y_train_over)
pred_proba_over = lr_clf_over.predict_proba(X_test_scaled)
```


```python
get_eval_by_threshold(y_test,pred_proba_over[:,1].reshape(-1,1),thresholds)
```

    threshold: 0.3
    confusion matrix
    [[1252 3399]
     [ 113 1236]]
    accuracy:0.4146666666666667, preicison:0.26666666666666666, recall:0.916234247590808,f1 score:0.4131016042780748
    threshold: 0.4
    confusion matrix
    [[3270 1381]
     [ 425  924]]
    accuracy:0.699, preicison:0.4008676789587852, recall:0.6849518161601186,f1 score:0.5057471264367815
    threshold: 0.5
    confusion matrix
    [[3699  952]
     [ 537  812]]
    accuracy:0.7518333333333334, preicison:0.4603174603174603, recall:0.6019273535952557,f1 score:0.5216832637327335
    threshold: 0.6
    confusion matrix
    [[4001  650]
     [ 634  715]]
    accuracy:0.786, preicison:0.5238095238095238, recall:0.5300222386953298,f1 score:0.5268975681650699
    threshold: 0.7
    confusion matrix
    [[4299  352]
     [ 780  569]]
    accuracy:0.8113333333333334, preicison:0.6178067318132465, recall:0.4217939214232765,f1 score:0.5013215859030837
    threshold: 0.8
    confusion matrix
    [[4465  186]
     [ 926  423]]
    accuracy:0.8146666666666667, preicison:0.6945812807881774, recall:0.31356560415122314,f1 score:0.43207354443309504
    


```python
precision_recall_curve_plot(y_test,lr_clf_over.predict_proba(X_test_scaled)[:,1])
```


![png](Default_files/Default_123_0.png)



```python
get_eval_by_threshold(y_test,pred_proba_over[:,1].reshape(-1,1),thresholds=[0.60])
```

    threshold: 0.6
    confusion matrix
    [[4001  650]
     [ 634  715]]
    accuracy:0.786, preicison:0.5238095238095238, recall:0.5300222386953298,f1 score:0.5268975681650699
    

The threshold became less conservative in the case of over-sampled data. However, the overall performance didn't change significantly. This shows that changing the data structure might not increase the performance of the model. Rather, it can be dangerous in terms of model biasness.

### ii) Interpretation of Model


```python
lr_clf_over.coef_
```




    array([[-0.17246633, -0.07386856,  0.01898566,  0.54807425,  0.04942481,
             0.04073617,  0.04604042,  0.09887402,  0.04257817, -0.25285019,
             0.24089041,  0.16359922, -0.01315442, -0.23453431,  0.14085813,
            -0.20803231, -0.37235934, -0.09927459, -0.00542634, -0.09022053,
            -0.05024187,  0.24590171,  0.05310215,  0.00612528, -0.00363965,
             0.00503445, -0.04651669,  0.04208886, -0.03948856, -0.01128152]])



In the case of Logistic Regression, we can get the coefficients and thus can interpret how much infuence each variable has on the outcome. 

## 2) Random Forest

### i) Hyperparameter Tuning

Grid Search to find best hyperparameters.

<b> With the Raw Model


```python
rf_clf = RandomForestClassifier()

params = {
    'n_estimators':[300],
    'max_depth':[20,25],
    'min_samples_split':[2,5]
}

grid_cv = GridSearchCV(rf_clf,param_grid=params,n_jobs=-1,scoring='f1')
grid_cv.fit(X_train_scaled,y_train)
```




    GridSearchCV(cv=None, error_score=nan,
                 estimator=RandomForestClassifier(bootstrap=True, ccp_alpha=0.0,
                                                  class_weight=None,
                                                  criterion='gini', max_depth=None,
                                                  max_features='auto',
                                                  max_leaf_nodes=None,
                                                  max_samples=None,
                                                  min_impurity_decrease=0.0,
                                                  min_impurity_split=None,
                                                  min_samples_leaf=1,
                                                  min_samples_split=2,
                                                  min_weight_fraction_leaf=0.0,
                                                  n_estimators=100, n_jobs=None,
                                                  oob_score=False,
                                                  random_state=None, verbose=0,
                                                  warm_start=False),
                 iid='deprecated', n_jobs=-1,
                 param_grid={'max_depth': [20, 25], 'min_samples_split': [2, 5],
                             'n_estimators': [300]},
                 pre_dispatch='2*n_jobs', refit=True, return_train_score=False,
                 scoring='f1', verbose=0)



Use the best hyperparameters.


```python
grid_cv.best_params_, grid_cv.best_score_
```




    ({'max_depth': 20, 'min_samples_split': 2, 'n_estimators': 300},
     0.4793279143222152)




```python
rf_clf_best = RandomForestClassifier(n_estimators=600,max_depth=20,min_samples_split=2)
rf_clf_best.fit(X_train_scaled,y_train)
pred = rf_clf_best.predict(X_test_scaled)
get_clf_eval(y_test,pred)
```

    confusion matrix
    [[4400  251]
     [ 874  475]]
    accuracy:0.8125, preicison:0.6542699724517906, recall:0.352112676056338,f1 score:0.4578313253012048
    

The performance doesn't seem to be very good. 

<b> With the Over-Sampled Model


```python
grid_cv_over = GridSearchCV(rf_clf,param_grid=params,n_jobs=-1,scoring='f1')
grid_cv_over.fit(X_train_over,y_train_over)
grid_cv_over.best_params_, grid_cv_over.best_score_
```




    ({'max_depth': 25, 'min_samples_split': 2, 'n_estimators': 300},
     0.8452617962143469)




```python
rf_clf_over_best = RandomForestClassifier(n_estimators=300,max_depth=25,min_samples_split=2)
rf_clf_over_best.fit(X_train_over,y_train_over)
pred_over = rf_clf_over_best.predict(X_test_scaled)
get_clf_eval(y_test,pred_over)
```

    confusion matrix
    [[4516  135]
     [ 160 1189]]
    accuracy:0.9508333333333333, preicison:0.898036253776435, recall:0.8813936249073387,f1 score:0.8896371118593341
    

The F1 Score increased to 0.8896 and the accuracy increased to 0.95.
 <b>Compared to Logistic Regression, the performance is much better.

### ii) Model Interpretation

Although Random Forest (or other Blackbox models) might be outstanding in terms of performance, it is difficult to interpret the model.
We can only roughly interpret the model by using a <b>Feature Importance Plot.

<b> Top 20 Features


```python

ftr_importances_values = rf_clf_over_best.feature_importances_
ftr_importance = pd.Series(ftr_importances_values,index=X_train.columns)
ftr_top20 = ftr_importance.sort_values(ascending=False)[:20]

plt.figure(figsize=(8,6))
plt.title("Feature Importance Top 20")
sns.barplot(x=ftr_top20,y=ftr_top20.index)
plt.show()

```


![png](Default_files/Default_145_0.png)


<b> Bottom 10 Features


```python
ftr_low10 = ftr_importance.sort_values(ascending=True)[:10]

plt.figure(figsize=(8,6))
plt.title("Feature Importance Lowest 10")
sns.barplot(x=ftr_low10,y=ftr_low10.index)
plt.show()
```


![png](Default_files/Default_147_0.png)


As shown in EDA, variables such as PAY_0 and LIMIT_BAL came out to be important. <br>
However, some variables like AGE weren't expected. <br>
<b> Variable Imprtance Plots should only be used for a rough understanding for the model.
It's not highly trustable b/c it comes out rather "random" and can change depending on the random state.
