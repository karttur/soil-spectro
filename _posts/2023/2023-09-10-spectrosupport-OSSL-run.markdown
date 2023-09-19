---
layout: post
title: Run ossl-xspectre modules
categories: libspectrosupport
excerpt: "How to run the modules in the ossl-xspectre package"
tags:
  - ossl-xpsectre
  - Python
  - run
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-09-10 11:27'
modified: '2023-09-10 11:27'
comments: true
share: true
---

### Introduction

This post assumes that you have [cloned or downloaded the OSSL-pydev repo](../spectrosupport-OSSL-clone) and setup an environment for running python on your machine. The latter for instance by using [Anaconda](../spectrosupport-OSSL-anaconda) for defining the python environment and [Eclipse](../spectrosupport-OSSL-eclipse) as the IDE. You can also run the python package (<span class='package'>ossl-xspectre</span>) modules from the command line.

Before testing the ossl-xspectre modules you can download an OSSL sample framework over Sweden from [https://github.com/karttur/OSSL-data](https://github.com/karttur/OSSL-data). The framework does not contain the original OSSL data over Sweden (that you have to get directly from [OSSL](../../libspectrodata/spectrodata-OSSL4ML01-download)); but it contains an example of a complete command structure for importing, plotting and modelling OSSL data over Sweden.

### Default project file and folder structure

The processing of spectral data using the <span class='package'>ossl-xspectre</span> relies on a strict file and folder naming structure. This structure is explained in this section and reflected in the OSSL sample framework over Sweden.

#### Download and unzip data

The source of the Swedish OSSL data used as example is from the European wide sampling framework LUCAS. The root-folder where the Swedish data resides is thus named _LUCAS_. When downloading data from OSSL the received file is always called _data.zip_. This file must be put directly under the root-folder (_LUCAS_). When exploding _data.zip_ the result is a set of five csv files under a subfolder _data_ in the same folder as the file _data.zip_ itself. The initial file and folder structure of a project thus looks like this:

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

#### Import data

Importing (or arranging) data requires that you create a subfolder (default name _arranged-data_) under the root-folder (_LUCAS_ in this example). Directly under folder _arranged-data_ you need to create a project text file that contains links to json import command files that, in turn, contains the parameter defining the import. The default name of the import project file is _extract_rawdata.txt_. Under the folder _arranged-data_ you must also have the subfolder that contains all the json import command files. The default name of this subfolder is _json-import_. The actual json import command files (to be saved under _json-import_) should have names that reflect the arrangement of the import command and do not have any default names. The structure of the json import files are explained in the post [Import OSSL data](../../libspectrodata/spectrodata-OSSL4ML02-arrange):

```
|____LUCAS
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____neon.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
| |____arranged-data
| | |____extract_rawdata.txt
| | |____json-import
| | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json
```

The folder and file organisation above is ready for starting <span class='module'>ossl_import</span>. To run the module, you need one additional json file (default name: _import_ossl.json_) specifying the _rootpath_ and the default folder and file names:

```
{
  "rootpath": "/Local/path/to/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-import",
  "projFN": "extract_rawdata.txt",
  "createjsonparams": false
}
```

This file can reside anywhere on your machine and is not related to the folder and file structure above. The trick is that for each new dataset you work with you create a new json specification. Then all your efforts will be saved in a strictly organised manner and you can always go back and follow the processing. It is a bit complicated to set up the first time, but easy to replicate and nothing is lost. First a steep climb then a walk on the mesa. With a great view over the soil landscape!

##### Running the Sweden example

The example data for Sweden includes the structure outlined above, you only need to download the OSSL data as described in the post [Download OSSL data](../../libspectrodata/spectrodata-OSSL4ML01-download). Move the received zip file to the folder _LUCAS_ and unzip it. Then you need to edit the following local paths to reflect your machine and setup:

- _rootpath_ in the json specification file (_import_ossl.json_)
- all the paths in the project file (_extract_rawdata.txt_)
- _rootFP_ in each of the json command files (under the folder _json-import_)

With all the paths edited, run the import from the command line by typing:

<span class='terminal'>ossl_import /path/to/json/specification/file.json</span>

```
/path/to/json/specification/file.json
Processing /Users/thomasgumbricht/docs-local/OSSLtest/Sweden/LUCAS/arranged-data/extract_rawdata.txt
    jsonObj: /Users/thomasgumbricht/docs-local/OSSLtest/Sweden/LUCAS/arranged-data/json-import/import_ossl-spectra_sweden-LUCAS_nir_640-1050_10.json
        VISNIR extraction parameters saved as: /Users/thomasgumbricht/docs-local/OSSLtest/Sweden/LUCAS/arranged-data/visnir/OSSL-LUCAS-SE_640-1050_10/params-visnir_OSSL-LUCAS-SE_640-1050_10.json
        VISNIR extracted data saved as: /Users/thomasgumbricht/docs-local/OSSLtest/Sweden/LUCAS/arranged-data/visnir/OSSL-LUCAS-SE_640-1050_10/data-visnir_OSSL-LUCAS-SE_640-1050_10.json
```

If you are using <span class='app'>Eclipse</span>, the easiest alternative is to edit the last, main, section of the module <span class='module'>ossl_import</span>. The module is expecting a command line call, and to change that you need to comment out the part that waits for the call and instead enter the "/path/to/json/specification/file.json" directly in the module:

```
if __name__ == "__main__":
    '''
    '''

    '''
    if len(sys.argv) != 2:

        sys.exit('Give the link to the json file to run the process as the only argument')

    #Get the json file
    jsonFPN = sys.argv[1]

    if not os.path.exists(jsonFPN):

        exitstr = 'json file not found: %s' %(jsonFPN)
    '''       
    jsonFPN = "/path/to/json/specification/file.json"

    iniParams = ReadAnyJson(jsonFPN)

    SetupProcesses(iniParams)
```

Run the module from the top menu of <span class='app'>Eclipse</span>;

<span class='menu'>Run -> Run</span>

OSSL comes with three main categories of data 1) visible to near infrared (VISNIR), 2) mid infrared (MIR) and 3) data from the handheld field spectrometer NeoSpectra (NEON). OSSL data imported using the <span class='package'>ossl-xspectre</span> package module <span class='module'>OSSL_import</span> are divided into subfolder called _visnir_, _mir_ and _neon_ along this categorisation. The LUCAS data used in the Swedish example is restricted to VISNIR and running <span class='module'>OSSL_import</span> will generate a _visnir_ subfolder under the _arranged-data_ folder. The output of each import command will be saved in a separate subfolder under _visnir_, with names reflecting the import commands. Each import command generates a parameter file and a data file.

Running the json import command files listed above leads to the following file and folder structure:

```
|____LUCAS
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____neon.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
| |____arranged-data
| | |____extract_rawdata.txt
| | |____json-import
| | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json
| | |____visnir
| | | |____OSSL-LUCAS-SE_460-1050_10
| | | | |____params-visnir_OSSL-LUCAS-SE_460-1050_10.json
| | | | |____data-visnir_OSSL-LUCAS-SE_460-1050_10.json
```

For details on the parameters you can set for importing data, see the post on [Import OSSL data](../../libspectrodata/spectrodata-OSSL4ML02-arrange).

#### Plot data

The project and json command file and folder structure for plotting spectral and laboratory (wet-chemistry) data follow the same principles as for import. A project file (default name _plot_spectra.txt_) directly under the folder _arranged-data_ and a subfolder (default name:_json-plots_), also directly under _arranged-data_ that contains the json plot command files. If you used the Sweden example, the required folders and files are there. You just have to edit the paths as you did for the importing. Then run the module <span class='module'>OSSL_plot</span> pointing towards the json specification file for plotting (default name: _plot_ossl.json_). Again you can either run via the command line or using <span class='app'>Eclipse</span>.

Details about the options when defining the json command file for plotting is in the post on [Plot OSSL data](../../spectrodata-OSSL4ML03-plot). If requested to be saved, the plots are saved as png files in a subfolder called _plot_ (not all plot output files shown in diagram below).

```
|____LUCAS
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____neon.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
| |____arranged-data
| | |____extract_rawdata.txt
| | |____plot_spectra.txt
| | |____json-import
| | | |____import_OSSL-LUCAS-SE_nir_460-1050_10.json
| | |____json-plots
| | | |____plot_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
| | |____visnir
| | | |____OSSL-LUCAS-SE_460-1050_10
| | | | |____params-visnir_OSSL-LUCAS-SE_460-1050_10.json
| | | | |____data-visnir_OSSL-LUCAS-SE_460-1050_10.json
| | | | |____plot
| | | | | |____spectra+derivative.png
| | | | | |____boxwhisker_all-features.png
| | | | | |____histogram_all-features.png
| | | | | |____boxwhisker_sand.tot_usda.c60_w.pct.png
| | | | | |____histogram_sand.tot_usda.c60_w.pct.png
```

#### Model data

Running the script <span class='module'>OSSL_mlmodel</span> for Machine Learning (ML) based modelling of the OSSL data follows the same principles as when running import and plot. A project file (default name _ml-model_spectra.txt_) directly under the folder _arranged-data_ and a subfolder (default name:_json-ml-modeling_), also directly under _arranged-data_ that contains the json modelling command files. If you used the Sweden example, the required folders and files are there. You just have to edit the paths as you did for the importing. Then run the module <span class='module'>OSSL_mlmodel</span> pointing towards the json specification file for plotting (default name: _model_ossl.json_). Again you can either run via the command line or using <span class='app'>Eclipse</span>.

Details for how to parameterise the ML modelling are described in a series of instructions starting with the overview [Model OSSL data: 1 process-flow](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01/). The script <span class='module'>OSSL_mlmodel</span> and the json parameter file can be set to very simple (e.g. Ordinary Least Square - OLS) modelling of a single soil property. The processing will be fast. It can also be set to include several regressors, including Neural Networks, and more than a dozen soil properties each with an individually fitted tuning of the regressors. Then the processing time can easily expand to hours and even days.

if you start by editing the modelling files that came with the Swedish example dataset, and then run the script <span class='module'>OSSL_mlmodel</span>, you should end up with the following folders and files (explained in the post [Model OSSL data: 1 process-flow](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01/)):

```
|____LUCAS
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____neon.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
| |____arranged-data
| | |____extract_rawdata.txt
| | |____plot_spectra.txt
| | |____ml-model_spectra.txt
| | |____json-import
| | | |____import_ossl-spectra_sweden-LUCAS_nir_640-1050_10.json
| | |____json-plots
| | | |____plot_ossl-spectra_sweden-LUCAS_nir_640-1050_10.json
| | |____json-ml-modeling
| | | |____model-ossl-spectra_sweden-LUCAS_nir_640-1050_10.json
| | | |____hyper-param-exhaustive-tuning.json
| | | |____hyper-param-random-tuning.json
| | |____visnir
| | | |____OSSL-LUCAS-SE_640-1050_10
| | | | |____plot
| | | | | |____boxwhisker_clay.tot_usda.a334_w.pct.png
| | | | | |____boxwhisker_ec_usda.a364_ds.m.png
| | | | | |____boxwhisker_n.tot_usda.a623_w.pct.png
| | | | | |____histogram_oc_usda.c729_w.pct.png
| | | | | |____boxwhisker_all-features.png
| | | | | |____histogram_all-features.png
| | | | | |____histogram_ec_usda.a364_ds.m.png
| | | | | |____histogram_cec_usda.a723_cmolc.kg.png
| | | | | |____spectra+derivative.png
| | | | | |____boxwhisker_oc_usda.c729_w.pct.png
| | | | | |____boxwhisker_sand.tot_usda.c60_w.pct.png
| | | | | |____histogram_silt.tot_usda.c62_w.pct.png
| | | | | |____boxwhisker_ph.h2o_usda.a268_index.png
| | | | | |____histogram_k.ext_usda.a725_cmolc.kg.png
| | | | | |____histogram_clay.tot_usda.a334_w.pct.png
| | | | | |____histogram_sand.tot_usda.c60_w.pct.png
| | | | | |____boxwhisker_silt.tot_usda.c62_w.pct.png
| | | | | |____boxwhisker_cec_usda.a723_cmolc.kg.png
| | | | | |____boxwhisker_k.ext_usda.a725_cmolc.kg.png
| | | | | |____histogram_ph.h2o_usda.a268_index.png
| | | | | |____histogram_n.tot_usda.a623_w.pct.png
| | | | |____mlmodel
| | | | | |____images
| | | | | | |____testtg_oc_usda.c729_w.pct_OLS-model_feat-imp.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_OLS-model_tt-result.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_RandForRegr-model_feat-imp.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_RandForRegr-model_permut-imp.png
| | | | | | |____testtg_OLS-multi-results.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_RandForRegr-model_permut-imp.png
| | | | | | |____testtg_oc_usda.c729_w.pct_OLS-model_kfold-result.png
| | | | | | |____testtg_oc_usda.c729_w.pct_RandForRegr-model_kfold-result.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg-multi-results.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_RandForRegr-model_feat-imp.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_RandForRegr-model_permut-imp.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_OLS-model_kfold-result.png
| | | | | | |____testtg_RandForRegr-multi-results.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_OLS-model_feat-imp.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_RandForRegr-model_tt-result.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_RandForRegr-model_kfold-result.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_OLS-model_kfold-result.png
| | | | | | |____testtg_oc_usda.c729_w.pct_OLS-model_permut-imp.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_RandForRegr-model_feat-imp.png
| | | | | | |____testtg_oc_usda.c729_w.pct-multi-results.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_RandForRegr-model_kfold-result.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_OLS-model_permut-imp.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_OLS-model_tt-result.png
| | | | | | |____testtg_oc_usda.c729_w.pct_OLS-model_tt-result.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct-multi-results.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg-multi-results.png
| | | | | | |____testtg_oc_usda.c729_w.pct_RandForRegr-model_tt-result.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_OLS-model_feat-imp.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_RandForRegr-model_tt-result.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_OLS-model_feat-imp.png
| | | | | | |____testtg_oc_usda.c729_w.pct_RandForRegr-model_permut-imp.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_OLS-model_permut-imp.png
| | | | | | |____testtg_n.tot_usda.a623_w.pct_OLS-model_tt-result.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_OLS-model_permut-imp.png
| | | | | | |____testtg_oc_usda.c729_w.pct_RandForRegr-model_feat-imp.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_RandForRegr-model_kfold-result.png
| | | | | | |____testtg_k.ext_usda.a725_cmolc.kg_RandForRegr-model_tt-result.png
| | | | | | |____testtg_cec_usda.a723_cmolc.kg_OLS-model_kfold-result.png
| | | | | |____json
| | | | | | |____testtg_OSSL-LUCAS-SE_640-1050_10_results.json
| | | | | | |____testtg_OSSL-LUCAS-SE_640-1050_10_params.json
| | | | | |____pickle
| | | | | | |____testtg_modelid_oc_usda.c729_w.pct_RandForRegr_Kfold.xsp
| | | | | | |____testtg_modelid_n.tot_usda.a623_w.pct_OLS_trainTest.xsp
| | | | | | |____testtg_modelid_n.tot_usda.a623_w.pct_OLS_Kfold.xsp
| | | | | | |____testtg_modelid_cec_usda.a723_cmolc.kg_OLS_Kfold.xsp
| | | | | | |____testtg_modelid_k.ext_usda.a725_cmolc.kg_OLS_trainTest.xsp
| | | | | | |____testtg_modelid_cec_usda.a723_cmolc.kg_RandForRegr_trainTest.xsp
| | | | | | |____testtg_modelid_oc_usda.c729_w.pct_OLS_Kfold.xsp
| | | | | | |____testtg_modelid_cec_usda.a723_cmolc.kg_RandForRegr_Kfold.xsp
| | | | | | |____testtg_modelid_n.tot_usda.a623_w.pct_RandForRegr_Kfold.xsp
| | | | | | |____testtg_modelid_oc_usda.c729_w.pct_RandForRegr_trainTest.xsp
| | | | | | |____testtg_modelid_cec_usda.a723_cmolc.kg_OLS_trainTest.xsp
| | | | | | |____testtg_modelid_k.ext_usda.a725_cmolc.kg_OLS_Kfold.xsp
| | | | | | |____testtg_modelid_k.ext_usda.a725_cmolc.kg_RandForRegr_Kfold.xsp
| | | | | | |____testtg_modelid_k.ext_usda.a725_cmolc.kg_RandForRegr_trainTest.xsp
| | | | | | |____testtg_modelid_oc_usda.c729_w.pct_OLS_trainTest.xsp
| | | | | | |____testtg_modelid_n.tot_usda.a623_w.pct_RandForRegr_trainTest.xsp
| | | | |____params-visnir_OSSL-LUCAS-SE_640-1050_10.json
| | | | |____data-visnir_OSSL-LUCAS-SE_640-1050_10.json
```
