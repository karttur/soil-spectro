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

This post is part of a series on organising and analysing data from the Open Soil Spectral Library (OSSL). It is also the second post in a sub-series on how to apply Machine Learning (ML) for predicting soil properties from spectral data. To run the scripts used in this post you need to setup a Python environment, and clone or download the python scripts from a [GitHub repository (repo)](https://github.com/karttur/OSSL-pydev/), as explained in the post [Clone the OSSL python package](../../libspectrosupport/spectrosupport-OSSL-clone).

### Introduction

The [previous post](../spectrodata-OSSL4ML05-mlmodel01) covered the overall process-flow of the python script <span class='module'>OSSL_mlmodel.py</span> and how to get model results both as json documents and as plots. The topic of this post is how to change the symbolisation and plot layout of the graphical results that are derived from <span class='module'>OSSL_mlmodel.py</span>. The figures with the plots are all generated with the python library [<span class='package'>matplotlib</span>](https://matplotlib.org).

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed.  

### Matplotlib

[<span class='package'>matplotlib</span>](https://matplotlib.org) is a versatile graphics editor and drawer, that can be used for designing and constructing complex plots, including figures with rows and columns of subplots. This post on symbolisation and layout of the results from Machine Learning (ML) modelling only uses a very limited set of matplotlib tools.

#### Basic layout options

There are 4 basic plot layout options that can be set for displaying the result of the ML modelling (illustrated in figure 1):

1. Single plots showing feature importance for a combination of one feature and one regressor (bar chart)
2. Single plot showing the predictive power for a combination of one feature and one regressor (scatter plot)
3. Multi-plot rows and columns for single feature with rows showing the results of different regressors applied for predicting this single feature, and
4. Multi-plot rows and columns for single regressor with rows showing the results of applying this single regressor to different target features.


<figure class="half">
  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png" alt="image">
  </a>  

  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_tt-result.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_tt-result.png" alt="image">
  </a>

	<a href="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png">
  <img src="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png" alt="image">
  </a>


	<a href="../../images/testtg_RandForRegr-multi-results.png">
  <img src="../../images/testtg_RandForRegr-multi-results.png" alt="image">
  </a>

	<figcaption>Figure 1. The four basic plot types that can be generated from the module OSSL_mlmodel; top row: single feature permutation importance and feature/coefficient importance; bottom row: multi-column plots for single feature (with each row showing the results of a single regressor) and for regressors (with each row showing the results for a single feature).</figcaption>
</figure>

Alternatives 1 (feature importance) and 2 (predictive power) each are available in 2 versions. Feature importance can be both [permutation importance](../spectrodata-OSSL4ML05-mlmodel01#permutation-importance-evaluation) and [coefficient importance](../spectrodata-OSSL4ML05-mlmodel01#coefficient-importance). And predictive power can be both from [train/test divided prediction](../spectrodata-OSSL4ML05-mlmodel01#traintest-divided-prediction) and [cross validated prediction](../spectrodata-OSSL4ML05-mlmodel01#cross-validated-prediction). There are thus in total 4 different kinds of individual plots that can be defined (figure 2).

<figure class="half">
  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png" alt="image">
  </a>  
	<a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_feat-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_feat-imp.png" alt="image">
  </a>

  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_tt-result.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_tt-result.png" alt="image">
  </a>  
	<a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_kfold-result.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_kfold-result.png" alt="image">
  </a>

	<figcaption>Figure 2. The four single plot types that can be generated from the module OSSL_mlmodel; top row: permutation importance and feature/coefficient importance; bottom row: train-test prediction evaluation and kfold prediction evaluation.</figcaption>
</figure>

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

The example above creates a 3-column multi-plot for each target feature defined in the json command file, figure 3.

<figure>
	<a href="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png">
  <img src="../../images/LUCAS_SE_oc_usda.c729_w.pct-multi-results.png" alt="image">
  </a>

	<figcaption>Figure 3. Target feature multi-plot, with each row showing the results of applying a different regressor; columns show 1) permutation importance, 2) feature importance and 3) Kfold model prediction results. The figure uses Organic Carbon (oc) as an example; the symbolisation of the target feature (grey) and the varying markers for the different regressors are defined as explained below.</figcaption>
</figure>

#### Single regressor multi-plots

The json coding for creating multi-plots for single regressors looks like this:

```
      "regressionModels": {
        "apply": true,
        "figSize": {
          "x": 0,
          "y": 0,
          "xadd": 0.25,
          "yadd": 0.25
        },
        "hwspace": {
          "hspace": 0.25,
          "wspace": 0.25
        },
        "columns": [
          "permutationImportance",
          "trainTest",
          "Kfold"
        ]
      }
```

The example above creates a 3-column multi-plot for each regressor defined in the json command file, figure 4.

<figure>
	<a href="../../images/testtg_RandForRegr-multi-results.png">
  <img src="../../images/testtg_RandForRegr-multi-results.png" alt="image">
  </a>

	<figcaption>Figure 4. Regression nodel multi-plot, with each row showing the results of a different target feature; columns show 1) permutation importance, 2) traint/test model prediction results and 3) Kfold model prediction results. The figure uses the random forest regressor as an example; the symbolisation of the target features and the varying markers for the different regressors are defined as explained below.</figcaption>
</figure>

#### Target feature symbolisation

The target features (soil properties) are symbolised either as bars (in the bar-charts showing the feature importance) or as scatters (in the observed versus predicted scatter plots showing model performance). The colour and label for each target feature, symbolised both as bars and scatter markers, are set in the json command file:

```
"targetFeatureSymbols": {
    "caco3_usda.a54_w.pct": {
      "color": "whitesmoke",
      "label": "CaCo3"
    },
    "cec_usda.a723_cmolc.kg": {
      "color": "seagreen",
      "label": "Cation Exc. Cap."
    },
    "cf_usda.c236_w.pct": {
      "color": "sienna",
      "label": "Crane fraction"
    },
    "clay.tot_usda.a334_w.pct": {
      "color": "tan",
      "label": "Clay cont."
    },
    "ec_usda.a364_ds.m": {
      "color": "dodgerblue",
      "label": "Electric cond."
    },
    "k.ext_usda.a725_cmolc.kg": {
      "color": "lightcyan",
      "label": "Potassion (K)"
    },
    "n.tot_usda.a623_w.pct": {
      "color": "darkcyan",
      "label": "Nitrogen (N) [tot]"
    },
    "oc_usda.c729_w.pct": {
      "color": "dimgray",
      "label": "Organic carbon (C)"
    },
    "p.ext_usda.a274_mg.kg": {
      "color": "firebrick",
      "label": "Phosphorus (P)"
    },
    "ph.cacl2_usda.a481_index": {
      "color": "lemonchiffon",
      "label": "pH (CaCl)"
    },
    "ph.h2o_usda.a268_index": {
      "color": "lightyellow",
      "label": "pH (H20)"
    },
    "sand.tot_usda.c60_w.pct": {
      "color": "orange",
      "label": "Sand cont."
    },
    "silt.tot_usda.c62_w.pct": {
      "color": "khaki",
      "label": "Silt cont."
    }
  }
```

#### Regressor feature symbolisation

If you look carefully at the multi-row plots in figure 3 (showing the prediction of organic carbon using OLS and RandForRegr) you can see that the markers have different shape. The marker shape and size can be set differently for each regressor in the json command file:

```
"regressionModelSymbols": {
  "OLS": {
    "marker": ".",
    "size": 100
  },
  "TheilSen": {
    "marker": "v",
    "size": 50
  },
  "Huber": {
    "marker": "^",
    "size": 50
  },
  "KnnRegr": {
    "marker": "s",
    "size": 50
  },
  "DecTreeRegr": {
    "marker": "P",
    "size": 50
  },
  "SVR": {
    "marker": "*",
    "size": 50
  },
  "RandForRegr": {
    "marker": "h",
    "size": 50
  },
  "MLP": {
    "marker": "D",
    "size": 50
  }
},
"modelTests": {
  "apply": true,
  "trainTest": {
    "apply": true,
    "testSize": 0.3,
    "plot": true,
    "marker": "s"
  },
  "Kfold": {
    "apply": true,
    "folds": 10,
    "plot": true,
    "marker": "."
  }
}
```

#### Horisontal and vertical spacing

For the multi-row/multi-column plots it is a bit tricky to get the height and width spacing correct - the axis labels often tend to overlap. Also because different target features have different units and thus different numerical ranges - which leads to different lengths of the tick mark text. Matplotlib does not handle that automatically and thus I have added the possibility of manually setting both figure height and width and the height and width spacing.

```
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

```

If, like in the example above, _figSize_ _x_, _y_, _xadd_ and _yadd_ are set to _0_ the script will look for default settings under _subFigSize_:

```
      "subFigSize": {
        "x": 3,
        "y": 3,
        "xadd": 0.1,
        "yadd": 0.1
      }
```

If _subFigSize_ is used, the total figure size will be set as:

- width = "nr of columns" * _x_ + _xadd_
- height = "nr of rows" * _y_ + _yadd_

Tuning these number it is possible to achieve a plot with nice looking distances between the sub-plots both in the horizontal and the vertical.
