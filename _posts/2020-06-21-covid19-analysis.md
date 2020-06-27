---
layout: post
title: "COVID19 Data Analysis Using Python"
date: 2020-06-21

description: "A fun little project in where I apply methods of statistical analysis and data exploration to COVID-19 infection rates and related macroeconomic data"
certid: CHWDWKFJK2X2
projectid: covid-19
---

This is a fun little project in where I apply basic statistical analysis of COVID-19 infection rates and comparing them to macroeconomic data. You can check out the project's GitHub page, but I'll break down __some__ of the subtasks here. I won't go over everything, just the relevant bits ;-)

__Task 1: Importing the modules__

Alright, this is pretty straightforward. I will import the necessary modules needed for this project:

```python
import pandas as pd # do a "pip3 install pandas" if you don't have it.
import numpy as np # do a "pip3 install numpy" if you don't have it.
import seaborn as sns # do a "pip3 install seaborn" if you don't have it.
import matplotlib.pyplot as plt # do a "pip3 install matplotlib" if you don't have it.
```

__Task 2: Processing the COVID-19 Dataset__

First, I will import the COVID-19 dataset (```Datasets/covid19_Confirmed_dataset.csv```) with _pandas'_ [read_csv()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html) function and storing it in a variable called ```corona_dataset_csv```. To get a "feel" of what I'll be working with, I'll take a look at the first ten lines with [head()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.head.html) function parameterised with a ```10```, and finally I will work out the dimensions of the dataset with the [.shape](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.shape.html) tuple.

```python
corona_dataset_csv = pd.read_csv("Datasets/covid19_Confirmed_dataset.csv")
corona_dataset_csv.head(10)
corona_dataset_csv.shape
```

Next, I need to clean out rubbish in the dataset, and make it workable with analysis. So, I will delete useless columns with the [drop()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.drop.html) function with parameters _axis_ and _inplace_ set to ```axis=1``` and ```inplace=True``` respectively, and then store this new table into ```df```. I shall then group each region by its respective country with the [groupby()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html) function, sum it up, and place them into a variable called ```corona_dataset_aggregated```.

```python
df = corona_dataset_csv.drop(["Lat", "Long"],axis=1,inplace=True)
corona_dataset_aggregated = corona_dataset_csv.groupby("Country/Region").sum()
```

I will now do further data exploration by plotting the change in infection rate for the countries China, Italy and Spain with matplotlib's [plot()](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.plot.html) function, coupled with a nice legend.

```python
corona_dataset_aggregated.loc["China"].plot()
corona_dataset_aggregated.loc["Italy"].plot()
corona_dataset_aggregated.loc["Spain"].plot()
plt.legend()
```

__Task 3: Working out a good measure__

I'll calculate the difference---the average change in rate---with the [diff()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.diff.html) function, and then work out the derivative's maximum with the [max()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.max.html) function for the countries China, Italy and Spain.

```python
corona_dataset_aggregated.loc["China"].diff().max() # 15136.0
corona_dataset_aggregated.loc["Italy"].diff().max() # 6557.0
corona_dataset_aggregated.loc["Spain"].diff().max() # 9630.0
```

__Task 3 (cont): Find the maximum infection rate for all countries__

The following snippet calculates the maximum infection rate for all countries:

```python
countries = list(corona_dataset_aggregated.index)
max_infection_rates = []

for c in countries:
    max_infection_rates.append(corona_dataset_aggregated.loc[c].diff().max())

corona_dataset_aggregated["max_infection_rate"] = max_infection_rates
```

__Task 4: The happiness report dataset__

I want to work out the relationship between infection rate by country and metrics found within the happiness report. I imported the ```worldwide_happiness_report.csv``` dataset and dropped the columns that were not of interest:

```python
happiness_report_csv = pd.read_csv("Datasets/worldwide_happiness_report.csv")
useless_cols = ["Overall rank", "Score", "Generosity", "Perceptions of corruption"]
happiness_report_csv.drop(useless_cols, axis=1, inplace=True)
happiness_report_csv.set_index("Country or region", inplace=True)
```

I also did an "inner join" of the happiness report, stored the inner join into a variable called ```data``` and computed its correlation matrix with a function called ```corr()```.

```python
data = corona_data.join(happiness_report_csv, how="inner")
data.corr()
```

__Final Task (5): Regression models of infection rates v. some macroeconomic statistic__

Finally, I used seaborn's ```regplot()``` to do statistical analysis by creating simple regression models and comparing them to some macroeconomic variable (s.a. GDP or social support):

```python
x = data["GDP per capita"]
y = data["max_infection_rate"]
sns.regplot(x, np.log(y))

x = data["Social support"]
y = data["max_infection_rate"]
sns.regplot(x, np.log(y))

x = data["Healthy life expectancy"]
y = data["max_infection_rate"]
sns.regplot(x, np.log(y))

x = data["Freedom to make life choices"]
y = data["max_infection_rate"]
sns.regplot(x, np.log(y))
```
