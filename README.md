# Summary of Feature Selection Simulation

Author: Max Kuhn


## Introduction




This repo contains simulations related to the effect of machine learning models when additional predictors (that are pure noise) are included. The simulation considers the effect of the following factors:

* **models**: random forest, boosted trees, bagged trees, single layer neural networks, multivariate adaptive regression splines, K-nearest neighbors, the glmnet, and ordinary linear regression. 

* **training set size**: the data set consisted of either 500 or 1,000 data points. 

* **the number of extra predictors**: anywhere from 25 to 200 random noise predictors were added. Models with no noise predictors were assessed to get a baseline for performance. 

Each configuration of these parameters was repeated 100 times. Each repeat uses the same random number seeds so that the simulation results are matched. Within a _seed_ value, the change in performance between the baseline model (with no additional predictors) and one with additional predictors is a head-to-head assessment of the impact. 

Models with tuning parameters were tuned via 10-fold cross-validation. SVMs, neural networks, random forest, and boosted trees were all tuned with random search using 25 to 50 combinations. All other models used regular grid search. The details can be found in the `files` directories. 



## Simulation Data 

The data used in the simulation come from Sapp _et al._ (2014) as implemented in the [`caret` package.](https://www.rdocumentation.org/packages/caret/versions/6.0-81/topics/SLC14_1) All informative predictors are independent Gaussian random variables with mean zero and a variance of 9. The true prediction equation is:

```r
x_1 + sin(x_2) + log(abs(x_3)) + x_4 ^ 2 + x_5 * x_6 +
  I(x_7 * x_8 * x_9 < 0) + I(x_10 > 0) + x_11 * I(x_11 > 0) + sqrt(abs(x_12)) +
  cos(x_13) + 2 * x_14 + abs(x_15) + I(x_16 < -1) + x_17 * I(x_17 < -1) - 2 * x_18 -
  x_19 * x_20
```

The random error here is also Gaussian with mean zero and a variance of 9. Extra, non-informative predictors are independent standard uniform random data. 

Fore the two linear models, we assumed that people actually analyzing these data would detect the two linear interaction terms. For this reason, we included those terms in the linear model fits. 

## The Effect of Extra Predictors




The patterns for each model when extra noise predictors are included are:

<img src="figure/effect-plot-1.png" title="plot of chunk effect-plot" alt="plot of chunk effect-plot" width="95%" style="display: block; margin: auto;" />

Each point is the average of all simulation results. There were several model negatively affected by the inclusion of irrelevant data. and only a few were close to the lower bound of possible RMSE values (about 3). 

We can also look at these results as the average change from the baseline results:

<img src="figure/change-plot-1.png" title="plot of chunk change-plot" alt="plot of chunk change-plot" width="95%" style="display: block; margin: auto;" />

## Ability to Detect Informative Predictors

Since these data are simulated, we can determine how well some models selected predictors. The models assessed here are those that automatically include feature selection in their training process. 

We can define two measures of performance:

 * _sensitivity_ is the rate that models appropriately select the 20 informative predictors. This is the true positive rate for feature selection.
 * _specificity_ is the rate that models do not select the noise predictors. The false positive rate is 1 - specificity. 

These terms can be computed for each simulation and the averages are plotted below in a manner similar to an ROC curve. The best possible results would be values near the upper left corner of the plot. 

<img src="figure/roc-plot-1.png" title="plot of chunk roc-plot" alt="plot of chunk roc-plot" width="40%" style="display: block; margin: auto;" />

Different models are optimizing different types of errors. 

## Reproducibility

All of the files used in the simulation are contained in this repo. 

 * `files` the R code used for each simulation. The file names are `effects_{n}_{p}_{seed}.R` where `n` is the training set size, `p` is the numnber of extra predictors, and `seed` is the random number seed. `make -i -j 18` was used to run the files in a terminal process. 
 * `logs` contains the corresponding `Rout` files. 
 * `RData` hs the saved results for each simulation. 
 * `template.R` is has the basic code for each simulation with placeholders for the varying factors. `configure.R` creates 50 sets of simulations based on the template. Two passes of the simulation were used to generate 100 separate simulation sets. 
 
The R package versions between the two passes probably differ slightly. Here is the information from the first pass:

```r
> print(session_info())
─ Session info ───────────────────────────────────────────────────────────────
 setting  value                       
 version  R version 3.5.0 (2018-04-23)
 os       macOS High Sierra 10.13.6   
 system   x86_64, darwin15.6.0        
 ui       X11                         
 language (EN)                        
 collate  en_US.UTF-8                 
 ctype    en_US.UTF-8                 
 tz       America/New_York            
 date     2018-12-10                  

─ Packages ───────────────────────────────────────────────────────────────────
 package       * version    date       lib source        
 assertthat      0.2.0      2017-04-11 [1] CRAN (R 3.5.0)
 backports       1.1.2      2017-12-13 [1] CRAN (R 3.5.0)
 base64enc       0.1-3      2015-07-28 [1] CRAN (R 3.5.0)
 bindr           0.1.1      2018-03-13 [1] CRAN (R 3.5.0)
 bindrcpp        0.2.2      2018-03-29 [1] CRAN (R 3.5.0)
 broom           0.5.0      2018-07-17 [1] CRAN (R 3.5.0)
 caret         * 6.0-81     2018-11-20 [1] CRAN (R 3.5.0)
 cellranger      1.1.0      2016-07-27 [1] CRAN (R 3.5.0)
 class           7.3-14     2015-08-30 [1] CRAN (R 3.5.0)
 cli             1.0.1      2018-09-25 [1] CRAN (R 3.5.0)
 codetools       0.2-15     2016-10-05 [1] CRAN (R 3.5.0)
 colorspace      1.3-2      2016-12-14 [1] CRAN (R 3.5.0)
 crayon          1.3.4      2017-09-16 [1] CRAN (R 3.5.0)
 Cubist        * 0.2.2      2018-05-21 [1] CRAN (R 3.5.0)
 data.table      1.11.8     2018-09-30 [1] CRAN (R 3.5.0)
 dplyr         * 0.7.8      2018-11-10 [1] CRAN (R 3.5.0)
 e1071           1.7-0      2018-07-28 [1] CRAN (R 3.5.0)
 earth         * 4.6.3      2018-05-07 [1] CRAN (R 3.5.0)
 forcats       * 0.3.0      2018-02-19 [1] CRAN (R 3.5.0)
 foreach       * 1.4.4      2017-12-12 [1] CRAN (R 3.5.0)
 generics        0.0.2      2018-11-29 [1] CRAN (R 3.5.0)
 ggplot2       * 3.1.0      2018-10-25 [1] CRAN (R 3.5.0)
 glmnet        * 2.0-16     2018-04-02 [1] CRAN (R 3.5.0)
 glue            1.3.0      2018-07-17 [1] CRAN (R 3.5.0)
 gower           0.1.2      2017-02-23 [1] CRAN (R 3.5.0)
 gtable          0.2.0      2016-02-26 [1] CRAN (R 3.5.0)
 haven           1.1.2      2018-06-27 [1] CRAN (R 3.5.0)
 hms             0.4.2      2018-03-10 [1] CRAN (R 3.5.0)
 httr            1.3.1      2017-08-20 [1] CRAN (R 3.5.0)
 igraph          1.2.2      2018-07-27 [1] CRAN (R 3.5.0)
 ipred         * 0.9-8      2018-11-05 [1] CRAN (R 3.5.0)
 iterators       1.0.10     2018-07-13 [1] CRAN (R 3.5.0)
 jsonlite        1.5        2017-06-01 [1] CRAN (R 3.5.0)
 keras         * 2.2.4      2018-11-22 [1] CRAN (R 3.5.0)
 kernlab       * 0.9-27     2018-08-10 [1] CRAN (R 3.5.0)
 kknn          * 1.3.1      2016-03-26 [1] CRAN (R 3.5.0)
 lattice       * 0.20-38    2018-11-04 [1] CRAN (R 3.5.0)
 lava            1.6.4      2018-11-25 [1] CRAN (R 3.5.0)
 lazyeval        0.2.1      2017-10-29 [1] CRAN (R 3.5.0)
 lubridate       1.7.4      2018-04-11 [1] CRAN (R 3.5.0)
 magrittr        1.5        2014-11-22 [1] CRAN (R 3.5.0)
 MASS            7.3-51.1   2018-11-01 [1] CRAN (R 3.5.0)
 Matrix        * 1.2-15     2018-11-01 [1] CRAN (R 3.5.0)
 ModelMetrics    1.2.2      2018-11-03 [1] CRAN (R 3.5.0)
 modelr          0.1.2      2018-05-11 [1] CRAN (R 3.5.0)
 munsell         0.5.0      2018-06-12 [1] CRAN (R 3.5.0)
 nlme            3.1-137    2018-04-07 [1] CRAN (R 3.5.0)
 nnet            7.3-12     2016-02-02 [1] CRAN (R 3.5.0)
 pillar          1.3.0      2018-07-14 [1] CRAN (R 3.5.0)
 pkgconfig       2.0.2      2018-08-16 [1] CRAN (R 3.5.0)
 plotmo        * 3.5.0      2018-08-20 [1] CRAN (R 3.5.0)
 plotrix       * 3.7-4      2018-10-03 [1] CRAN (R 3.5.0)
 plyr            1.8.4      2016-06-08 [1] CRAN (R 3.5.0)
 prodlim         2018.04.18 2018-04-18 [1] CRAN (R 3.5.0)
 purrr         * 0.2.5      2018-05-29 [1] CRAN (R 3.5.0)
 R6              2.3.0      2018-10-04 [1] CRAN (R 3.5.0)
 ranger        * 0.10.1     2018-06-04 [1] CRAN (R 3.5.0)
 Rcpp            1.0.0      2018-11-07 [1] CRAN (R 3.5.0)
 readr         * 1.1.1      2017-05-16 [1] CRAN (R 3.5.0)
 readxl          1.1.0      2018-04-20 [1] CRAN (R 3.5.0)
 recipes         0.1.4      2018-11-19 [1] CRAN (R 3.5.0)
 reshape2        1.4.3      2017-12-11 [1] CRAN (R 3.5.0)
 reticulate      1.10       2018-08-05 [1] CRAN (R 3.5.0)
 rlang           0.3.0.1    2018-10-25 [1] CRAN (R 3.5.0)
 rpart           4.1-13     2018-02-23 [1] CRAN (R 3.5.0)
 rstudioapi      0.8        2018-10-02 [1] CRAN (R 3.5.0)
 rvest           0.3.2      2016-06-17 [1] CRAN (R 3.5.0)
 scales          1.0.0      2018-08-09 [1] CRAN (R 3.5.0)
 sessioninfo   * 1.1.1      2018-11-05 [1] CRAN (R 3.5.0)
 stringi         1.2.4      2018-07-20 [1] CRAN (R 3.5.0)
 stringr       * 1.3.1      2018-05-10 [1] CRAN (R 3.5.0)
 survival        2.43-1     2018-10-29 [1] CRAN (R 3.5.0)
 TeachingDemos * 2.10       2016-02-12 [1] CRAN (R 3.5.0)
 tensorflow      1.10       2018-11-19 [1] CRAN (R 3.5.0)
 tfruns          1.4        2018-08-25 [1] CRAN (R 3.5.0)
 tibble        * 1.4.2      2018-01-22 [1] CRAN (R 3.5.0)
 tidyr         * 0.8.2      2018-10-28 [1] CRAN (R 3.5.0)
 tidyselect      0.2.5      2018-10-11 [1] CRAN (R 3.5.0)
 tidyverse     * 1.2.1      2017-11-14 [1] CRAN (R 3.5.0)
 timeDate        3043.102   2018-02-21 [1] CRAN (R 3.5.0)
 whisker         0.3-2      2013-04-28 [1] CRAN (R 3.5.0)
 withr           2.1.2      2018-03-15 [1] CRAN (R 3.5.0)
 xgboost       * 0.71.2     2018-06-09 [1] CRAN (R 3.5.0)
 xml2            1.2.0      2018-01-24 [1] CRAN (R 3.5.0)
 zeallot         0.1.0      2018-01-28 [1] CRAN (R 3.5.0)

[1] /Library/Frameworks/R.framework/Versions/3.5/Resources/library
```
 




