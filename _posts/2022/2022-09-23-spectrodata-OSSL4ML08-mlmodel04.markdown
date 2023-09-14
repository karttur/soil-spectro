---
layout: post
title: "Model OSSL data: 4 manual settings"
categories: libspectrodata
excerpt: "A-priori definition of covariates and regressor parameters for robust models."
tags:
  - OSSL
  - machine learning
  - modeling
  - scikit learn
  - sklearn
  - parameterisation
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-23 11:27'
modified: '2023-09-13 11:27'
comments: true
share: true
---

This is the last post in a series on organising and analysing data from the Open Soil Spectral Library (OSSL). It is also the last post in a sub-series on how to apply Machine Learning (ML) for predicting soil properties from spectral data. To run the scripts used in this post you need to setup a Python environment, and clone or download the python scripts from a [GitHub repository (repo)](https://github.com/karttur/OSSL-pydev/), as explained in the post [Clone the OSSL python package](../../libspectrosupport/spectrosupport-OSSL-clone).

### Introduction

In the previous posts you have developed models for predicting different soil properties from spectral data. If you applied the different options for selecting a subset of features and/or applying hyper-parameter tuning, you have probably discovered that a subset of the original data is sufficient for predicting a particular soil property. And that each predicted property relies on a different set of covariates (spectra and spectra derivatives).

this last post describes how you can use the knowledge you gained on "parsimonious" model parameterisation for a-priori definition of the covariates to use, and the model definition.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed.

### Manually defining the covariates

In the json command file used for parameterising the process-flow of the <span class='module'>OSSL_mlmodel</span> module you can define a fixed set of spectral wavelengths and wavelength derivates as the covariates to use. If you do that, all the feature selection steps are removed from the process-flow:

```
"manualFeatureSelection": {
    "comment": "Manual feature selection overrides other selection alternatives",
    "apply": true,
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
```
