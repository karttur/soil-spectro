---
layout: post
title: "Chemometric modelling: 7 Variance thresholding"
categories: spectromodel
excerpt: "Spectral signal variance threshold selector."
tags:
  - OSSL
  - machine learning
  - preprocessing
  - scikit learn
  - sklearn
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-11-05 11:27'
modified: '2024-04-21'
comments: true
share: true
---

### Introduction

A flat (constant signal) spectrum carries a minimum of information while spectrum with distinct troughs and peaks carries abundant information. Ignoring the chemometric target, the variance of a spectral signal can be used as a quick method for reducing the number of spectra by discarding those that carries the least information. This is exactly what is done by the [variance threshold selector](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html) method implemented in the process flow. Because the method requires neither a target feature nor a regressor, it is more general (but also more crude) compared to other covariate selection methods. It is thus placed in its own sub-category, _generalFeatureSelection_. The other covariate (or feature) selection methods that require either target or both target and regressor for selecting covariates, can not be applied in combination. The _generalFeatureSelection_ method of variance threshold can, however always be applied as an initial feature selection also when applying a more specific selector (under _specificFeatureSelection_ in the json commands).

Compared to the original [Scikit leaern (sklearn) variance threshold selector](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.VarianceThreshold.html), the threshold in the process flow can be parameterised in three different ways:

- by giving a fraction that directly relates to the variation (where variation depends on the original spectra itself as well as the preprocessing) (e.g. 0.02),
- by giving an integer for discarding a fixed number of bands (e.g. 5)
- by giving an integer followed by a percent sign [%] for discarding a percentage of the input bands (e.g. 50%).

The most common approach when applying variance thresholds for reducing the number covariates is to standardise the covariates, e.g. by autoscaling or a minmaxscaler. In the process flow the covariates can be autoscaled in the [standardisation step](../spectromodel-04-standardisation/). In the variance threshold process you can either use _None_ scaler or apply a _MinMaxScaler_. But as discussed in the [standardisation step](../spectromodel-04-standardisation/), this kind of rescaling tends to increase the noise and thus also the risk of selecting the most noisy bands rather than those carrying relevant information (see illustration in Figure 1).

To support setting a threshold you can set the argument _onlyShowVarianceList_ to _true_, that will stop the process flow with a list of the variance of all covariates:

```
"generalFeatureSelection": {
    "apply": true,
    "varianceThreshold": {
      "apply": true,
      "onlyShowVarianceList": true,
      "scaler": "MinMaxScaler",
      "threshold": "50%"
    }
  }
```
The response in this example are covariates denoted with a _v_ followed by the wavelength in nanometer - which is how the process flow labels derivatives:

```
band (variance)
d870 (0.011)
d430 (0.012)
d710 (0.015)
d670 (0.018)
d750 (0.019)
d790 (0.020)
d630 (0.022)
d830 (0.023)
d590 (0.027)
d470 (0.029)
d550 (0.034)
d510 (0.037)
```

The response is sorted after the variance in each covariate. Inspect the variance of the covariates, set the threshold (50% in the example below) and change _onlyShowVarianceList_ to _false_ to run the thresholding of covariates:

```
"generalFeatureSelection": {
    "apply": true,
    "varianceThreshold": {
      "apply": true,
      "onlyShowVarianceList": false,
      "scaler": "MinMaxScaler",
      "threshold": "50%"
    }
  }
```

To skip the _MinMaxScaler_ set _scaler_ to _None_:

```
"generalFeatureSelection": {
    "apply": true,
    "varianceThreshold": {
      "apply": true,
      "onlyShowVarianceList": false,
      "scaler": "None",
      "threshold": "50%"
    }
  }
```

The result of the two argument settings above are illustrated as the middle row in Figure 1, that also illustrates variance selection from the original spectral signals (top row) and from a PCA decomposition (bottom row). Each selection is done both without (left columns) and with (right columns) the _MinMaxScaler_.

<figure class="half">

<a href="/images/spectromodel_varthres-noscaling_reflectance.png"><img src="/images/spectromodel_varthres-noscaling_reflectance.png" alt="image"></a>

<a href="/images/spectromodel_varthres-minmaxscaling_reflectance.png"><img src="/images/spectromodel_varthres-minmaxscaling_reflectance.png" alt="image"></a>

<a href="/images/spectromodel_varthres-noscaling_derivatives.png"><img src="/images/spectromodel_varthres-noscaling_derivatives.png" alt="image"></a>

<a href="/images/spectromodel_varthres-minmaxscaler_derivatives.png"><img src="/images/spectromodel_varthres-minmaxscaler_derivatives.png" alt="image"></a>

<a href="/images/spectromodel_varthres-noscaling_pca.png"><img src="/images/spectromodel_varthres-noscaling_pca.png" alt="image"></a>

<a href="/images/spectromodel_varthres-minmaxscaling_pca.png"><img src="/images/spectromodel_varthres-minmaxscaling_pca.png" alt="image"></a>

<figcaption>Figure 1. Variance thresholding for selecting covariates; From top to bottom the rows show original spectral signals (top), derivatives (middle) and PCA decompositions (bottom); the left columns show variance selection without applying a scaler and the right columns after applying the MinMaxScaler. Note how the selection of information carrying bands in the original reflectance spectra is almost completely reversed when applying the scaler whereas the selection is more stable for derivatives and completely stable for decomposed bands.
</figcaption>
</figure>
