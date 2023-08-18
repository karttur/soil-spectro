---
layout: post
title: Model OSSL data
categories: libspectrodata
excerpt: "Model OSSL data using Python's Machine Learning (ML) package Scikit learn (sklearn)."
tags:
  - OSSL
  - machine learning
  - modeling
  - scikit learn
  - sklearn
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-17 11:27'
modified: '2023-07-05 11:27'
comments: true
share: true
---

### Introduction

As the Open Soil Spectral Library (OSSL) contains thousands of individual samples, each with thousands of recorded reflectances in different wavelengths, and additional physical and chemical soil properties, it is a suitable dataset for applying Machine Learning (ML) modelling. This post both illustrates how to apply ML for modelling soil properties from soil spectral data, and is at the same time a hands-on manual for modeling soil spectra from OSSL using the python script <span class='module'>OSSL_mlmodel.py</span>.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [arranging](../spectrodata-OSSL4ML01-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages <span class='package'>matplotlib</span> and <span class='package'>scikit learn (sklearn)</span> installed.  

### Machine Learning

Machine Learning (ML) includes a range of methods for preparing, refining, selecting, aggregating, evaluating and modelling different dependent properties from a set of independent features (covariates). For the modelling of soil properties from spectral data I have built a predefined process-flow structure. All steps in the process-flow, except the actual regression from spectral signals to soil properties, are optional. The process-flow is governed by a command file using the json syntax. Editing the json file you can chose which target features, regressors and steps to include in your own process-flow. The general steps included are:

1. Global data cleaning and selection
- outlier detection and removal
- variance threshold feature selection
2. Target property related feature selection
- univariate feature selection
- feature clustering
3. Model related feature selection
- permutation importance selection
- Recursive Feature Elimination (RFE)
4. Feature importance evaluation
- permutation importance
- coefficient importance
5. Regression model and target property related hyper parameter tuning
- random tuning
- exhaustive grid search
6. Model fitting and evaluation
- train/test divided prediction
- cross validated prediction (kfold)

The next sections go through the general steps oulined above. If you just want to run the <span class='module'>OSSL_mlmodel</span> package and do not need the instuctions, just jump to the [??? NOT YET DONE](#SNULLE) section.

#### Global data cleaning and selection

The OSSL data contain a large number of samples, each with hundreds or even thousands of recorded wavelength reflectances. The data can contain errors (outliers) and using all the data can lead to over-parameterised models. It is also inevitable that some wavelengths (or bands) contain less information compared to others; a band with no variation (i.e. constant reflection) does not contain any relevant information for ML modelling.

The global data cleaning and selection methods analyse the independent features (the covariates) disregarding both the target and the method for estimation of the target variations. It is a more crude method for discarding data compared to the methods that compare the covariates in relation to the target and the estimator (the two following steps in the process-flow). The global methods are, however comparatively fast and applying the global methods means that all subsequent processing in the framework will use the cleaned and reduced dataset.

##### Outlier removal

To remove outliers the framework includes four different outlier detectors available in the package scikit learn:
- [IsolationForest](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html)
- [EllipticEnvelope](https://scikit-learn.org/stable/modules/generated/sklearn.covariance.EllipticEnvelope.html)
- [LocalOutlierFactor](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LocalOutlierFactor.html)
- [OneClassSVM](https://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html)

In the json command file you turn the outlier on by the following lines:
```
  "removeOutliers": {
    "apply": true,
    "detector": "LocalOutlierFactor",
    "contamination": 0.1
    },
```

The only parameter that can be changed in the present version of the framework is _contamination_ (applying to the parameter _nu_ in the detector _OneClassSVM_).

##### variance threshold feature selection

Applying the sklearn method [VarianceThreshold](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html) removes all low-variance features with variances below a stated threshold. The removal is unrelated to the target features and the regression models. To neutralise the range of the input features the sklearn [MinMaxScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html) is applied as a pre-process. The only parameter that you can set in the process-flow command file is the _threshold_ for retaining or discarding a feature. You will probably have to iteratively test different thresholds. In the json command you include variance feature selection by these lines:

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

The global feature selection using variance threshold is unrelated to the properties that are to be predicted. To select the features (covariates) that relate to a specific target (soil chemical and physical properties in this example) the process-flow includes [univariate feature selection](https://scikit-learn.org/stable/modules/feature_selection.html#univariate-feature-selection). Scikit learn includes several selectors that can be used with the univariate feature selection, but at present only the the KBest selector is implemented.

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

##### Feature agglomeration

Scikit learn contains a range of methods for [feature agglomeration](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.cluster). The Ward clustering implemented in the framework has the advantage that you can tune the number of clusters to request from the main agglomeration. To include the Ward clustering in the framework, edit the json command file thus:

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

The most advanced options for reducing the number of covariates considers both the target property and the applied regressor. The process-flow includes two methods that can be used for this kind of model related feature selection: [permutation importance selection](https://scikit-learn.org/stable/modules/permutation_importance.html) and [Recursive Feature Elimination (RFE)](https://scikit-learn.org/stable/modules/feature_selection.html#recursive-feature-elimination). Permutation importance can be applied to all kinds of regressors, whereas RFE is not applicable for example for KnnRegr or MLP. if you request RFE for any of these, the process-flow will instead do a Permutation importance selection. You can only apply one of the two model related feature selection methods in each modelling exercise. If both are turned on, the process-flow will automatically select permutation importance.

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

##### Recursive Feature Elimination (RFE)

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

Note that RFE, and even more so, RFECV, will take a long time if you have large datasets with many features. Thus it is recommended to first select features using either global [variance threshold](#variance-threshold-feature-selection), [Univariate feature selection](#univariate-feature-selection) or [Feature agglomeration](#feature-agglomeration), or actually a combination of two of these methods. Doing so will reduce the number of features evaluated using RFE/RFECV and considerably speed up the processing time.

#### Feature importance evaluation

After having selected the features to use (or not) you can choose to evaluate the feature importances for each combination of target feature and regression model. The evaluation is done both for [permutation importance](https://scikit-learn.org/stable/modules/permutation_importance.html) (the decrease in a model score when a single feature value is randomly shuffled) and for model coefficients or feature importances (depends on the type of regressor).

Note that some regressors do not generate any coefficients or feature importances (KnnRegr and MLP) and then only the permutation importance is reported.

If requested, the importance will, in addition to text reporting in the json output file, also be shown on screen or saved as a <span class='file'>png</span> file (figure 1). To include feature importance evaluation edit the json command file:

```
"featureImportance": {
  "apply": true,
  "reportMaxFeatures": 12,
  "permutationRepeats": 10
}
```
Except from setting the parameter _apply_ to _true_ to invoke the feature importance evaluation, you also have to give the maximum number of features to include in the reporting, _reportMaxFeatures_ . The reporting will always show the highest ranking features. Then you also have to give the number of _permutationRepeats_ to use in the evaluation. Figure 1 shows the feature importance evaluation for the target feature oc_usda.c729_w.pct. The rows show results for the regressors OLS (Ordinary Least Square) and RandForRegr (Random Forest Regression), while the columns show coefficient(OLS)/estimators(RandForRegr) importance. The error bars shows the standard deviation (1 std).

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

	<figcaption>Figure 1. Feature importance evaluation for target feature oc_usda.c729_w.pct; The rows show results for the regressors OLS (Ordinary Least Square) and RandForRegr (Random Forest Regression), while the columns show permutation importance and coefficient(OLS)/estimators(RandForRegr); error bars shows the standard deviation (1 std).</figcaption>
</figure>

#### Regression model and target property related hyper parameter tuning

[Hyper-parameters](https://scikit-learn.org/stable/modules/grid_search.html) are parameters that determine how an estimator learns to find patterns and are not directly learnt within estimators. Put differently, each time an estimator is applied for fitting a model, the training depends on the setting of the hyper-parameters. Tweaking the hyper-parameter setting is thus an important part of fitting ML models to generate the best predictions for a particular target feature. The ML skeleton herein can use two different methods for hyper-parameter optimisation: [random tuning](https://scikit-learn.org/stable/modules/grid_search.html#randomized-parameter-optimization) and [exhaustive grid search](https://scikit-learn.org/stable/modules/grid_search.html#exhaustive-grid-search).

To invoke hyper-parameter tuning in the framework, edit the json commant file:

```
"hyperParameterTuning": {
  "apply": false,
  "fraction": 0.5,
  "nIterSearch": 6,
  "n_best_report": 3,
  "randomTuning": {
    "apply": true
  },
  "exhaustiveTuning": {
    "apply": false
  }
}
```

The actual setting of the tuning space for the hyper-parameters are set in separate json command files given under the input tag.

```
"input": {
    "jsonSpectraDataFilePath": "/path/to/file/with/arranged/OSSLdata",
    "jsonSpectraParamsFilePath": "/path/to/file/with/arranged/parameters",
    "hyperParameterRandomTuning": "/path/to/file/with/search/space/for/random/tuning",
    "hyperParameterExhaustiveTuning": "/path/to/file/with/griddata/for/exhaustive/tuning"
},
```

In the present version of the framework you can set hyper-parameter tuning for the following regressors:

- [k-nearest neighbors regressor (KnnRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsRegressor.html),
- [decision tree regressor (DecTreeRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html),
- [support vector regressor (SVR)](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVR.html),
- [random forest regressor (RandForRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html), and
- [multi-layer perceptron regressor (MLP)](https://scikit-learn.org/stable/modules/generated/sklearn.neural_network.MLPRegressor.html).

Where MLP is a neural network type of regressor.

Using exhaustive grid search you can also test a few different hyper-parameter settings for some linear regression models:

- [Oridnary Least Square (OLS)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html),
- [Theil-Sen Regressor (TheilSen)](https://scikit-learn.org/stable/auto_examples/linear_model/plot_theilsen.html), and
- [Huber Regressor (Huber)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.HuberRegressor.html).

##### Random tuning

In random tuning you specify the search space for each hyper-parameter to tune and then allow a set number of iterations for finding the best tuning. Only a limited set of hyper-parameters are included in the present version of the process-flow. The regressors and hyper-parameters that can be tuned must be given in a separate json file (see previous section), with the following options available for random tuning:

```
{
  "RandomTuning": {
    "KnnRegr": {
      "apply": false,
      "tuningParams": {
        "n_neighbors": {
          "min": 4,
          "max": 12
        },
        "leaf_size": {
          "min": 10,
          "max": 50
        },
        "weights": [
          "uniform",
          "distance"
        ],
        "p": [
          1,
          2
        ],
        "algorithm": [
          "auto",
          "ball_tree",
          "kd_tree",
          "brute"
        ]
      }
    },
    "DecTreeRegr": {
      "apply": false,
      "tuningParams": {
        "max_depth": [
          3,
          0
        ],
        "min_samples_split": {
          "min": 2,
          "max": 6
        },
        "min_samples_leaf": {
          "min": 1,
          "max": 4
        }
      }
    },
    "SVR": {
      "apply": false,
      "tuningParams": {
        "kernel": [
          "linear",
          "rbf"
        ],
        "epsilon": [
          0.05,
          0.1,
          0.2
        ],
        "C": [
          1,
          2,
          5,
          10
        ]
      }
    },
    "RandForRegr": {
      "apply": true,
      "tuningParams": {
        "max_depth": [
          3,
          2,
          1,
          0
        ],
        "n_estimators": {
          "min": 10,
          "max": 50
        },
        "max_features": {
          "min": 1,
          "max": 12
        },
        "min_samples_split": {
          "min": 2,
          "max": 6
        },
        "min_samples_leaf": {
          "min": 1,
          "max": 4
        },
        "bootstrap": [
          true,
          false
        ]
      }
    },
    "MLP": {
      "apply": false,
      "tuningParams": {
        "solver": [
        "lbfgs",
        "sgd",
        "adam"
        ],
        "alpha": {
          "min": 0.0001,
          "max": 0.001
        },
        "max_iter": {
          "min": 200,
          "max": 300
        }
      }
    }
  }
}
```

##### Exhaustive grid search

The exhaustive grid search method is more comprehensive and searches all combinations of hyper-parameters given as input. If you for instance want to tune 3 hyper parameters, each with 3 options, the grid search will amount to 3^3 (= 27) test runs, if you instead set 4 hyper parameters and 4 options, the number of iterations will increase to 256 (4^4). For large datasets and complex regressors that might take a very long time.  

In the process-flow, only a limited set of hyper-parameters are included . Regressors and hyper-parameters that can be tuned must be given in a separate json file (see previous section), with the following options available for grid search tuning:

```
{
  "ExhaustiveTuning": {
    "OLS": {
      "apply": false,
      "tuningparams": {
        "fit_intercept": [
          true,
          false
        ]
      }
    },
    "TheilSen": {
      "apply": false,
      "tuningparams": {
        "fit_intercept": [
          true,
          false
        ],
        "tol": [
          0.00001,
          0.0001
        ],
        "random_state": [
          30,
          42
        ]
      }
    },
    "Huber": {
      "apply": false,
      "tuningparams": {
        "epsilon": [
          1.25,
          1.35,
          1.45
        ],
        "alpha": [
          0.00001,
          0.0001
        ],
        "tol": [
          0.00001,
          0.0001
        ],
        "fit_intercept": [
          true,
          false
        ]
      }
    },
    "KnnRegr": {
      "apply": false,
      "tuningparams": {
        "n_neighbors": [
          4,
          5,
          6,
          7
        ],
        "weights": [
          "uniform",
          "distance"
        ],
        "algorithm": [
          "ball_tree",
          "kd_tree",
          "brute"
        ],
        "leaf_size": [
          15,
          20,
          30
        ],
        "p": [
          1,
          2
        ]
      }
    },
    "DecTreeRegr": {
      "apply": false,
      "tuningparams": {
        "splitter": [
          "best",
          "random"
        ],
        "max_depth": [
          0,
          1,
          2,
          3
        ],
        "min_samples_split": [
          2,
          3,
          4
        ],
        "min_samples_leaf": [
          1,
          2,
          3,
          4
        ]
      }
    },
    "SVR": {
      "apply": false,
      "tuningparams": {
        "kernel": [
          "linear",
          "rbf"
        ],
        "epsilon": [
          0.05,
          0.1,
          0.2
        ],
        "C": [
          1,
          1.5,
          2
        ]
      }
    },
    "RandForRegr": {
      "apply": true,
      "tuningparams": {
        "max_depth": [
          3,
          2,
          1,
          0
        ],
        "n_estimators": [
          20,
          30,
          40
        ],
        "min_samples_split": [
          2,
          4,
          6
        ],
        "min_samples_leaf": [
          1,
          2,
          3,
          4
        ],
        "bootstrap": [
          true,
          false
        ]
      }
    },
    "MLP": {
      "apply": false,
      "tuningparams": {
        "hidden_layer_sizes": [
          [
            80,
            80
          ],
          [
            100,
            100
          ],
          [
            120,
            120
          ]
        ],
        "solver": [
          "adam"
        ],
        "alpha": [
          0.001,
          0.0001
        ],
        "max_iter": [
          200,
          300
        ]
      }
    }
  }
}
```
