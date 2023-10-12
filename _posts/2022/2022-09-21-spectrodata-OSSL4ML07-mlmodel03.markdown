---
layout: post
title: "Model OSSL data: 3 hyper-parameter tuning"
categories: libspectrodata
excerpt: "Hyper-parameter tuning for tweaking Machine Learning models."
tags:
  - OSSL
  - machine learning
  - modeling
  - scikit learn
  - sklearn
  - hyper-parameter tuning
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-21 11:27'
modified: '2023-08-05 11:27'
comments: true
share: true
---

This post is part of a series on organising and analysing data from the Open Soil Spectral Library (OSSL). It is also the third post in a sub-series on how to apply Machine Learning (ML) for predicting soil properties from spectral data. To run the scripts used in this post you need to setup a Python environment, and clone or download the python scripts from a [GitHub repository (repo)](https://github.com/karttur/OSSL-pydev/), as explained in the post [Clone the OSSL python package](../../libspectrosupport/spectrosupport-OSSL-pydev-clone).

### Introduction

A hyper-parameter controls the learning process and is not itself trained. Some hyper-parameters does not affect the training as such, only the speed or efficiency of the learning. Among the latter are for instance the number of kernels that are employed when running a regressor. In this post you will have the option of tuning some hyper-parameters that affect the learning. With the aim of further improving the performance of a regressor.

### Prerequisites

To follow the hands-on instructions, this post requires that you completed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the packages [<span class='package'>matplotlib</span>](https://matplotlib.org) and [<span class='package'>scikit learn (sklearn)</span>](https://scikit-learn.org/stable/) installed.

### Regressor hyper-parameters

Each regressor has a different set of hyper-parameters that can be set. The full list of hyper-parameters of each regressor is in the online scikit-learn documentation:

- [Oridnary Least Square (OLS)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html),
- [Theil-Sen Regressor (TheilSen)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.TheilSenRegressor.html),
- [Huber Regressor (Huber)](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.HuberRegressor.html).
- [k-nearest neighbors regressor (KnnRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsRegressor.html),
- [decision tree regressor (DecTreeRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html),
- [support vector regressor (SVR)](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVR.html),
- [random forest regressor (RandForRegr)](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html), and
- [multi-layer perceptron regressor (MLP)](https://scikit-learn.org/stable/modules/generated/sklearn.neural_network.MLPRegressor.html).

#### Hyper-parameter tuning

Scikit-learn provides two generic approaches for tuning the hyper-parameters of any regressor:

- randomized search (RandomizedSearchCV), and
- exhaustive tuning (GridSearchCV).

Both approaches are implemented in the <span class='module'>OSSL_mlmodel.py</span> module.

##### Randomized search hyper-parameter tuning

The [randomized search approach](https://scikit-learn.org/stable/modules/grid_search.html#randomized-parameter-optimization) applies a search space for each hyper-parameter as defined by the user. While it has a random element, it has two distinct advantages over an exhaustive search:

- it is more parsimonious if large search spaces are considered, and
- parameters that do not affect performance does not decrease efficiency.

To apply a randomized hyper parameter tuning as part of the process-flow, edit the json command file:

```
"hyperParameterTuning": {
    "apply": true,
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

The setting above starts the randomized hyper-parameter tuning as part of the process-flow in <span class='module'>OSSL_mlmodel.py</span>. The parameter _fraction_ defines the cross-validation fraction, _nIterSearch_ is the total number of randomized search over the total hyper-parameter space defined (set in a separate file as explained below), the number in _n_best_report_ is the number of the highest ranking tunings that are reported and saved with the results. The tuning is repeated independently for each combination of regressor and target feature.

The parameterizatin of the randomized tuning must be set in a separate json file, linked under the "input" tag of the json command file:

```
"input": {
    "jsonSpectraDataFilePath": "/path/to/arranged-data/visnir/project/data-visnir_source_b0-b1_bw.json",
    "jsonSpectraParamsFilePath": "/path/to/arranged-data/visnir/project/params-visnir_source_b0-b1_bw.json",
    "hyperParameterRandomTuning": "/path/to/hyper-param-random-tuning.json",
    "hyperParameterExhaustiveTuning": "/path/to/hyper-param-exhaustive-tuning.json"
  }
```

The json command file defining the randomized tuning (_hyper-param-random-tuning.json_) can be located anywhere on your machine and can be used across different projects. The default randomised search command file looks like this:

```
{
  "RandomTuning": {
    "OLS": {
      "apply": false,
      "tuningParams": {}
    },
    "TheilSen": {
      "apply": false,
      "tuningParams": {}
    },
    "Huber": {
      "apply": false,
      "tuningParams": {}
    },
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
        "hidden_layer_sizes": [
          [
            8,
            12
          ],
          [
            40,
            40
          ],
          [
            100,
            100
          ]
        ],
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

The settings above are fairly restricted. You can use them as a starting point for finding out if you are able to improve model performance, especially with the more advanced regressors. Then you can develop the hyper-parameters to tune the search spaces iteratively to achieve better and better results.

##### Exhaustive hyper-parameter tuning

For the [Exhaustive grid search](https://scikit-learn.org/stable/modules/grid_search.html#exhaustive-grid-search) a list (grid) of search alternatives for each hyper-parameter to tune must be given and is then looped over. The search space thus grows exponentially fast when new hyper-parameters are added or the search space for a single parameter expanded. It can rather quickly become untenably large and take hours, even days, to loop over. But it is exhaustive and leaves no stone unturned.

To apply an exhaustive hyper-parameter tuning as part of the process-flow, edit the json command file:

```
"hyperParameterTuning": {
    "apply": true,
    "fraction": 0.5,
    "nIterSearch": 6,
    "n_best_report": 3,
    "randomTuning": {
      "apply": false
    },
    "exhaustiveTuning": {
      "apply": true
    }
  }
```

The setting above starts the exhaustive hyper-parameter tuning as part of the process-flow in <span class='module'>OSSL_mlmodel.py</span>. The parameter _fraction_ defines the cross-validation fraction, the number in _n_best_report_ is the number of the highest ranking tunings that are reported and saved with the results. The tuning is repeated independently for each combination of regressor and target feature.

The parameterizatin of the exhaustive tuning must be set in a separate json file, linked under the "input" tag of the json command file:

```
"input": {
    "jsonSpectraDataFilePath": "/path/to/arranged-data/visnir/project/data-visnir_source_b0-b1_bw.json",
    "jsonSpectraParamsFilePath": "/path/to/arranged-data/visnir/project/params-visnir_source_b0-b1_bw.json",
    "hyperParameterRandomTuning": "/path/to/hyper-param-random-tuning.json",
    "hyperParameterExhaustiveTuning": "/path/to/hyper-param-exhaustive-tuning.json"
  }
```

The json command file defining the exhaustive (grid-search) tuning (_hyper-param-exhaustive-tuning.json_) can be located anywhere on your machine and can be used across different projects. The default exhaustive tuning search command file looks like this:

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

For the linear regressors (OLS, TheilSen, Huber) the exhaustive tuning only lists a few hyper-parameters and looping all combinations will be fast. For the more advanced regressors, however, the search space when considering all possible combinations is large. Consequently the tuning will take a long time, especially if you have a large number of covariates. One way to speed up the exhaustive tuning is thus to reduce the number of covariates by applying any of the suggested methods for feature selection (see the post [Model OSSL data: 1 process-flow](../spectrodata-OSSL4ML05-mlmodel01)).
