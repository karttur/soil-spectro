---
layout: post
title: OSSL example framework
categories: libspectrosupport
excerpt: "OSSL example framework from GitHub"
tags:
  - Python
  - GitHub
  - OSSL
  - clone
  - download
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-09-06 11:27'
modified: '2023-10-11 11:27'
comments: true
share: true
---
### Introduction

The <span class='package'>OSSL-pydev</span> python package relies on a set of strictly organized directories and files. The user can set the names of both the files and the directories, but the internal structure must remain the same. This post explains the structure, and how it is hierarchically organised.

To follow this instruction the best is to clone or download the prepared framework example on [Swedish OSSL data](https://github.com/karttur/OSSL-data). Note that the actual OSSL data is **not** inluded, that you have to download separately as explained in the post [Download OSSL data](../../libspectrodata/spectrodata-OSSL4ML01-download/).

### Example framework

The prepared OSSL example framework is available in the GitHub repository [https://github.com/karttur/OSSL-data](https://github.com/karttur/OSSL-data). The example apllies to LUCAS Near Infrared (NIR) dataset over Sweden. Download, or clone the repo (for details on accessing a GitHub repo see the [previous post](../spectrosupport-OSSL-pydev-clone#download-ossl-pydev-from-github)).

Once accessed the tree of directories and files (with GitHub related files removed), should look like this:

```
|____Sweden
|____import_ossl.json
|____plot_ossl.json
|____model_ossl.json
|____targetfeaturesymbols.json
|____hyperparamtuning
| |____hyper-param-exhaustive-tuning-ossl.json
| |____hyper-param-random-tuning-ossl.json
| |____LUCAS
| | |____arranged-data
| | | |____extract_rawdata.json
| | | |____plot_spectra.json
| | | |____ml-model_spectra.json
| | | |____json-import
| | | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json
| | | |____json-plots
| | | | |____plot-OSSL-LUCAS-SE_nir_460-1050_10.json
| | | |____json-ml-modeling
| | | | |____model-OSSL-LUCAS-SE_nir_460-1050_10.json
```

The same structure, but this time with comments (#):

```
|____Sweden # Regional identity - user defined
|____import_ossl.json # Root arguemnts defining the datasets to import and directory and file structure for input and output
|____plot_ossl.json # Root arguments defining the datasets to plot and directory and file structure for input and output
|____model_ossl.json # Root arguments defining the datasets to model and directory and file structure for input and output
|____targetfeaturesymbols.json # The symbols to apply for target features when plotting and modelling
|____hyperparamtuning # Optional directory with settings for tuning hyper parameters
| |____hyper-param-exhaustive-tuning-ossl.json # Exhaustive tuning settings
| |____hyper-param-random-tuning-ossl.json # Random tuning settings
| |____LUCAS # Spectral source or range identiry - user defined
| | |____arranged-data # Directory for all output (results) - default naming that can be changed
| | | |____extract_rawdata.json # Project file for importing (extracting) OSSL data - default naming that can be changed
| | | |____plot_spectra.json # Project file for plotting imported OSSL data - default naming that can be changed
| | | |____ml-model_spectra.json  # Project file for modelling imported OSSL data - default naming that can be changed
| | | |____json-import # Directory with json parameter files for importing OSSL data - default naming that can be changed
| | | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for importing OSSL data
| | | |____json-plots # Directory with json parameter files for plotting OSSL data - default naming that can be changed
| | | | |____plot-OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for plotting OSSL data
| | | |____json-ml-modeling # Directory with json parameter files for modelling OSSL data - default naming that can be changed
| | | | |____model-OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for modelling OSSL data
```

#### Root argument files - not part of the hierarchy

The files directly under the top folder (<span class='file'>Sweden</span>) are **not** part of the strict hierarchical strucure. These root argument files rather define the names of the directories and files in the hierarchical structure, but are not themselves part of the hierarchy. This is also the case for the two hyper-parameter parameter files. Thus you can safely move the following files and folders to any location you prefer:

- import_ossl.json
- plot_ossl.json
- model_ossl.json
- targetfeaturesymbols.json
- hyperparamtuning/hyper-param-exhaustive-tuning-ossl.json
- hyperparamtuning/hyper-param-random-tuning-ossl.json

#### Core hierarchical structure

The core structure when starting a project using the <span class='package'>OSSL-pydev</span> package thus boils down the following:

```
|____Sweden # Regional identity - user defined
| |____LUCAS # Spectral source or range identiry - user defined
| | |____arranged-data # Directory for all output (results) - default naming that can be changed
| | | |____extract_rawdata.json # Project file for importing (extracting) OSSL data - default naming that can be changed
| | | |____plot_spectra.json # Project file for plotting imported OSSL data - default naming that can be changed
| | | |____ml-model_spectra.json  # Project file for modelling imported OSSL data - default naming that can be changed
| | | |____json-import # Directory with json parameter files for importing OSSL data - default naming that can be changed
| | | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for importing OSSL data
| | | |____json-plots # Directory with json parameter files for plotting OSSL data - default naming that can be changed
| | | | |____plot-OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for plotting OSSL data
| | | |____json-ml-modeling # Directory with json parameter files for modelling OSSL data - default naming that can be changed
| | | | |____model-OSSL-LUCAS-SE_nir_460-1050_10.json # json parameter file for modelling OSSL data
```

#### Adding the OSSL data to the hierarchical structure

To actually run the project you also have to add the OSSL data. As explained above, you have to [download the OSSL data](../../libspectrodata/spectrodata-OSSL4ML01-download/) separately. All downloads from OSSL are named <span class='file'>data.zip</span>. Move the zip file to the root folder of your project (<span class='file'>LUCAS</span> in the example), and unzip it. The following content should be added to your project root folder:

```
|____LUCAS
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____neon.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
```

#### Root arguments

The three main functions of the <span class='package'>OSSL-pydev</span> package, <span class='module'>OSSL_import</span>, <span class='module'>OSSL_plot</span> and <span class='module'>OSSL_mlmodel</span> each requires a single argument - the name of the file defining the root arguments. These files in essence just point towards the downloaded OSSL dataset and identifies the directory and file structure of the dataset and the import, plot and model parameter files. Figure 1 below graphically illustrates the three argument files. As stated above, they can be placed anywhere on your machine. You can thus assemble all your OSSL root arguments in a single place while the actual datasets can be spread out to other locations.

<figure>
  <a href="../../images/OSSL-data_rootjsonfilescontent.png">
  <img src="../../images/OSSL-data_rootjsonfilescontent.png" alt="image">
  </a>
	<figcaption>Figure 1. Schematic structure of the argument files required for importing, plotting and modelling OSSL data; the files must be json formatted (see text).</figcaption>
</figure>

#### Import OSSL data

The content of the root json argument file for importing (synonyms: extracting or rearranging) OSSL downloaded data has the following content:

```
{
  "rootpath": "/path/to/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-import",
  "projFN": "extract_rawdata.json",
  "createjsonparams": false
}
```

As mentioned above, the json argument file itself can be located anywhere and have any name. Also the paths given as _"key":"value"_ pairs in the json argument file can be set freely, except the _"rootpath"_ that must point to the actual project directory. The paths defined in the argument file, however, must be internally consistent and hierarchically organised as illustrated in figure 2 (remember that all data files downdloaded from [OSSL](https://soilspectroscopy.org) are named <span class='file'>data.zip</span> and contain identically named csv files).

<figure>
  <a href="../../images/import_json_docs.png">
  <img src="../../images/import_json_docs.png" alt="image">
  </a>
	<figcaption>Figure 2. Hierarchical structure of OSSL data directories and files used by the OSSL-import module. Users can change the naming of all directories and files but the internal structure must remain.</figcaption>
</figure>

#### Plot OSSL data

The content of the json root argument file for plotting imported OSSL data has the following content:

```
{
  "rootpath": "/Local/path/to/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-plots",
  "projFN": "plot_spectra.json",
  "targetfeaturesymbols" :"/Local/path/to/targetfeaturesymbols.json",
  "createjsonparams": false
}
```

The root argument file can be located anywhere, while the interal structure of the directory and file structure must be consistent. Again you can change the file and directory names, but not their relative hierarchical organisation, figur 3. The json structured file _symbols.json_, defines symbolisation of the target features in any plot. It is a universal parameter file for any OSSL data (but you need to add any new target feature name and its symbol - for now it is set up for the LUCAS target features). You can locate it anywhere and use the same file for all your OSSL projects.

<figure>
  <a href="../../images/plot_json_docs.png">
  <img src="../../images/plot_json_docs.png" alt="image">
  </a>
	<figcaption>Figure 3. Hierarchical structure of OSSL data directories and files used by the OSSL-plot module. Users can change the naming of all directories and files but the internal structure must remain.</figcaption>
</figure>

#### Model OSSL data

The content of the json root argument file for modelling imported OSSL differs slightly. The _projFN_ argument is changed from a single text file (.txt) to a list of json formatted files (.json):

```
{
  "rootpath": "/Local/path/to/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "projFN": [
    "ml-model_spectra_tar-feat-pretransfrom_comp.json"
  ],
  "jsonfolder": "json-ml-modeling",
  "targetfeaturesymbols" :"/Local/path/to/targetfeaturesymbols.json",
  "createjsonparams": false
}
```

Also the root argument file for modelling can be located anywhere, while the internal structure of the directory and file structure must be consistent. Again you can change the file and directory names, but not their relative hierarchical organisation, figur 4. The two json files defining (optional) hyper-parameter tuning are also universal and can be used with any OSSL data. You can move them to any location and give the full path in the json modelling parameter file (e.g. _model_ID0_xlx0-wly0_wl.json_ in figure 4) when you want to apply hyper-parameter tuning. Details for how to apply hyper-parameter tuning is in the post [hyper-parameter tuning](../../libspectrodata/spectrodata-OSSL4ML07-mlmodel03/).

<figure>
  <a href="../../images/model_json_docs.png">
  <img src="../../images/model_json_docs.png" alt="image">
  </a>
	<figcaption>Figure 4. Hierarchical structure of OSSL data directories and files used by the OSSL-model module. Users can change the naming of all directories and files but the internal structure must remain. NOTE that the file _plot_spectra.txt_ in the figure is replaced with one or more json files.</figcaption>
</figure>

#### Universal parameters for symbols and hyper parameter tuning

Symbolisation of target features (i.e. soil properties) and [hyper-parameter tuning](../../libspectrodata/spectrodata-OSSL4ML07-mlmodel03/) of the Machine Learning regressors are not specific for any particular OSSL dataset. They are preferable regarded as universal for any soil data. The three json files that define symbolisation, random and exhaustive hyper-parameter tuning are thus better kept in a separate directory. In the downloaded example dataset, the three json files are included with the Swedish dataset for convinience.
