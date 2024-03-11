---
layout: post
title: Specdal package
categories: libspectrosupport
excerpt: "Add the Specdal package for reading field spectroscopy data from ASD, SVC, and PSR spectrometers"
tags:
  - ossl-xpsectre
  - Python
  - specdal
  - ASD
  - SVC
  - PSR
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-10-20 11:27'
modified: '2023-10-20 11:27'
comments: true
share: true
---

### Introduction

High grade field spectrometers typically save spectral scans in proprietary formats. The python package [<span class='package'>specdal</span>](https://pypi.org/project/specdal/) can read the formats of ASD, SVC and PSR spectrometers. The package also provides useful functions pre-processing and clustering spectral data.

This post is a manual for how to add <span class='package'>specdal</span> to your virtual python environment, import <span class='package'>specdal</span> to the <span class='package'>OSSL-xspectre</span> package and then import (rearrange) ASD spectral scans.

### Adding Specdal to the virtual environment

Open a terminal <span class='app'>Terminal</span> window. By default the active virtual <span class='app'>Anaconda</span> Python environment is "base", indicated by the prompt as:

```
(base)
```

Do not install Cubist with "base", instead _activate_ the virtual Python environment you created for the package _ossl-xspectre_ (e.g. "ossl_py38"):

<span class='terminal'>% conda activate ossl_py38</span>

The prompt should change and instead of "(base)" should now read "(ossl_py38)"

```
(ossl_py38)
```

Now run the <span class='terminalapp'>pip</span> command:

<span class='terminal'>pip install specdal</span>

You have added the Specdal package to the virtual environment.

### Importing the Specdal package to OSSL_mlmodel.py

TO BE COMPLETED
