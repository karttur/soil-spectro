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

As the Open Soil Spectral Library (OSSL) contains thousands of individual samples, each with thousands of recorded reflectances in different wavelengths, and additional physical and chemical soil properties, it is an almost ideal dataset for applying machine Learning (ML) modelling. This post both illustrates how to apply ML for modelling soil properties from soil spectral data, and is at the same time a hands-on manual for modeling soil spectra from OSSL using the python script <span class='module'>OSSL_mlmodel.py</span>.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [arranging](../spectrodata-OSSL4ML01-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages <span class='package'>matplotlib</span> and <span class='package'>scikit learn (sklearn)</span> installed.  

### Machine Learning

Machine Learning (ML) includes a range of methods for preparing, refining, selecting, aggregating, evaluating and modelling different dependent properties from a set of independent features (covariates). For the modelling of soil properties from spectral data I have built a predefined process framework. All steps in the framework, except the actual regression from spectral signals to soil properties, are optional. The general steps included in the framework are:

1. Global data cleaning and selection,
- outlier detection and removal,
- variance threshold feature selection,
2. Target property related feature selection
- univariate feature selection
- feature clustering
3. Model related feature selection
- Recursive Feature Elimination (RFE)
4. Feature importance evaluation
- permutation importance
- coefficient importance
5. Regression model and target property related hyper parameter tuning
- random tuning
- exhaustive tuning
6. Model fitting and evaluation
- train/test divided prediction
- cross validated prediction (kfold)

The next sections go through the general steps oulined above. If you just want to run the <span class='module'>OSSL_mlmodel</span> package and do not need the instuctions, just jump to the [???](#SNULLE) section.

#### Global data cleaning and selection

The OSSL data contain a large number of samples, each with hundreds or thousands of recorded wavelength reflectances. The data can contain errors (outliers) and using all the data can lead to over-parameterised models. It is also inevitable that some wavelengths(or bands) contain less information compared to others; a band with no variation (i.e. constant reflection) does not contain any relevant information for ML modelling.

Applying the global methods means that all subsequent processing in the framework will use the cleaned and reduced dataset.

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

Applying the sklearn method [VarianceThreshold](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html) removes all low-variance features with variances below a stated threshold. The removal is unrelated to the target features and the regression models. To neutralise the range of the input features the sklearn [MinMaxScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html) is applied as a pre-process.
In the json command you include variance feature selection by these lines:

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

The global feature selection using variance threshold is unrelated to the properties that are to be predicted. To select the features (covariates) that relate to a specific property (soil chemical and physical properties in this example) there are two main methods included in the framework, [univariate feature selection][https://scikit-learn.org/stable/modules/feature_selection.html#univariate-feature-selection] (using a KBest selector) and [Recursive Feature Elimination (RFE)](https://scikit-learn.org/stable/modules/feature_selection.html#recursive-feature-elimination). In general, RFE is the superior method, especially if applied using Cross Validation (RFECV).

Another route for reducing the number of features is to cluster (agglomerate) them. Features that show the similar variations in relation to the target feature are collected to a single feature. For the framework I have written a Ward clustering routine that also includes a tuning function for determining the optimum number of clusters to form.

In the framework you can in principle invoke both a univariate feature selection and a clustering, but in normal cases you would only use one for each model building exercise.

##### Univariate feature selection

There are several methods available for univariate feature selection. In the present version of the framework I have inlcuded [SelectKBest](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html) and [RFE](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFE.html).

To invoke one of the methods, wet the one you want to use to _True_ in the json command file:

```
"modelFeatureSelection": {
  "apply": true,
  "univariateSelection": {
    "apply": false,
    "SelectKBest": {
      "apply": true,
      "n_features": 5
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
  },
  "RFE": {
    "apply": true,
    "CV": true,
    "n_features_to_select": 5,
    "step": 1
  }
},
```

As clear from the code snippet above there are other methods available in scikit learn for advanced feature selection. These are, however, not implemented in the present generation of the framework.

The main parameter to set in both _SelectKBest_ and _RFE_ is the number of features to target (_n_feature_ for _SelectKBest_ ; _n_features_to_select_ for _RFE_). For _RFE_ you also need to set whether to use Cross Validation (CV) (strongly recommended) and the _step_.

The univariate feature selection is set separately for each target feature (sol property) to model and the selection of KBest features for one soil property has no affect on the selection of features for other soil properties.

##### Feature agglomeration

Also for feature agglomeration scikit learn offers several methods (see [here](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.cluster)). The Ward clustering implemented in the framework has the advantage that you can tune the number of clusters to request from the main agglomeration. To include the Ward clustering in the framework, edit the json command file thus:

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

In the example above I have asked the tuning function to evaluate all optional cluster sizes between 4 and 12, and set it to a kfold strategy of 3 folds. As the framework will seek an optimal number of clusters (for each target feature), I have set the _n_clusers_ parameter for the main _wardClustering_ to _0_. If I would not have requested _tuneWardClustering_ that number must instead be set to the actual number of clusters requested from _wardClustering_.

#### Model related feature selection

The most advanced feature selection method implemented in the framework is the [Recursive Feature Elimination (RFE)](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFE.html), that can also be run with Cross Validation (CV) as the sklearn [RFECV](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFECV.html) method. RFE is applied specifically for each combination of target feature and regression model. To use RFE/RFECV as part of your project, edit the following lines of the json command file:

```
"RFE": {
  "apply": true,
  "CV": true,
  "n_features_to_select": 5,
  "step": 1
}
```

Note that RFE, and even more so, RFECV, will take a long time if you have larg datasets with many featured. Thus it is recommended to first select features using either  global [variance threshold](#variance-threshold-feature-selection) or [Univariate feature selection](#univariate-feature-selection) or [Feature agglomeration](#feature-agglomeration), or actually a combination of two of these methods. Doing so will reduce the number of features evaluated using RFE/RFECV and considerably speed up the processing time.

#### Feature importance evaluation

After having selected the features to use (or not) you can choose to evaluate the feature importances for each combination of target feature and regression model. The evaluation is done both for [permutation importance](https://scikit-learn.org/stable/modules/permutation_importance.html) (the decrease in a model score when a single feature value is randomly shuffled) and for coefficient or estimators (depends on the type of regressor). If requested, the importance will, in addition to text reporting, also be shown on screen or saved as a <span class='file'>png</span> file, figure 1.
