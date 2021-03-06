---
title: Tidymodels for beginners
author: ''
date: '2020-08-15'
slug: tidymodels-for-beginners
categories: [tidymodels]
tags: []
subtitle: ''
summary: 'In this lesson you will learn all that you need to know about tidymodels to get you started with the framework'
authors: []
lastmod: '2020-08-15T22:55:13+01:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: true
projects: []
---
For this lesson i prepared a decision chart that will help people who are looking to start using the tidymodels framework but don't know where to begin from understand it from a starter point of view and i will also answer some popular tidymodels related questions i have come across online.  
I have seen people complaining about how they want to start using the tidymodels r framework but haven't really found tutorials that breaks things down enough to their level. If you are in that category of people then this lesson is for you. R is a great programming language that continues to evolve, one of the things that attracts people to R is its simplicity and how there is always a package for anything/anytask. 
It might interest you to know that the brain behind R's popular machine learning package "caret" is also part of the team behind the tidymodels framework.  

The diagram below is an overview chart i created to give you a general understanding of the tidymodels workflow  

{{< figure library="true" src="tidymodels_framework_overview.png" alt="tidymodels framework overview">}} 

* *the `recipes` package is an alternative method for creating and pre-processing design matrices that can be used for modeling or visualization*  
* *the `rsample` package contains a set of functions to create different types of resamples and corresponding classes for their analysis*  
* *the `dials` package provides a framework for defining, creating and managing tunning parameters for modeling*
* *the `tunes` package contains functions and classes to be used in conjunction with other `tidymodels` packages for finding reasonable values of hyper-parameters in models*  
* *`parsnip` is a collection of modeling packages designed with common APIs and shared philosophy*  
* *`yardstick` is a package to estimate how well models are working using tidy data principles*


## **Some popular tidymodels related questions i have come across online:**  

1. what is tidymodes?
2. How many packages are in the tidymodels framework?
3. How can i install tidymodels?
4. Is tidymodels better than caret?
5. Can bootstrap resampling be done with tidymodels?
6. How fast is tidymodels for modeling?

## **Answers:**

<center> <h3> <b>WHAT IS TIDYMODELS</b> </h3> </center>  

Tidymodels is a collection of r packages for modeling and machine learning using tidyverse principles.  

<center> <h3> <b>HOW MANY PACKAGES ARE IN THE TIDYMODELS FRAMEWORK</b> </h3> </center>  

As at the time of this write up there are 15 packages in tidymodels v0.1.1  

<center> <h3> <b>HOW CAN I INSTALL TIDYMODELS</b> </h3> </center>  

Like any other r package available on cran tidymodels can be installed using `install.packages("tidymodels")` or using devtools.

<center> <h3> <b>IS TIDYMODELS BETTER THAN CARET</b> </h3> </center>  

People tend to ask this particular question a lot. The caret package is much older and has more functionalities when compared to tidymodels, but with the pace at which the tidymodels team are regularly updating the framework with new packages and functionalities i think it would become the preferred choice for most people soon.  

<center> <h3> <b>CAN BOOTSTRAP RESAMPLING BE DONE WITH TIDYMODELS</b> </h3> </center>  

Yes, bootstrap resampling can be done with the `rsample` package inside the tidymodels framework.  

<center> <h3> <b>HOW FAST IS TIDYMODELS FOR MODELING</b> </h3> </center>  

tidymodels framework supports the use of multiple cores for processing, however, one still needs to load packages like `foreach`, `doMC`, `doSNOW`, `doParallel` independently to register the parallel backend.  

## EXAMPLE  

Now that we have a general understanding of the tidymodels framework let us experiment with a simple example.
We will use the popular mtcars dataset for this example. We will build a linear regression model the tidymodels way, Our goal is to predict fuel consumption (mpg) given displacement (disp).  

### Install package

```
install.packages("tidymodels") # skip if you have already installed it..
```

### Load package for use


```r
library(tidymodels)
```

```
## -- Attaching packages ---------------------------------------------------------------------- tidymodels 0.1.1 --
```

```
## v broom     0.7.0      v recipes   0.1.13
## v dials     0.0.9      v rsample   0.0.8 
## v dplyr     1.0.2      v tibble    3.0.3 
## v ggplot2   3.3.2      v tidyr     1.1.2 
## v infer     0.5.3      v tune      0.1.1 
## v modeldata 0.0.2      v workflows 0.2.0 
## v parsnip   0.1.3      v yardstick 0.0.7 
## v purrr     0.3.4
```

```
## -- Conflicts ------------------------------------------------------------------------- tidymodels_conflicts() --
## x purrr::discard() masks scales::discard()
## x dplyr::filter()  masks stats::filter()
## x dplyr::lag()     masks stats::lag()
## x recipes::step()  masks stats::step()
```


### Load the dataset


```r
data("mtcars") # the dataset is available in R
```

### Pre-process 
The only pre-processing we will do is to remove all other variables from the dataset and keep only variables of interest "mpg", "disp"

**Create a recipe and remove some variables with the** `step_rm` **function**

```r
prep_rec <- recipe(mpg~., data = mtcars) %>% 
  step_rm(cyl, hp, drat, wt, qsec, vs, am, gear, carb) %>% 
  prep()
```

**See what our prep_rec looks like**


```r
prep_rec
```

```
## Data Recipe
## 
## Inputs:
## 
##       role #variables
##    outcome          1
##  predictor         10
## 
## Training data contained 32 data points and no missing data.
## 
## Operations:
## 
## Variables removed cyl, hp, drat, wt, qsec, vs, am, gear, carb [trained]
```
**Notice the "Variables removed" part indicating all the variables we got rid of**


### See what the pre-processed data looks like with the `juice()` function


```r
mtcars_juiced <- juice(prep_rec)


head(mtcars_juiced)
```

```
## # A tibble: 6 x 2
##    disp   mpg
##   <dbl> <dbl>
## 1   160  21  
## 2   160  21  
## 3   108  22.8
## 4   258  21.4
## 5   360  18.7
## 6   225  18.1
```
**`juice()` is used to extract pre-processed data out of a prepared recipe**

### Fit model


```r
my_model <- linear_reg() %>% 
  set_engine("lm") %>% 
  fit(mpg~., data = mtcars_juiced)
```

### Make prediction


```r
(predicted <- my_model %>% predict(mtcars_juiced) %>% mutate(actual = mtcars_juiced$mpg)) %>% head()
```

```
## # A tibble: 6 x 2
##   .pred actual
##   <dbl>  <dbl>
## 1  23.0   21  
## 2  23.0   21  
## 3  25.1   22.8
## 4  19.0   21.4
## 5  14.8   18.7
## 6  20.3   18.1
```

**View metric**


```r
predicted %>% metrics(truth = actual, estimate = .pred)
```

```
## # A tibble: 3 x 3
##   .metric .estimator .estimate
##   <chr>   <chr>          <dbl>
## 1 rmse    standard       3.15 
## 2 rsq     standard       0.718
## 3 mae     standard       2.61
```


## Wrap-up  

The example above is only to give you a glimpse of how powerful and easy the tidymodels framework is, there is much more that can be done with the packages that it compounds, the idea was just to get you started and show its beautiful interface.. 

Don't forget to share this lesson to others if you find it helpful... Thanks.
