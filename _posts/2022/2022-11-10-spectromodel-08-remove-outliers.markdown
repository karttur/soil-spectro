---
layout: post
title: "Chemometric modelling: 8 Remove outliers"
categories: spectromodel
excerpt: "Spectral signal variance threshold selector."
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

### Introduction

Outliers can origin from a variety of different sources, including instrument failure, sample mixup, human translation or spelling error etc. Outliers can also have unproportional large influence on model definition and calibration. Removing any outliers prior to formulating any chemometric translation model for spectra is thus often crucial. (The very high quality OSSL spectral data used in this tutorial, however, are almost flawless).

If an outlier detector is applied in the model formulation and calibration, the same detector, with preserved parameterisations, should also be applied when evaluating the model and using it for predicting compositions of novel samples.

In the process flow, four methods for outlier detection are provided. One of them (_localoutlierfactor_), however, is not suited for transfer from training to test or novel data. The following three methods are thus available for detecting and removing outliers:

- [iforest (isolationforest)](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html),
- [ee (eenvelope, ellipticenvelope)](https://scikit-learn.org/stable/modules/generated/sklearn.covariance.EllipticEnvelope.html), and
- [1csvm (1c-svm, oneclasssvm)](https://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html).

The methods work both on covariates only, and on a combination of covariates and a target feature. Thus the outlier detection as implemented in the process flow applies a different selection for each target variable - taking back all samples when fitting the selector to a new target (regardless if the target feature itself was evaluated in the outlier detection or not).

The user can set the detection to operate on:

- all covariates,
- all covariates and the target feature,
- a selected list of named covariates,
- a selected list of named covariates and the target feature.

The first example below shows the json arguments for applying all covariates and no target feature using the _iforest_ detector:

```
"removeOutliers": {
    "apply": true,
    "covarList": [
      "*"
    ],
    "includeTarget": false,
    "detector": "iforest",
    "contamination": 0.1,
    "random_state": 42
  }
```

The second example shows the arguments for applying the _1csvm_ detector using the first principal component (default name in the process flow for the first component is pc-001 - it can not be altered), combined with the target. This detector setting can be illustrated in diagram, and is the plot in the lower right corner of Figure 1.

```
"removeOutliers": {
    "apply": true,
    "covarList": [
      "pc-001"
    ],
    "includeTarget": true,
    "detector": "1csvm",
    "contamination": 0.1,
    "random_state": 42
  }
```

The result of the outlier detection can be diagrammatically presented, but so far only when there are two (2) features used for detection, of which one can be the target feature (but it can also be two covariates). I need to look into this in more detail to understand better how the outliers are detected and how to illustrate the results with more than two input vectors.

As decomposition using PCA stores a large part of the information in the first (= 0:th vector in python) component, I tend to think that combining PCA component 1 and the target feature shoudl give the best result. But I am not sure.

In figure 1 below I have tested two different datasets against the three outlier detectors, spectral data after meancentring and after decomposition. All examples use one covariate (the first pca component or the 530 nm spectral band) and the target feature (total nitrogen [N]).

<figure class="third">

<a href="/images/spectromodel_removoutliers-iforest_spectra-meancenter_N.png"><img src="/images/spectromodel_removoutliers-iforest_spectra-meancenter_N.png" alt="image"></a>

<a href="/images/spectromodel_removoutliers-ee_spectra-meancenter_N.png"><img src="/images/spectromodel_removoutliers-ee_spectra-meancenter_N.png" alt="image"></a>

<a href="/images/spectromodel_removoutliers-1csvm_spectra-meancenter_N.png"><img src="/images/spectromodel_removoutliers-1csvm_spectra-meancenter_N.png" alt="image"></a>

<a href="/images/spectromodel_removoutliers-iforest_pca-varthres_N.png"><img src="/images/spectromodel_removoutliers-iforest_pca-varthres_N.png" alt="image"></a>

<a href="/images/spectromodel_removoutliers-ee_pca-varthres_N.png"><img src="/images/spectromodel_removoutliers-ee_pca-varthres_N.png" alt="image"></a>

<a href="/images/spectromodel_removoutliers-1csvm_pca-varthres_N.png"><img src="/images/spectromodel_removoutliers-1csvm_pca-varthres_N.png" alt="image"></a>

<figcaption>Figure 1. Variance thresholding for selecting covariates; From top to bottom the rows show original spectral signals (top), derivatives (middle) and PCA decompositions (bottom); the left columns show variance selection without applying a scaler and the right columns after applying the MinMaxScaler. Note how the selection of information carrying bands in the original reflectance spectra is almost completely reversed when applying the scaler whereas the selection is more stable for derivatives and completely stable for decomposed bands.
</figcaption>
</figure>
