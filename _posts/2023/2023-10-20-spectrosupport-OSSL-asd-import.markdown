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

With the <span class='package'>Cubist</span> package added to your virtual environment, you can import <span class='package'>Cubist</span> to the <span class='module'>OSSL_mlmodel.py</span> module.

Open the <span class='file'>OSSL_mlmodel.py</span> file in a text editor (or in <span class='app'>Eclipse</span>). At row 113 you should see the following line (commented with #):

```
#from cubist import Cubist
```

Remove the comment ("#").

Further down, at row 1131, find the following text:

```
       '''    
       if hasattr(self.regressionModels, 'Cubist') and self.regressionModels.Cubist.apply:
           self.regressorModels.append(('Cubist', Cubist( **self.jsonparamsD['regressionModels']['Cubist']['hyperParams'])))
           self.modelSelectD['Cubist'] = []
       '''
```

This is the section that defines the Cubist regressor as part of the suite of regressors. This section is also commented but using the triple single quote signs (\`\`\`). Remove the triple single quote signs that define the comment.

If you do not find the rows importing and defining the Cubist model, just search for "cubist" in the module file.

### Add Cubist to the model json command file

To invoke Cubist in a project run you also have to add the Cubist model, and its marker layout, to your json model command file.

```
    "MLP": {
      "apply": false,
      "hyperParams": {
        "hidden_layer_sizes": [
          100,
          100
        ],
        "max_iter": 200,
        "tol": 0.001,
        "epsilon": 1e-8
      }
    },
    "Cubist": {
      "apply": true,
      "hyperParams": {
      }
    }
  },
  "regressionModelSymbols": {
    "OLS": {
        "marker": ".",
        "size": 100
      },
      ...
      ...
      "MLP": {
        "marker": "D",
        "size": 50
      },
      "Cubist": {
        "marker": "D",
        "size": 50
      }
    },
    "modelTests": {
```

### Fixing a bug in the Cubist package code

There is "bug" in the <span class='package'>Cubist</span> - it uses an outdates command and will get stranded if you include it in the process-flow. Try running the <span class='module'>OSSL_mlmodel.py</span>. It probably reports an error message. To get the line where the error occurred, click on the reported error site in the text output from running the script. The error probably happened in the file called <span class='file'>_quinlan_attributes.py</span>, find that file in the output text, click it, and you should come to the exact line with the bug; row 68 in the class='package'>Cubist</span> package support file <span class='file'>_quinlan_attributes.py</span>:

```
return {col_name: _get_data_format(col_data) for col_name, col_data in df.iteritems()}
```

The bug is that the command _df.iteritems()_ is outdated and must be replaced with _df.items()_:

```
return {col_name: _get_data_format(col_data) for col_name, col_data in df.items()}
```

Fix it, save the updated support module and try to run <span class='module')>OSSL_mlmodel.py</span> again.
