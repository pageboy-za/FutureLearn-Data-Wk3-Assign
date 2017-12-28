
# Project 3: GDP and life expectancy

by Michel Wermelinger, 27 August 2015, updated 5 April 2016

This is the project notebook for Week 3 of The Open University's [_Learn to code for Data Analysis_](http://futurelearn.com/courses/learn-to-code) course.

Richer countries can afford to invest more on healthcare, on work and road safety, and other measures that reduce mortality. On the other hand, richer countries may have less healthy lifestyles. Is there any relation between the wealth of a country and the life expectancy of its inhabitants?

The following analysis checks whether there is any correlation between the total gross domestic product (GDP) of a country in 2013 and the life expectancy of people born in that country in 2013.

## Getting the data

Two datasets of the World Bank are considered. One dataset, available at <http://data.worldbank.org/indicator/NY.GDP.MKTP.CD>, lists the GDP of the world's countries in current US dollars, for various years. The use of a common currency allows us to compare GDP values across countries. The other dataset, available at <http://data.worldbank.org/indicator/SP.DYN.LE00.IN>, lists the life expectancy of the world's countries. The datasets were downloaded as CSV files in March 2016.


```python
import warnings
warnings.simplefilter('ignore', FutureWarning)

from pandas import *

YEAR = 2013
GDP_INDICATOR = 'NY.GDP.MKTP.CD'
gdpReset = read_csv('WB GDP 2013.csv')

LIFE_INDICATOR = 'SP.DYN.LE00.IN'
lifeReset = read_csv('WB LE 2013.csv')
lifeReset.head()
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
      <th>country</th>
      <th>year</th>
      <th>SP.DYN.LE00.IN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arab World</td>
      <td>2013</td>
      <td>70.631305</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Caribbean small states</td>
      <td>2013</td>
      <td>71.901964</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Central Europe and the Baltics</td>
      <td>2013</td>
      <td>76.127583</td>
    </tr>
    <tr>
      <th>3</th>
      <td>East Asia &amp; Pacific (all income levels)</td>
      <td>2013</td>
      <td>74.604619</td>
    </tr>
    <tr>
      <th>4</th>
      <td>East Asia &amp; Pacific (developing only)</td>
      <td>2013</td>
      <td>73.657617</td>
    </tr>
  </tbody>
</table>
</div>




```python
POPULATION_INDICATOR = 'SP.POP.TOTL'
popReset = read_csv('WB POP 2013.csv')

popReset.head()
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
      <th>country</th>
      <th>year</th>
      <th>SP.POP.TOTL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arab World</td>
      <td>2013</td>
      <td>3.770967e+08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Caribbean small states</td>
      <td>2013</td>
      <td>6.975819e+06</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Central Europe and the Baltics</td>
      <td>2013</td>
      <td>1.037137e+08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>East Asia &amp; Pacific (all income levels)</td>
      <td>2013</td>
      <td>2.248867e+09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>East Asia &amp; Pacific (developing only)</td>
      <td>2013</td>
      <td>2.006073e+09</td>
    </tr>
  </tbody>
</table>
</div>



## Cleaning the data

Inspecting the data with `head()` and `tail()` shows that:

1. the first 34 rows are aggregated data, for the Arab World, the Caribbean small states, and other country groups used by the World Bank;
- GDP and life expectancy values are missing for some countries.

The data is therefore cleaned by:
1. removing the first 34 rows;
- removing rows with unavailable values.


```python
gdpCountries = gdpReset[34:].dropna()
lifeCountries = lifeReset[34:].dropna()
popCountries = popReset[34:].dropna()
```

## Transforming the data

The World Bank reports GDP in US dollars and cents. To make the data easier to read, the GDP is converted to millions of British pounds (the author's local currency) with the following auxiliary functions, using the average 2013 dollar-to-pound conversion rate provided by <http://www.ukforex.co.uk/forex-tools/historical-rate-tools/yearly-average-rates>. 


```python
def roundToMillions (value):
    return round(value / 1000000)

def usdToGBP (usd):
    return usd / 1.564768

GDP = 'GDP (£m)'
gdpCountries[GDP] = gdpCountries[GDP_INDICATOR].apply(usdToGBP).apply(roundToMillions)
gdpCountries.head()
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
      <th>country</th>
      <th>year</th>
      <th>NY.GDP.MKTP.CD</th>
      <th>GDP (£m)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Afghanistan</td>
      <td>2013</td>
      <td>2.045894e+10</td>
      <td>13075</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Albania</td>
      <td>2013</td>
      <td>1.278103e+10</td>
      <td>8168</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Algeria</td>
      <td>2013</td>
      <td>2.097035e+11</td>
      <td>134016</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Andorra</td>
      <td>2013</td>
      <td>3.249101e+09</td>
      <td>2076</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Angola</td>
      <td>2013</td>
      <td>1.383568e+11</td>
      <td>88420</td>
    </tr>
  </tbody>
</table>
</div>



The unnecessary columns can be dropped.


```python
COUNTRY = 'country'
headings = [COUNTRY, GDP]
gdpClean = gdpCountries[headings]
gdpClean.head()
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
      <th>country</th>
      <th>GDP (£m)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Afghanistan</td>
      <td>13075</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Albania</td>
      <td>8168</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Algeria</td>
      <td>134016</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Andorra</td>
      <td>2076</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Angola</td>
      <td>88420</td>
    </tr>
  </tbody>
</table>
</div>



The World Bank reports the life expectancy with several decimal places. After rounding, the original column is discarded.


```python
LIFE = 'Life expectancy (years)'
lifeCountries[LIFE] = lifeCountries[LIFE_INDICATOR].apply(round)
headings = [COUNTRY, LIFE]
lifeClean = lifeCountries[headings]
lifeClean.head()
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
      <th>country</th>
      <th>Life expectancy (years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Afghanistan</td>
      <td>60</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Albania</td>
      <td>78</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Algeria</td>
      <td>75</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Angola</td>
      <td>52</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Antigua and Barbuda</td>
      <td>76</td>
    </tr>
  </tbody>
</table>
</div>



## Combining the data

The tables are combined through an inner join on the common 'country' column. 


```python
gdpVsLife = merge(gdpClean, lifeClean, on=COUNTRY, how='inner')
gdpVsLife.head()
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>13075</td>
      <td>60</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>8168</td>
      <td>78</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>134016</td>
      <td>75</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>88420</td>
      <td>52</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antigua and Barbuda</td>
      <td>767</td>
      <td>76</td>
    </tr>
  </tbody>
</table>
</div>



## Calculating the correlation

To measure if the life expectancy and the GDP grow together, the Spearman rank correlation coefficient is used. It is a number from -1 (perfect inverse rank correlation: if one indicator increases, the other decreases) to 1 (perfect direct rank correlation: if one indicator increases, so does the other), with 0 meaning there is no rank correlation. A perfect correlation doesn't imply any cause-effect relation between the two indicators. A p-value below 0.05 means the correlation is statistically significant.


```python
from scipy.stats import spearmanr

gdpColumn = gdpVsLife[GDP]
lifeColumn = gdpVsLife[LIFE]
(correlation, pValue) = spearmanr(gdpColumn, lifeColumn)
print('The correlation is', correlation)
if pValue < 0.05:
    print('It is statistically significant.')
else:
    print('It is not statistically significant.')
```

    The correlation is 0.501023238967
    It is statistically significant.


The value shows a direct correlation, i.e. richer countries tend to have longer life expectancy, but it is not very strong.

## Showing the data

Measures of correlation can be misleading, so it is best to see the overall picture with a scatterplot. The GDP axis uses a logarithmic scale to better display the vast range of GDP values, from a few million to several billion (million of million) pounds.


```python
%matplotlib inline
gdpVsLife.plot(x=GDP, y=LIFE, kind='scatter', grid=True, logx=True, figsize=(10, 5))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x113480550>




![png](output_18_1.png)


The plot shows there is no clear correlation: there are rich countries with low life expectancy, poor countries with high expectancy, and countries with around 10 thousand (10<sup>4</sup>) million pounds GDP have almost the full range of values, from below 50 to over 80 years. Towards the lower and higher end of GDP, the variation diminishes. Above 40 thousand million pounds of GDP (3rd tick mark to the right of 10<sup>4</sup>), most countries have an expectancy of 70 years or more, whilst below that threshold most countries' life expectancy is below 70 years. 

Comparing the 10 poorest countries and the 10 countries with the lowest life expectancy shows that total GDP is a rather crude measure. The population size should be taken into account for a more precise definiton of what 'poor' and 'rich' means. Furthermore, looking at the countries below, droughts and internal conflicts may also play a role in life expectancy. 


```python
# the 10 countries with lowest GDP
gdpVsLife.sort_values(GDP).head(10)
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>87</th>
      <td>Kiribati</td>
      <td>108</td>
      <td>66</td>
    </tr>
    <tr>
      <th>141</th>
      <td>Sao Tome and Principe</td>
      <td>195</td>
      <td>66</td>
    </tr>
    <tr>
      <th>111</th>
      <td>Micronesia, Fed. Sts.</td>
      <td>202</td>
      <td>69</td>
    </tr>
    <tr>
      <th>168</th>
      <td>Tonga</td>
      <td>277</td>
      <td>73</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Comoros</td>
      <td>383</td>
      <td>63</td>
    </tr>
    <tr>
      <th>157</th>
      <td>St. Vincent and the Grenadines</td>
      <td>461</td>
      <td>73</td>
    </tr>
    <tr>
      <th>140</th>
      <td>Samoa</td>
      <td>509</td>
      <td>73</td>
    </tr>
    <tr>
      <th>180</th>
      <td>Vanuatu</td>
      <td>512</td>
      <td>72</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Grenada</td>
      <td>538</td>
      <td>73</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Gambia, The</td>
      <td>578</td>
      <td>60</td>
    </tr>
  </tbody>
</table>
</div>




```python
# the 10 countries with lowest life expectancy
gdpVsLife.sort_values(LIFE).head(10)
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>95</th>
      <td>Lesotho</td>
      <td>1418</td>
      <td>49</td>
    </tr>
    <tr>
      <th>160</th>
      <td>Swaziland</td>
      <td>2916</td>
      <td>49</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Central African Republic</td>
      <td>983</td>
      <td>50</td>
    </tr>
    <tr>
      <th>146</th>
      <td>Sierra Leone</td>
      <td>3092</td>
      <td>50</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Chad</td>
      <td>8276</td>
      <td>51</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Cote d'Ivoire</td>
      <td>19998</td>
      <td>51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>88420</td>
      <td>52</td>
    </tr>
    <tr>
      <th>124</th>
      <td>Nigeria</td>
      <td>329100</td>
      <td>52</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Cameroon</td>
      <td>18896</td>
      <td>55</td>
    </tr>
    <tr>
      <th>153</th>
      <td>South Sudan</td>
      <td>8473</td>
      <td>55</td>
    </tr>
  </tbody>
</table>
</div>




```python
def populationMillion(populationThousands):
    return round((populationThousands /1000))

headings = [COUNTRY, POPULATION_INDICATOR]
popClean = popCountries[headings]
```


```python
gdpVsLifeWithPop = merge(gdpVsLife, popClean, on=COUNTRY, how='inner')
POP = 'Population Total (M)'
column = gdpVsLifeWithPop[POPULATION_INDICATOR]
gdpVsLifeWithPop[POP] = column.apply(populationMillion)
headings = [COUNTRY, GDP, LIFE, POP]

GLP = gdpVsLifeWithPop[headings]
GLP.head()
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
      <th>Population Total (M)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>13075</td>
      <td>60</td>
      <td>30682</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>8168</td>
      <td>78</td>
      <td>2897</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>134016</td>
      <td>75</td>
      <td>38186</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>88420</td>
      <td>52</td>
      <td>23448</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antigua and Barbuda</td>
      <td>767</td>
      <td>76</td>
      <td>90</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate GDP per capita by adding the population data from exercises and then dividing GDP by the
# population in millions. 


GDP_PER_CAP = 'GDP Per Capita'

GLP[GDP_PER_CAP] = GLP[GDP]/GLP[POP]

```


```python
GLP.sort_values(GDP_PER_CAP).head(10)
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
      <th>Population Total (M)</th>
      <th>GDP Per Capita</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>103</th>
      <td>Malawi</td>
      <td>2482</td>
      <td>61</td>
      <td>16190</td>
      <td>0.153305</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Burundi</td>
      <td>1735</td>
      <td>56</td>
      <td>10466</td>
      <td>0.165775</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Central African Republic</td>
      <td>983</td>
      <td>50</td>
      <td>4711</td>
      <td>0.208661</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Congo, Dem. Rep.</td>
      <td>19182</td>
      <td>58</td>
      <td>72553</td>
      <td>0.264386</td>
    </tr>
    <tr>
      <th>123</th>
      <td>Niger</td>
      <td>4910</td>
      <td>61</td>
      <td>18359</td>
      <td>0.267444</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Liberia</td>
      <td>1244</td>
      <td>61</td>
      <td>4294</td>
      <td>0.289707</td>
    </tr>
    <tr>
      <th>102</th>
      <td>Madagascar</td>
      <td>6783</td>
      <td>65</td>
      <td>22925</td>
      <td>0.295878</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Gambia, The</td>
      <td>578</td>
      <td>60</td>
      <td>1867</td>
      <td>0.309588</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Ethiopia</td>
      <td>30451</td>
      <td>63</td>
      <td>94558</td>
      <td>0.322035</td>
    </tr>
    <tr>
      <th>151</th>
      <td>Somalia</td>
      <td>3420</td>
      <td>55</td>
      <td>10268</td>
      <td>0.333074</td>
    </tr>
  </tbody>
</table>
</div>




```python
GLP.sort_values(LIFE).head(10)
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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
      <th>Population Total (M)</th>
      <th>GDP Per Capita</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>95</th>
      <td>Lesotho</td>
      <td>1418</td>
      <td>49</td>
      <td>2083</td>
      <td>0.680749</td>
    </tr>
    <tr>
      <th>160</th>
      <td>Swaziland</td>
      <td>2916</td>
      <td>49</td>
      <td>1251</td>
      <td>2.330935</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Central African Republic</td>
      <td>983</td>
      <td>50</td>
      <td>4711</td>
      <td>0.208661</td>
    </tr>
    <tr>
      <th>146</th>
      <td>Sierra Leone</td>
      <td>3092</td>
      <td>50</td>
      <td>6179</td>
      <td>0.500405</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Chad</td>
      <td>8276</td>
      <td>51</td>
      <td>13146</td>
      <td>0.629545</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Cote d'Ivoire</td>
      <td>19998</td>
      <td>51</td>
      <td>21622</td>
      <td>0.924891</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>88420</td>
      <td>52</td>
      <td>23448</td>
      <td>3.770897</td>
    </tr>
    <tr>
      <th>124</th>
      <td>Nigeria</td>
      <td>329100</td>
      <td>52</td>
      <td>172817</td>
      <td>1.904327</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Cameroon</td>
      <td>18896</td>
      <td>55</td>
      <td>22211</td>
      <td>0.850750</td>
    </tr>
    <tr>
      <th>153</th>
      <td>South Sudan</td>
      <td>8473</td>
      <td>55</td>
      <td>11454</td>
      <td>0.739742</td>
    </tr>
  </tbody>
</table>
</div>




```python
#ID the richest countries with the lowest life expectancy i.e < 60
life = GLP[LIFE]

lowLife = GLP[life < 60]

lowLife.sort_values(GDP, ascending=False).head(10)


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
      <th>country</th>
      <th>GDP (£m)</th>
      <th>Life expectancy (years)</th>
      <th>Population Total (M)</th>
      <th>GDP Per Capita</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>124</th>
      <td>Nigeria</td>
      <td>329100</td>
      <td>52</td>
      <td>172817</td>
      <td>1.904327</td>
    </tr>
    <tr>
      <th>152</th>
      <td>South Africa</td>
      <td>234056</td>
      <td>57</td>
      <td>53157</td>
      <td>4.403108</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>88420</td>
      <td>52</td>
      <td>23448</td>
      <td>3.770897</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Cote d'Ivoire</td>
      <td>19998</td>
      <td>51</td>
      <td>21622</td>
      <td>0.924891</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Congo, Dem. Rep.</td>
      <td>19182</td>
      <td>58</td>
      <td>72553</td>
      <td>0.264386</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Cameroon</td>
      <td>18896</td>
      <td>55</td>
      <td>22211</td>
      <td>0.850750</td>
    </tr>
    <tr>
      <th>184</th>
      <td>Zambia</td>
      <td>17140</td>
      <td>59</td>
      <td>15246</td>
      <td>1.124229</td>
    </tr>
    <tr>
      <th>173</th>
      <td>Uganda</td>
      <td>15761</td>
      <td>58</td>
      <td>36573</td>
      <td>0.430946</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Equatorial Guinea</td>
      <td>10951</td>
      <td>57</td>
      <td>797</td>
      <td>13.740276</td>
    </tr>
    <tr>
      <th>116</th>
      <td>Mozambique</td>
      <td>10237</td>
      <td>55</td>
      <td>26467</td>
      <td>0.386784</td>
    </tr>
  </tbody>
</table>
</div>



South Africa and Nigeria are the two standouts - with **high** GPD and **low** life expectancy.


```python
gdpPerC_Column = GLP[GDP_PER_CAP]
lifeColumn = GLP[LIFE]
(correlation, pValue) = spearmanr(gdpPerC_Column, lifeColumn)
print('The correlation is', correlation)
if pValue < 0.05:
    print('It is statistically significant.')
else:
    print('It is not statistically significant.')
```

    The correlation is 0.850278801176
    It is statistically significant.


Looking at the correlation between **GDP Per Capita** it shows a statistically significantly high correlation with life expectancy. Much greater than 


```python
GLP.plot(x=GDP_PER_CAP, y=LIFE, kind='scatter', grid=True, logx=True, figsize=(10, 5))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1145de748>




![png](output_31_1.png)


The Scatter plot of GDP Per Capita also seems to show some clustering there seems to be a trend line 

## Conclusions

To sum up, there is no strong correlation between a country's wealth and the life expectancy of its inhabitants: there is often a wide variation of life expectancy for countries with similar GDP, countries with the lowest life expectancy are not the poorest countries, and countries with the highest expectancy are not the richest countries. Nevertheless there is some relationship, because the vast majority of countries with a life expectancy below 70 years is on the left half of the scatterplot.

Using the [NY.GDP.PCAP.PP.CD](http://data.worldbank.org/indicator/NY.GDP.PCAP.PP.CD) indicator, GDP per capita in current 'international dollars', would make for a better like-for-like comparison between countries, because it would take population and purchasing power into account. Using more specific data, like expediture on health, could also lead to a better analysis.
