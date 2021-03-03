# ml-notes

## General

- [An up-to-date collection](https://cran.r-project.org/web/views/MachineLearning.html) and classification of R Machine Learning packages on CRAN
- [`mlr3` package](https://github.com/mlr-org/mlr3/)
- [`caret` package](https://cran.r-project.org/web/packages/caret/index.html)
- [`Xgboost` package](https://xgboost.readthedocs.io/en/latest/) 
  General methodology for classification and regression, very powerfull (seems to be), builds on (weak) model ensembles, usually (decision) trees (closer to Random Forest in that regard) - to check at some point!

## Feature selection/importance

- For **linear regression** (i.e. after using the `lm` function in R), a good package that provides *feature selection* based on diverse criteria (e.g. p-value) is [`SignifReg`](https://cran.r-project.org/web/packages/SignifReg/index.html).
- Correlation can be used in 2 ways:
  - Remove features with an absolute correlation of **0.75 or higher** when finding the correlation between variables in a training dataset (NO RESPONSE is used here - unsupervised so-to-speak)
  - Variables that correlate highly with the response output, have **higher importance or significance**
  - Visualize correlations with: `corrplot` library ([examples](https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html))
- Use **Lasso (L1)** regularization or **Elastic Net (L1 and L2)** to remove unimportant features - **larger coefficient => more importance** (zeroed coefficients => good riddance!)
  - Use [`gmlnet` package](https://cran.r-project.org/web/packages/glmnet/index.html)
  - [Nice analysis](https://www4.stat.ncsu.edu/~post/josh/LASSO_Ridge_Elastic_Net_-_Examples.html)
  for cross-validating the `alpha` parameter
  - [Slides for Elastic Nets](https://web.stanford.edu/~hastie/TALKS/enet_talk.pdf) 
- Use [`randomForest` package](https://cran.r-project.org/web/packages/randomForest/index.html) 
  - Visualize importance: `varImpPlot()`
  - [Article: Tune number of Trees?](https://stats.stackexchange.com/questions/348245/do-we-have-to-tune-the-number-of-trees-in-a-random-forest) - `500` is alright in general, **tune the `mtry` parameter** using the function `RFtune()`!!!
  - If `random.Forest()` is run with `proximity=TRUE` (keep *N* less than 10000, depending on your RAM as well) it generates a *N* x *N* matrix of **proximity (similarity)** (*N* = number of rows/data points).
  This can be scaled to 2D using `MDSplot()` (same data, same response vector, same random forest used to train the data), which internally uses the `stat::cmdscale()` to **see the dataset (every point) in 2D** (very slow).
- For faster and multi-threaded Random Forests, use the [`ranger`](https://github.com/imbs-hl/ranger) R package
  - No tuning offered for `mtry` (so do that with `randomForest` on random samples of your dataset), but everything else is better and faster!
- Use the [Boruta R package](https://cran.r-project.org/web/packages/Boruta/index.html) and plot the importance boxplot result!
  - Uses `ranger` under the hood currently, so multi-thread support for free
- **Neural networks** in conjunction with L1-regularization for feature selection? Dropouts also important for zeroing weights in a neural network.
  - Use the `keras` package for NN/deep learning

## Correlation

- [Article: Correlation Test Between Two Variables in R](http://www.sthda.com/english/wiki/correlation-test-between-two-variables-in-r)
- Use `ggscatter`: prints `R^2` and p-value.
- Use `ComplexHeatmap` to **visualize a correlation matrix between two variables of interest**, 
e.g. mRNA and protein expressions (if the data dimensions are large)
- [On the correlation measure to use between two variables **(continuous vs discrete)**](https://medium.com/@outside2SDs/an-overview-of-correlation-measures-between-categorical-and-continuous-variables-4c7f85610365). From this article I quote: "The idea behind using logistic regression to understand correlation between cont and categorical variables is actually quite straightforward and follows as such: **If there is a relationship between the categorical and continuous variable**, we should be able to construct an accurate predictor of the categorical variable from the continuous variable. If the **resulting classifier has a high degree of fit**, is accurate, sensitive, and specific we can conclude the two variables share a relationship and are indeed correlated."

## Logistic Regression in R

- [Application and Interpretation article](https://rpubs.com/rslbliss/r_logistic_ws)
- If the **response categorical variable is binary**, use `stats::glm(family = binomial)`
- For **multiple response classes**, use **Ordinal Logistic Regression** only when the *proportional odds assumption* is true (the relationship between each pair of response groups is the same - e.g. in a survey where you answer with 3 choices, the distance between *unlikely* and *somewhat likely* may be shorter than the distance between *somewhat likely* and *very likely*). In R, use `MASS::polr` or `rms::lrm`. Otherwise, go with **Multinomial Logistic Regression**: `nnet:multinom`.
- Measures of goodness of fit (fit statistics - related to model validation) for **logistic binary regression**: [article IBM](https://www.ibm.com/support/knowledgecenter/SSLVMB_24.0.0/spss/tutorials/plum_germcr_rsquare.html)
  - Mcfaddens `R^2`: `pscl:pR2`, `rms` => returns `res$stats` (has Nagelkerke's `R^2`)
  - [Mcfaddens `R^2` interpretation](https://stats.stackexchange.com/questions/82105/mcfaddens-pseudo-r2-interpretation)

## Dimensionality Reduction

- **PCA** (Prinicipal Componenent Analysis): [article](http://www.sthda.com/english/articles/31-principal-component-methods-in-r-practical-guide/112-pca-principal-component-analysis-essentials/)
  - Use R packages `FactoMineR` (run PCA) and `factoexta` (for visualization)
- **MCA** (Multiple Correspondence Analysis - PCA for categorical variables): [article](http://www.sthda.com/english/articles/31-principal-component-methods-in-r-practical-guide/114-mca-multiple-correspondence-analysis-in-r-essentials/)
- **UMAP**: a non-linear dimensionality reduction method, check R package [uwot](https://github.com/jlmelville/uwot)

## PR (Precision-Recall curves)

- [Intro Article](https://classeval.wordpress.com/introduction/introduction-to-the-precision-recall-plot/)

## Hierarchical Clustering Analysis

- [Nice explanatory article](https://uc-r.github.io/hc_clustering)
- Comparing Dendrograms in R
  - [Datanovia article](https://www.datanovia.com/en/lessons/comparing-cluster-dendrograms-in-r/)
  - R package [dendextend](https://talgalili.github.io/dendextend/articles/dendextend.html)
