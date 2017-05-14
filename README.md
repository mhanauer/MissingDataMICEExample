# MissingDataMICEExample
---
title: "Missing Data Example with MICE in R"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

First, we need to library the MICE package and set the data that we will be using for the example.  The nhanes 2 data set is a small data set designed for missing data examples.  It contains four variables that are quantitative and binary (yes no variables).  Using the MICE package is easy and all that you need to do is select your data and use set.seed for reproducibility.  

This creates the default five imputed data sets, which are each analyzed separately in the fit object.  To obtain the correct standard errors, data set should be analyzed separately and then their parameter estimates can be combined after the analyses are completed, which is described below with the pool function.

```{r, message=FALSE, warning=FALSE}
library(mice)
data("nhanes2")
head(nhanes2)

nhances2.imp = mice(nhanes2, seed = 12345)
```
Then we can get a summary of what the imputation method did.  It first shows us the number of imputed data sets it created, which in this case is five.  Then it shows the number of missing values per variable.  It also describes the imputation method used.  For categorical variables such as hyp, it uses a log regression, because the data is not continuous.  

For the predictor matrix, it shows the variables used in predicting missing values for that variable.  For example, when predicting missing values for age, the model used all three of the other included variables.

Then we can run a regression using the imputed data sets with the standard lm function in R.  Here we create a model that regresses chl on age and bmi.

Finally, we can use the pool function to pool the regression results together over the five data sets to obtain one final result.

```{r}
summary(nhances2.imp)

fit = with(nhances2.imp, lm(chl ~ age + bmi))
round(summary(pool(fit)),2)

```
