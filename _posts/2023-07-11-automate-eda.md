---
layout: post
title: "Automate the Exploratory Data Analysis (EDA) to Understand the Data Faster and Easier"
date: 2023-07-11
categories: [Data Science, Python, R]
tags: [eda, python, r, data-analytics, data-visualization, ydata-profiling, sweetviz, autoviz, dtale]
author: Mochamad Kautzar Ichramsyah
description: "A walkthrough of automated EDA libraries in both R and Python — covering dataMaid, DataExplorer, SmartEDA, ydata-profiling, dtale, sweetviz, and autoviz using the classic iris dataset."
image: https://miro.medium.com/v2/resize:fit:1200/1*OOPA6XR-TnAZ3XeqgN6Egw.png
canonical_url: https://medium.com/codex/automate-the-exploratory-data-analysis-eda-to-understand-the-data-faster-not-better-2ed6ff230eed
---

> *Originally published on [Medium – CodeX](https://medium.com/codex/automate-the-exploratory-data-analysis-eda-to-understand-the-data-faster-not-better-2ed6ff230eed) · 11 min read*

---

## What is EDA?

EDA is one of the most important things we need to do as an approach to understand the dataset better. Almost all data analytics or data science professionals do this process before generating insights or doing data modeling. **In real life, this process takes a lot of time, depending on the complexity and completeness of the dataset we have.** More variables mean more exploration needed to get the summary before doing the next steps.

That's why, using R or Python — the most common programming languages for data analysis — some packages help do that process faster and easier, but not necessarily *better*. Why not better? Because they only give us a summary, before we focus on deeper exploration of any variables we find "interesting".

> *"The 80/20 rule applies: 80 percent of a data analyst or scientist's valuable time is spent simply finding, cleansing, and organizing data, leaving only 20 percent to perform analysis."*

---

## Which Libraries Can We Use?

### R Libraries
1. `dataMaid`
2. `DataExplorer`
3. `SmartEDA`

### Python Libraries
1. `ydata-profiling`
2. `dtale`
3. `sweetviz`
4. `autoviz`

Let's try each library to see what they look like and how they help with EDA! I'll use the classic [iris dataset](https://en.wikipedia.org/wiki/Iris_flower_data_set) throughout.

---

## Loading the Dataset

**In R:**

```r
# iris is part of R's default — no need to load any packages
df = iris
# use "head()" to show the first 6 rows
head(df)
```

**In Python:**

```python
from sklearn.datasets import load_iris
import numpy as np
import pandas as pd

iris = load_iris()

df = pd.DataFrame(
    data=np.c_[iris['data'], iris['target']],
    columns=iris['feature_names'] + ['species']
)

# set species as a categorical variable
df['species'] = pd.Categorical.from_codes(iris.target, iris.target_names)

df.head()
```

---

## R: dataMaid

```r
install.packages("dataMaid")
library(dataMaid)
makeDataReport(df, output = "html", replace = TRUE)
```

With one line of code, you already get a lot of information:

- **150 observations, 5 variables**
- Variable checks depending on data type: miscoded missing values, levels with < 6 obs, outliers
- A summary table including variable class, unique values, missing observations, and detected problems
- Central measurements (mean, median) + histograms for each numerical variable
- `Sepal.Width` and `Petal.Length` flagged as having possible outliers
- `Species` confirmed as a balanced factor — 50 observations per class

---

## R: DataExplorer

```r
install.packages("DataExplorer")
library(DataExplorer)
create_report(df)
```

DataExplorer covers similar ground to dataMaid but adds a few valuable extras:

- **QQ plots** for each numerical variable
- **Correlation matrix** — for example:
  - `Petal.Width` and `Petal.Length` have a strong positive correlation of **0.96** (wider petal = longer petal)
  - `Species_setosa` and `Petal.Length` have a strong negative correlation of **-0.92** (shorter petal = likely setosa)
- **PCA plot** — percentage of variance explained by principal components (62% with the first two)
- **Relative importance** — `Petal.Length` has the highest feature importance (~0.5)

---

## R: SmartEDA

```r
install.packages("SmartEDA")
library(SmartEDA)
ExpReport(df, op_file = 'SmartEDA_df.html')
```

SmartEDA adds a few things not seen in the previous two packages:

- **Density plots** with skewness and kurtosis measurements — useful for checking normality
- **Scatter plots** between all numerical variable pairs — a visual alternative to the correlation matrix
- Standard summary statistics alongside the visualizations

---

## R: Conclusion

Using all three packages, we quickly learn:

1. **No missing variables** → skip the cleaning step
2. **Outliers detected** in `Sepal.Width` and `Petal.Length` → know where to start cleaning
3. **Non-normal distributions** identified → decide if transformation is needed
4. **Correlation insights** → understand variable relationships at a glance
5. **PCA and feature importance** → ready for modeling decisions

---

## Python: ydata-profiling

```python
pip install ydata-profiling
from ydata_profiling import ProfileReport

pr_df = ProfileReport(df)
pr_df
```

Highlights compared to the R packages:

- **Interactive output** — tabbed navigation, expandable sections, filterable columns
- **Narrative alerts** — e.g., sentences like *"Variable X is highly correlated with Variable Y"*
- All the standard stats: distributions, missing values, correlations

---

## Python: dtale

```python
pip install dtale
import dtale

dtale.show(df)
```

dtale is fundamentally different in its approach — it's an **interactive data explorer** that opens in your browser. You can:

- Sort, filter, and drill into columns on the fly
- Build charts interactively without writing code
- View a correlation matrix, scatter plots, histograms — all clickable

> ⚠️ Common install issue: `ImportError: cannot import name 'escape' from 'jinja2'`. Fix [here](https://stackoverflow.com/questions/71718167/importerror-cannot-import-name-escape-from-jinja2).

---

## Python: sweetviz

```python
pip install sweetviz
import sweetviz

analyze_df = sweetviz.analyze([df, "df"], target_feat='species')
analyze_df.show_html('analyze.html')
```

sweetviz has the most visually polished output of all the Python packages. Key features:

- Side-by-side **target feature comparison** — it highlights how each feature differs across the target classes
- Beautiful, magazine-style HTML report
- Great for quick before/after comparisons (e.g., train vs. test splits)

> *"Human beings are visual creatures. The human brain processes images 60,000 times faster than text, and 90% of information transmitted to the brain is visual."*

---

## Python: autoviz

```python
pip install autoviz
from autoviz import AutoViz_Class

av = AutoViz_Class()
avt = av.AutoViz(
    "",
    sep=",",
    depVar="",
    dfte=df,
    header=0,
    verbose=1,
    lowess=False,
    chart_format="server",
    max_rows_analyzed=10000,
    max_cols_analyzed=10,
    save_plot_dir=None
)
```

autoviz takes a different display approach:

- Output is split across **multiple browser tabs** (rather than one scrollable report)
- Adds **violin plots** — a hybrid of box plot and kernel density estimate, great for visualizing distribution shape
- Good for a quick automated visual overview without writing any plotting code

---

## Python: Conclusion

Comparing all four Python libraries:

| Library | Output Style | Interactivity | Unique Strength |
|---|---|---|---|
| `ydata-profiling` | Single HTML | Medium | Narrative alerts, completeness |
| `dtale` | Browser app | High | Real-time filtering & exploration |
| `sweetviz` | Single HTML | Low | Visual polish, target comparison |
| `autoviz` | Multi-tab browser | Low | Violin plots, quick overview |

---

## Overall Conclusion

> *Which one should I use? Which one is the best?*

**It depends.** Cutting the time spent on EDA is already a win. Use these packages as a starting point — a quick map of the terrain — before diving into the deeper, manual exploration.

In my opinion, exploring data should be the **"fun" part** of the process. Don't be afraid to get your hands dirty doing EDA manually. Sometimes the non-automated method is still the best. These tools are a complement, not a replacement.

---

*Thanks for reading! If you found any mistakes or have questions, feel free to reach out.*

*Tags: `data-analytics` `data-science` `python` `r` `eda` `data-visualization`*
