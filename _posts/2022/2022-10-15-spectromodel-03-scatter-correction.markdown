---
layout: post
title: "Chemometric modelling: 3 scatter correction"
categories: spectromodel
excerpt: "Spectral signal scatter correction."
tags:
  - OSSL
  - machine learning
  - preprocessing
  - scikit learn
  - sklearn
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-10-15 11:27'
modified: '2024-04-20'
comments: true
share: true
---

### Introduction

Scattering effects occur because of variations in specular (mirror like) reflectances, variations in the sample matrix material particle sizes and the path length of the emitted and reflected light from its source via the sample to the sensor. These effects can be additive or multiplicative and distorts the individual spectra even if derived from the same instrument.

Scatter correction is a widely applied technology for reducing spectral scatter effects while retaining the information related to mainly chemical composition properties. For a deeper discussion see the online article [Two scatter correction techniques for NIR spectroscopy in Python](https://nirpyresearch.com/two-scatter-correction-techniques-nir-spectroscopy-python/), that I also used for scripting the SNV and MSC methods in the process flow.

The process flow includes 5 different methods for scatter correction:

- [norm-L1](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.normalize.html),
- [norm-L2](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.normalize.html),
- [norm-max](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.normalize.html),
- [Standard Normal Variate (SNV)](https://nirpyresearch.com/two-scatter-correction-techniques-nir-spectroscopy-python/), and
- [Multiplicative Scatter Correction (MSC)](https://nirpyresearch.com/two-scatter-correction-techniques-nir-spectroscopy-python/).

The 3 normalisation functions scales each sample (spectrum) to a range between 0 and 1. _norm-L1_ is the sum of absolute values, or Manhattan distance. _norm-L2_, also called the Euclidean (distance) norm, is the square root of the sum of squares of each value. In general _norm-L2_ is more accurate and _norm-L1_ faster to compute. _norm-max_ forces the maximum value of each spectra to have a value of 1. It is less useful for correcting spectral data.

SNV normalisation is identical to z-score standardisation, but applied for each sample (spectrum or row). In other words, in SNV each spectrum is first subtracted by its own mean and then divided by its own standard deviation. MSC is similar but uses an ensemble mean derived from a larger set of spectra.

In some circumstances it can be advantageous to execute two scatter corrections in sequence, with the second using the output from the first as input. The second should rather be either SNV or MSC, whereas the first can be any of the five (including SNV or MSC).

#### norm-L1

The example below shows how to define a norm-L1 (Manhattan) normalisation in the process flow - the result is illustrated in figure 1.

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "l1"
      ]
    }
  }
```

#### norm-L2

The example below shows how to define a norm-L2 (Euclidean) normalisation in the process flow - the result is illustrated in figure 1.

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "l2"
      ]
    }
  }
```

#### norm-max

The example below shows how to define a norm-max normalisation in the process flow - the result is illustrated in figure 1.

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "max"
      ]
    }
  }
```

#### SNV

The example below shows how to define an SNV normalisation in the process flow - the result is illustrated in figure 1.

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "snv"
      ]
    }
  }
```

#### MSC

The example below shows how to define an MSC normalisation in the process flow - the result is illustrated in figure 1.

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "msc"
      ]
    }
  }
```

#### Illustration

Figure 1 illustrates 5 different functions for spectral scatter correction, all available as part of the process flow.

<figure class="half">

<a href="/images/spectromodel_scatter-correction_l1.png"><img src="/images/spectromodel_scatter-correction_l1.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_l2.png"><img src="/images/spectromodel_scatter-correction_l2.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_max.png"><img src="/images/spectromodel_scatter-correction_max.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_snv.png"><img src="/images/spectromodel_scatter-correction_snv.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_msc.png"><img src="/images/spectromodel_scatter-correction_msc.png" alt="image"></a>

<figcaption>Figure 1. Scatter correction methods included in the process flow; top row: norm-l1 and norm-l2, middle row: norm-max and SNV, and bottom row: MSC.</figcaption>
</figure>

#### Double normalisation

As noted above consecutive scaling can improve the scatter correction. In the process flow this is achieved by listing 2 scaling functions in the json command structure:

```
  "spectraInfoEnhancement": {
    "apply": true,
    "scatterCorrection": {
      "apply": true,
      "scaler": [
        "msc",
        "snv"
      ]
    }
  }
```

Figure 2 illustrates 4 options for consecutive scaling:

- SNV+SNV
- SNV+MSC
- MSC+SNV
- MSC+MSC


<figure>

<figure class="half">

<a href="/images/spectromodel_scatter-correction_snv+snv.png"><img src="/images/spectromodel_scatter-correction_snv+snv.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_snv+msc.png"><img src="/images/spectromodel_scatter-correction_snv+msc.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_msc+snv.png"><img src="/images/spectromodel_scatter-correction_msc+snv.png" alt="image"></a>

<a href="/images/spectromodel_scatter-correction_msc+msc.png"><img src="/images/spectromodel_scatter-correction_msc+msc.png" alt="image"></a>

<figcaption>Figure 2. Examples of consecutive scaling function for acatter correction flow; top row: SNV+SNV and SNV+MSC, bottom row: MSC+SNV and MSC+MSC. As the input is high quality laboratory spectra, the consecutive scaling only generates minimal scaling compared to single scaling.</figcaption>
</figure>
