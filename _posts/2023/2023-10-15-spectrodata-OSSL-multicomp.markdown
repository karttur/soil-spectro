---
layout: post
title: "Model OSSL data: 5 Multi project comparison"
categories: libspectrodata
excerpt: "Compare results from different parameter settings, feature selections and hyper-parameterisations"
tags:
  - ossl-xpsectre
  - Python
  - project
  - comparision
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-10-15 11:27'
modified: '2023-10-30 11:27'
comments: true
share: true
---

This post is part of a series on organising and analysing data from the Open Soil Spectral Library (OSSL). It is also a post in a sub-series on how to apply Machine Learning (ML) for predicting soil properties from spectral data. To run the scripts used in this post you need to setup a Python environment, and clone or download the python scripts from a [GitHub repository (repo)](https://github.com/karttur/OSSL-pydev/), as explained in the post [Clone the OSSL python package](../../libspectrosupport/spectrosupport-OSSL-pydev-clone).

### Introduction

Previous examples in this blog have illustrated how to change parameter settings that affect e.g. data transformation and feature selection.
Results are then presented for each selected target feature, modelled with the suite of selected regressors. While this allows you to change any setting, it does not support comparing results from different parameters (or hyper-parameters) settings. You can in principe compare the outcome of two different settings by looking at the result, either in numerical or graphical form. But that is a bit cumbersome.

This post is an instructions for how to setup a multi project comparison. This is accomplished by listing two or more json modelling command files in the project file and then adding a json _multiprojectcomparison_ command file. The latter overrides some of the settings of the individually listed modelling command files, e.g. to make sure that the same targets and regressors are set. When run, the results of each modelling command file is assembled in a single result file and for each target feature figures comparing the results of the individual modelling settings are produced.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed. To understand how to edit the json command file it is also recommended that you complete the instructions on [Model OSSL data: 1 process-flow](../spectrodata-OSSL4ML05-mlmodel01). If you want to compare different hyper-parameter settings, the post on [Model OSSL data: 3 hyper-parameter tuning](../spectrodata-OSSL4ML07-mlmodel03) is recommended.

### Json command file editing

To run a multi project comparison you need to edit the project file and:

- list at least 2 _projectFiles_
- add a _multiprojectcomparison_ json file

Here is an example of a project list file that lists two _projectFiles_ and also defines a _multiprojectcomparison_ command file:

```
{
  "projectFiles": [
    "model-OSSL-LUCAS-SE_nir_460-1050_10_remote-outliers-00.json",
    "model-OSSL-LUCAS-SE_nir_460-1050_10_remote-outliers-01.json"
  ],
  "multiprojectcomparison": "/local/path/to/multiprojectcomparison.json"
}
```

The listed _projectFiles_ should differ in one or more aspects that you want to compare. The file _multiprojectcomparison_ will override some of the settings in the listed _projectFiles_, including:

- the listed _targetFeatures_
- whether or not to apply _manualFeatureSelection_,
- whether or not to apply _globalFeatureSelection_,
- whether or not to apply _targetFeatureSelection_,
- whether or not to apply _modelFeatureSelection_,
- whether or not to apply _featureAgglomeration_,
- whether or not to apply _hyperParameterTuning_,
- whether or not to apply _featureImportance_,
- whether or not to apply _modelling_,
- the _regressionModels_ to apply, and
- the _modelTests_ to apply

The _multiprojectcomparison_ will only force the actual parameters for _featureImportance_ and _modelTests_. This assures that in the comparison the evaluations of the differing parameter settings are evaluated in the same way.

#### Example 1: Compare detection of outliers

The first example illustrates how to compare model performances when setting different thresholds for removing outliers. Four different thresholds for outlier removal are compared:

- no threshold
- threshold=0.1
- threshold=0.2
- threshold=0.3

##### Json command files

The 4 different thresholds (parameter called _contamination_, see example below) must be defined in 4 individual json command files. In this example, the json command files are identical except for the definitions of the outlier thresholds and the prefix used for saving the output of each individual trial.

Json command file with no outlier detection:
```
"output": {
    "prefix": "outliers-00",
    "setdate": false
  },
  ...
  "removeOutliers": {
    "detector": "1csvm",
    "contamination": 0.0
  },
```

Json command file with outlier detection threshold set to 0.1:
```
"output": {
    "prefix": "outliers-01",
    "setdate": false
  },
  ...
  "removeOutliers": {
    "detector": "1csvm",
    "contamination": 0.1
  },
```
The editing of the additional json command files follows the same principle.

##### Multi project comparison file

The Multi project comparison commands for this example:

```
{
  "apply": true,
  "prefix": "otlier-comp-test",
  "trialid": {
    "0": "no outliers",
    "1": "outliers 0.1",
    "2": "outliers 0.2",
    "3": "outliers 0.3",
  },
  "targetFeatures": [
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct"
  ],
  "removeOutliers": {
    "apply": true
  },
  "manualFeatureSelection": {
    "apply": false
  },
  "globalFeatureSelection": {
    "apply": false
  },
  "targetFeatureSelection": {
    "apply": false
  },
  "modelFeatureSelection": {
    "apply": false
  },
  "featureAgglomeration": {
    "apply": false
  },
  "hyperParameterTuning": {
    "apply": false,
  },
  "featureImportance": {
    "apply": true,
    "reportMaxFeatures": 4,
    "permutationRepeats": 10,
    "coefficientImportance": {
      "apply": true
    },
    "permutationImportance": {
      "apply": true
    }
  },
  "modelling": {
    "apply": true
  },
  "regressionModels": {
    "OLS": {
      "apply": true
    },
    "TheilSen": {
      "apply": false
    },
    "Huber": {
      "apply": false
    },
    "KnnRegr": {
      "apply": false
    },
    "DecTreeRegr": {
      "apply": false
    },
    "SVR": {
      "apply": false
    },
    "RandForRegr": {
      "apply": true
    },
    "MLP": {
      "apply": false
    }
  },
  "modelTests": {
    "apply": true,
    "trainTest": {
      "apply": false,
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
    "screenShow": false,
    "savePng": true,
    "figSize": {
      "x": 0,
      "y": 0,
      "xadd": 0.25,
      "yadd": 0.25
    },
    "hwspace": {
      "hspace": 0.25,
      "wspace": 0.25
    }
  }
}
```

This example will evaluate the impact of setting different outlier threshold for two target feature:

- n.tot_usda.a623_w.pct, and
- oc_usda.c729_w.pct,

and two regressors:

- OLS, and
- RandForRegr.

The multi comparison command file will force change the settings of i) target features to include, and ii) regressors to apply in the individual project files. These will be changed to values set in the multi comparison command file. Also the settings for presenting results (_featureImportance_ and _modelTest_) will be forced to be coherent. This is done to define consistent rows and columns, and panel content in the resulting graphical plots. The overriding of input (target features and regressors) and output (feature importance and model tests) can not be altered by the user.

In the example also the applications of other processing alternatives (i.e. other than outlier removal) are forced skipped (all set to _false_):
- manual feature selection,
- global feature selection,
- target feature selection,
- model feature selection,
- feature agglomeration, or
- hyper-parameter tuning.

The user can chose to include multiple processing steps, that can either be identical or varying between the trials included in the comparison. It is thus in principle possible to use only four files, each with different settings for each processing step, and use the multi project comparison file to open or close the processing steps to evaluate.

##### Results

Figures 1 to 4 show the results of the comparison of different outlier removal thresholds.

<figure>
  <img src="../../images/otlier-comp-test_n.tot_usda.a623_w.pct_permutationImportance.png" alt="image">
	<figcaption>Figure 1. Comparison of permutation importances for modelling total nitrogen (N) from spectral data (OSSL data over Sweden); columns illustrate different levels of outlier removal, rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>

<figure>
  <img src="../../images/otlier-comp-test_oc_usda.c729_w.pct_permutationImportance.png" alt="image">
	<figcaption>Figure 2. Comparison of permutation importances for modelling Soil Organic Carbon (SOC) from spectral data (OSSL data over Sweden); columns illustrate different levels of outlier removal, rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>

<figure>
  <img src="../../images/otlier-comp-test_n.tot_usda.a623_w.pct_Kfold.png" alt="image">
	<figcaption>Figure 3. Comparison of Kfold (n=10) model tests for predicting total nitrogen (N) from spectral data (OSSL data over Sweden); columns illustrate different levels of outlier removal, rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>

<figure>
  <img src="../../images/otlier-comp-test_oc_usda.c729_w.pct_Kfold.png" alt="image">
	<figcaption>Figure 4. Comparison of Kfold (n=10) model tests for predicting Soil Organic Carbon from spectral data (OSSL data over Sweden); columns illustrate different levels of outlier removal, rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>

#### Example 2: Compare target data transformation

The second example compares the effects of transforming target data. Four different transformations are tested:

- Linear (no transformation),
- logarithmic (log),
- square root (sqrt), and
- reciprocal.

Data transformation is not defined directly in the json command file, but in a separate "targetfeaturetransforms" json file. The "targetfeaturetransforms" json file must be inked to each json command file, under _input_:

```
  "input": {
    ...
    ...
    "targetfeaturetransforms": "/path/to/targetfeaturetransforms.json"
  }
```

In order to compare the effects of different data transformation algorithms you must create four json files defining the different target feature transformations. Then also create four json command files, each pointing at the corresponding transformation definition. In the project file you need to list the four json command files and add a "multiprojectcomparison" project file. The latter should set all the options for applying feature selection to false (assures that all four trials to be compared use the same [i.e. No] feature selection as part of the Machine Learning modelling).

##### Target feature transformation

The target feature transformation files define the transformation algorithms to apply for all defined target features. There are 6 different algorithms that can be applied; if none is selected there will be no transformation (i.e. the target feature data will remain in its original form). The transformations that can be applied include:

- log,
- sqrt,
- reciprocal,
- boxcox,
- yeojohnson, and
- quantile.

For each target feature you can only set one of these to _true_; if all are set to _false_, the target feature data will remain in its original form. You can also apply a standardisation of the target feature data, but that is not covered in this post.

Example of transformation definition for total nitrogen:

```
    "n.tot_usda.a623_w.pct": {
      "log": false,
      "sqrt": true,
      "reciprocal": false,
      "boxcox": false,
      "yeojohnson": false,
      "quantile": false
    }
```
In the example above, the total nitrogen data will be transformed using square root (sqrt).

##### Json command files

The json command files need to each link to its respective target feature transformation definition, and should have a prefix that indicates the transformation. Below is an excerpt of the key parameters to set in each command file:

```
"input": {
  ...
  ...
  "targetfeaturetransforms": "/path/to/targetfeaturetransforms.json"",
},
"output": {
  "prefix": "pretransform-linear",
  "setdate": false
},
```

##### Multi project comparison file

The Multi project comparison commands for this example:

```
{
  "apply": true,
  "prefix": "tar-feat-pretransform",
  "trialid": {
    "0": "linear",
    "1": "log",
    "2": "sqrt",
    "3": "reciprocal",
  },
  "comment": "if set to true targetFeatures and regressionModels override the settings in each json command file for modeling",
  "targetFeatures": [
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct"
  ],
  "removeOutliers": {
    "apply": false
  },
  "manualFeatureSelection": {
    "apply": false
  },
  "globalFeatureSelection": {
    "apply": false
  },
  "targetFeatureSelection": {
    "apply": false
  },
  "modelFeatureSelection": {
    "apply": false
  },
  "featureAgglomeration": {
    "apply": false
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
    "reportMaxFeatures": 4,
    "permutationRepeats": 10,
    "coefficientImportance": {
      "apply": true
    },
    "permutationImportance": {
      "apply": true
    }
  },
  "modelling": {
    "apply": true
  },
  "regressionModels": {
    "OLS": {
      "apply": true
    },
    "TheilSen": {
      "apply": false
    },
    "Huber": {
      "apply": false
    },
    "KnnRegr": {
      "apply": false
    },
    "DecTreeRegr": {
      "apply": false
    },
    "SVR": {
      "apply": false
    },
    "RandForRegr": {
      "apply": false
    },
    "MLP": {
      "apply": false
    }
  },
  "modelTests": {
    "apply": true,
    "trainTest": {
      "apply": false,
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
    "screenShow": false,
    "savePng": true,
    "figSize": {
      "x": 0,
      "y": 0,
      "xadd": 0.25,
      "yadd": 0.25
    },
    "hwspace": {
      "hspace": 0.25,
      "wspace": 0.25
    }
  }
}
```

##### Results

Figures 5 to 8 show the results of the comparison of different target feature transformations.

<figure>
  <img src="../../images/tar-feat-pretransform_n.tot_usda.a623_w.pct_permutationImportance.png" alt="image">
	<figcaption>Figure 5. Comparison of permutation importances for modelling total nitrogen (N) from spectral data (OSSL data over Sweden); columns illustrate the permutation importances after applying different data transformations to the nitrogen input value; from left to right: no transformation (linear), logarithmic, square root and reciprocal transformation; rows show results for the regressors OSL (top) and Random Forest (bottom). </figcaption>
</figure>

<figure>
  <img src="../../images/tar-feat-pretransform_oc_usda.c729_w.pct_permutationImportance.png" alt="image">
	<figcaption>Figure 6. Comparison of permutation importances for modelling Soil Organic Carbon (SOC) from spectral data (OSSL data over Sweden); columns illustrate the permutation importances after applying different data transformations to the SOC input value; from left to right: no transformation (linear), logarithmic, square root and reciprocal transformation; rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>

<figure>
  <img src="../../images/tar-feat-pretransform_n.tot_usda.a623_w.pct_Kfold.png" alt="image">
	<figcaption>Figure 7. Comparison of Kfold (n=10) model tests for predicting total nitrogen (N) from spectral data (OSSL data over Sweden); columns illustrate different data transformations applied to the nitrogen input value; from left to right: no transformation (linear), logarithmic, square root and reciprocal transformation; rows show results for the regressors OSL (top) and Random Forest (bottom).
  </figcaption>
</figure>

<figure>
  <img src="../../images/tar-feat-pretransform_oc_usda.c729_w.pct_Kfold.png" alt="image">
	<figcaption>Figure 8. Comparison of Kfold (n=10) model tests for predicting total Soil Organic Carbon (SOC) from spectral data (OSSL data over Sweden); columns illustrate different data transformations applied to the SOC input value; from left to right: no transformation (linear), logarithmic, square root and reciprocal transformation; rows show results for the regressors OSL (top) and Random Forest (bottom).</figcaption>
</figure>
