---
layout: post
title: "Model OSSL data: 1 process-flow"
categories: libspectrodata
excerpt: "Model OSSL data using Python's Machine Learning (ML) package Scikit learn (sklearn)."
tags:
  - OSSL
  - machine learning
  - modelling
  - scikit learn
  - sklearn
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-17 11:27'
modified: '2023-07-30 11:27'
comments: true
share: true
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>
### Introduction

As the Open Soil Spectral Library (OSSL) contains thousands of individual samples, each with thousands of recorded reflectances in different wavelengths and additional physical and chemical soil properties, it is a suitable dataset for applying Machine Learning (ML) modelling. This post both illustrates how to apply ML for modelling soil properties from soil spectral data, and is at the same time a hands-on manual for modelling soil spectra from OSSL using the python script <span class='module'>OSSL_mlmodel.py</span>.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [arranging](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed.  

### Machine Learning

Machine Learning (ML) includes a range of methods for preparing, refining, selecting, aggregating, evaluating and modelling different dependent properties from a set of independent features (covariates). For the ML modelling of soil properties from spectral data I have built a predefined process-flow structure. The process-flow is governed by a command file using json syntax. Editing the json file you can chose which target features, regressors and steps to include in your own ML modelling.

### Selecting target features and regressors

The first you need to do before running the process-flow is to define the target features to predict and the regressors (ML models) to apply.

#### Target features

The target features to evaluate must of course correspond to properties that are available in the input data. The physical and chemical soil properties available are listed in the json output from when you [arranged](../spectrodata-OSSL4ML02-arrange) the OSSL data. Each re-arranged data point will list the laboratory observed soil properties under the tag _abundances_:

```
    "abundances": [
        {
          "substance": "caco3_usda.a54_w.pct",
          "value": 0.1
        },
        ...
        ...
        {
          "substance": "silt.tot_usda.c62_w.pct",
          "value": 28.0
        }
      ]
    }
```

The target features to choose from are those under the _substance_ tags.

In the json command file for the process-flow, list the target features you want to include:

```
"targetFeatures": [
    "cec_usda.a723_cmolc.kg",
    "ec_usda.a364_ds.m",
    "k.ext_usda.a725_cmolc.kg",
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct"
  ],
```

#### Regression models

You need to include at least one (1) regressor to either run a prediction or evaluate the importance of the covariates (i.e. the band reflectance or the derivatives). The present version of the process-flow implements the following regressors:

- [Oridnary Least Square (OLS)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html),
- [Theil-Sen Regressor (TheilSen)](https://scikit-learn.org/stable/auto_examples/linear_model/plot_theilsen.html),
- [Huber Regressor (Huber)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.HuberRegressor.html).
- [k-nearest neighbors regressor (KnnRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsRegressor.html),
- [decision tree regressor (DecTreeRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html),
- [support vector regressor (SVR)](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVR.html),
- [random forest regressor (RandForRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html), and
- [multi-layer perceptron regressor (MLP)](https://scikit-learn.org/stable/modules/generated/sklearn.neural_network.MLPRegressor.html).

Where MLP is a neural network type of regressor.

In the json command file, each regressor must be given with its abbreviation and  hyper-parameter (_HyperParams_) settings. If no hyper-parameters are give you still have to include the empty curly brackets (_dictionary_ in python terms):

```
  "regressionModels": {
    "OLS": {
      "apply": true,
      "hyperParams": {}
    },
    ...
    ...
    "RandForRegr": {
      "apply": true,
      "hyperParams": {
        "n_estimators": 30
      }    
    }
  },
```

### Setting the process steps

The general steps in process-flow are:

1. [Global data cleaning and selection](#global-data-cleaning-and-selection)
- [outlier detection and removal](#outlier-detection-and-removal)
- [variance threshold feature selection](#variance-threshold-feature-selection)
2. [Target property related feature selection](#target-property-related-feature-selection)
- [univariate feature selection](#univariate-feature-selection)
- [feature clustering](#feature-clustering)
3. [Model related feature selection](#model-related-feature-selection)
- [permutation importance selection](#permutation-importance-selection)
- [Recursive Feature Elimination (RFE)](#recursive-feature-elimination))
4. [Feature importance evaluation](#feature-importance-evaluation)
- [permutation importance evaluation](#permutation-importance-evaluation)
- [coefficient importance](#coefficient-importance)
5. [Hyper-parameter tuning](#hyper-parameter-tuning)
6. [Model fitting and evaluation](#model-fitting-and-evaluation)
- [train/test divided prediction](#traintest-divided-prediction)
- [cross validated prediction](#cross-validated-prediction)

The next sections go through the general steps oulined above. If you just want to run the <span class='module'>OSSL_mlmodel</span> package and do not need the instructions, just jump to the section [Complete process-flow for Swedish OSSL data](#complete-process-flow-for-swedish-ossl-data).

#### Global data cleaning and selection

The OSSL data contain a large number of samples, each with hundreds or even thousands of recorded wavelength reflectances. The data can contain errors (outliers) and using all the data can lead to over-parameterised models. It is also inevitable that some wavelengths (or bands) contain less information compared to others; a band with no variation (i.e. constant reflection) does not contain any relevant information for ML modelling.

The global data cleaning and selection methods analyse the independent features (the covariates) disregarding both the target and the ML model for estimation of the target variations. It is a more crude method for discarding data compared to the methods that compare the covariates in relation to the target and the estimator (the two following steps in the process-flow). The global methods are, however comparatively fast and applying the global methods means that all subsequent processing in the framework will use the cleaned and reduced dataset.

##### Outlier detection and removal

To remove outliers the process-flow implements four different outlier detectors available in the package scikit learn:
- [IsolationForest](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html),
- [EllipticEnvelope](https://scikit-learn.org/stable/modules/generated/sklearn.covariance.EllipticEnvelope.html),
- [LocalOutlierFactor](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LocalOutlierFactor.html), and
- [OneClassSVM](https://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html).

In the json command file you turn the outlier detection and removal on by the following lines:
```
  "removeOutliers": {
    "apply": true,
    "detector": "LocalOutlierFactor",
    "contamination": 0.1
    },
```

The only parameter that can be changed in the present version of the framework is _contamination_ (applying to the parameter _nu_ in the detector _OneClassSVM_).

##### Variance threshold feature selection

Applying the sklearn method [VarianceThreshold](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html) removes all low-variance features with variances below a stated threshold. The removal is unrelated to the target features and regression model. To neutralise the range of the input features the sklearn [MinMaxScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html) is applied as a pre-process. The only parameter that you can set in the process-flow command file is the _threshold_ for retaining or discarding a feature. You will probably have to iteratively test different thresholds. In the json command you include variance feature selection by these lines:

```
  "globalFeatureSelection": {
    "apply": true,
    "scaler": "MinMaxScaler",
    "varianceThreshold": {
      "threshold": 0.025
    }
  },
```

#### Target property related feature selection

The global feature selection using variance threshold (the section above) is unrelated to the properties that are to be predicted. To select the features (covariates) that relate to a specific target (soil chemical and physical properties in this example) the process-flow implements [univariate feature selection](https://scikit-learn.org/stable/modules/feature_selection.html#univariate-feature-selection). Scikit learn includes several selectors that can be used with the univariate feature selection, but at present only the the [KBest selector](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html#sklearn.feature_selection.SelectKBest) is implemented in the process-flow.

Another route for reducing the number of features is to cluster (agglomerate) them. Features that show similar variations in relation to the target feature are collected to a single feature. For the framework I have written a Ward clustering routine that also includes a tuning function for determining the optimum number of clusters to form as a preprocess to the Ward clustering.

In the framework you can in principle invoke both the univariate feature selection and the clustering, but in normal cases you would only use one for each model building exercise.

##### Univariate feature selection

There are several methods available for univariate feature selection. In the present version of the framework I have included [SelectKBest](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html). The only parameter to set is _n_features_, the number of features to retain from the selection. To invoke the KBest univariate feature selection in the process-flow, edit the json command file like this:

```
"targetFeatureSelection": {
  "apply": true,
  "univariateSelection": {
    "apply": true,
    "SelectKBest": {
      "apply": true,
      "n_features": 15
    }
  }
},
```

The univariate feature selection is set separately for each target feature (soil property) to model and the selection of KBest features for one target property has no affect on the selection of features for other soil properties.

##### Feature clustering

Scikit learn contains a range of methods for clustering, or [feature agglomeration](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.cluster). The Ward clustering implemented in the framework has the advantage that you can tune the number of clusters to request from the main agglomeration. To include the Ward clustering in the framework, edit the json command file thus:

```
  "featureAgglomeration": {
    "apply": true,
    "wardClustering": {
      "apply": true,
      "n_clusters": 0,
      "affinity": "euclidean",
      "tuneWardClustering": {
        "apply": true,
        "kfolds": 3,
        "clusters": [
          4,
          5,
          6,
          7,
          8,
          9,
          10,
          11,
          12
        ]
      }
    }
  },
```

In the example above I have asked the tuning function to evaluate all optional cluster sizes between 4 and 12, and set the tuning process to a kfold strategy of 3 folds. As the framework will seek an optimal number of clusters (for each target feature), I have set the _n_clusers_ parameter for the main _wardClustering_ to _0_. If  _tuneWardClustering_ is not requested, that number must instead be set to the actual number of clusters requested from _wardClustering_.

#### Model related feature selection

The most advanced options for reducing the number of covariates considers both the target property and the applied regressor. The process-flow includes two methods that can be used for this kind of model related feature selection: [Permutation Importance Selection](https://scikit-learn.org/stable/modules/permutation_importance.html) and [Recursive Feature Elimination (RFE)](https://scikit-learn.org/stable/modules/feature_selection.html#recursive-feature-elimination). Permutation importance can be applied to all kinds of regressors, whereas RFE is not applicable for example for KnnRegr or MLP. if you request RFE for any of these, the process-flow will instead do a Permutation Importance Selection. You can only apply one of the two model related feature selection methods in each modelling exercise. If both are turned on, the process-flow will automatically select permutation importance.

##### Permutation importance selection

Permutation importance is defined as the variation in a model score when a single feature value is randomly shuffled; the larger the difference in score, the more important is the shuffled feature. Applying the permutation importance selection you can set the _permutationRepeats_, the _steps_ and then the _n_features_to_select_. Edit the json command file to invoke permutation importance selection:

```
"modelFeatureSelection": {
  "apply": true,
  "permutationSelector": {
    "apply": true,
    "permutationRepeats": 6,
    "n_features_to_select": 12,
    "step": 1
  },
}
```

The implemented permutation importance selection methods is also part of the process-flow [Feature importance evaluation](#feature-importance-evaluation) which allows you to graphically examine the relative importance for different features.

##### Recursive Feature Elimination

[RFE](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFE.html) can be applied it two modes, with and without Cross Validation (CV). While [RFECV](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFECV.html) takes a bit longer it is the recommended mode. As note above RFE does not work with all regressors, and is only applied if the permutation selector is turned off. If RFE/RFECV is invoked, regressors that can not apply RFE/REFCV instead use permutation selection. To use RFE/RFECV as part of your project, edit the following lines of the json command file:

```
"modelFeatureSelection": {
  "apply": true,
  "permutationSelector": {
    "apply": false,
    "permutationRepeats": 6,
    "n_features_to_select": 12,
    "step": 1
  },
  "RFE": {
    "apply": true,
    "CV": true,
    "n_features_to_select": 12,
    "step": 1
  }
}
```

Note that RFE, and even more so, RFECV, will take a long time if you have large datasets with many features. Thus it is recommended to first select features using either global [variance threshold](#variance-threshold-feature-selection), [Univariate feature selection](#univariate-feature-selection) or [Feature clustering](#feature-clustering), or actually a combination of two of these methods. Doing so will reduce the number of features evaluated using RFE/RFECV and considerably speed up the processing time.

#### Feature importance evaluation

After having selected the features to use (or not) you can choose to evaluate the feature importances for each combination of target feature and regression model. The evaluation is done both for [Permutation importance](https://scikit-learn.org/stable/modules/permutation_importance.html) (the decrease in a model score when a single feature value is randomly shuffled) and for model coefficients or feature importances (depends on the type of regressor).

To include feature importance evaluation edit the json command file:

```
"featureImportance": {
  "apply": true,
  "reportMaxFeatures": 12,
  "permutationRepeats": 10
}
```
Except from setting the parameter _apply_ to _true_ to invoke the feature importance evaluation, you also have to give the maximum number of features to include in the reporting, _reportMaxFeatures_ . The reporting will always show the highest ranking features. Then you also have to give the number of _permutationRepeats_ to use in the evaluation. Figure 1 shows the feature importance evaluation for the target feature oc_usda.c729_w.pct (organic carbon). The rows show results for the regressors OLS (Ordinary Least Square) and RandForRegr (Random Forest Regression), while the columns show coefficient(OLS)/estimators(RandForRegr) importance. The error bars shows the standard deviation (see below).

<figure class="half">

  <a href="../../images/oc_usda.c729_w.pct_OLS-model_permut-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_OLS-model_permut-imp.png" alt="image">
  </a>

  <a href="../../images/oc_usda.c729_w.pct_OLS-model_feat-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_OLS-model_feat-imp.png" alt="image">
  </a>

  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png" alt="image">
  </a>

  <a href="../../images/oc_usda.c729_w.pct_RandForRegr-model_feat-imp.png">
  <img src="../../images/oc_usda.c729_w.pct_RandForRegr-model_feat-imp.png" alt="image">
  </a>

	<figcaption>Figure 2. Feature importance evaluation for target feature oc_usda.c729_w.pct; The rows show results for the regressors OLS (Ordinary Least Square) and RandForRegr (Random Forest Regression), while the columns show permutation importance and coefficient(OLS)/estimators(RandForRegr); error bars shows the standard deviation (1 std).</figcaption>
</figure>

##### Permutation importance evaluation

The permutation importance is evaluated with the same methods applied for the [Permutation importance selection](#permutation-importance-selection). It applies to all regressors.

##### Coefficient importance

The coefficent importanec (for linear regressors) or feature importance (for e.g. tree and forest regressors) are the relative weights of the features selected for modelling the selected target feature. For the tree/forest based regressors the feature importances can be evaluated statistically (mean and standard deviation), whereas for linear models only a single value is reported. This is reflected in both text reports and plots. Note that some regressors do not generate any coefficients or feature importances (e.g. KnnRegr and MLP) and then only the permutation importance is reported. If a multi-column plot with coefficient importance included, the column for coefficient importance will be blanc for these regressors.

#### Hyper-parameter tuning

[Hyper-parameters](https://scikit-learn.org/stable/modules/grid_search.html) are parameters that determine how an estimator learns to find patterns and are not directly learnt within estimators. Put differently, each time an estimator is applied for fitting a model, the training depends on the setting of the hyper-parameters. Tweaking the hyper-parameter setting is thus an important part of fitting ML models to generate the best predictions for a particular target feature. It is, however, a rather complex and time-consuming process and is dealt with in a separate post: [Model OSSL data: 3 hyper-parameter tuning](../spectrodata-OSSL4ML06-mlmodel07).

For this post, I suggest that you leave the hyper-parameter tuning off:

```
"hyperParameterTuning": {
  "apply": false,
  "fraction": 0.5,
  "nIterSearch": 6,
  "n_best_report": 3,
  "randomTuning": {
    "apply": false
  },
  "exhaustiveTuning": {
    "apply": false
  }
}
```

#### Model fitting and evaluation

The last step in the process-flow is to run the selected regressors against selected target features using the defined process-flow. There are two model fitting and testing concepts implemented in the process-flow:

- Dividing the dataset in train and test subsets, and
- Cross validated (Kfold) iterative testing.

You can chose to skip Model fitting and evaluation all together, for instance if your objective is to select which features to use. Or you can choose to run one of the two model test concepts, or both. The editing of the json command file is following the same principles as above; to apply both model test methods, edit the command file like this:

```
"modelTests": {
    "apply": true,
    "trainTest": {
      "apply": true,
      "testSize": 0.3,
    },
    "Kfold": {
      "apply": true,
      "folds": 10,
    }
  }
```

For the _trainTest_ method you need to give the fraction of the input data to use for testing, as the parameter _testSize_. For the _Kfold_ method you must give the number of _folds_.

##### train/test divided prediction

The train/test divided methods for determining model predication power divides the input dataset in 2 subsets, one for calibrating the setting of the regressor "coefficients", and a (usually) smaller fraction to use for testing the regressor "coefficients" settings. The division 70/30 percent (0.7/0.3) is widely adopted. The train/test method is less comprehensive compared to the Kfold method.

##### cross validated prediction

[Cross validated prediction](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_val_predict.html) splits the data into multiple training and testing subsets and is repeated (folded) as stated by the parameters _folds_. The test size equals the inversion of _folds_ (i.e if folds = 10 then the test size is 0.1) and each data point is included in the test fraction once, and only once.

### Complete process-flow for Swedish OSSL data

The complete process-flow for the Swedish OSSL data (downloaded and arranged in the previous posts) is available under the toggle button.

<button id= "toggle_process-flow-se" onclick="hiddencode('process-flow-se')">Hide/Show json command for complete process-flow</button>

<div id="process-flow-se" style="display:none">

{% capture text-capture %}
{% raw %}

```
{
  "verbose": 1,
  "id": "Sweden_LUCAS_460-1050_10_20230808",
  "name": "Sweden_LUCAS_460-1050_10",
  "userId": "thomasg",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignId": "OSSL-LUCAS-SE",
    "campaignShortId": "OSSL-LUCAS-SE",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json",
    "jsonSpectraParamsFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/params-visnir_LUCAS_460-1050_10.json",
    "hyperParameterRandomTuning": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-ml-modeling/hyper-param-random-tuning-ossl-spectra_sweden-LUCAS_nir_460-1050_10.json",
    "hyperParameterExhaustiveTuning": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-ml-modeling/hyper-param-exhaustive-tuning-ossl-spectra_sweden-LUCAS_nir_460-1050_10.json"
  },
  "output": {
    "prefix": "testtg",
    "setdate": false
  },
  "targetFeatures": [
    "cec_usda.a723_cmolc.kg",
    "k.ext_usda.a725_cmolc.kg",
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct"
  ],
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
  },
  "derivatives": {
    "apply": false,
    "join": false
  },
  "removeOutliers": {
    "comment": "removes sample outliers based on spectra only - globally applied as preprocess",
    "apply": true,
    "detectorMethodList": [
      "iforest (isolationforest)",
      "ee (eenvelope,ellipticenvelope)",
      "lof (lofactor,localoutlierfactor)",
      "1csvm (1c-svm, oneclasssvm)"
    ],
    "detector": "1csvm",
    "contamination": 0.1
  },
  "manualFeatureSelection": {
    "comment": "Manual feature selection overrides other selection alternatives",
    "apply": false,
    "spectra": [
      "675",
      "685",
      "705",
      "715",
      "735"
    ],
    "derivatives": {
      "firstWaveLength": [
        "675"
      ],
      "lastWaveLength": [
        "735"
      ]
    }
  },
  "globalFeatureSelection": {
    "comment": [
      "removes spectra with variance below given thresholds - globally applied as preprocess"
    ],
    "apply": false,
    "scaler": "MinMaxScaler",
    "varianceThreshold": {
      "threshold": 0.025
    }
  },
  "targetFeatureSelection": {
    "comment": [
      "feature selection using target data"
    ],
    "apply": true,
    "univariateSelection": {
      "apply": true,
      "SelectKBest": {
        "apply": true,
        "n_features": 25
      },
      "selectPercentile": {
        "implemented": false,
        "apply": false,
        "percentile": 10
      },
      "genericUnivariateSelect": {
        "implemented": false,
        "apply": false,
        "hyperParameters": {}
      }
    }
  },
  "modelFeatureSelection": {
    "comment": [
      "feature selection using target + regressor"
    ],
    "apply": false,
    "permutationSelector": {
      "apply": false,
      "permutationRepeats": 6,
      "n_features_to_select": 8,
      "step": 1
    },
    "RFE": {
      "apply": false,
      "CV": false,
      "n_features_to_select": 8,
      "step": 1
    }
  },
  "featureAgglomeration": {
    "apply": false,
    "agglomerativeClustering": {
      "apply": false,
      "implemented": false
    },
    "wardClustering": {
      "apply": false,
      "n_clusters": 0,
      "affinity": "euclidean",
      "tuneWardClustering": {
        "apply": true,
        "kfolds": 3,
        "clusters": [
          2,
          3,
          4,
          5,
          6,
          7,
          8,
          9,
          10,
          11,
          12
        ]
      }
    }
  },
  "hyperParameterTuning": {
    "apply": false,
    "fraction": 0.5,
    "nIterSearch": 6,
    "n_best_report": 3,
    "randomTuning": {
      "apply": false
    },
    "exhaustiveTuning": {
      "apply": false
    }
  },
  "featureImportance": {
    "apply": true,
    "reportMaxFeatures": 8,
    "permutationRepeats": 10
  },
  "modelling": {
    "apply": true
  },
  "regressionModels": {
    "OLS": {
      "apply": true,
      "hyperParams": {
        "fit_intercept": false
      }
    },
    "TheilSen": {
      "apply": false,
      "hyperParams": {}
    },
    "Huber": {
      "apply": false,
      "hyperParams": {}
    },
    "KnnRegr": {
      "apply": false,
      "hyperParams": {}
    },
    "DecTreeRegr": {
      "apply": false,
      "hyperParams": {}
    },
    "SVR": {
      "apply": false,
      "hyperParams": {
        "kernel": "linear",
        "C": 1.5,
        "epsilon": 0.05
      }
    },
    "RandForRegr": {
      "apply": true,
      "hyperParams": {
        "n_estimators": 30
      }
    },
    "MLP": {
      "apply": false,
      "hyperParams": {
        "hidden_layer_sizes": [
          100,
          100
        ],
        "max_iter": 200,
        "tol": 0.001,
        "epsilon": 1e-8
      }
    }
  },
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
  },
  "plot": {
    "tightLayout": false,
    "singles": {
      "apply": true,
      "figSize": {
        "x": 0,
        "y": 0,
        "xadd": 0.25,
        "yadd": 0.25
      },
      "screenShow": false,
      "savePng": true
    },
    "rows": {
      "apply": true,
      "subFigSize": {
        "x": 3,
        "y": 3,
        "xadd": 0.1,
        "yadd": 0.1
      },
      "screenShow": true,
      "savePng": true,
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
      },
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
    }
  }
}
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

Running the process-flow will generate three different types of results:

- a json formatted complete report,
- conserved model settings ("pickle" files) for all combinations of regressor and target features, and
- plot of feature importances and model predictions, also for all combinations of regressor and target features (optional).

#### json report

The json report includes the outcome of each step included in the process-flow. In addition it lists all the covariates selected for each combination of regressor and target features; also the mean square error and score of each combination is given. The model setting for the reported results are always saved, as "pickle" (conserved) model states. These "pickle" files can be loaded and the model settings applied to any other dataset that contains the same features and bands (i.e. OSSL data over other countries/regions or sample dates.

The json result file for the Swedish OSSL data and the process-flow defined above is almost 1000 rows and hidden under the toggle button.

<button id= "toggle_result-se" onclick="hiddencode('result-se')">Hide/Show json json for modelling OSSL data over Sweden</button>

<div id="result-se" style="display:none">

{% capture text-capture %}
{% raw %}

```
{
  "originalInputColumns": 59,
  "globalTweaks": {
    "removeOutliers": {
      "method": "1csvm",
      "nOutliersRemoved": 411
    }
  },
  "targetFeatureSelection": {
    "cec_usda.a723_cmolc.kg": {
      "method": "SelectKBest",
      "nFeaturesRemoved": 34
    },
    "k.ext_usda.a725_cmolc.kg": {
      "method": "SelectKBest",
      "nFeaturesRemoved": 34
    },
    "n.tot_usda.a623_w.pct": {
      "method": "SelectKBest",
      "nFeaturesRemoved": 34
    },
    "oc_usda.c729_w.pct": {
      "method": "SelectKBest",
      "nFeaturesRemoved": 34
    }
  },
  "featureImportance": {
    "cec_usda.a723_cmolc.kg": {
      "OLS": {
        "permutationsImportance": {
          "645": {
            "mean_accuracy_decrease": 2142325.127543466,
            "std": 71254.03285367714
          },
          "635": {
            "mean_accuracy_decrease": 1986723.276198183,
            "std": 65805.04455308293
          },
          "725": {
            "mean_accuracy_decrease": 812465.2256568633,
            "std": 27993.302591305855
          },
          "685": {
            "mean_accuracy_decrease": 559712.1737331812,
            "std": 19007.595742512323
          },
          "695": {
            "mean_accuracy_decrease": 485390.73162536195,
            "std": 16542.88395393226
          },
          "715": {
            "mean_accuracy_decrease": 367926.49454165966,
            "std": 12629.93241658395
          },
          "675": {
            "mean_accuracy_decrease": 285914.19691024564,
            "std": 9652.344061260414
          },
          "735": {
            "mean_accuracy_decrease": 213051.77139050717,
            "std": 7349.228558815168
          }
        },
        "featureImportance": {
          "645": {
            "linearCoefficient": 246973.4674020558
          },
          "635": {
            "linearCoefficient": -240624.92151107272
          },
          "725": {
            "linearCoefficient": -145198.55334763447
          },
          "685": {
            "linearCoefficient": -121876.21646876815
          },
          "695": {
            "linearCoefficient": 112968.33844896617
          },
          "715": {
            "linearCoefficient": 97817.32364734416
          },
          "675": {
            "linearCoefficient": 87685.58324521426
          },
          "595": {
            "linearCoefficient": -78080.77592492555
          }
        }
      },
      "RandForRegr": {
        "permutationsImportance": {
          "565": {
            "mean_accuracy_decrease": 0.28635039209315927,
            "std": 0.012483381951013114
          },
          "585": {
            "mean_accuracy_decrease": 0.25867593274872813,
            "std": 0.014171282671730068
          },
          "555": {
            "mean_accuracy_decrease": 0.18065845854126056,
            "std": 0.020943323392986605
          },
          "755": {
            "mean_accuracy_decrease": 0.07846801190884165,
            "std": 0.009686268879793979
          },
          "735": {
            "mean_accuracy_decrease": 0.025329555431646868,
            "std": 0.007652292258404499
          },
          "595": {
            "mean_accuracy_decrease": 0.020348502600112027,
            "std": 0.003332552424145211
          },
          "545": {
            "mean_accuracy_decrease": 0.019069121284108436,
            "std": 0.00655696422147034
          },
          "575": {
            "mean_accuracy_decrease": 0.018350720058689964,
            "std": 0.005767818451422918
          }
        },
        "featureImportance": {
          "555": {
            "MDI": 0.1587611298869648,
            "std": 0.2029025944491511
          },
          "565": {
            "MDI": 0.13355195437917838,
            "std": 0.1875616912025412
          },
          "585": {
            "MDI": 0.10933669833987764,
            "std": 0.17090068008136722
          },
          "515": {
            "MDI": 0.06092444236866471,
            "std": 0.10450428235261014
          },
          "755": {
            "MDI": 0.052787445712253994,
            "std": 0.029502503508809227
          },
          "525": {
            "MDI": 0.04188563775825373,
            "std": 0.0760597569209434
          },
          "705": {
            "MDI": 0.03847681267368939,
            "std": 0.047619087108398614
          },
          "535": {
            "MDI": 0.03335813794726748,
            "std": 0.08261382394115557
          }
        }
      }
    },
    "k.ext_usda.a725_cmolc.kg": {
      "OLS": {
        "permutationsImportance": {
          "645": {
            "mean_accuracy_decrease": 2468278.2776789167,
            "std": 60637.63790521384
          },
          "635": {
            "mean_accuracy_decrease": 1385040.6509942084,
            "std": 34398.55009249118
          },
          "615": {
            "mean_accuracy_decrease": 1050463.096359942,
            "std": 26566.69401616157
          },
          "655": {
            "mean_accuracy_decrease": 572575.826310299,
            "std": 13904.153978193413
          },
          "725": {
            "mean_accuracy_decrease": 557615.3387913108,
            "std": 12630.848187832913
          },
          "695": {
            "mean_accuracy_decrease": 486811.02869416645,
            "std": 11307.888341467928
          },
          "715": {
            "mean_accuracy_decrease": 477303.46509368607,
            "std": 10893.685987614375
          },
          "575": {
            "mean_accuracy_decrease": 443762.00158378173,
            "std": 11459.045682438042
          }
        },
        "featureImportance": {
          "645": {
            "linearCoefficient": 9729.286445723226
          },
          "635": {
            "linearCoefficient": -7369.65938464753
          },
          "615": {
            "linearCoefficient": 6590.453464002772
          },
          "575": {
            "linearCoefficient": 4663.995284953057
          },
          "655": {
            "linearCoefficient": -4637.551432853138
          },
          "725": {
            "linearCoefficient": -4427.259531860341
          },
          "695": {
            "linearCoefficient": -4161.08232855299
          },
          "715": {
            "linearCoefficient": 4100.110963964124
          }
        }
      },
      "RandForRegr": {
        "permutationsImportance": {
          "765": {
            "mean_accuracy_decrease": 0.31790518919360894,
            "std": 0.01918789580008025
          },
          "565": {
            "mean_accuracy_decrease": 0.18043111360139816,
            "std": 0.017919254752159684
          },
          "575": {
            "mean_accuracy_decrease": 0.13385176662211573,
            "std": 0.011424711055254734
          },
          "535": {
            "mean_accuracy_decrease": 0.08110276336223315,
            "std": 0.0112708596659814
          },
          "525": {
            "mean_accuracy_decrease": 0.06489451793278703,
            "std": 0.009798149193829183
          },
          "555": {
            "mean_accuracy_decrease": 0.05173227680768873,
            "std": 0.007039639265452218
          },
          "755": {
            "mean_accuracy_decrease": 0.04648801142831831,
            "std": 0.004982234258063359
          },
          "735": {
            "mean_accuracy_decrease": 0.04059370794750774,
            "std": 0.0072975351377627205
          }
        },
        "featureImportance": {
          "565": {
            "MDI": 0.12659487216256524,
            "std": 0.12935752609135287
          },
          "575": {
            "MDI": 0.09286225721922374,
            "std": 0.11602534384034562
          },
          "525": {
            "MDI": 0.0738140442616421,
            "std": 0.05575835660374327
          },
          "555": {
            "MDI": 0.06817066220399984,
            "std": 0.10328842789394192
          },
          "545": {
            "MDI": 0.06811959965400934,
            "std": 0.09959251627671926
          },
          "765": {
            "MDI": 0.059457595096362156,
            "std": 0.025447857707465316
          },
          "595": {
            "MDI": 0.0408413478622239,
            "std": 0.07183792056899006
          },
          "585": {
            "MDI": 0.04041500800779009,
            "std": 0.04889526983127451
          }
        }
      }
    },
    "n.tot_usda.a623_w.pct": {
      "OLS": {
        "permutationsImportance": {
          "685": {
            "mean_accuracy_decrease": 1847875.7383914646,
            "std": 43705.744845748704
          },
          "675": {
            "mean_accuracy_decrease": 1014269.4260904057,
            "std": 24051.309975302433
          },
          "695": {
            "mean_accuracy_decrease": 349380.75671949383,
            "std": 8256.015007291719
          },
          "585": {
            "mean_accuracy_decrease": 213745.11438477496,
            "std": 5275.9865578542685
          },
          "635": {
            "mean_accuracy_decrease": 211822.29312264238,
            "std": 5094.95117417782
          },
          "595": {
            "mean_accuracy_decrease": 194431.5309977783,
            "std": 4796.669035816178
          },
          "555": {
            "mean_accuracy_decrease": 165948.3559744564,
            "std": 4057.895244600783
          },
          "705": {
            "mean_accuracy_decrease": 160009.84042061635,
            "std": 3775.82521672814
          }
        },
        "featureImportance": {
          "685": {
            "linearCoefficient": 6515.49216732041
          },
          "675": {
            "linearCoefficient": -4857.1395135108605
          },
          "695": {
            "linearCoefficient": -2818.5745760560194
          },
          "585": {
            "linearCoefficient": 2527.1233295341885
          },
          "555": {
            "linearCoefficient": 2446.659469925014
          },
          "595": {
            "linearCoefficient": -2354.0393966709057
          },
          "525": {
            "linearCoefficient": 2333.9685624908725
          },
          "635": {
            "linearCoefficient": -2308.734776110277
          }
        }
      },
      "RandForRegr": {
        "permutationsImportance": {
          "715": {
            "mean_accuracy_decrease": 0.12718266925316374,
            "std": 0.012518502892438414
          },
          "615": {
            "mean_accuracy_decrease": 0.041354265546978096,
            "std": 0.004703069796240487
          },
          "485": {
            "mean_accuracy_decrease": 0.0369928944970095,
            "std": 0.005991249644123221
          },
          "495": {
            "mean_accuracy_decrease": 0.022403747853120947,
            "std": 0.005563191826291091
          },
          "705": {
            "mean_accuracy_decrease": 0.016873274230714542,
            "std": 0.002762928688617482
          },
          "625": {
            "mean_accuracy_decrease": 0.01393536711315766,
            "std": 0.0023989080228772265
          },
          "575": {
            "mean_accuracy_decrease": 0.013177571786896259,
            "std": 0.0022914715244820366
          },
          "555": {
            "mean_accuracy_decrease": 0.012570744064369843,
            "std": 0.0036388195812096127
          }
        },
        "featureImportance": {
          "485": {
            "MDI": 0.1917881740585853,
            "std": 0.2826202681023468
          },
          "495": {
            "MDI": 0.18020322244893458,
            "std": 0.27323239812847133
          },
          "475": {
            "MDI": 0.06225452172718376,
            "std": 0.15542552574180532
          },
          "525": {
            "MDI": 0.05646124691772123,
            "std": 0.15498289591531023
          },
          "555": {
            "MDI": 0.056233123547062544,
            "std": 0.15352144405603435
          },
          "545": {
            "MDI": 0.052754992069279374,
            "std": 0.15684438576743176
          },
          "565": {
            "MDI": 0.05094059741913668,
            "std": 0.15150598253872188
          },
          "575": {
            "MDI": 0.042057732655273,
            "std": 0.11463697383675055
          }
        }
      }
    },
    "oc_usda.c729_w.pct": {
      "OLS": {
        "permutationsImportance": {
          "695": {
            "mean_accuracy_decrease": 970571.336906384,
            "std": 27224.882985307522
          },
          "685": {
            "mean_accuracy_decrease": 770870.6124462709,
            "std": 21537.687800556472
          },
          "635": {
            "mean_accuracy_decrease": 643445.954748732,
            "std": 17604.850705061457
          },
          "675": {
            "mean_accuracy_decrease": 520696.98314973305,
            "std": 14480.672446557044
          },
          "645": {
            "mean_accuracy_decrease": 461680.34591451817,
            "std": 12682.169928875799
          },
          "705": {
            "mean_accuracy_decrease": 199855.52092998085,
            "std": 5638.842492174508
          },
          "615": {
            "mean_accuracy_decrease": 189474.14311856672,
            "std": 5170.755226663838
          },
          "525": {
            "mean_accuracy_decrease": 189315.1678876113,
            "std": 6012.288853457256
          }
        },
        "featureImportance": {
          "695": {
            "linearCoefficient": -120748.49956575861
          },
          "685": {
            "linearCoefficient": 108120.50777816672
          },
          "635": {
            "linearCoefficient": -103309.25070591885
          },
          "675": {
            "linearCoefficient": -89395.42596497189
          },
          "645": {
            "linearCoefficient": 86523.89503289931
          },
          "525": {
            "linearCoefficient": 76141.64247004794
          },
          "505": {
            "linearCoefficient": -75424.71810712792
          },
          "495": {
            "linearCoefficient": 74680.13960052058
          }
        }
      },
      "RandForRegr": {
        "permutationsImportance": {
          "705": {
            "mean_accuracy_decrease": 0.10658572864150333,
            "std": 0.009112261756806362
          },
          "465": {
            "mean_accuracy_decrease": 0.07114947264859019,
            "std": 0.0033296932407242317
          },
          "585": {
            "mean_accuracy_decrease": 0.05759236394811764,
            "std": 0.0067261304328874805
          },
          "595": {
            "mean_accuracy_decrease": 0.05074744814728965,
            "std": 0.00374615350635183
          },
          "695": {
            "mean_accuracy_decrease": 0.04191765567980597,
            "std": 0.004456993977490808
          },
          "605": {
            "mean_accuracy_decrease": 0.0414918981045954,
            "std": 0.0026014831930515595
          },
          "485": {
            "mean_accuracy_decrease": 0.03784337922726858,
            "std": 0.002189142096614051
          },
          "635": {
            "mean_accuracy_decrease": 0.035657269366898135,
            "std": 0.0028216919409234866
          }
        },
        "featureImportance": {
          "465": {
            "MDI": 0.15958612674457212,
            "std": 0.26134373689444296
          },
          "485": {
            "MDI": 0.14204045818809566,
            "std": 0.2623343887108612
          },
          "555": {
            "MDI": 0.0827676272727553,
            "std": 0.2001239693825927
          },
          "475": {
            "MDI": 0.08204928387922038,
            "std": 0.20011643698838572
          },
          "545": {
            "MDI": 0.07583302192791327,
            "std": 0.20007209392201802
          },
          "525": {
            "MDI": 0.07326114796037066,
            "std": 0.19626020689606302
          },
          "495": {
            "MDI": 0.05632074377531513,
            "std": 0.16571899296764434
          },
          "565": {
            "MDI": 0.05188653124959077,
            "std": 0.1639998510681376
          }
        }
      }
    }
  },
  "appliedModelingFeatures": {
    "cec_usda.a723_cmolc.kg": {
      "OLS": [
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715",
        "725",
        "735",
        "745",
        "755"
      ],
      "RandForRegr": [
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715",
        "725",
        "735",
        "745",
        "755"
      ]
    },
    "k.ext_usda.a725_cmolc.kg": {
      "OLS": [
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715",
        "725",
        "735",
        "745",
        "755",
        "765"
      ],
      "RandForRegr": [
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715",
        "725",
        "735",
        "745",
        "755",
        "765"
      ]
    },
    "n.tot_usda.a623_w.pct": {
      "OLS": [
        "475",
        "485",
        "495",
        "505",
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715"
      ],
      "RandForRegr": [
        "475",
        "485",
        "495",
        "505",
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705",
        "715"
      ]
    },
    "oc_usda.c729_w.pct": {
      "OLS": [
        "465",
        "475",
        "485",
        "495",
        "505",
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705"
      ],
      "RandForRegr": [
        "465",
        "475",
        "485",
        "495",
        "505",
        "515",
        "525",
        "535",
        "545",
        "555",
        "565",
        "575",
        "585",
        "595",
        "605",
        "615",
        "625",
        "635",
        "645",
        "655",
        "665",
        "675",
        "685",
        "695",
        "705"
      ]
    }
  },
  "modelResults": {
    "trainTest": {
      "cec_usda.a723_cmolc.kg": {
        "OLS": {
          "mse": 122.27242211906663,
          "r2": 0.48831121615177386,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_cec_usda.a723_cmolc.kg_OLS_trainTest.xsp"
        },
        "RandForRegr": {
          "mse": 101.49516677833613,
          "r2": 0.3500832852351041,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_cec_usda.a723_cmolc.kg_RandForRegr_trainTest.xsp"
        }
      },
      "k.ext_usda.a725_cmolc.kg": {
        "OLS": {
          "mse": 0.2779824590499334,
          "r2": 0.3610021531760156,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_k.ext_usda.a725_cmolc.kg_OLS_trainTest.xsp"
        },
        "RandForRegr": {
          "mse": 0.4405139695443757,
          "r2": 0.1718104392766573,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_k.ext_usda.a725_cmolc.kg_RandForRegr_trainTest.xsp"
        }
      },
      "n.tot_usda.a623_w.pct": {
        "OLS": {
          "mse": 0.09086518001601741,
          "r2": 0.6945887693867323,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_n.tot_usda.a623_w.pct_OLS_trainTest.xsp"
        },
        "RandForRegr": {
          "mse": 0.08079380450450449,
          "r2": 0.7492747761176939,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_n.tot_usda.a623_w.pct_RandForRegr_trainTest.xsp"
        }
      },
      "oc_usda.c729_w.pct": {
        "OLS": {
          "mse": 52.18931396991764,
          "r2": 0.7550853892931986,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_oc_usda.c729_w.pct_OLS_trainTest.xsp"
        },
        "RandForRegr": {
          "mse": 46.61687906446446,
          "r2": 0.7828779159834647,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_oc_usda.c729_w.pct_RandForRegr_trainTest.xsp"
        }
      }
    },
    "Kfold": {
      "cec_usda.a723_cmolc.kg": {
        "OLS": {
          "mse": 155.5368753095911,
          "r2": 0.4488238965732262,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_cec_usda.a723_cmolc.kg_OLS_Kfold.xsp"
        },
        "RandForRegr": {
          "mse": 164.82000332383666,
          "r2": 0.4159272711503528,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_cec_usda.a723_cmolc.kg_RandForRegr_Kfold.xsp"
        }
      },
      "k.ext_usda.a725_cmolc.kg": {
        "OLS": {
          "mse": 0.3350638398714068,
          "r2": 0.3495948891400069,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_k.ext_usda.a725_cmolc.kg_OLS_Kfold.xsp"
        },
        "RandForRegr": {
          "mse": 0.4055360560422809,
          "r2": 0.2127986010393339,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_k.ext_usda.a725_cmolc.kg_RandForRegr_Kfold.xsp"
        }
      },
      "n.tot_usda.a623_w.pct": {
        "OLS": {
          "mse": 0.10409364182580515,
          "r2": 0.6627269506188842,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_n.tot_usda.a623_w.pct_OLS_Kfold.xsp"
        },
        "RandForRegr": {
          "mse": 0.07815435003454388,
          "r2": 0.7467726606908363,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_n.tot_usda.a623_w.pct_RandForRegr_Kfold.xsp"
        }
      },
      "oc_usda.c729_w.pct": {
        "OLS": {
          "mse": 60.53643178256006,
          "r2": 0.7176343354830652,
          "hyperParameterSetting": {
            "fit_intercept": false
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_oc_usda.c729_w.pct_OLS_Kfold.xsp"
        },
        "RandForRegr": {
          "mse": 41.94697233180138,
          "r2": 0.8043428664661596,
          "hyperParameterSetting": {
            "n_estimators": 30
          },
          "pickle": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/mlmodel/Sweden_LUCAS_460-1050_10/pickle/testtg_modelid_oc_usda.c729_w.pct_RandForRegr_Kfold.xsp"
        }
      }
    }
  }
}
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

#### Conserved model settings

The conserved model settings are stored in "pickle" files; with one pickle file for each combination of regressor and target feature. The pickle files are under the subfolder _pickle_. The "pickle" files are in binary format and not intelligible for a human reader. The actual settings and data used are reported in a human intelligible format in the [json report](#json-report).

#### Image plots

If requested in the json command file, a range of image plots showing feature importances and model performance are saved as png files. the [next post](../spectrodata-OSSL4ML06-mlmodel/) in this series gives the details on how to layout and save the results as image plots. You can choose to save png images of each individual plot, or save multi-row/multi-columns where the rows either represent different target features and a single regressor, or different regressors and a single target feature.

If you choose to save the full suite of image plots from the json command file for Swedish OSSL data above, the process-flow will render 25 individual image plots and 9 multi-plot images.

Single plot images generated from a combination of 4 target features and 2 regressor models:

- 4 targets * 2 model = 8 permutation importance plot images
- 4 targets * 2 model = 8 feature importance plot images
- 4 targets * 2 model = 8 train/test model predictions
- 4 targets * 2 model = 8 cross-validated model predictions

Multi plot images generated from a combination of 4 target features and 2 regressor models:

- 4 multi-row target images, and
- 2 multi-row regressor images.

The columns to include in the multi-row plots is defined in the json command file (and explained in the [next post](../spectrodata-OSSL4ML06-mlmodel/). Altogether there are 4 columns that can be included in the multi-row plots:

- permutation importance (permutationImportance),
- feature (coefficient) importance (featureImportance),
- train/test model prediction (trainTest) and
- cross validated model predication (Kfold).

The columns to include are listed in the json command file using the compact text listed above:

```
"columns": [
  "permutationImportance",
  "featureImportance",
  "Kfold"
]
```
The individual panels of the multi-row plots are identical to the single panel plots, thus the examples in figures 3 to 5 illustrate the layout of most plots.

<figure>
  <a href="../../images/testtg_oc_usda.c729_w.pct-multi-results.png">
  <img src="../../images/testtg_oc_usda.c729_w.pct-multi-results.png" alt="image">
  </a>
  <figcaption>Figure 3. Plots for the result of modelling soil organic carbon (oc). showing permutation importance, train/test and Kfold model results as columns and the regressors ordinary least square and Random Forest as rows. </figcaption>
</figure>

<figure>
  <a href="../../images/testtg_n.tot_usda.a623_w.pct-multi-results.png">
  <img src="../../images/testtg_n.tot_usda.a623_w.pct-multi-results.png" alt="image">
  </a>
  <figcaption>Figure 4. Plots for the result of modelling soil nitrogen (N) showing permutation importance, train/test and Kfold model results as columns and the regressors ordinary least square and Random Forest as rows. </figcaption>
</figure>

<figure>
  <a href="../../images/testtg_RandForRegr-multi-results.png">
  <img src="../../images/testtg_RandForRegr-multi-results.png" alt="image">
  </a>
  <figcaption>Figure 5. Plots for the result of applying the Random Forest regressor to 5 different target features (rows); with the columns showing permutation importance, feature importance and the Kfold model result. </figcaption>
</figure>

### Symbolisation and plot layout

In the plot examples above I have set different colours to each target feature and different markers (symbols) for each regressor. Apart from feature and regressor symbolisation, you can also set height and width, including the space distribution between the sub-plots etc. The topic of the [next post](../spectrodata-OSSL4ML06-mlmodel02) in this series is how to alter the symbolisation and layout of the plots.
