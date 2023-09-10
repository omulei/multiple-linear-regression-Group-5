**Project Title:**  

*Optimizing Real Estate Strategies in King County: A Data-Driven Approach for Haven-Kings Property Management*

**By:**  
                - [Julliet Iswana](https://github.com/Iswana-O)
                - [Wayne Kipngeno Korir](https://github.com/waynekipngeno)
                - [Eva Kiio](https://github.com/evamwende)
                - [Oscar Mulei](https://github.com/omulei)
                
---------------------
   **Supervisor:** 
   [Asha Deen](https://moringaschool.com/courses/data-science-course-part-time/)
   
---------------------
                
<img src="Images/Country%20Home.png" width="950" height="500">


**Business Case:**
In the thriving real estate market of King County, the capacity to make strategic pricing decisions can either enhance profits or turn assets into liabilities. Haven-Kings Property Management, with its vast portfolio, stands at a pivotal juncture where conventional pricing methods no longer suffice. The digital age demands a shift from age-old practices like the Comparative Market Analysis (CMA) and the "1% Rule" to a more agile, data-driven approach. By leveraging advanced analytical methods, such as multiple regression analysis, property management firms can tap into precise pricing strategies, enhancing revenue streams and ensuring sustainable growth.

**Overview:**
This project aims to harness the power of data analytics to revolutionize Haven-Kings Property Management's pricing strategies in King County's competitive real estate landscape.

**Objective:**
Guided by data insights and multiple regression analysis, the team sets out to:
1. **Optimize House Pricing:** Craft a model that tailors pricing based on property features, regional nuances, and emerging market trends.

2. **Dynamic Pricing Recommendations:** Pivot rental prices in real-time, aligning with market fluctuations and property specifics.

**Research Questions:**
1. **House Pricing:** 
   - Dissect key determinants shaping house prices in King County.
   - Analyze the interplay between property attributes and their market value.
   - Appraise the precision of the regression model in predicting property prices.
     
2. **Dynamic Pricing Recommendations:**
   - Construct a robust framework for real-time pricing using multiple regression analysis.
   - Spotlight pivotal factors for adaptive rental rate revisions.
   - Assess the ramifications of dynamic pricing on revenue trajectories and market stature.

**Data Source:**
The foundation of this project rests on the [kc_house_data.csv](./data/kc_house_data.csv) dataset from King County House Sales.

---

**King County House Sales Dataset Summary**:
- **Time Period**: Sales from May 2014 to May 2015.
- **Data Points**: 21,613 houses.
- **Features**: Total of 20, not including the price.
- **Main Features Categories**:
  - **Size & Structure**: Includes attributes like number of bedrooms, bathrooms, living area size, etc.
  - **Location & View**: Covers waterfront status, views, and geographical details.
  - **Quality & Condition**: Grades and condition ratings.
  - **Age & Renovations**: Information about when built and if/when renovated.
  - **Recent Renovations**: Data about the size of living area and lot from the last 15 years.

- **General Insights**: House price is mainly driven by factors like number of bedrooms, living area size, and location. 
- **Analysis Approach**: Starts with data loading and exploration, followed by visualization and regression analyses.
---


```python
# libraries for numerical
import pandas as pd
import numpy as np

# libraries for visualization
import seaborn as sns
import matplotlib.pyplot as plt

# to plot the diagrams within the cells
%matplotlib inline

# libraries for machine learning
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split, cross_val_score, KFold
from sklearn.metrics import mean_squared_error, r2_score
import statsmodels.api as sm
import statsmodels.stats.api as sms
import statsmodels.formula.api as smf
from statsmodels.stats.outliers_influence import variance_inflation_factor
import scipy.stats as stats
from scipy.stats import pearsonr
from itertools import combinations


import warnings
warnings.filterwarnings('ignore')

```


```python
#Import and preview the data
df = pd.read_csv("data/kc_house_data.csv")
display(df.info())
df.head()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21597 entries, 0 to 21596
    Data columns (total 21 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   id             21597 non-null  int64  
     1   date           21597 non-null  object 
     2   price          21597 non-null  float64
     3   bedrooms       21597 non-null  int64  
     4   bathrooms      21597 non-null  float64
     5   sqft_living    21597 non-null  int64  
     6   sqft_lot       21597 non-null  int64  
     7   floors         21597 non-null  float64
     8   waterfront     19221 non-null  object 
     9   view           21534 non-null  object 
     10  condition      21597 non-null  object 
     11  grade          21597 non-null  object 
     12  sqft_above     21597 non-null  int64  
     13  sqft_basement  21597 non-null  object 
     14  yr_built       21597 non-null  int64  
     15  yr_renovated   17755 non-null  float64
     16  zipcode        21597 non-null  int64  
     17  lat            21597 non-null  float64
     18  long           21597 non-null  float64
     19  sqft_living15  21597 non-null  int64  
     20  sqft_lot15     21597 non-null  int64  
    dtypes: float64(6), int64(9), object(6)
    memory usage: 3.5+ MB
    


    None





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
      <th>id</th>
      <th>date</th>
      <th>price</th>
      <th>bedrooms</th>
      <th>bathrooms</th>
      <th>sqft_living</th>
      <th>sqft_lot</th>
      <th>floors</th>
      <th>waterfront</th>
      <th>view</th>
      <th>...</th>
      <th>grade</th>
      <th>sqft_above</th>
      <th>sqft_basement</th>
      <th>yr_built</th>
      <th>yr_renovated</th>
      <th>zipcode</th>
      <th>lat</th>
      <th>long</th>
      <th>sqft_living15</th>
      <th>sqft_lot15</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7129300520</td>
      <td>10/13/2014</td>
      <td>221900.0</td>
      <td>3</td>
      <td>1.00</td>
      <td>1180</td>
      <td>5650</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NONE</td>
      <td>...</td>
      <td>7 Average</td>
      <td>1180</td>
      <td>0.0</td>
      <td>1955</td>
      <td>0.0</td>
      <td>98178</td>
      <td>47.5112</td>
      <td>-122.257</td>
      <td>1340</td>
      <td>5650</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6414100192</td>
      <td>12/9/2014</td>
      <td>538000.0</td>
      <td>3</td>
      <td>2.25</td>
      <td>2570</td>
      <td>7242</td>
      <td>2.0</td>
      <td>NO</td>
      <td>NONE</td>
      <td>...</td>
      <td>7 Average</td>
      <td>2170</td>
      <td>400.0</td>
      <td>1951</td>
      <td>1991.0</td>
      <td>98125</td>
      <td>47.7210</td>
      <td>-122.319</td>
      <td>1690</td>
      <td>7639</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5631500400</td>
      <td>2/25/2015</td>
      <td>180000.0</td>
      <td>2</td>
      <td>1.00</td>
      <td>770</td>
      <td>10000</td>
      <td>1.0</td>
      <td>NO</td>
      <td>NONE</td>
      <td>...</td>
      <td>6 Low Average</td>
      <td>770</td>
      <td>0.0</td>
      <td>1933</td>
      <td>NaN</td>
      <td>98028</td>
      <td>47.7379</td>
      <td>-122.233</td>
      <td>2720</td>
      <td>8062</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2487200875</td>
      <td>12/9/2014</td>
      <td>604000.0</td>
      <td>4</td>
      <td>3.00</td>
      <td>1960</td>
      <td>5000</td>
      <td>1.0</td>
      <td>NO</td>
      <td>NONE</td>
      <td>...</td>
      <td>7 Average</td>
      <td>1050</td>
      <td>910.0</td>
      <td>1965</td>
      <td>0.0</td>
      <td>98136</td>
      <td>47.5208</td>
      <td>-122.393</td>
      <td>1360</td>
      <td>5000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1954400510</td>
      <td>2/18/2015</td>
      <td>510000.0</td>
      <td>3</td>
      <td>2.00</td>
      <td>1680</td>
      <td>8080</td>
      <td>1.0</td>
      <td>NO</td>
      <td>NONE</td>
      <td>...</td>
      <td>8 Good</td>
      <td>1680</td>
      <td>0.0</td>
      <td>1987</td>
      <td>0.0</td>
      <td>98074</td>
      <td>47.6168</td>
      <td>-122.045</td>
      <td>1800</td>
      <td>7503</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 21 columns</p>
</div>




```python
# Summary statistics for the numerical columns
df.describe()
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
      <th>id</th>
      <th>price</th>
      <th>bedrooms</th>
      <th>bathrooms</th>
      <th>sqft_living</th>
      <th>sqft_lot</th>
      <th>floors</th>
      <th>sqft_above</th>
      <th>yr_built</th>
      <th>yr_renovated</th>
      <th>zipcode</th>
      <th>lat</th>
      <th>long</th>
      <th>sqft_living15</th>
      <th>sqft_lot15</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>2.159700e+04</td>
      <td>2.159700e+04</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>2.159700e+04</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>17755.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
      <td>21597.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>4.580474e+09</td>
      <td>5.402966e+05</td>
      <td>3.373200</td>
      <td>2.115826</td>
      <td>2080.321850</td>
      <td>1.509941e+04</td>
      <td>1.494096</td>
      <td>1788.596842</td>
      <td>1970.999676</td>
      <td>83.636778</td>
      <td>98077.951845</td>
      <td>47.560093</td>
      <td>-122.213982</td>
      <td>1986.620318</td>
      <td>12758.283512</td>
    </tr>
    <tr>
      <th>std</th>
      <td>2.876736e+09</td>
      <td>3.673681e+05</td>
      <td>0.926299</td>
      <td>0.768984</td>
      <td>918.106125</td>
      <td>4.141264e+04</td>
      <td>0.539683</td>
      <td>827.759761</td>
      <td>29.375234</td>
      <td>399.946414</td>
      <td>53.513072</td>
      <td>0.138552</td>
      <td>0.140724</td>
      <td>685.230472</td>
      <td>27274.441950</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000102e+06</td>
      <td>7.800000e+04</td>
      <td>1.000000</td>
      <td>0.500000</td>
      <td>370.000000</td>
      <td>5.200000e+02</td>
      <td>1.000000</td>
      <td>370.000000</td>
      <td>1900.000000</td>
      <td>0.000000</td>
      <td>98001.000000</td>
      <td>47.155900</td>
      <td>-122.519000</td>
      <td>399.000000</td>
      <td>651.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.123049e+09</td>
      <td>3.220000e+05</td>
      <td>3.000000</td>
      <td>1.750000</td>
      <td>1430.000000</td>
      <td>5.040000e+03</td>
      <td>1.000000</td>
      <td>1190.000000</td>
      <td>1951.000000</td>
      <td>0.000000</td>
      <td>98033.000000</td>
      <td>47.471100</td>
      <td>-122.328000</td>
      <td>1490.000000</td>
      <td>5100.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>3.904930e+09</td>
      <td>4.500000e+05</td>
      <td>3.000000</td>
      <td>2.250000</td>
      <td>1910.000000</td>
      <td>7.618000e+03</td>
      <td>1.500000</td>
      <td>1560.000000</td>
      <td>1975.000000</td>
      <td>0.000000</td>
      <td>98065.000000</td>
      <td>47.571800</td>
      <td>-122.231000</td>
      <td>1840.000000</td>
      <td>7620.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>7.308900e+09</td>
      <td>6.450000e+05</td>
      <td>4.000000</td>
      <td>2.500000</td>
      <td>2550.000000</td>
      <td>1.068500e+04</td>
      <td>2.000000</td>
      <td>2210.000000</td>
      <td>1997.000000</td>
      <td>0.000000</td>
      <td>98118.000000</td>
      <td>47.678000</td>
      <td>-122.125000</td>
      <td>2360.000000</td>
      <td>10083.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>9.900000e+09</td>
      <td>7.700000e+06</td>
      <td>33.000000</td>
      <td>8.000000</td>
      <td>13540.000000</td>
      <td>1.651359e+06</td>
      <td>3.500000</td>
      <td>9410.000000</td>
      <td>2015.000000</td>
      <td>2015.000000</td>
      <td>98199.000000</td>
      <td>47.777600</td>
      <td>-121.315000</td>
      <td>6210.000000</td>
      <td>871200.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Drop id column as it isn't relevant for our analysis
df.drop(columns=['id'], axis=1, inplace=True)
```

We've identified the following columns with `object` data type: `date`, `waterfront`, `view`, `condition`, `grade`, and `sqft_basement`.
To convert these columns to `float`:
1. **`waterfront` Column**: 
   - Converts 'NO' values to 0 and 'YES' values to 1.
2. **`view` Column**: 
   - Maps descriptive values such as 'NONE', 'AVERAGE', etc., to their corresponding numeric representations (0 to 4).
3. **`condition` Column**: 
   - Maps descriptive values like 'Poor', 'Fair', etc., to numeric values ranging from 0 to 4.
4. **`grade` Column**: 
   - Extracts numeric values from the strings and converts them to float.


```python
# Convert 'waterfront' to float
df['waterfront'] = df['waterfront'].replace({'NO': 0, 'YES': 1})

# Convert 'view' to float
view_mapping = {'NONE': 0, 'AVERAGE': 1, 'FAIR': 2, 'GOOD': 3, 'EXCELLENT': 4}
df['view'] = df['view'].replace(view_mapping)

# Convert 'condition' to float
condition_mapping = {'Poor': 0, 'Fair': 1, 'Average': 2, 'Good': 3, 'Very Good': 4}
df['condition'] = df['condition'].replace(condition_mapping)

# Convert 'grade' to float by extracting the number from the string
df['grade'] = df['grade'].str.extract('(\d+)').astype(float)

# Verify the conversions
df[['waterfront', 'view', 'condition', 'grade']].dtypes

```




    waterfront    float64
    view          float64
    condition       int64
    grade         float64
    dtype: object




```python
df['sqft_basement'] = df.sqft_basement.replace('?', 0.0)
df.sqft_basement.value_counts()

#Convert sqft_basement to float
df['sqft_basement']= df.sqft_basement.astype('float64')
```


```python
#Convert date column to 2 separate columns for month and year
date = df['date'].str.split('/', expand=True)
df['month_sold'] = date[0].astype('float64')
df['year_sold'] = date[2].astype('float64')

#Drop original date column
df.drop(columns=['date'], axis=1, inplace=True)
```


```python
#Convert year_built to age
df['age'] = 2015 - df.yr_built
df = df.drop(columns=['yr_built'], axis=1)
```


```python
# Confirm no missing values
df.isna().sum()
```




    price               0
    bedrooms            0
    bathrooms           0
    sqft_living         0
    sqft_lot            0
    floors              0
    waterfront       2376
    view               63
    condition           0
    grade               0
    sqft_above          0
    sqft_basement       0
    yr_renovated     3842
    zipcode             0
    lat                 0
    long                0
    sqft_living15       0
    sqft_lot15          0
    month_sold          0
    year_sold           0
    age                 0
    dtype: int64




```python
# percentage of missing values
for x in ['waterfront', 'view', 'yr_renovated']:
    percent_missing = df[x].isnull().sum() * 100 / len(df)
    print(x, ':', percent_missing)
```

    waterfront : 11.00152798999861
    view : 0.29170718155299347
    yr_renovated : 17.78950780200954
    


```python
# Drop missing values 
df.dropna(subset=['waterfront', 'view', 'yr_renovated'], inplace=True)
```


```python
display(df.info())
df.head()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 15762 entries, 1 to 21596
    Data columns (total 21 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   price          15762 non-null  float64
     1   bedrooms       15762 non-null  int64  
     2   bathrooms      15762 non-null  float64
     3   sqft_living    15762 non-null  int64  
     4   sqft_lot       15762 non-null  int64  
     5   floors         15762 non-null  float64
     6   waterfront     15762 non-null  float64
     7   view           15762 non-null  float64
     8   condition      15762 non-null  int64  
     9   grade          15762 non-null  float64
     10  sqft_above     15762 non-null  int64  
     11  sqft_basement  15762 non-null  float64
     12  yr_renovated   15762 non-null  float64
     13  zipcode        15762 non-null  int64  
     14  lat            15762 non-null  float64
     15  long           15762 non-null  float64
     16  sqft_living15  15762 non-null  int64  
     17  sqft_lot15     15762 non-null  int64  
     18  month_sold     15762 non-null  float64
     19  year_sold      15762 non-null  float64
     20  age            15762 non-null  int64  
    dtypes: float64(12), int64(9)
    memory usage: 2.6 MB
    


    None





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
      <th>price</th>
      <th>bedrooms</th>
      <th>bathrooms</th>
      <th>sqft_living</th>
      <th>sqft_lot</th>
      <th>floors</th>
      <th>waterfront</th>
      <th>view</th>
      <th>condition</th>
      <th>grade</th>
      <th>...</th>
      <th>sqft_basement</th>
      <th>yr_renovated</th>
      <th>zipcode</th>
      <th>lat</th>
      <th>long</th>
      <th>sqft_living15</th>
      <th>sqft_lot15</th>
      <th>month_sold</th>
      <th>year_sold</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>538000.0</td>
      <td>3</td>
      <td>2.25</td>
      <td>2570</td>
      <td>7242</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2</td>
      <td>7.0</td>
      <td>...</td>
      <td>400.0</td>
      <td>1991.0</td>
      <td>98125</td>
      <td>47.7210</td>
      <td>-122.319</td>
      <td>1690</td>
      <td>7639</td>
      <td>12.0</td>
      <td>2014.0</td>
      <td>64</td>
    </tr>
    <tr>
      <th>3</th>
      <td>604000.0</td>
      <td>4</td>
      <td>3.00</td>
      <td>1960</td>
      <td>5000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4</td>
      <td>7.0</td>
      <td>...</td>
      <td>910.0</td>
      <td>0.0</td>
      <td>98136</td>
      <td>47.5208</td>
      <td>-122.393</td>
      <td>1360</td>
      <td>5000</td>
      <td>12.0</td>
      <td>2014.0</td>
      <td>50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>510000.0</td>
      <td>3</td>
      <td>2.00</td>
      <td>1680</td>
      <td>8080</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2</td>
      <td>8.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>98074</td>
      <td>47.6168</td>
      <td>-122.045</td>
      <td>1800</td>
      <td>7503</td>
      <td>2.0</td>
      <td>2015.0</td>
      <td>28</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1230000.0</td>
      <td>4</td>
      <td>4.50</td>
      <td>5420</td>
      <td>101930</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2</td>
      <td>11.0</td>
      <td>...</td>
      <td>1530.0</td>
      <td>0.0</td>
      <td>98053</td>
      <td>47.6561</td>
      <td>-122.005</td>
      <td>4760</td>
      <td>101930</td>
      <td>5.0</td>
      <td>2014.0</td>
      <td>14</td>
    </tr>
    <tr>
      <th>6</th>
      <td>257500.0</td>
      <td>3</td>
      <td>2.25</td>
      <td>1715</td>
      <td>6819</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2</td>
      <td>7.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>98003</td>
      <td>47.3097</td>
      <td>-122.327</td>
      <td>2238</td>
      <td>6819</td>
      <td>6.0</td>
      <td>2014.0</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 21 columns</p>
</div>




```python
# Confirm no missing values
df.isna().sum()
```




    price            0
    bedrooms         0
    bathrooms        0
    sqft_living      0
    sqft_lot         0
    floors           0
    waterfront       0
    view             0
    condition        0
    grade            0
    sqft_above       0
    sqft_basement    0
    yr_renovated     0
    zipcode          0
    lat              0
    long             0
    sqft_living15    0
    sqft_lot15       0
    month_sold       0
    year_sold        0
    age              0
    dtype: int64




-----

### Data Exploration:

Let's begin with data exploration to better understand the dataset's characteristics.

To get an idea of the linear relationship between features and price, we will use a regplot for each feature.



```python
#Preview the relationship between features and target variables
X = df.drop(columns=['price'], axis=1)
for col in X.columns:
    plt.subplots(1, 1)
    sns.regplot(X[col], df.price)
    plt.title(col)
```


    
![png](./Images/output_17_0.png)
    



    
![png](./Images/output_17_1.png)
    



    
![png](./Images/output_17_2.png)
    



    
![png](./Images/output_17_3.png)
    



    
![png](./Images/output_17_4.png)
    



    
![png](./Images/output_17_5.png)
    



    
![png](./Images/output_17_6.png)
    



    
![png](./Images/output_17_7.png)
    



    
![png](./Images/output_17_8.png)
    



    
![png](./Images/output_17_9.png)
    



    
![png](./Images/output_17_10.png)
    



    
![png](./Images/output_17_11.png)
    



    
![png](./Images/output_17_12.png)
    



    
![png](./Images/output_17_13.png)
    



    
![png](./Images/output_17_14.png)
    



    
![png](./Images/output_17_15.png)
    



    
![png](./Images/output_17_16.png)
    



    
![png](./Images/output_17_17.png)
    



    
![png](./Images/output_17_18.png)
    



    
![png](./Images/output_17_19.png)
    


There are several features where there is a clear linear relationship with price, but there are some features where the relationship is not so clear. We can further look at the correlation between all of our features and price to get a better idea of which features have a linear relationship with the dependent variable. 

Now that we have our data cleaned, we will look at how each predictor correlates with price and we'll plot the correlations as well.


```python
X = df.drop(columns=['price'], axis=1)
y = df.price

correlation_list = []
skipped_columns = []

for col in X.columns:
    # Check if the column contains only numeric data and doesn't have NaN values
    if pd.api.types.is_numeric_dtype(X[col]) and not X[col].isnull().any():
        correlation_list.append(pearsonr(X[col], y)[0])
    else:
        skipped_columns.append(col)
        print(f"Skipping column {col} as it is not numeric or contains NaN values.")

correlations = {}
for i, j in zip(X.columns, correlation_list):
    correlations[i] = round(j, 2)

sorted_correlations = pd.DataFrame(sorted(correlations.items(), key=lambda x: x[1], reverse=True), columns=['feature', 'correlations'])
sorted_correlations['feature'] = sorted_correlations['feature'].astype('string')
sorted_correlations

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
      <th>feature</th>
      <th>correlations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>sqft_living</td>
      <td>0.71</td>
    </tr>
    <tr>
      <th>1</th>
      <td>grade</td>
      <td>0.66</td>
    </tr>
    <tr>
      <th>2</th>
      <td>sqft_above</td>
      <td>0.61</td>
    </tr>
    <tr>
      <th>3</th>
      <td>sqft_living15</td>
      <td>0.58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bathrooms</td>
      <td>0.53</td>
    </tr>
    <tr>
      <th>5</th>
      <td>view</td>
      <td>0.40</td>
    </tr>
    <tr>
      <th>6</th>
      <td>sqft_basement</td>
      <td>0.32</td>
    </tr>
    <tr>
      <th>7</th>
      <td>bedrooms</td>
      <td>0.31</td>
    </tr>
    <tr>
      <th>8</th>
      <td>lat</td>
      <td>0.31</td>
    </tr>
    <tr>
      <th>9</th>
      <td>waterfront</td>
      <td>0.27</td>
    </tr>
    <tr>
      <th>10</th>
      <td>floors</td>
      <td>0.26</td>
    </tr>
    <tr>
      <th>11</th>
      <td>yr_renovated</td>
      <td>0.12</td>
    </tr>
    <tr>
      <th>12</th>
      <td>sqft_lot</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>13</th>
      <td>sqft_lot15</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>14</th>
      <td>condition</td>
      <td>0.03</td>
    </tr>
    <tr>
      <th>15</th>
      <td>long</td>
      <td>0.02</td>
    </tr>
    <tr>
      <th>16</th>
      <td>year_sold</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>17</th>
      <td>month_sold</td>
      <td>-0.01</td>
    </tr>
    <tr>
      <th>18</th>
      <td>zipcode</td>
      <td>-0.05</td>
    </tr>
    <tr>
      <th>19</th>
      <td>age</td>
      <td>-0.05</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Plot the feature correlation to price
plt.figure(figsize=(12, 10))
sns.set_style('whitegrid')
sns.barplot(sorted_correlations['correlations'], sorted_correlations['feature'])
plt.title('Feature Correlation to Price', fontsize=16)
plt.xlabel('Correlations')
plt.ylabel('Features')
plt.yticks(fontsize=12)
plt.xticks(np.linspace(0, 1, 5))
for ind, val in enumerate(sorted_correlations['correlations']):
    plt.text(x= -0.05, y= ind, s= val, fontsize=12)
    plt.axhline(ind - .5, alpha=0.5)
plt.show()
```


    
![png](./Images/output_21_0.png)
    


As expected there are some multicollinearity issues which we need to address.

We note that `sqft_above` and `sqft_living` are highly positively correlated, which is expected as `sqft_above` is the square footage of the house apart from basement (and we saw earlier that most houses did not have a basement). We will choose to keep `sqft_living` as it encompasses more information and drop the `sqt_above` feature. We also see that `sqft_living15` is moderately highly correlated with `sqft_living` and so we will drop this feature too.

We also note that `sqft_lot` and `sqft_lot15` are highly positively correlated and we will choose to keep `sqft_lot` as it relates directly to the house as opposed to its neighbours (and so potentially easier to obtain data for and generalise.)

---
### Modeling

To address the research questions using multiple regression models,[Julliet](https://github.com/Iswana-O) and [Wayne](https://github.com/waynekipngeno) identified the appropriate independent variables (features) for each question and then created models based on those variables to predict the dependent variable (target). For our dataset, the dependent variable will be `price` for most models, as many of the questions revolve around house pricing and its determinants.

Given the research questions and the features in our dataset, here are the proposed models:

#### 1. **House Pricing Models**:

<div style="margin-left: 40px;">

<u>*Model 1: House Price Determinants*</u>

**Objective**: Identify the primary determinants of house prices in King County.
- **Features**: All available attributes except the house price.
- **Target Variable**: Price.
- **Method**: Multiple linear regression will be used to understand the contribution of each feature to the house price.

<u>*Model 2: Impact of Living Space on House Price*</u>

**Objective**: Understand the relationship between the living space (in square feet) and the price of houses in King County.
- **Features**: `sqft_living`.
- **Target Variable**: Price.
- **Method**: Simple linear regression was used to quantify the linear relationship between living space and house price. The predictor `sqft_living` was chosen due to its high correlation coefficient of approximately \(0.702\) with the target variable, indicating a strong positive linear relationship.

<u>*Model 3: Property Attributes and Their Influence on Market Value*</u>

- **Objective:** Assess how property attributes, including geographical aspects, affect market value.
- **Features:** Variables like `grade`, `condition`, `view`, `sqft_living`, `age_house`, and others describe the property.
- **Target:** The property's market value or "price".
- **Method:** Multiple linear regression, enhanced with preprocessing techniques like Box-Cox transformation and one-hot encoding.
    

</div>

#### 2. **Dynamic Pricing Recommendations Models**:
<div style="margin-left: 40px;"> 
    
<u>*Model 4: Dynamic Pricing Recommendations*</u>

**Objective**: Develop a model to offer real-time pricing suggestions for rental properties under "Haven-Kings".
- **Features**: Those attributes that are deemed significant for rental pricing, such as location (`lat`, `long`), `view`, property size (`sqft_living`, `sqft_lot`), and others.
- **Target Variable**: Price.
- **Method**: Multiple linear regression will provide coefficients for each feature, guiding dynamic pricing adjustments.
</div>

----

#### <u>*Model 1: House Price Determinants*</u>

**Objective**: Identify the primary determinants of house prices in King County.
- **Features**: All available attributes except the house price.
- **Target Variable**: Price.
- **Method**: Multiple linear regression will be used to understand the contribution of each feature to the house price.



```python
def build_and_evaluate_linear_model(dataframe):
    """
    Build and evaluate a linear regression model using the given dataframe.
    
    Args:
        dataframe (pd.DataFrame): The input data.
        
    Returns:
        model (statsmodels model): A fitted linear regression model.
    """
    
    # Prepare data
    X = dataframe.drop(columns=['price'], axis=1)
    y = dataframe.price
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.20, random_state=123)
    
    # Fit the sklearn model
    linreg = LinearRegression().fit(X_train, y_train)
    
    # Display sklearn model metrics
    print('Training Score:', round(linreg.score(X_train, y_train), 2))
    print('Test Score:', round(linreg.score(X_test, y_test), 2))
    print('Coefficients:', linreg.coef_)
    
    # Fit the statsmodels model for detailed summary
    X_train_const = sm.add_constant(X_train)
    sm_model = sm.OLS(y_train, X_train_const).fit()
    print(sm_model.summary())
    
    return sm_model

def plot_qq_of_residuals(model):
    """
    Plot the QQ plot of residuals for the given linear regression model.
    
    Args:
        model (statsmodels model): A fitted linear regression model.
    """
    sns.set_style("whitegrid")
    
    # Create and customize QQ plot
    fig, ax = plt.subplots(figsize=(18, 10))
    sm.qqplot(model.resid, dist=stats.norm, line='45', fit=True, ax=ax)
    ax.set_title("QQ Plot of Residuals", fontsize=20)
    ax.set_xlabel("Theoretical Quantiles", fontsize=16)
    ax.set_ylabel("Sample Quantiles", fontsize=16)
    sns.despine()
    plt.show()

# Build and evaluate the model
model = build_and_evaluate_linear_model(df)

# Plot the QQ plot for the model residuals
plot_qq_of_residuals(model)

```

    Training Score: 0.71
    Test Score: 0.68
    Coefficients: [-3.57439404e+04  4.70431772e+04  1.21620206e+02  1.08804339e-01
      8.63177465e+03  6.13690732e+05  6.15776333e+04  2.90407722e+04
      9.50660820e+04  6.16625223e+01  3.00678038e+01  2.03930440e+01
     -5.39723872e+02  5.96059842e+05 -1.98415708e+05  2.08665048e+01
     -3.51055164e-01  1.38101384e+03  3.90461119e+04  2.74425344e+03]
                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                  price   R-squared:                       0.707
    Model:                            OLS   Adj. R-squared:                  0.707
    Method:                 Least Squares   F-statistic:                     1522.
    Date:                Sun, 10 Sep 2023   Prob (F-statistic):               0.00
    Time:                        22:16:15   Log-Likelihood:            -1.7190e+05
    No. Observations:               12609   AIC:                         3.438e+05
    Df Residuals:                   12588   BIC:                         3.440e+05
    Df Model:                          20                                         
    Covariance Type:            nonrobust                                         
    =================================================================================
                        coef    std err          t      P>|t|      [0.025      0.975]
    ---------------------------------------------------------------------------------
    const         -7.912e+07    1.3e+07     -6.073      0.000   -1.05e+08   -5.36e+07
    bedrooms      -3.574e+04   2427.792    -14.723      0.000   -4.05e+04    -3.1e+04
    bathrooms      4.704e+04   4288.807     10.969      0.000    3.86e+04    5.54e+04
    sqft_living     121.6202     23.290      5.222      0.000      75.968     167.273
    sqft_lot          0.1088      0.061      1.771      0.077      -0.012       0.229
    floors         8631.7746   4717.448      1.830      0.067    -615.142    1.79e+04
    waterfront     6.137e+05   2.36e+04     25.995      0.000    5.67e+05     6.6e+05
    view           6.158e+04   3043.718     20.231      0.000    5.56e+04    6.75e+04
    condition      2.904e+04   3093.270      9.388      0.000     2.3e+04    3.51e+04
    grade          9.507e+04   2830.659     33.584      0.000    8.95e+04    1.01e+05
    sqft_above       61.6625     23.163      2.662      0.008      16.260     107.065
    sqft_basement    30.0678     22.990      1.308      0.191     -14.996      75.131
    yr_renovated     20.3930      4.797      4.251      0.000      10.989      29.797
    zipcode        -539.7239     43.234    -12.484      0.000    -624.469    -454.979
    lat            5.961e+05   1.41e+04     42.381      0.000    5.68e+05    6.24e+05
    long          -1.984e+05   1.72e+04    -11.512      0.000   -2.32e+05   -1.65e+05
    sqft_living15    20.8665      4.549      4.588      0.000      11.951      29.782
    sqft_lot15       -0.3511      0.095     -3.705      0.000      -0.537      -0.165
    month_sold     1381.0138    923.466      1.495      0.135    -429.121    3191.149
    year_sold      3.905e+04   6184.963      6.313      0.000    2.69e+04    5.12e+04
    age            2744.2534     94.990     28.890      0.000    2558.059    2930.448
    ==============================================================================
    Omnibus:                    10891.167   Durbin-Watson:                   1.966
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):          1206511.382
    Skew:                           3.620   Prob(JB):                         0.00
    Kurtosis:                      50.372   Cond. No.                     7.30e+08
    ==============================================================================
    
    Notes:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    [2] The condition number is large, 7.3e+08. This might indicate that there are
    strong multicollinearity or other numerical problems.
    


    
![png](./Images/output_25_1.png)
    


**OLS Regression Results Interpretation for Extended Model**

The regression results provide insights into a more complex model with multiple predictors:

- **Model Scores:**
  - **Training Score (0.71):** This suggests the model explains 71% of the variance in the training data.
  - **Test Score (0.68):** The model explains 68% of the variance in the test data, which is slightly lower than the training score but still indicative of a reasonable fit.

- **Coefficients:** The coefficients represent the change in the dependent variable (price) for a one-unit change in the predictor, holding other predictors constant. For instance, every additional bedroom reduces the price by approximately $35,744, while waterfront properties increase the price by about $613,791.

- **Model Metrics:**
  - **R-squared (0.707):** The model explains about 70.7% of the variance in the price. This is consistent with the given training score.
  - **Adjusted R-squared (0.707):** Adjusted for the number of predictors in the model, the explained variance remains at 70.7%, which is a good sign.

- **Statistical Significance:**
  - Several predictors like `bedrooms`, `bathrooms`, `waterfront`, `view`, `grade`, `zipcode`, `lat`, `long`, and `age` have p-values near zero, suggesting they are statistically significant predictors.
  - Some variables, such as `sqft_lot`, `floors`, `sqft_basement`, and `month_sold`, have larger p-values, indicating they might not be as statistically significant in predicting the house price.

- **Residual Analysis:**
  - The significant Omnibus and Jarque-Bera tests suggest potential deviations from normality in the residuals.
  - The Durbin-Watson statistic is close to 2, indicating little autocorrelation in the residuals, which is favorable.

- **Condition Number (7.3e+08):** The extremely large condition number warns of potential multicollinearity issues in the data. This suggests that some predictors might be correlated, which can affect the stability of the coefficient estimates.

**Conclusion:**
The extended regression model, with multiple predictors, captures a substantial portion of the variance in house prices, with a training and test score of 0.71 and 0.68 respectively. However, potential multicollinearity and non-normality in residuals hint at areas to refine. Careful consideration of variable selection and further diagnostic tests could enhance the model's reliability.


#### <u>*Model 2: Impact of Living Space on House Price- the target variable with a strong positive linear relationship.*</u>

**Objective**: Understand the relationship between the living space (in square feet) and the price of houses in King County.
- **Features**: `sqft_living`.
- **Target Variable**: Price.
- **Method**: Simple linear regression was used to quantify the linear relationship between living space and house price. The predictor `sqft_living` was chosen due to its high correlation coefficient of approximately \(0.702\) with the target variable, indicating a strong positive linear relationship.


```python
# Scatter plot to visualize the relationship between sqft_living and price
def create_scatter_plot(x, y, title, xlabel, ylabel, ax=None):
    if ax is None:
        plt.figure(figsize=(10, 6))
        ax = plt.gca()
    ax.scatter(x, y, alpha=0.5)
    ax.set_title(title)
    ax.set_xlabel(xlabel)
    ax.set_ylabel(ylabel)
    ax.grid(True)

create_scatter_plot(df['sqft_living'], df['price'], 'Price vs. Sqft_Living', 'Sqft_Living', 'Price')
plt.show()

# Create variables to store the independent and dependent variables
X_OLS = df[["sqft_living"]]
y_OLS = df["price"]

# Creating and fitting the Model
results = sm.OLS(endog=y_OLS, exog=sm.add_constant(X_OLS)).fit()

# Function to create scatter plot of residuals
def residuals_plot(x, residuals, title, ax=None):
    if ax is None:
        fig, ax = plt.subplots(figsize=(8, 6))
    sns.scatterplot(x=x, y=residuals, ax=ax, alpha=0.5, color="blue", label="Residuals")
    ax.axhline(y=0, color="black", linestyle="--", label="Zero Residuals Line")
    sns.regplot(x=x, y=residuals, ax=ax, scatter=False, color="red", lowess=True, label="Smooth Line")
    ax.set_title(title, fontsize=16)
    ax.set_xlabel("sqft_living", fontsize=12)
    ax.set_ylabel("Residuals", fontsize=12)
    ax.legend(loc="upper left")
    ax.grid(True, linestyle='--', alpha=0.6)

residuals_plot(df["sqft_living"], results.resid, "Residuals vs. sqft_living")
plt.show()

# Function to create QQ plot
def create_qq_plot(residuals, title):
    fig, ax = plt.subplots(figsize=(8, 6))
    sm.qqplot(residuals, dist=stats.norm, line='45', fit=True, ax=ax)
    ax.set_title(title, fontsize=16)
    ax.set_xlabel("Theoretical Quantiles", fontsize=12)
    ax.set_ylabel("Sample Quantiles", fontsize=12)
    sns.despine()
    plt.show()

create_qq_plot(results.resid, "QQ Plot of Residuals")

# Calculate the Box-Cox transformation for the dependent variable (y_OLS)
transformed_y, lambda_value = stats.boxcox(y_OLS)
print(f"Lambda value for Box-Cox transformation: {lambda_value}")

# Histogram of Transformed y (Box-Cox)
plt.hist(transformed_y, bins=50, density=True, alpha=0.5, color='b')
plt.title('Histogram of Transformed y (Box-Cox)')
plt.show()

# Create and fit the OLS model using the transformed y
box_cox_results = sm.OLS(endog=transformed_y, exog=sm.add_constant(X_OLS)).fit()
box_cox_residuals = transformed_y - box_cox_results.fittedvalues

residuals_plot(df["sqft_living"], box_cox_residuals, "Residuals vs. sqft_living (Box-Cox Transformed Model)")
plt.show()

create_qq_plot(box_cox_residuals, "QQ Plot of Residuals (Box-Cox Transformed Model)")

# Print the summary of the OLS model and calculate RMSE
print(box_cox_results.summary())
predictions = box_cox_results.fittedvalues
rmse_original_units = np.sqrt(np.mean((y_OLS - predictions) ** 2))
print("RMSE in Original Units (Dollars):", rmse_original_units)
```


    
![png](./Images/output_28_0.png)
    



    
![png](./Images/output_28_1.png)
    



    
![png](./Images/output_28_2.png)
    


    Lambda value for Box-Cox transformation: -0.24602697608824728
    


    
![png](./Images/output_28_4.png)
    



    
![png](./Images/output_28_5.png)
    



    
![png](./Images/output_28_6.png)
    


                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                      y   R-squared:                       0.457
    Model:                            OLS   Adj. R-squared:                  0.457
    Method:                 Least Squares   F-statistic:                 1.328e+04
    Date:                Sun, 10 Sep 2023   Prob (F-statistic):               0.00
    Time:                        22:16:18   Log-Likelihood:                 43401.
    No. Observations:               15762   AIC:                        -8.680e+04
    Df Residuals:                   15760   BIC:                        -8.678e+04
    Df Model:                           1                                         
    Covariance Type:            nonrobust                                         
    ===============================================================================
                      coef    std err          t      P>|t|      [0.025      0.975]
    -------------------------------------------------------------------------------
    const           3.8672      0.000   1.27e+04      0.000       3.867       3.868
    sqft_living   1.54e-05   1.34e-07    115.237      0.000    1.51e-05    1.57e-05
    ==============================================================================
    Omnibus:                      222.257   Durbin-Watson:                   1.982
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):              232.600
    Skew:                          -0.286   Prob(JB):                     3.10e-51
    Kurtosis:                       3.164   Cond. No.                     5.65e+03
    ==============================================================================
    
    Notes:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    [2] The condition number is large, 5.65e+03. This might indicate that there are
    strong multicollinearity or other numerical problems.
    RMSE in Original Units (Dollars): 656934.8069415551
    

**OLS Regression Results Interpretation**

The OLS regression provides insights into the relationship between `sqft_living` and the dependent variable:

- **R-squared (0.457):** This indicates that the model explains about 45.7% of the variance in the dependent variable. While this captures a significant portion of the relationship, there's still a substantial amount of variance that remains unexplained.

- **Coefficient for sqft_living (1.54e-05):** This coefficient suggests that for every unit increase in square footage (`sqft_living`), the dependent variable increases by \(1.54 \times 10^{-5}\). The predictor's p-value is almost zero, confirming its statistical significance in the model.

- **Residual Analysis:**
  - **Omnibus and Jarque-Bera Tests:** Both tests are significant, implying that the model's residuals might not be normally distributed. This might violate one of the linear regression assumptions.
  - **Durbin-Watson (1.982):** This value is close to 2, which is a good sign as it suggests that there's little autocorrelation in the residuals.

- **RMSE (656,934.8):** The Root Mean Square Error measures the average prediction error of the model. An RMSE of 656,934.8 units indicates a considerable prediction error.

- **Notes on Condition Number (5.65e+03):** The large condition number suggests that there might be strong multicollinearity in the data or other numerical problems that can affect the stability and reliability of the regression coefficients.

**Conclusion:**
While the regression model captures some aspects of the relationship between `sqft_living` and the dependent variable, there's room for improvement. The significant RMSE and potential multicollinearity issues highlight areas to address in model refinement.

---
#### <u>*Model 3: Property Attributes and Their Influence on Market Value*</u>

- **Objective:** Assess how property attributes, including geographical aspects, affect market value.
- **Features:** Variables like `grade`, `condition`, `view`, `sqft_living`, `age_house`, and others describe the property.
- **Target:** The property's market value or "price".
- **Method:** Multiple linear regression, enhanced with preprocessing techniques like Box-Cox transformation and one-hot encoding.



```python
# 1. Box-Cox Transformation of the Target Variable
# create variables to store the independent and dependent variables
X_OLS = df[["sqft_living"]]
y_OLS = df["price"]

# Box-Cox transformation of y_OLS
transformed_y, _ = stats.boxcox(y_OLS)
df['transformed_price'] = transformed_y

# 2. One-hot Encoding & Model Building with Extended Features
df_encoded = pd.get_dummies(df, columns=['grade', 'condition', 'view'], drop_first=True)

# Dynamically select the extended feature columns (including 'age_house' if it exists)
extended_features = ['sqft_living']
if 'age_house' in df_encoded.columns:
    extended_features.append('age_house')
extended_features += [col for col in df_encoded.columns if 'grade_' in col or 'condition_' in col]

X_extended = df_encoded[extended_features]
y = df_encoded['transformed_price']

# Adding a constant for the intercept
X_extended_with_const = sm.add_constant(X_extended)

# Building the OLS regression model
model_extended = sm.OLS(endog=y, exog=X_extended_with_const)
results_extended = model_extended.fit()

# Display the model summary and diagnostic plots
print(results_extended.summary())

# Diagnostic plots function
def plot_diagnostics(results):
    fig, axs = plt.subplots(nrows=2, ncols=1, figsize=(8, 12))
    
    # Residuals vs Fitted values
    sns.residplot(x=results.fittedvalues, y=results.resid, ax=axs[0], lowess=True, 
                  scatter_kws={'alpha': 0.5}, line_kws={'color': 'red', 'lw': 1, 'alpha': 0.8})
    axs[0].set_title('Residuals vs Fitted')
    axs[0].set_xlabel('Fitted values')
    axs[0].set_ylabel('Residuals')
    
    # QQ plot
    sm.qqplot(results.resid, dist=stats.norm, line='45', fit=True, ax=axs[1])
    axs[1].set_title('QQ Plot')
    plt.tight_layout()
    plt.show()

plot_diagnostics(results_extended)

# RMSE Calculation
predictions = results_extended.fittedvalues
rmse_original_units = np.sqrt(np.mean((y_OLS - predictions) ** 2))
print("RMSE in Original Units (Dollars):", rmse_original_units)

```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:      transformed_price   R-squared:                       0.548
    Model:                            OLS   Adj. R-squared:                  0.547
    Method:                 Least Squares   F-statistic:                     1271.
    Date:                Sun, 10 Sep 2023   Prob (F-statistic):               0.00
    Time:                        22:16:18   Log-Likelihood:                 44837.
    No. Observations:               15762   AIC:                        -8.964e+04
    Df Residuals:                   15746   BIC:                        -8.952e+04
    Df Model:                          15                                         
    Covariance Type:            nonrobust                                         
    ===============================================================================
                      coef    std err          t      P>|t|      [0.025      0.975]
    -------------------------------------------------------------------------------
    const           3.8670      0.014    267.524      0.000       3.839       3.895
    sqft_living  8.279e-06   1.94e-07     42.681      0.000     7.9e-06    8.66e-06
    grade_4.0      -0.0116      0.015     -0.797      0.425      -0.040       0.017
    grade_5.0      -0.0052      0.014     -0.367      0.713      -0.033       0.022
    grade_6.0       0.0038      0.014      0.273      0.785      -0.024       0.031
    grade_7.0       0.0127      0.014      0.900      0.368      -0.015       0.040
    grade_8.0       0.0214      0.014      1.520      0.129      -0.006       0.049
    grade_9.0       0.0295      0.014      2.093      0.036       0.002       0.057
    grade_10.0      0.0353      0.014      2.501      0.012       0.008       0.063
    grade_11.0      0.0381      0.014      2.698      0.007       0.010       0.066
    grade_12.0      0.0407      0.014      2.863      0.004       0.013       0.069
    grade_13.0      0.0395      0.015      2.676      0.007       0.011       0.068
    condition_1    -0.0097      0.003     -2.789      0.005      -0.016      -0.003
    condition_2    -0.0044      0.003     -1.347      0.178      -0.011       0.002
    condition_3    -0.0013      0.003     -0.389      0.697      -0.008       0.005
    condition_4     0.0045      0.003      1.377      0.168      -0.002       0.011
    ==============================================================================
    Omnibus:                       88.275   Durbin-Watson:                   1.980
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):               89.641
    Skew:                          -0.182   Prob(JB):                     3.43e-20
    Kurtosis:                       3.057   Cond. No.                     9.50e+05
    ==============================================================================
    
    Notes:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    [2] The condition number is large, 9.5e+05. This might indicate that there are
    strong multicollinearity or other numerical problems.
    


    
![png](./Images/output_31_1.png)
    


    RMSE in Original Units (Dollars): 656934.8063250879
    

**OLS Regression Results Interpretation for Transformed Price Model**

The regression results shed light on a model where the dependent variable is the `transformed_price`. 

- **Model Metrics:**
  - **R-squared (0.548):** The model explains 54.8% of the variance in the transformed price. This indicates a moderate fit, suggesting that over 45% of the variance in the transformed price remains unexplained by the predictors.

- **Coefficient Interpretations:**
  - **sqft_living (8.279e-06):** For every unit increase in `sqft_living`, the `transformed_price` increases by \(8.279 \times 10^{-6}\). This predictor is statistically significant with a p-value close to zero.
  - **Grade Variables:** Variables representing different grades (`grade_4.0` to `grade_13.0`) have coefficients indicating their impact on `transformed_price` relative to some baseline grade. However, not all of them are statistically significant. For example, `grade_9.0` to `grade_13.0` show statistical significance with p-values less than 0.05, while others like `grade_5.0` and `grade_6.0` have larger p-values, suggesting they might not be significant predictors.
  - **Condition Variables:** Variables representing different property conditions (`condition_1` to `condition_4`) show their impact on the `transformed_price`. Among these, only `condition_1` is statistically significant with a p-value less than 0.05.

- **Residual Analysis:**
  - The Omnibus and Jarque-Bera tests are significant, suggesting potential deviations from normality in the residuals.
  - The Durbin-Watson statistic is close to 2, indicating there is likely no autocorrelation in the residuals.

- **Condition Number (9.5e+05):** The large condition number is a warning sign for potential multicollinearity issues among the predictors. This can destabilize the coefficient estimates and may need further investigation.

**Conclusion:**
The regression model for the `transformed_price` captures a moderate amount of the variance, with `sqft_living` and certain grade levels emerging as significant predictors. However, potential multicollinearity issues and non-normality in residuals highlight areas for model refinement. Considering the significance and relevance of predictors can enhance the model's predictive power.

#### <u>*Model 4: Dynamic Pricing Recommendations*</u>

**Objective**: Develop a model to offer real-time pricing suggestions for rental properties under "Haven-Kings".
- **Features**: Those attributes that are deemed significant for rental pricing, such as location (`lat`, `long`), `view`, property size (`sqft_living`, `sqft_lot`), and others.
- **Target Variable**: Price.
- **Method**: Multiple linear regression will provide coefficients for each feature, guiding dynamic pricing adjustments.


```python
# Encode the view variable
df_encoded = pd.get_dummies(df, columns=['view'], prefix='view', drop_first=True)

# Create a binary column 'renovated'
df_encoded['renovated'] = df_encoded['yr_renovated'].apply(lambda x: 1 if x > 0 else 0)

# Visualization

# 1. Distribution of prices based on renovation status
plt.figure(figsize=(10, 5))
plt.subplot(1, 2, 1)
plt.boxplot([df_encoded[df_encoded['renovated'] == 0]['price'], df_encoded[df_encoded['renovated'] == 1]['price']],
            vert=False, labels=['Non-Renovated', 'Renovated'])
plt.title('Price by Renovation Status')
plt.xlabel('Price')
plt.ylabel('Renovation Status')

# 2. Distribution of prices based on latitude and longitude
plt.subplot(1, 2, 2)
plt.scatter(df_encoded['lat'], df_encoded['price'], alpha=0.2, label='Latitude', color='blue')
plt.scatter(df_encoded['long'], df_encoded['price'], alpha=0.2, label='Longitude', color='red')
plt.title('Price by Latitude and Longitude')
plt.xlabel('Location (Lat/Long)')
plt.ylabel('Price')
plt.legend(loc='upper left')
plt.tight_layout()
plt.show()

# 3. Distribution of prices based on sqft_living and sqft_lot
plt.figure(figsize=(10, 5))
plt.subplot(1, 2, 1)
plt.scatter(df_encoded['sqft_living'], df_encoded['price'], alpha=0.2)
plt.title('Price by Sqft Living')
plt.xlabel('Sqft Living')
plt.ylabel('Price')

plt.subplot(1, 2, 2)
plt.scatter(df_encoded['sqft_lot'], df_encoded['price'], alpha=0.2)
plt.title('Price by Sqft Lot')
plt.xlabel('Sqft Lot')
plt.ylabel('Price')
plt.tight_layout()
plt.show()

# 4. Distribution of prices based on view
view_columns = [col for col in df_encoded.columns if 'view_' in col]
view_prices = [df_encoded[df_encoded[col] == 1]['price'] for col in view_columns]

plt.figure(figsize=(10, 5))
plt.boxplot(view_prices, vert=False, labels=view_columns)
plt.title('Price by View')
plt.xlabel('Price')
plt.ylabel('View Quality')
plt.grid(True, axis='x')
plt.show()

# Regression Analysis

# Define the selected columns for regression
selected_columns = ['lat', 'long', 'sqft_living', 'sqft_lot'] + view_columns + ['renovated']

X = df_encoded[selected_columns]
y = df_encoded['price']

# Adding a constant for the intercept
X = sm.add_constant(X)

# Regression Model
results = sm.OLS(endog=y, exog=X).fit()

# Summary of Regression Model
print(results.summary())

```


    
![png](./Images/output_34_0.png)
    



    
![png](./Images/output_34_1.png)
    



    
![png](./Images/output_34_2.png)
    


                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                  price   R-squared:                       0.628
    Model:                            OLS   Adj. R-squared:                  0.628
    Method:                 Least Squares   F-statistic:                     2953.
    Date:                Sun, 10 Sep 2023   Prob (F-statistic):               0.00
    Time:                        22:16:25   Log-Likelihood:            -2.1676e+05
    No. Observations:               15762   AIC:                         4.335e+05
    Df Residuals:                   15752   BIC:                         4.336e+05
    Df Model:                           9                                         
    Covariance Type:            nonrobust                                         
    ===============================================================================
                      coef    std err          t      P>|t|      [0.025      0.975]
    -------------------------------------------------------------------------------
    const        -5.88e+07   1.73e+06    -33.973      0.000   -6.22e+07   -5.54e+07
    lat          6.798e+05   1.33e+04     51.238      0.000    6.54e+05    7.06e+05
    long        -2.163e+05   1.39e+04    -15.551      0.000   -2.44e+05   -1.89e+05
    sqft_living   263.6425      2.162    121.932      0.000     259.404     267.881
    sqft_lot        0.0462      0.045      1.026      0.305      -0.042       0.134
    view_1.0     1.291e+05   9009.802     14.329      0.000    1.11e+05    1.47e+05
    view_2.0     1.424e+05   1.48e+04      9.653      0.000    1.13e+05    1.71e+05
    view_3.0     1.939e+05   1.26e+04     15.374      0.000    1.69e+05    2.19e+05
    view_4.0     5.611e+05   1.52e+04     37.022      0.000    5.31e+05    5.91e+05
    renovated    1.028e+05   9169.272     11.215      0.000    8.49e+04    1.21e+05
    ==============================================================================
    Omnibus:                    12006.309   Durbin-Watson:                   1.976
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):           787116.490
    Skew:                           3.104   Prob(JB):                         0.00
    Kurtosis:                      37.058   Cond. No.                     4.26e+07
    ==============================================================================
    
    Notes:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    [2] The condition number is large, 4.26e+07. This might indicate that there are
    strong multicollinearity or other numerical problems.
    

OLS Regression Analysis Summary:

1. **Model Overview**:
   - **R-squared**: 0.628 - This model explains approximately 62.8% of the variance in house prices.
   - The model is statistically significant with a very low p-value, indicating a very low chance that observed relationships are due to random chance.

2. **Key Coefficients**:
   - Latitude (lat): Positive relation. An increase in latitude is associated with an increase in house price by approximately $679,800.
   - Longitude (long): Negative relation. An increase in longitude reduces the house price by approximately $216,300.
   - Sqft of Living Space (sqft_living): Positive relation. An increase in living space (in sqft) raises the house price by approximately $263.64.
   
   - View Quality: Houses with better views (especially `view_4.0` category) have significantly higher prices.
   - Renovated: Renovated houses are priced about $102,800 higher than non-renovated houses on average.

3. **Considerations**:
   - **Sqft of Lot Space (sqft_lot)**: This predictor might not be significant due to a p-value greater than 0.05.
   - **Residuals**: They might not be normally distributed.
   - **Multicollinearity**: Some predictors might be correlated, as indicated by the large condition number.

### Conclusion



```python

```

