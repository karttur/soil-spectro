---
layout: post
title: "Model OSSL data: 2 plot layout"
categories: libspectrodata
excerpt: "Symbolisationa and plot layout isn matplotlib for results from Machine Learning modelling of OSSL data."
tags:
  - OSSL
  - machine learning
  - modeling
  - scikit learn
  - sklearn
  - layout
  - symbolisation
  - matplotlib
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-19 11:27'
modified: '2023-08-05 11:27'
comments: true
share: true
---

### Introduction

The [previous post](../spectrodata-OSSL4ML05-mlmodel02) you covered the overall process-flow of the python script <span class='module'>OSSL_mlmodel.py</span> and how to get model results both as json documents and as plots. The topic of this post is how to change the symbolisation and plot layout of the graphical results that are derived from <span class='module'>OSSL_mlmodel.py</span>. The figures with the plots are all generated with the python library [<span class='package'>matplotlib</span>](https://matplotlib.org).

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed.  

### Matplotlib

[<span class='package'>matplotlib</span>](https://matplotlib.org) is a versatile graphics editor and drawer, that can be used for designing and constructing complex plots, including figures with rows and columns of subplots. This post on symbolisation and layout of the results from Machine Learning (ML) modelling only uses a very limited set of matplotlib tools.

There are 4 basic plot layout options that can be set for displaying the result of the ML modelling:

1. Single plots showing feature importance for a combination of one feature and one regressor (bar chart)
2. Single plot showing the predictive power for a combination of one feature and one regressor (scatter plot)
3. Multi-plot rows and columns for single feature with rows showing the results of different regressors applied for predicting this single feature, and
4. Multi-plot rows and columns for single regressor with rows showing the results of applying this single regressor to different target features.

Alternatives 1 (feature importance) and 2 (predictive) power each are available in 2 versions. Feature importance can be both [permutation importance](../spectrodata-OSSL4ML05-mlmodel02#permutation-importance-evaluation) or [coefficient importance](spectrodata-OSSL4ML05-mlmodel02#coefficient-importance). And predictive power can be both from [train/test divided prediction](spectrodata-OSSL4ML05-mlmodel02#train-test-divided-prediction) and [cross validated prediction](spectrodata-OSSL4ML05-mlmodel02#cross-validated-prediction). There are thus in total 4 different kinds of individual plots that can be defined.

That there are 4 options for individual plots leads to the multi-plots having a maximum of 4 columns - being the 4 possible individual plots. The rows in a multi-plot are defined by the input items of the modelling; for single feature multi-plots the number of rows equals the number of regressors to be tested; for single regressor multi-plots the number of rows equals the number of target features to be modelled. The columns to include in the multi-plots are then defined using the following code words:

- permutationImportance
- featureInportance
- trainTest
- Kfold

#### Single feature multi-plots

The json coding for creating multi-plots for single features looks like this:

```
      "targetFeatures": {
        "apply": true,
        "figSize": {
          "x": 0,
          "y": 0,
          "xadd": 0,
          "yadd": 0
        },
        "hwspace": {
          "hspace": 0.25,
          "wspace": 0.25
        },
        "columns": [
          "permutationImportance",
          "featureImportance",
          "Kfold"
        ]
      }
```

The example above creates a 3-column multi-plot for each target feature defined in the json command file, figure 1.

<figure>
	<a href="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png">
  <img src="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png" alt="image">
  </a>

	<figcaption>Figure 1. Target feature multi-plot, with each row showing the results of applying a different regressor; columns show 1) permutation importance, 2) feature importance and 3) Kfold model prediction results. The figure uses Organic Carbon (oc) as an example; the symbolisation of the target feature (grey) and the varying markers for the different regressors are defined as explained below.</figcaption>
</figure>
