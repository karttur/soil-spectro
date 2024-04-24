---
layout: post
title: "Chemometric modelling: 10 specific feature selection"
categories: spectromodel
excerpt: "Specific feature selection regarding target feature or target feature and regressor."
tags:
  - OSSL
  - machine learning
  - preprocessing
  - scikit learn
  - sklearn
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-11-10 11:27'
modified: '2024-04-21'
comments: true
share: true
---

### Process flow - specificFeatureSelection

Specific feature selection (_specificFeatureSelection_) are the most fine tuned and advanced methods for selecting covariates. This also means that they require more computing power. It is thus advisable to reduce the number of covariates entering this step in the process flow.

There are three (3) specfic feature selection methods aviable, but only one can be applied in each model formulation:
- [Univariate selection (univariateSelection)](https://scikit-learn.org/stable/auto_examples/feature_selection/plot_feature_selection.html),
- [Permutation selector (permutationSelector)](https://scikit-learn.org/stable/modules/permutation_importance.html), and.
- Random Feature Elemination (RFE)

If a specific feature selection is applied it is the last step before the regression modeling.

```
|____SpectralData
| |____filter
| | |____singlefilter
| | |____multiFilter
| |____dataSetSplit
| | |____spectralInfoEnhancement
| | | |____scatterCorrection
| | | |____standardisation
| | | |____derivatives
| | | |____decompose
| | |____generalFeatureSelection
| | | |____varianceThreshold
| | |____targetFeatureExtract
| | | |____removeOutliers
| | | |____regressorExtract
| | | | |____specificFeatureAgglomeration
| | | | | |____wardClustering
| | | | |____specificFeatureSelection
| | | | | |____univariateSelection
| | | | | |____permutationSelector
| | | | | |____RFE
```

### Introduction

#### Univaraite Feature Selection

[Univariate feature selection](https://scikit-learn.org/stable/auto_examples/feature_selection/plot_feature_selection.html) uses an F-test as default for calculating p-values (univariate scores) for each covariate. Model fitting for calculating the scores is done against the target feature. You have to define the number of covariate feature to retain a-priori (parameter _n_features_)
To apply the Univariate feature selection as part of the process flow, edit the command file thus:

```
"specificFeatureSelection": {
    "apply": true,
    "univariateSelection": {
      "apply": false,
      "SelectKBest": {
        "apply": true,
        "n_features": 4
      }
    }
```

Figure 1 shows the outcomes of selecting 4 covariates from meancenterd spectra (left) derivatives (middle) and PCA decomposed bands (right). In all cases total nitrogen [N] was set as the target feature. The top row shows the selection of covaraites for the Ordinary Least Square (OLS) regressor; the bottom row for the Random Forest regressor.

<figure class="third">

<a href="../../images/spectromodel_univselect_4_spectra-meancenter_N_OLS.png"><img src="../../images/spectromodel_univselect_4_spectra-meancenter_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_univarsel_4_derivatives_N_OLS.png"><img src="../../images/spectromodel_univarsel_4_derivatives_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_univarsel_4_pca_N_OLS.png"><img src="../../images/spectromodel_univarsel_4_pca_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_univarsel_4_spectra-meancenter_N_RandomForest.png"><img src="../../images/spectromodel_univarsel_4_spectra-meancenter_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_univarsel_4_derivative_N_RandomForest.png"><img src="../../images/spectromodel_univarsel_4_derivative_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_univarsel_4_pca_N_RandomForest.png"><img src="../../images/spectromodel_univarsel_4_pca_N_RandomForest.png" alt="image"></a>

<figcaption>Figure 1. Ward clustering of covariates; From top to bottom the rows show clustering of meancentred spectral signals (top), clustering of derivatives (middle) and clustering after PCA decompositions (bottom); the left columns show clustering with a fixed number of output clusters and the right columns after applying a tuning for deciding the optimal number of clusters.
</figcaption>
</figure>

#### Permutation Selection

Permutation feature importance measures the strength of the contribution of each covariate to a fitted model’s statistical performance. The covariates are shuffled randomly and the change in model statistical perforance when omitting a covaraites defines its strength. This generic method of evaluating covariates can be applied ultiple times to any regressor and can thus be used for covariate selection for any combination of target feature and regressor.

Figure 2 shows the outcomes of selecting 4 covariates from meancenterd spectra (left) derivatives (middle) and PCA decomposed bands (right). In all cases total nitrogen [N] was set as the target feature. The top row shows the selection of covaraites for the Ordinary Least Square (OLS) regressor; the bottom row for the Random Forest regressor.

<figure class="third">

<a href="../../images/spectromodel_permsel_4_spectra-meancenter_N_OLS.png"><img src="../../images/spectromodel_permsel_4_spectra-meancenter_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_permsel_4_derivatives_N_OLS.png"><img src="../../images/spectromodel_permsel_4_derivatives_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_permsel_4_pca_N_OLS.png"><img src="../../images/spectromodel_permsel_4_pca_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_permsel_4_spectra-meancenter_N_RandomForest.png"><img src="../../images/spectromodel_permsel_4_spectra-meancenter_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_permsel_4_derivative_N_RandomForest.png"><img src="../../images/spectromodel_permsel_4_derivative_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_permsel_4_pca_N_RandomForest.png"><img src="../../images/spectromodel_permsel_4_pca_N_RandomForest.png" alt="image"></a>

<figcaption>Figure 2. Ward clustering of covariates; From top to bottom the rows show clustering of meancentred spectral signals (top), clustering of derivatives (middle) and clustering after PCA decompositions (bottom); the left columns show clustering with a fixed number of output clusters and the right columns after applying a tuning for deciding the optimal number of clusters.
</figcaption>
</figure>

#### RFE

Feature ranking with recursive feature elimination.

Given an external estimator that assigns weights to features (e.g., the coefficients of a linear model), the goal of recursive feature elimination (RFE) is to select features by recursively considering smaller and smaller sets of features. First, the estimator is trained on the initial set of features and the importance of each feature is obtained either through any specific attribute or callable. Then, the least important features are pruned from current set of features. That procedure is recursively repeated on the pruned set until the desired number of features to select is eventually reached.

<figure class="third">

<a href="../../images/spectromodel_rfe_4_spectra-meancenter_N_OLS.png"><img src="../../images/spectromodel_rfe_4_spectra-meancenter_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfe_4_derivatives_N_OLS.png"><img src="../../images/spectromodel_rfe_4_derivatives_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfe_4_pca_N_OLS.png"><img src="../../images/spectromodel_rfe_4_pca_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfe_4_spectra-meancenter_N_RandomForest.png"><img src="../../images/spectromodel_rfe_4_spectra-meancenter_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_rfe_4_derivative_N_RandomForest.png"><img src="../../images/spectromodel_rfe_4_derivative_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_rfe_4_pca_N_RandomForest.png"><img src="../../images/spectromodel_rfe_4_pca_N_RandomForest.png" alt="image"></a>

<figcaption>Figure 2. Ward clustering of covariates; From top to bottom the rows show clustering of meancentred spectral signals (top), clustering of derivatives (middle) and clustering after PCA decompositions (bottom); the left columns show clustering with a fixed number of output clusters and the right columns after applying a tuning for deciding the optimal number of clusters.
</figcaption>
</figure>

#### RFECV

<figure class="third">

<a href="../../images/spectromodel_rfecv_4_spectra-meancenter_N_OLS.png"><img src="../../images/spectromodel_rfecv_4_spectra-meancenter_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfecv_4_derivatives_N_OLS.png"><img src="../../images/spectromodel_rfecv_4_derivatives_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfecv_4_pca_N_OLS.png"><img src="../../images/spectromodel_rfecv_4_pca_N_OLS.png" alt="image"></a>

<a href="../../images/spectromodel_rfecv_4_spectra-meancenter_N_RandomForest.png"><img src="../../images/spectromodel_rfecv_4_spectra-meancenter_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_rfecv_4_derivative_N_RandomForest.png"><img src="../../images/spectromodel_rfecv_4_derivative_N_RandomForest.png" alt="image"></a>

<a href="../../images/spectromodel_rfecv_4_pca_N_RandomForest.png"><img src="../../images/spectromodel_rfecv_4_pca_N_RandomForest.png" alt="image"></a>

<figcaption>Figure 2. Ward clustering of covariates; From top to bottom the rows show clustering of meancentred spectral signals (top), clustering of derivatives (middle) and clustering after PCA decompositions (bottom); the left columns show clustering with a fixed number of output clusters and the right columns after applying a tuning for deciding the optimal number of clusters.
</figcaption>
</figure>
