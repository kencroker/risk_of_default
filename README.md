# Analyzing Borrowers' Risk of Defaulting

In this project, I will prepare a report for a hypothetical bank’s loan division. I aim to find out if a customer’s marital status and number of children has an impact on whether they will default on a loan. Each row of the dataset has information on a particular borrower, including whether or not they defaulted.

Here is what each of the columns of the dataset means:
- **children**: the number of children in the family
- **days_employed**: how long the customer has been working
- **dob_years**: the customer’s age
- **education**: the customer’s education level
- **education_id**: identifier for the customer’s education
- **family_status**: the customer’s marital status
- **family_status_id**: identifier for the customer’s marital status
- **gender**: the customer’s gender
- **income_type**: the customer’s income type
- **debt**: whether the customer has defaulted on a loan
- **total_income**: monthly income
- **purpose**: reason for taking out a loan

## Opening the Data File


```python
import pandas as pd
try:
    df = pd.read_csv('credit_scoring_eng.csv')
except:
    df = pd.read_csv('/datasets/credit_scoring_eng.csv')
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-8437.673028</td>
      <td>42</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>40620.102</td>
      <td>purchase of the house</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-4024.803754</td>
      <td>36</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>17932.802</td>
      <td>car purchase</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>-5623.422610</td>
      <td>33</td>
      <td>Secondary Education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23341.752</td>
      <td>purchase of the house</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-4124.747207</td>
      <td>32</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>42820.568</td>
      <td>supplementary education</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>25378.572</td>
      <td>to have a wedding</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
     #   Column            Non-Null Count  Dtype  
    ---  ------            --------------  -----  
     0   children          21525 non-null  int64  
     1   days_employed     19351 non-null  float64
     2   dob_years         21525 non-null  int64  
     3   education         21525 non-null  object 
     4   education_id      21525 non-null  int64  
     5   family_status     21525 non-null  object 
     6   family_status_id  21525 non-null  int64  
     7   gender            21525 non-null  object 
     8   income_type       21525 non-null  object 
     9   debt              21525 non-null  int64  
     10  total_income      19351 non-null  float64
     11  purpose           21525 non-null  object 
    dtypes: float64(2), int64(5), object(5)
    memory usage: 2.0+ MB
    


```python
df.isna().sum()
```




    children               0
    days_employed       2174
    dob_years              0
    education              0
    education_id           0
    family_status          0
    family_status_id       0
    gender                 0
    income_type            0
    debt                   0
    total_income        2174
    purpose                0
    dtype: int64




```python
df.isna().sum() / len(df)
```




    children            0.000000
    days_employed       0.100999
    dob_years           0.000000
    education           0.000000
    education_id        0.000000
    family_status       0.000000
    family_status_id    0.000000
    gender              0.000000
    income_type         0.000000
    debt                0.000000
    total_income        0.100999
    purpose             0.000000
    dtype: float64



### Conclusion

Based on the output of df.info(), we have 2 columns with float values, 5 columns with integer values, and 5 columns with object (string) values. It seems like all variables (columns) have the correct data type for the values they represent (gender is an object, total_income is a float, etc).

Based on the output of df.isna().sum(), most columns are free of missing values. However, we still have 2174 missing values in the days_employed column and 2174 missing values in the total_income column. 2174 missing values accounts for about 10% of the total values in these columns. We will need to take care of these values.

## Data Preprocessing

### Processing missing values


```python
print(f'The median amount of income is {df.total_income.median()}.')
print(f'The mean amount of income is {df.total_income.mean()}.')
```

    The median amount of income is 23202.87.
    The mean amount of income is 26787.56835465871.
    

The mean and median incomes are pretty close together. That means outliers are not skewing the data too much. Just to be safe, I'll use the median rather than the mean.
I plan to take the missing total_income values and replace them with the median value of total_income.

Now I will look at the mean and median values for the days_employed column:


```python
print(f'The median number of days employed is {df.days_employed.median()}.')
print(f'The mean number of days employed is {df.days_employed.mean()}.')
```

    The median number of days employed is -1203.369528770489.
    The mean number of days employed is 63046.497661473615.
    

Wow! There's a HUGE difference between the mean and median. Maybe there's some prominent outliers, or perhaps another variable like income_type could be linked to the number of days employed.


```python
df['income_type'].value_counts()
```




    employee                       11119
    business                        5085
    retiree                         3856
    civil servant                   1459
    entrepreneur                       2
    unemployed                         2
    paternity / maternity leave        1
    student                            1
    Name: income_type, dtype: int64




```python
df[df.total_income.isna()]['income_type'].value_counts()
```




    employee         1105
    business          508
    retiree           413
    civil servant     147
    entrepreneur        1
    Name: income_type, dtype: int64



Let's group people by income_type to see if there's a link between income_type and days_employed.


```python
df.groupby('income_type').days_employed.count()
```




    income_type
    business                        4577
    civil servant                   1312
    employee                       10014
    entrepreneur                       1
    paternity / maternity leave        1
    retiree                         3443
    student                            1
    unemployed                         2
    Name: days_employed, dtype: int64




```python
df.groupby('income_type').days_employed.median()
```




    income_type
    business                        -1547.382223
    civil servant                   -2689.368353
    employee                        -1574.202821
    entrepreneur                     -520.848083
    paternity / maternity leave     -3296.759962
    retiree                        365213.306266
    student                          -578.751554
    unemployed                     366413.652744
    Name: days_employed, dtype: float64



I notice a huge difference between the median value of days_employed for retired individuals and the median value for non-retired individuals. 

**The median value for days_employed for most income types is negative, but it's positive for retired. I'm honestly not sure why this is. If a negative number represents the number of days since getting a job, then the negative values might make sense. But the median value of days_employed is 365213 days, or roughly 1000 years. I have never heard of someone who has lived 1000 years.** 

Let's deal with each group (retirees and non-retirees) individually.

### Conclusion

I plan to take the missing total_income values and replace them with the median value of total_income.


```python
median_income = df['total_income'].median()
df['total_income'] = df['total_income'].fillna(median_income)
```

The missing values in the total_income column have been replaced with the median value for the dataset.

I now plan to split the rows with missing days_employed values into two groups: the rows with retirees and the rows with non-retirees.
For the retirees, I'll fill in the missing values with the median value of days_employed for retired individuals.
For the non-retirees, I'll fill in the missing values witht the median value of days_employed for the non-retirees.


```python
retirees_emp = df[df.income_type == 'retiree']['days_employed'].median()
non_retirees_emp = df[df.income_type != 'retiree']['days_employed'].median()
```


```python
df.loc[(df.income_type == 'retiree') & (df.days_employed.isnull()), 'days_employed'] = retirees_emp
df.loc[(df.income_type != 'retiree') & (df.days_employed.isnull()), 'days_employed'] = non_retirees_emp
```

Let's check that our changes actually worked.


```python
df.isna().sum()
```




    children            0
    days_employed       0
    dob_years           0
    education           0
    education_id        0
    family_status       0
    family_status_id    0
    gender              0
    income_type         0
    debt                0
    total_income        0
    purpose             0
    dtype: int64



No more missing values! Hooray!

### Data type replacement


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
     #   Column            Non-Null Count  Dtype  
    ---  ------            --------------  -----  
     0   children          21525 non-null  int64  
     1   days_employed     21525 non-null  float64
     2   dob_years         21525 non-null  int64  
     3   education         21525 non-null  object 
     4   education_id      21525 non-null  int64  
     5   family_status     21525 non-null  object 
     6   family_status_id  21525 non-null  int64  
     7   gender            21525 non-null  object 
     8   income_type       21525 non-null  object 
     9   debt              21525 non-null  int64  
     10  total_income      21525 non-null  float64
     11  purpose           21525 non-null  object 
    dtypes: float64(2), int64(5), object(5)
    memory usage: 2.0+ MB
    

It seems like all the columns have the appropriate data type except for the days_employed and income column.
These columns contain float values, but these values should be converted to integers.

I will pass the argument 'int' into the astype() method to do this:


```python
df['days_employed'] = df['days_employed'].astype('int')
df['total_income'] = df['total_income'].astype('int')

df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
     #   Column            Non-Null Count  Dtype 
    ---  ------            --------------  ----- 
     0   children          21525 non-null  int64 
     1   days_employed     21525 non-null  int32 
     2   dob_years         21525 non-null  int64 
     3   education         21525 non-null  object
     4   education_id      21525 non-null  int64 
     5   family_status     21525 non-null  object
     6   family_status_id  21525 non-null  int64 
     7   gender            21525 non-null  object
     8   income_type       21525 non-null  object
     9   debt              21525 non-null  int64 
     10  total_income      21525 non-null  int32 
     11  purpose           21525 non-null  object
    dtypes: int32(2), int64(5), object(5)
    memory usage: 1.8+ MB
    

Everything worked! Now the dataset contains 7 integer valued columns and 5 object valued columns.

I can save some extra storage space by converting some of the columns from int64 to int8.
The children, education_id, family_status_id, and debt columns all store int64 values. But I don't actually need all 64 bits. The values are simple enough that we could store them using 8 bits each. So I will convert them to the int8 data type.


```python
df['children'] = df['children'].astype('int8')
df['education_id'] = df['education_id'].astype('int8')
df['family_status_id'] = df['family_status_id'].astype('int8')
df['debt'] = df['debt'].astype('int8')
```

Okay, now let's check the data types again:


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
     #   Column            Non-Null Count  Dtype 
    ---  ------            --------------  ----- 
     0   children          21525 non-null  int8  
     1   days_employed     21525 non-null  int32 
     2   dob_years         21525 non-null  int64 
     3   education         21525 non-null  object
     4   education_id      21525 non-null  int8  
     5   family_status     21525 non-null  object
     6   family_status_id  21525 non-null  int8  
     7   gender            21525 non-null  object
     8   income_type       21525 non-null  object
     9   debt              21525 non-null  int8  
     10  total_income      21525 non-null  int32 
     11  purpose           21525 non-null  object
    dtypes: int32(2), int64(1), int8(4), object(5)
    memory usage: 1.2+ MB
    

It worked!

### Conclusion

Great! Now we have 7 integer valued columns and 5 object (string) valued columns.
It looks like we've converted each column to its appropriate data type.

### Processing duplicates

Our dataframe might contain duplicate rows. Duplicates can arise if the borrowers accidentally submit a form twice, or a form gets submitted twice in a database.

Let's use the duplicated() method to see how many duplicated rows we have:


```python
df.duplicated().sum()
```




    54



Wow! That's a lot of duplicate rows! Let's see which rows they are.


```python
df[df.duplicated()]
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2849</th>
      <td>0</td>
      <td>-1629</td>
      <td>41</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>purchase of the house for my family</td>
    </tr>
    <tr>
      <th>4182</th>
      <td>1</td>
      <td>-1629</td>
      <td>34</td>
      <td>BACHELOR'S DEGREE</td>
      <td>0</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>wedding ceremony</td>
    </tr>
    <tr>
      <th>4851</th>
      <td>0</td>
      <td>365213</td>
      <td>60</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>wedding ceremony</td>
    </tr>
    <tr>
      <th>5557</th>
      <td>0</td>
      <td>365213</td>
      <td>58</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>to have a wedding</td>
    </tr>
    <tr>
      <th>7808</th>
      <td>0</td>
      <td>365213</td>
      <td>57</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>having a wedding</td>
    </tr>
    <tr>
      <th>8583</th>
      <td>0</td>
      <td>365213</td>
      <td>58</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>unmarried</td>
      <td>4</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>supplementary education</td>
    </tr>
    <tr>
      <th>9238</th>
      <td>2</td>
      <td>-1629</td>
      <td>34</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buying property for renting out</td>
    </tr>
    <tr>
      <th>9528</th>
      <td>0</td>
      <td>365213</td>
      <td>66</td>
      <td>secondary education</td>
      <td>1</td>
      <td>widow / widower</td>
      <td>2</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>transactions with my real estate</td>
    </tr>
    <tr>
      <th>9627</th>
      <td>0</td>
      <td>365213</td>
      <td>56</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>transactions with my real estate</td>
    </tr>
    <tr>
      <th>10462</th>
      <td>0</td>
      <td>365213</td>
      <td>62</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>buy commercial real estate</td>
    </tr>
    <tr>
      <th>10697</th>
      <td>0</td>
      <td>-1629</td>
      <td>40</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>business</td>
      <td>0</td>
      <td>23202</td>
      <td>to have a wedding</td>
    </tr>
    <tr>
      <th>10864</th>
      <td>0</td>
      <td>365213</td>
      <td>62</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>10994</th>
      <td>0</td>
      <td>365213</td>
      <td>62</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>11791</th>
      <td>0</td>
      <td>-1629</td>
      <td>47</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>building a property</td>
    </tr>
    <tr>
      <th>12373</th>
      <td>0</td>
      <td>-1629</td>
      <td>58</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buy real estate</td>
    </tr>
    <tr>
      <th>12375</th>
      <td>1</td>
      <td>-1629</td>
      <td>37</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buy real estate</td>
    </tr>
    <tr>
      <th>12736</th>
      <td>0</td>
      <td>365213</td>
      <td>59</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>to become educated</td>
    </tr>
    <tr>
      <th>13025</th>
      <td>1</td>
      <td>-1629</td>
      <td>44</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>second-hand car purchase</td>
    </tr>
    <tr>
      <th>13639</th>
      <td>0</td>
      <td>365213</td>
      <td>64</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>car</td>
    </tr>
    <tr>
      <th>13773</th>
      <td>0</td>
      <td>-1629</td>
      <td>35</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>to have a wedding</td>
    </tr>
    <tr>
      <th>13878</th>
      <td>1</td>
      <td>-1629</td>
      <td>31</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>business</td>
      <td>0</td>
      <td>23202</td>
      <td>purchase of the house</td>
    </tr>
    <tr>
      <th>13942</th>
      <td>0</td>
      <td>-1629</td>
      <td>44</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>car purchase</td>
    </tr>
    <tr>
      <th>14432</th>
      <td>2</td>
      <td>-1629</td>
      <td>36</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>civil servant</td>
      <td>0</td>
      <td>23202</td>
      <td>getting an education</td>
    </tr>
    <tr>
      <th>14832</th>
      <td>0</td>
      <td>-1629</td>
      <td>50</td>
      <td>secondary education</td>
      <td>1</td>
      <td>unmarried</td>
      <td>4</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>property</td>
    </tr>
    <tr>
      <th>15091</th>
      <td>0</td>
      <td>-1629</td>
      <td>58</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>having a wedding</td>
    </tr>
    <tr>
      <th>15188</th>
      <td>0</td>
      <td>-1629</td>
      <td>60</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buy real estate</td>
    </tr>
    <tr>
      <th>15273</th>
      <td>0</td>
      <td>365213</td>
      <td>57</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>wedding ceremony</td>
    </tr>
    <tr>
      <th>16176</th>
      <td>0</td>
      <td>-1629</td>
      <td>47</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>16378</th>
      <td>0</td>
      <td>-1629</td>
      <td>46</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>university education</td>
    </tr>
    <tr>
      <th>16902</th>
      <td>2</td>
      <td>-1629</td>
      <td>39</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>to have a wedding</td>
    </tr>
    <tr>
      <th>16904</th>
      <td>1</td>
      <td>-1629</td>
      <td>32</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buying a second-hand car</td>
    </tr>
    <tr>
      <th>17379</th>
      <td>0</td>
      <td>-1629</td>
      <td>54</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>business</td>
      <td>0</td>
      <td>23202</td>
      <td>transactions with commercial real estate</td>
    </tr>
    <tr>
      <th>17755</th>
      <td>1</td>
      <td>-1629</td>
      <td>43</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>to become educated</td>
    </tr>
    <tr>
      <th>17774</th>
      <td>1</td>
      <td>-1629</td>
      <td>40</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>business</td>
      <td>0</td>
      <td>23202</td>
      <td>building a real estate</td>
    </tr>
    <tr>
      <th>18328</th>
      <td>0</td>
      <td>-1629</td>
      <td>29</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buy residential real estate</td>
    </tr>
    <tr>
      <th>18349</th>
      <td>1</td>
      <td>-1629</td>
      <td>30</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>civil servant</td>
      <td>0</td>
      <td>23202</td>
      <td>purchase of the house for my family</td>
    </tr>
    <tr>
      <th>18428</th>
      <td>0</td>
      <td>365213</td>
      <td>64</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>housing</td>
    </tr>
    <tr>
      <th>18521</th>
      <td>0</td>
      <td>-1629</td>
      <td>56</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>to have a wedding</td>
    </tr>
    <tr>
      <th>18563</th>
      <td>0</td>
      <td>-1629</td>
      <td>54</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>transactions with my real estate</td>
    </tr>
    <tr>
      <th>18755</th>
      <td>0</td>
      <td>365213</td>
      <td>58</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>to become educated</td>
    </tr>
    <tr>
      <th>19041</th>
      <td>0</td>
      <td>-1629</td>
      <td>56</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>having a wedding</td>
    </tr>
    <tr>
      <th>19184</th>
      <td>0</td>
      <td>-1629</td>
      <td>46</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>to own a car</td>
    </tr>
    <tr>
      <th>19321</th>
      <td>0</td>
      <td>-1629</td>
      <td>23</td>
      <td>secondary education</td>
      <td>1</td>
      <td>unmarried</td>
      <td>4</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>second-hand car purchase</td>
    </tr>
    <tr>
      <th>19387</th>
      <td>0</td>
      <td>-1629</td>
      <td>38</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>business</td>
      <td>0</td>
      <td>23202</td>
      <td>having a wedding</td>
    </tr>
    <tr>
      <th>19688</th>
      <td>0</td>
      <td>365213</td>
      <td>61</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>real estate transactions</td>
    </tr>
    <tr>
      <th>19832</th>
      <td>0</td>
      <td>-1629</td>
      <td>48</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>19946</th>
      <td>0</td>
      <td>-1629</td>
      <td>57</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>second-hand car purchase</td>
    </tr>
    <tr>
      <th>20116</th>
      <td>0</td>
      <td>365213</td>
      <td>57</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>M</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>wedding ceremony</td>
    </tr>
    <tr>
      <th>20165</th>
      <td>0</td>
      <td>-1629</td>
      <td>42</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>purchase of the house for my family</td>
    </tr>
    <tr>
      <th>20702</th>
      <td>0</td>
      <td>365213</td>
      <td>64</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>supplementary education</td>
    </tr>
    <tr>
      <th>21032</th>
      <td>0</td>
      <td>365213</td>
      <td>60</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>to become educated</td>
    </tr>
    <tr>
      <th>21132</th>
      <td>0</td>
      <td>-1629</td>
      <td>47</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>21281</th>
      <td>1</td>
      <td>-1629</td>
      <td>30</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>23202</td>
      <td>buy commercial real estate</td>
    </tr>
    <tr>
      <th>21415</th>
      <td>0</td>
      <td>365213</td>
      <td>54</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>23202</td>
      <td>housing transactions</td>
    </tr>
  </tbody>
</table>
</div>



Now let's use the drop_duplicates() method to drop these rows.


```python
df = df.drop_duplicates()
```

Let's check that our call to drop_duplicates() worked.


```python
df.duplicated().sum()
```




    0



### Conclusion

Duplicate rows have been dropped.

## Categorizing Data

I would like to find out if a borrower's ability to repay a loan is linked to their income or the amount of children they have.
Since the values in some columns are so spread out, analyzing them can be rather tricky.
Categorizing the diverse range of column values into a few distinct groups could make the analysis process much simpler and more effective.
So I will begin the process of categorization.

#### Age Categories

Let's categorize by age group. 


```python
df['dob_years'].describe()
```




    count    21471.000000
    mean        43.279074
    std         12.574291
    min          0.000000
    25%         33.000000
    50%         42.000000
    75%         53.000000
    max         75.000000
    Name: dob_years, dtype: float64




```python
df[df.dob_years < 18].head()
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>99</th>
      <td>0</td>
      <td>346541</td>
      <td>0</td>
      <td>Secondary Education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>11406</td>
      <td>car</td>
    </tr>
    <tr>
      <th>149</th>
      <td>0</td>
      <td>-2664</td>
      <td>0</td>
      <td>secondary education</td>
      <td>1</td>
      <td>divorced</td>
      <td>3</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>11228</td>
      <td>housing transactions</td>
    </tr>
    <tr>
      <th>270</th>
      <td>3</td>
      <td>-1872</td>
      <td>0</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>16346</td>
      <td>housing renovation</td>
    </tr>
    <tr>
      <th>578</th>
      <td>0</td>
      <td>397856</td>
      <td>0</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>15619</td>
      <td>construction of own property</td>
    </tr>
    <tr>
      <th>1040</th>
      <td>0</td>
      <td>-1158</td>
      <td>0</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>divorced</td>
      <td>3</td>
      <td>F</td>
      <td>business</td>
      <td>0</td>
      <td>48639</td>
      <td>to own a car</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df.dob_years <18]['gender'].value_counts()
```




    F    72
    M    29
    Name: gender, dtype: int64




```python
df['gender'].value_counts()
```




    F      14189
    M       7281
    XNA        1
    Name: gender, dtype: int64



The people with an age of 0 (the people who didn't enter their age) are much more likely to be women than the people in the general dataset. This may be because, in general, women are more hesitatnt to reveal their age.

This is an example of missing values that are Missing Not at Random. When we make our age groups, the "Age Undisclosed" people should have their own category.

I will start by making a function to help categorize the age values.


```python
def categorize_age(age):
    if age == 0:
        return "Undisclosed"
    elif age < 18:
        return "Minor who somehow got a loan"
    elif age < 30:
        return '18-29'
    elif age < 39:
        return '30-39'
    elif age < 49:
        return '40-49'
    elif age < 59:
        return '50-59'
    elif age < 69:
        return '60-69'
    else:
        return '70+'
```

To create the age_group column with these categories, I will use the apply() method and apply it to the dob_years column.


```python
df.loc[:, 'age_group'] = df.loc[:, 'dob_years'].apply(categorize_age)
```

Let's check to make sure that worked:


```python
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
      <th>age_group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-8437</td>
      <td>42</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>40620</td>
      <td>purchase of the house</td>
      <td>40-49</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-4024</td>
      <td>36</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>17932</td>
      <td>car purchase</td>
      <td>30-39</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>-5623</td>
      <td>33</td>
      <td>Secondary Education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23341</td>
      <td>purchase of the house</td>
      <td>30-39</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-4124</td>
      <td>32</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>42820</td>
      <td>supplementary education</td>
      <td>30-39</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266</td>
      <td>53</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>25378</td>
      <td>to have a wedding</td>
      <td>50-59</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['age_group'].value_counts()
```




    40-49          5422
    30-39          5091
    50-59          4727
    18-29          3181
    60-69          2693
    70+             256
    Undisclosed     101
    Name: age_group, dtype: int64



Great! It worked!

#### Income Categories

Now let's categorize borrowers by income. First, I will take a peek at the distribution of incomes:


```python
df.total_income.describe()
```




    count     21471.000000
    mean      26433.089097
    std       15677.424382
    min        3306.000000
    25%       17224.500000
    50%       23202.000000
    75%       31320.000000
    max      362496.000000
    Name: total_income, dtype: float64



Okay, great! The minimum income is 3306, so I don't need to worry about the "value of zero" problem that I dealt with when making age group categories.

I'll make my groups as follows:
- Less than 10,000
- 10,000 to 20,000
- 20,000 to 30,000
- 30,000 to 40,000
- 40,000 to 50,000
- 50,000 to 100,000
- 100,000+

I will start by making a function to help categorize the incomes.


```python
def categorize_income(income):
    if income < 20000:
        return 'Below 20,000'
    elif income < 30000:
        return 'From 20,000 to 30,000'
    elif income < 40000:
        return 'From 30,000 to 40,000'
    elif income < 50000:
        return 'From 40,000 to 50,000'
    elif income > 50000:
        return 'Over 50,000'
    else:
        return "Categorization Error"
```

To create the income_group column with these categories, I will use the apply() method and apply it to the total_income column.


```python
df.loc[:, 'income_group'] = df.loc[:, 'total_income'].apply(categorize_income)
```

Let's check to make sure that worked:


```python
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
      <th>age_group</th>
      <th>income_group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-8437</td>
      <td>42</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>40620</td>
      <td>purchase of the house</td>
      <td>40-49</td>
      <td>From 40,000 to 50,000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-4024</td>
      <td>36</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>17932</td>
      <td>car purchase</td>
      <td>30-39</td>
      <td>Below 20,000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>-5623</td>
      <td>33</td>
      <td>Secondary Education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23341</td>
      <td>purchase of the house</td>
      <td>30-39</td>
      <td>From 20,000 to 30,000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-4124</td>
      <td>32</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>42820</td>
      <td>supplementary education</td>
      <td>30-39</td>
      <td>From 40,000 to 50,000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266</td>
      <td>53</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>25378</td>
      <td>to have a wedding</td>
      <td>50-59</td>
      <td>From 20,000 to 30,000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['income_group'].value_counts()
```




    From 20,000 to 30,000    8183
    Below 20,000             7369
    From 30,000 to 40,000    3107
    From 40,000 to 50,000    1492
    Over 50,000              1320
    Name: income_group, dtype: int64



Great! It works!

#### Children Categories

Okay, now let's move on to categorizing the values in the children column. First, let's use value_counts() to see which values are in the column: 


```python
df['children'].value_counts()
```




     0     14107
     1      4809
     2      2052
     3       330
     20       76
    -1        47
     4        41
     5         9
    Name: children, dtype: int64



Most people have between 0 and 5 children. Seventy six people are listed as having 20 children. It's tempting to think this is some sort of input error. But such large families actually exist in real life, so I'll keep the "20 children" values as they are.

Some people are listed as having -1 children. Now that's impossible. This is most likely an input error. **I will make the assumption that these people intended to claim that they have 1 child. I will now use the loc method to edit the data accordingly**.


```python
df.loc[df.children == -1, 'children'] = 1
```

Let's check that this worked:


```python
df['children'].value_counts()
```




    0     14107
    1      4856
    2      2052
    3       330
    20       76
    4        41
    5         9
    Name: children, dtype: int64



Great! No negative children anymore. Now I will create the following categories for the children values:
- "No children"
- "1 to 2 children"
- "3+ children"

I will first write a function to help me create the categories.


```python
def categorize_children(children):
    if children == 0:
        return "No children"
    elif 0 < children <= 2:
        return "1 to 2 children"
    elif children >= 3:
        return "3+ children"
    else:
        return "Categorization Error"
```

Now I will create a children_group column by applying my function to the children column.


```python
df['children_group'] = df['children'].apply(categorize_children)
```

Let's check that the children_group column was made and that the value counts for this column make sense:


```python
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
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
      <th>age_group</th>
      <th>income_group</th>
      <th>children_group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-8437</td>
      <td>42</td>
      <td>bachelor's degree</td>
      <td>0</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>40620</td>
      <td>purchase of the house</td>
      <td>40-49</td>
      <td>From 40,000 to 50,000</td>
      <td>1 to 2 children</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-4024</td>
      <td>36</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>F</td>
      <td>employee</td>
      <td>0</td>
      <td>17932</td>
      <td>car purchase</td>
      <td>30-39</td>
      <td>Below 20,000</td>
      <td>1 to 2 children</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>-5623</td>
      <td>33</td>
      <td>Secondary Education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>23341</td>
      <td>purchase of the house</td>
      <td>30-39</td>
      <td>From 20,000 to 30,000</td>
      <td>No children</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-4124</td>
      <td>32</td>
      <td>secondary education</td>
      <td>1</td>
      <td>married</td>
      <td>0</td>
      <td>M</td>
      <td>employee</td>
      <td>0</td>
      <td>42820</td>
      <td>supplementary education</td>
      <td>30-39</td>
      <td>From 40,000 to 50,000</td>
      <td>3+ children</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266</td>
      <td>53</td>
      <td>secondary education</td>
      <td>1</td>
      <td>civil partnership</td>
      <td>1</td>
      <td>F</td>
      <td>retiree</td>
      <td>0</td>
      <td>25378</td>
      <td>to have a wedding</td>
      <td>50-59</td>
      <td>From 20,000 to 30,000</td>
      <td>No children</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['children_group'].value_counts()
```




    No children        14107
    1 to 2 children     6908
    3+ children          456
    Name: children_group, dtype: int64



Everything worked! Hooray!

#### Purpose Categories

Now the only column I still need to categorize is the purpose column. This column would be hard to categorize directly since all of the values are phrases of multiple words.
So I will first lemmatize those object (string) values into lists of individual root words.

I will create a column called 'reason' where I will store these lists.


```python
import nltk
from nltk.stem import WordNetLemmatizer

wordnet_lemma = WordNetLemmatizer()
```


```python
def lemmatize_purpose(purpose):
    purpose = purpose.split(' ')
    lemmas = [wordnet_lemma.lemmatize(word, pos='n') for word in purpose]
    return lemmas
```


```python
df['reason'] = df['purpose'].apply(lemmatize_purpose)
```

Let's check that the column was created:


```python
df['reason']
```




    0        [purchase, of, the, house]
    1                   [car, purchase]
    2        [purchase, of, the, house]
    3        [supplementary, education]
    4            [to, have, a, wedding]
                        ...            
    21520        [housing, transaction]
    21521        [purchase, of, a, car]
    21522                    [property]
    21523        [buying, my, own, car]
    21524             [to, buy, a, car]
    Name: reason, Length: 21471, dtype: object



I want to see what the most common words in the 'reason' column are. I will create a Counter object that counts each word and how frequently it appears. I will loop through each list in the 'reason' column and add it to the Counter using the update() function.


```python
import collections
from collections import Counter

reason_counts = Counter()
for reason_list in df['reason']:
    reason_counts.update(reason_list)

print(reason_counts)
```

    Counter({'a': 5117, 'real': 4466, 'estate': 4466, 'car': 4308, 'purchase': 3306, 'education': 3110, 'to': 3071, 'of': 2994, 'transaction': 2604, 'property': 2539, 'my': 2390, 'buy': 2361, 'wedding': 2335, 'own': 2239, 'housing': 1905, 'house': 1904, 'buying': 1635, 'commercial': 1312, 'for': 1290, 'the': 1284, 'with': 1277, 'building': 1244, 'second-hand': 964, 'university': 948, 'supplementary': 907, 'getting': 868, 'ceremony': 793, 'having': 773, 'have': 769, 'renting': 652, 'out': 652, 'family': 638, 'construction': 635, 'renovation': 607, 'residential': 606, 'going': 496, 'get': 447, 'an': 442, 'profile': 436, 'higher': 426, 'become': 408, 'educated': 408})
    

It seems like real estate, cars, education, weddings, building/construction/renovation, and housing/renting are the most common reasons for borrowing money. I want to categorize each borrower by the main reason they borrowed money. To do this, I will create a function that takes in the list in the 'reason' column and returns a category.


```python
def categorize_reason(reason_list):
    if 'wedding' in reason_list:
        return "Wedding"
    elif 'education' in reason_list or 'educated' in reason_list or 'university' in reason_list:
        return "Education"
    elif 'car' in reason_list:
        return "Car"
    elif 'construction' in reason_list or 'renovation' in reason_list or 'building' in reason_list:
        return "Construction and Renovation"
    elif 'commercial' in reason_list or 'renting' in reason_list:
        return "Commercial Real Estate"
    elif 'estate' in reason_list or 'property' in reason_list or 'house' in reason_list:
        return "Real Estate"
    elif 'housing' in reason_list:
        return "Other Housing"
    else:
        return "Category Undetermined"
```

Now I will create a column called 'reason_group' with the categories I just made by using apply() to apply the categorize_reason() function to the 'reason' column.


```python
df['reason_group'] = df['reason'].apply(categorize_reason)
```

Let's check that this worked:


```python
df['reason_group'].value_counts()
```




    Real Estate                    5066
    Car                            4308
    Education                      4014
    Construction and Renovation    2486
    Wedding                        2335
    Commercial Real Estate         1964
    Other Housing                  1298
    Name: reason_group, dtype: int64



The column was created, and all the borrowers were categorized by their reason for borrowing. Hooray!

### Conclusion

#### Age Categories

I have now sorted the ages in the dob_years column into age categories.

Along the way, I found a surprise: some values in the dob_years column were listed as being 0 years of age.
As far as I know, newborn babies can't just walk up to a bank and demand the banker give them a loan. (Well, maybe if they cried and made a really cute face, they could get the banker to give a few dollars out of sympathy).

What's far more likely is that some people didn't want to fill in a value for their age, so they left it blank. This blank value then somehow turned into a zero.
It's tempting to delete these values from the dataset or impute them with the median age.
But these values are Missing **NOT** at Random: It's well known that many women would rather not disclose their age. Indeed, the people in this dataset who did not disclose their age are **far** more likely to identify as women than the people in the general dataset.
So instead of imputing these values, I created my own category for these people called "Undisclosed".

After using the value_counts() function to peek at the age distributions, here's what I found:
- The most common age group is people in their 30s and 40s.
- Most borrowers are between the ages of 18 and 69. Only a handful (256) are over 70, and a few (101) refused to disclose their ages.


#### Income Categories

Then I repeated the categorization process for the income distributions. After using the value_counts() function to peek at the income distributions, I found that most people made an income between 10,000 and 30,000.

#### Children Categories

Most borrowers do not have children. Some had 1 or 2 children. Only a small minority of the borrowers had three or more children.

#### Purpose Categories

To categorize the strings in the 'purpose' column, I first split the strings into lists of individual words. I lemmatized the words (so that the words 'houses' and 'house' both get stored as 'house'), then created a column called 'reason' to store these lemmatized words.
Lastly, I categorized by the lemmatized words in the 'reason' column.

People usually chose to borrow money for one of the following reasons:
- Buying a house
- Renovating a house
- Buying a car
- Paying for education
- Paying for housing
- Paying for a wedding

## Answer these questions

- Is there a relation between having kids and repaying a loan on time?


```python
df.pivot_table(
    index = 'children_group',
    values = 'debt',
    aggfunc = 'mean'
)
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
      <th>debt</th>
    </tr>
    <tr>
      <th>children_group</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1 to 2 children</th>
      <td>0.092501</td>
    </tr>
    <tr>
      <th>3+ children</th>
      <td>0.085526</td>
    </tr>
    <tr>
      <th>No children</th>
      <td>0.075353</td>
    </tr>
  </tbody>
</table>
</div>



The rate of default is 7.5% for borrowers without children, 9.3% for borrowers with one to two children, and 8.6% for borrowers with three or more children.

**Conclusion: From the data, it appears that borrowers without children are more likely to pay back loans on time than people with children.**

### Conclusion

- Is there a relation between marital status and repaying a loan on time?

Note: The rate of default is the average of the values in the 'debt' column.

The unmarried have the highest rate of default at 9.8%. The odds of default slightly decrease to 9.3% for borrowers in civil partnerships.

The married only default 7.5% of the time, and the divorced only default 7.1% of the time.
Widows and widowers have the lowest rates of default, at just 6.6%.


```python
df.pivot_table(
    index = 'family_status',
    values = 'debt',
    aggfunc = 'mean'
)
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
      <th>debt</th>
    </tr>
    <tr>
      <th>family_status</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>civil partnership</th>
      <td>0.093202</td>
    </tr>
    <tr>
      <th>divorced</th>
      <td>0.071130</td>
    </tr>
    <tr>
      <th>married</th>
      <td>0.075421</td>
    </tr>
    <tr>
      <th>unmarried</th>
      <td>0.097509</td>
    </tr>
    <tr>
      <th>widow / widower</th>
      <td>0.065693</td>
    </tr>
  </tbody>
</table>
</div>



**Conclusion: Unmarried borrowers and borrowers in civil partnerships are the least likely to pay back loans on time. Widowed borrowers are the most likely to pay back loans on time.**

**Married and divored borrowers are more likely than unmarried borrowers but less likely than widowed borrowers to pay back loans on time.**

### Conclusion

- Is there a relation between income level and repaying a loan on time?


```python
df.pivot_table(
    index = 'income_group',
    values = 'debt',
    aggfunc = 'mean'
)
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
      <th>debt</th>
    </tr>
    <tr>
      <th>income_group</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Below 20,000</th>
      <td>0.082508</td>
    </tr>
    <tr>
      <th>From 20,000 to 30,000</th>
      <td>0.085177</td>
    </tr>
    <tr>
      <th>From 30,000 to 40,000</th>
      <td>0.077889</td>
    </tr>
    <tr>
      <th>From 40,000 to 50,000</th>
      <td>0.068365</td>
    </tr>
    <tr>
      <th>Over 50,000</th>
      <td>0.069697</td>
    </tr>
  </tbody>
</table>
</div>



For borrowers who make an income of less than 20,000 per year, the rate of default is 8.25%.
The rate of default steadily declines with increased income.
For borrowers who make over 50,000 per year, the rate of default is only 6.97%.

**Conclusion: People with higher incomes may be more likely to pay back loans on time.**

### Conclusion

- How do different loan purposes affect on-time repayment of the loan?

For most borrowers, the probability of defaulting on a loan is somewhere between 7% and 8%. But for borrowers who wish to buy a car or pay for higher education, the probability of defaulting rises above 9%. 


```python
df.pivot_table(
    index = 'reason_group',
    values = 'debt',
    aggfunc = 'mean'
)
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
      <th>debt</th>
    </tr>
    <tr>
      <th>reason_group</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Car</th>
      <td>0.093547</td>
    </tr>
    <tr>
      <th>Commercial Real Estate</th>
      <td>0.076884</td>
    </tr>
    <tr>
      <th>Construction and Renovation</th>
      <td>0.072003</td>
    </tr>
    <tr>
      <th>Education</th>
      <td>0.092177</td>
    </tr>
    <tr>
      <th>Other Housing</th>
      <td>0.072419</td>
    </tr>
    <tr>
      <th>Real Estate</th>
      <td>0.070667</td>
    </tr>
    <tr>
      <th>Wedding</th>
      <td>0.079657</td>
    </tr>
  </tbody>
</table>
</div>



**Conclusion: Borrowers who loan money to buy a car or pay for education _may_ be less likely than other borrowers to repay their loans on time.**

## General conclusion

From the data, it appears that borrowers without children are more likely to pay back loans on time than people with children.

Unmarried borrowers and borrowers in civil partnerships are the least likely to pay back loans on time. Widowed borrowers are the most likely to pay back loans on time.

Married and divored borrowers are more likely than unmarried borrowers but less likely than widowed borrowers to pay back loans on time.

People with higher incomes may be more likely to pay back loans on time.

Borrowers who loan money to buy a car or pay for education may be less likely than other borrowers to repay their loans on time.
