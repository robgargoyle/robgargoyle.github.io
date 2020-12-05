# Ridgeline Plots: The Perfect Way to Visualize Data Distributions with Python

https://towardsdatascience.com/ridgeline-plots-the-perfect-way-to-visualize-data-distributions-with-python-de99a5493052


```python
!pip install joypy
```

    Collecting joypy
      Downloading joypy-0.2.2-py2.py3-none-any.whl (8.3 kB)
    Requirement already satisfied: pandas>=0.20.0 in c:\py\python37\lib\site-packages (from joypy) (1.1.3)
    Requirement already satisfied: numpy in c:\py\python37\lib\site-packages (from joypy) (1.16.3)
    Requirement already satisfied: matplotlib in c:\py\python37\lib\site-packages (from joypy) (3.3.2)
    Requirement already satisfied: scipy>=0.11.0 in c:\py\python37\lib\site-packages (from joypy) (1.5.3)
    Requirement already satisfied: python-dateutil>=2.7.3 in c:\py\python37\lib\site-packages (from pandas>=0.20.0->joypy) (2.8.0)
    Requirement already satisfied: pytz>=2017.2 in c:\py\python37\lib\site-packages (from pandas>=0.20.0->joypy) (2020.1)
    Requirement already satisfied: certifi>=2020.06.20 in c:\py\python37\lib\site-packages (from matplotlib->joypy) (2020.6.20)
    Requirement already satisfied: kiwisolver>=1.0.1 in c:\py\python37\lib\site-packages (from matplotlib->joypy) (1.1.0)
    Requirement already satisfied: pillow>=6.2.0 in c:\py\python37\lib\site-packages (from matplotlib->joypy) (8.0.1)
    Requirement already satisfied: pyparsing!=2.0.4,!=2.1.2,!=2.1.6,>=2.0.3 in c:\py\python37\lib\site-packages (from matplotlib->joypy) (2.4.0)
    Requirement already satisfied: cycler>=0.10 in c:\py\python37\lib\site-packages (from matplotlib->joypy) (0.10.0)
    Requirement already satisfied: six>=1.5 in c:\py\python37\lib\site-packages (from python-dateutil>=2.7.3->pandas>=0.20.0->joypy) (1.12.0)
    Requirement already satisfied: setuptools in c:\py\python37\lib\site-packages (from kiwisolver>=1.0.1->matplotlib->joypy) (41.2.0)
    Installing collected packages: joypy
    Successfully installed joypy-0.2.2
    


```python
from google.colab import drive
drive.mount('/content/drive')
```

    Drive already mounted at /content/drive; to attempt to forcibly remount, call drive.mount("/content/drive", force_remount=True).
    


```python
!dir
```

     Volume in drive C is Windows
     Volume Serial Number is F83F-7A9C
    
     Directory of C:\Users\rbthb\dev\graphics
    
    12/04/2020  07:22 PM    <DIR>          .
    12/04/2020  07:22 PM    <DIR>          ..
    11/09/2020  10:09 AM    <DIR>          .ipynb_checkpoints
    12/04/2020  07:22 PM    <DIR>          data
    12/04/2020  07:18 PM           223,978 ridgeline_plot_wx_aus.ipynb
    11/09/2020  10:33 AM            13,172 Untitled.ipynb
    11/09/2020  10:21 AM         8,150,213 wine_data_output.html
                   3 File(s)      8,387,363 bytes
                   4 Dir(s)  31,049,080,832 bytes free
    


```python
# map to google drive
import os 
#ipy_dir = '/content/drive/My Drive/Colab Notebooks'
ipy_dir = os.getcwd()
data_dir = os.path.join(ipy_dir,'data')
model_dir = os.path.join(ipy_dir, 'model')

os.makedirs(data_dir, exist_ok=True)
os.makedirs(model_dir, exist_ok=True)
```


```python
import pandas as pd
import matplotlib.pyplot as plt
from joypy import joyplot
```


```python
wxcsv_dfe = os.path.join(data_dir,'weatherAUS.csv')

wx_df = pd.read_csv(wxcsv_dfe,
                    usecols=['Date', 'Location', 'MinTemp', 'MaxTemp'])
sydney_df = wx_df.query("Location == 'Sydney'")
sydney_df = sydney_df.drop('Location', axis=1)
sydney_df['Date'] = sydney_df['Date'].astype('datetime64')
sydney_df['Month'] = sydney_df['Date'].dt.month_name()

sydney_df.head()


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
      <th>Date</th>
      <th>MinTemp</th>
      <th>MaxTemp</th>
      <th>Month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29497</th>
      <td>2008-02-01</td>
      <td>19.5</td>
      <td>22.4</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29498</th>
      <td>2008-02-02</td>
      <td>19.5</td>
      <td>25.6</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29499</th>
      <td>2008-02-03</td>
      <td>21.6</td>
      <td>24.5</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29500</th>
      <td>2008-02-04</td>
      <td>20.2</td>
      <td>22.8</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29501</th>
      <td>2008-02-05</td>
      <td>19.7</td>
      <td>25.7</td>
      <td>February</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('date min ', sydney_df['Date'].min(), 
      ', date max:' ,sydney_df['Date'].max(),
      ',count of days: ',sydney_df['Date'].nunique(1))
```

    date min  2008-02-01 00:00:00 , date max: 2017-06-25 00:00:00 ,count of days:  3337
    


```python
from pandas.api.types import CategoricalDtype

cat_month = CategoricalDtype(
    ['January', 'February', 'March', 'April', 'May', 'June',
     'July', 'August', 'September', 'October', 'November', 'December']
)

sydney_df['Month'] = sydney_df['Month'].astype(cat_month)

sydney_df.dtypes

sydney_df.head()

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
      <th>Date</th>
      <th>MinTemp</th>
      <th>MaxTemp</th>
      <th>Month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29497</th>
      <td>2008-02-01</td>
      <td>19.5</td>
      <td>22.4</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29498</th>
      <td>2008-02-02</td>
      <td>19.5</td>
      <td>25.6</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29499</th>
      <td>2008-02-03</td>
      <td>21.6</td>
      <td>24.5</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29500</th>
      <td>2008-02-04</td>
      <td>20.2</td>
      <td>22.8</td>
      <td>February</td>
    </tr>
    <tr>
      <th>29501</th>
      <td>2008-02-05</td>
      <td>19.7</td>
      <td>25.7</td>
      <td>February</td>
    </tr>
  </tbody>
</table>
</div>




```python
# ----- for one variable
plt.figure()

joyplot(
    data=sydney_df[['MaxTemp', 'Month']], 
    by='Month',
    figsize=(12, 8)
)
plt.title('Ridgeline Plot of Max Temperatures in Sydney', fontsize=20)
plt.show()

```


    <Figure size 432x288 with 0 Axes>



    
![png](output_9_1.png)
    



```python
# ----- for two variables
plt.figure()

ax, fig = joyplot(
    data=sydney_df[['MinTemp', 'MaxTemp', 'Month']], 
    by='Month',
    column=['MinTemp', 'MaxTemp'],
    color=['#686de0', '#eb4d4b'],
    legend=True,
    alpha=0.85,
    figsize=(12, 8)
)
plt.title('Ridgeline Plot of Min and Max Temperatures in Sydney', fontsize=20)
plt.show()
```


    <Figure size 432x288 with 0 Axes>



    
![png](output_10_1.png)
    

