---
layout: post
title: Import OSSL data
categories: libspectrodata
excerpt: "Arrange (import) OSSL data over a selected region using a python script to use for Machine Learning modelling"
tags:
  - OSSL
  - api
  - explorer
  - arrange
  - import
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-09 11:27'
modified: '2023-07-03 11:27'
comments: true
share: true
---

### Introduction

In this post you will arrange (import) a downloaded OSSL dataset using a python script. The script will combine data on the site, laboratory data and spectral scans. The combined data is saved as a <span class='file'>json</span> file and used for plotting, soil line extraction and Machine Learning analyses and modelling in the following posts.

### Prerequisites

This post requires that you downloaded OSSL as outlined in the [previous](../spectrodata-OSSL4ML01-download) post. You must also have access to a Python interpreter. For developing the Python scripts that you will use throughout these tutorials I setup my Python environments using [<span class='app'>Anaconda</span>](https://anaconda.org) including its terminal-app <span class='terminalapp'>conda</span>. You will most likely need to install [<span class='app'>Anaconda</span>](https://anaconda.org) or its minimalistic terminal-app version, [<span class='app'>miniconda</span>](https://docs.conda.io/projects/miniconda/en/latest/). If you are new to Python or setting up environments for Python for the the first time, I recommend that you install [<span class='app'>Anaconda</span>](https://anaconda.org). In a another of my "blogs" you find a more detailed manual for how to do that (written for MacOS):

- [Install Annaconda](https://karttur.github.io/setup-ide/setup-ide/install-anaconda/)

To run the python scripts for importing, exploring and analysing the OSSL data you only need a terminal window. If you want to have a Graphical User Interface (GUI) for running the scripts then you can setup a PyDev environment in <span class='app'>Eclipse</span> Integrated Development Environment (IDE). That is not required, but if you want to be able to rewrite or edit the Python package, <span class='app'>Eclipse</span> is an options:

- [Setup Eclipse for PyDev](https://karttur.github.io/setup-ide/setup-ide/install-eclipse/)

All the scripts for the OSSL processing are freely available on [GitHub](https://github.com/karttur/OSSL-py).

### Anaconda python environment

If you want to to use [<span class='app'>Anaconda</span>](https://anaconda.org) for setting up the virtual python environment, the [GitHub](https://github.com/karttur/OSSL-py/tree/main/py-env) repo contains two environment installation (<span class='file'>yml</span>):

- spectra-ossl_from-history_py38.yml, and
- spectra-ossl_full_py38.yml

The first version (_from-history_) should work across all OS platforms whereas the second version (_full_) is built on a MacOS and may contain packages that are not compatible with other OS. To setup the environment in <spanc class='terminalapp'>conda</span> run the command:

<span class='terminal'>conda env create --name envname --file=environments.yml</span>

conda env create --name spectra_py38 --file=spectra-ossl_from-history_py38.yml

### Arrange OSSL

At the end of the [previous](../spectrodata-OSSL4ML01-download) post I suggested how to organise downloaded OSSL files. It is better to put up that structure from the beginning as you will need to state folder paths in the command files that are used to pilot the Python script.

#### Python Module OSSL.py

The rearranging (or importing) of the downloaded OSSL data is done by a single stand-alone python module (script) called <span class='module'>OSSL_import.py</span>. The script is available from [GitHub](https://github.com).

TO BE FURTHER DEVELOPED

Running the <span class='module'>OSSL_import.py</span> script requires specifications of the paths and names of 1) the OSSL data and 2) the command files that define how to rearrange the data while importing:

- **rootpath**: full path to folder with a downloaded OSSL zip file; parent folder to  "sourcedatafolder", "arrangeddatafolder", and "jsonfolder"
- **sourcedatafolder**: subfolder under "rootpath" with the exploded content of the OSSL zip file (default = "data")
- **arrangeddatafolder**: subfolder under "rootpath" where the imported (rearranged) OSSL data will be stored
- **jsonfolder**: subfolder under "rootpath" where the json parameter file ("projFN") is located
- **projFN**: the name of an existing txt file that sequentially lists json parameter files to run, must be in the subfolder "jsonfolder"
- **createjsonparams**: if set to true the script will create a template json file and exit

##### json specification file

All of the paths and names listed above must be specified in a json file, and the local path to this json file is the only parameter that is required when running the <span class='module'>OSSL_import.py</span> script. The json file for importing the data over Sweden that were downloaded in the [previous](../spectrodata-OSSL4ML01-download) post looks like this:

```
{
  "rootpath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-import",
  "projFN": "extract_rawdata.txt"
  "createjsonparams": false
}
```

The paths/names of the OSSL data are those that you set when you downloaded and exploded in the [previous](../spectrodata-OSSL4ML01-download) post. Before you can actually import any data you must create 1) a json command file defining how to arrange the OSSL data, and 2) a text file that specifies the name of this json command file. The reason that the direct link to the command file is not given is that the project text file can link to any number of json command files. You can thus rearrange and import multiple versions (for instance with different wavelength coverage) using a single project file and a single run.

The first time you use the script you must copy or create and then edit the json command files. The script can generate a template command file for you, or you can download an example (the data over Sweden used in the previous posts) from the [GitHub repo](#). To generate a template change the parameter _createjsonparams_ to _true_.

```
{
  "rootpath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-import",
  "projFN": "extract_rawdata.txt",
  "createjsonparams": true
}
```

##### Run script

To run the script, open a <span class='app'>terminal</span> window. Change directory (<span class='terminalapp'>cd</span>) to where you downloaded the <span class='module'>OSSL_import.py</span> script (or give the full path).

Before you can run the script you probably have to set the script to have execution rights on your local machine. In MacOS and Linux you do that with the <span class='terminalapp'>chmod</span> (change mode) command:

 <span class='terminal'>chmod OSSL_import.py 755</span>

 Then you can run the script with the full, local path to the json file [above](#json-specification-file) as the only parameter:

<span class='terminal'>python OSSL_import.py \"/Users/thomasgumbricht/docs-local/OSSL/import_ossl.json\"</span>

With the parameter _createjsonparams_ set to _true_ the script will report that a template file was created:

```
% python OSSL_import.py "/Users/thomasgumbricht/docs-local/OSSL/import_ossl.json"
json parameter file created: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/template_import_ossl-spectra.json
 Edit the json file for your project and rename it to reflect the commands.
 Add the path of the edited file to your project file (extract_rawdata.txt).
 Then set createjsonparams to false and rerun script.
```

##### json command file

For each set of spectra that you want to work with you need to create a json command file. The full, local path to that file must then be added to the textfile identified with the parameter _projectfilename_ (default: _extract_rawdata.txt_). The json command file itself must be located in the subfolder identified with the parameter _jsonfolder_ (default: _json-import_). This is a bit complicated but just take it easy and follow the steps outlined below.

###### json command file structure

The json command file that defines the rearrangement of the OSSL data must have the following structure (scroll down to see a commented version):
```
{
  "verbose": 1,
  "id": "auto",
  "name": "auto",
  "userId": "youruserid - any for now",
  "importVersion": "OSSL-202308",
  "rootFP": "/path/to/folder/with/ossl/download",
  "campaign": {
    "campaignId": "OSSL-region-etc",
    "campaignShortId": "OSSL-xyz",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoGegion": "Sweden"
  },
  "soilSample": {
    "minDepth": 0,
    "maxDepth": 20
  },
  "visnir": {
    "apply": true,
    "subFP": "visnir",
    "beginWaveLength": 460,
    "endWaveLength": 1050,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "mir": {
    "apply": true,
    "subFP": "mir",
    "beginWaveLength": 2500,
    "endWaveLength": 8000,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "neon": {
    "apply": true,
    "subFP": "neon",
    "beginWaveLength": 1350,
    "endWaveLength": 2550,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "labData": [
    "caco3_usda.a54_w.pct",
    "cec_usda.a723_cmolc.kg",
    "cf_usda.c236_w.pct",
    "clay.tot_usda.a334_w.pct",
    "ec_usda.a364_ds.m",
    "k.ext_usda.a725_cmolc.kg",
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct",
    "p.ext_usda.a274_mg.kg",
    "ph.cacl2_usda.a481_index",
    "ph.h2o_usda.a268_index",
    "sand.tot_usda.c60_w.pct",
    "silt.tot_usda.c62_w.pct"
  ],
  "labDataRange": {
    "caco3_usda.a54_w.pct": {
      "min": 0,
      "max": 10
    }
  }
}
```

Here is the same command file, but with comments (you can not have comments in a json file so the structure below is just for explanations):

```
{
  "verbose": 1, # The verbosity during the script execution
  "id": "auto",
  "name": "auto",
  "userId": "youruserid - any for now", # For local use you can state anything here
  "importVersion": "OSSL-202308", # Keep the import version as that is read by further steps
  "campaign": { # A campaign is a set of related spectra, e.g. regional soil spectra
    "campaignId": "OSSL-region-etc", # Unique campaign identifier that indicate what data is used e.g. OSSL-NIR-SWEDEN
    "campaignShortId": "OSSL-xyz", # Short campaign identifier  e.g. OSSL-NIR-SE
    "campaignType": "laboratory", # Usually this is laboratory, but can also be field
    "theme": "soil", # The theme is usually soil, but could be e.g. wetland, desert or something else
    "product": "diffuse reflectance", # The type of spectral data
    "units": "fraction", # The units of the spectral data
    "geoGegion": "Sweden" # The geographic region
  },
  "rootFP": "/path/to/folder/with/ossl/download", # The root folder with the ossl download
  "soilSample": { # Restricts the data selection within the script
    "minDepth": 0, # Minimum depth of the samples to include
    "maxDepth": 20 # Maximum depth of the samples to include
  },
  "visnir": {
    "apply": true, # whether or not to import VISNIR spectra
    "subFP": "visnir", # subfolder for saving VISNIR spectra
    "beginWaveLength": 460, # The lower end band of the VISNIR spectra to save
    "endWaveLength": 1050, # The higher end band of the VISNIR spectra to save
    "inputBandWidth": 2, # The input band with of the VISNIR data [this by default 2 and should not be change]
    "outputBandWidth": 10 # The output band with of the VISNIR data
    # Comment - if you set visnirOutputBandWidth to more than inputBandWidth, the output spectra will be averaged over the output region
    # Thus setting 10 means that 5 sequential wavelengths will be averaged in the output
  },
  "mir": {
    "apply": true, # whether or not to import MIR spectra
    "subFP": "mir", # subfolder for saving MIR spectra
    "beginWaveLength": 2500, # The lower end band of the MIR spectra to save
    "endWaveLength": 8000, # The higher end band of the MIR spectra to save
    "inputBandWidth": 2, # The input band with of the MIR data [this by default 2 and should not be change]
    "outputBandWidth": 10 # The output band with of the MIR data
  },
  "neon": {
    "apply": true, # whether or not to import NEON spectra
    "subFP": "neon", # subfolder for saving NEON spectra
    "beginWaveLength": 1350, # The lower end band of the NEON spectra to save
    "endWaveLength": 2550, # The higher end band of the NEON spectra to save
    "inputBandWidth": 2, # The input band with of the NEON data [this by default 2 and should not be change]
    "outputBandWidth": 10 # The output band with of the NEON data
  },
  "labData": [ # The list of labData indicates which laboratry data to extract
    "caco3_usda.a54_w.pct", # You need to check the online documents for these ids
    "cec_usda.a723_cmolc.kg",
    "cf_usda.c236_w.pct",
    "clay.tot_usda.a334_w.pct",
    "ec_usda.a364_ds.m",
    "k.ext_usda.a725_cmolc.kg",
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct",
    "p.ext_usda.a274_mg.kg",
    "ph.cacl2_usda.a481_index",
    "ph.h2o_usda.a268_index",
    "sand.tot_usda.c60_w.pct",
    "silt.tot_usda.c62_w.pct"
  ],
  "labDataRange": { # Optionally you can restrict the data to within range of the labdata
    "caco3_usda.a54_w.pct": { # Example for only including spectra where CaCo3 < 10 %
      "min": 0,
      "max": 10
    }
  }
}
```

###### Editing the json command file

To actually run the rearrangement you need to edit the json command file to fit your input data and the output you want. The example below is the command file structure for rearranging the LUKAS NIR spectral data that I downloaded in the [previous](../spectrodata-OSSL4ML01-download) post:

```
{
  "verbose": 1,
  "id": "auto",
  "name": "auto",
  "userId": "thomasg",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignShortId": "OSSL-LUCAS-SE",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "rootFP": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "visnir": {
    "apply": true,
    "subFP": "visnir",
    "beginWaveLength": 460,
    "endWaveLength": 1050,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "mir": {
    "apply": false,
    "subFP": "mir",
    "beginWaveLength": 2500,
    "endWaveLength": 8000,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "neon": {
    "apply": false,
    "subFP": "neon",
    "beginWaveLength": 1350,
    "endWaveLength": 2550,
    "inputBandWidth": 2,
    "outputBandWidth": 10
  },
  "soilSample": {
    "minDepth": 0,
    "maxDepth": 20
  },
  "labData": [
    "caco3_usda.a54_w.pct",
    "cec_usda.a723_cmolc.kg",
    "cf_usda.c236_w.pct",
    "clay.tot_usda.a334_w.pct",
    "ec_usda.a364_ds.m",
    "k.ext_usda.a725_cmolc.kg",
    "n.tot_usda.a623_w.pct",
    "oc_usda.c729_w.pct",
    "p.ext_usda.a274_mg.kg",
    "ph.cacl2_usda.a481_index",
    "ph.h2o_usda.a268_index",
    "sand.tot_usda.c60_w.pct",
    "silt.tot_usda.c62_w.pct"
  ],
  "labDataRange": {
    "caco3_usda.a54_w.pct": {
      "min": 0,
      "max": 10
    }
  }
}
```

I then rename the file to reflect the rearrangement, in my case to _import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_, where:

- _sweden_ is the region,
- _LUCAS_ the source dataset,
- _nir_ the spectral region that is rearranged,
- _460_ the lower end wavelength of the output spectra,
- _1050_ the upper end wavelength of the output spectra, and
- _10_ the spectral resolution of the output spectra

The full path to the command file then becomes _/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_.

I then create the project file _extract_rawdata.txt_ (in the subfolder _arranged-data_) and enter the full path to my command file:

```
/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
```
###### Running the project file

To run the script (<spanc class='module'>OSSL_import.py</span>) make sure the [json specification file](#json-specification-file) links to both to the correct data folder and project file, and set the parameter __ to _false_:

```
{
  "rootpath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-import",
  "projFN": "extract_rawdata.txt"
  "createjsonparams": false
}
```

With all the data and folder structure in place, run the script from the command line:

<span class='terminal'></span>

I can now run the Python script _OSSL_import.py_

 The terminal will report the following:

```
python OSSL_import.py "/Users/thomasgumbricht/docs-local/OSSL/import_ossl.json"
Processing /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/extract_rawdata.txt
    jsonObj: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
        VISNIR extraction parameters saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/OSSL-LUCAS-SE_460-1050_10/params-visnir_LUCAS_460-1050_10.json
        VISNIR extracted data saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/OSSL-LUCAS-SE_460-1050_10/data-visnir_LUCAS_460-1050_10.json
```

As I defined the rearrangement for the Swedish LUKAS NIR spectra, the output from the script includes two files as reported above. Both the generated files are in json format.

###### Parameter output file

The parameter output file is simply a replica of the input parameter file. The copy is saved along the rearranged data as a reference to how it was created.

###### Data output file

The data output file contains the rearranged and combined OSSL data. The file for Sweden used in this example contains more than 4000 spectral scans along with the requested laboratory data and locations for all samples. Below is an excerpt of the output data file only showing the first sample (spectra), 4 bands and 4 laboratory results:

```
{
  "system": "LUCAS_460-1050_10",
  "userid": "thomasg",
  "importversion": "OSSL-202308",
  "campaign": {
    "campaingid": "LUCAS_460-1050_10",
    "campaignshortid": "OSSL-LUCAS-SE",
    "campaigntype": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "georegion": "Sweden",
    "minlat": 55.4209884198777,
    "maxlat": 68.4110037055234,
    "minlon": 11.2320818082897,
    "maxlon": 23.9404737429898
  },
  "wavelength": [
    465,
    475,
    ...
    ...
    1035,
    1045
  ],
  "spectra": [
    {
      "uuid": "a0de12055ea9c482219aeefa5dc8708b",
      "sitelocalid": "2009.46103936",
      "dataset": "LUCAS.WOODWELL.SSL",
      "latitude_dd": "58.436923481577",
      "longitude_dd": "14.9336633822394",
      "mindepth": "0",
      "maxdepth": "20",
      "scandatebegin": "2009-05-01",
      "scandateend": "2012-08-01",
      "sampleprep": "Sieved <2 mm",
      "instrument": "Metrohm NIRS XDS RapidContent Analyzer",
      "samplemean": [
        0.09561,
        0.095996,
        ...
        ...
        0.3327000000000009,
        0.3355280000000018
      ],
      "abundances": [
        {
          "substance": "caco3_usda.a54_w.pct",
          "value": 0.1
        },
        {
          "substance": "cec_usda.a723_cmolc.kg",
          "value": 14.4
        },
        ...
        ...
        {
          "substance": "sand.tot_usda.c60_w.pct",
          "value": 57.0
        },
        {
          "substance": "silt.tot_usda.c62_w.pct",
          "value": 28.0
        }
      ]
    }
  ]
}
```

##### Multiple rearrangements

If you further down the line want to test different wavelengths, bandwidhts or soils with particular characteristics, you can generate any number of outputs in one project. You simple create one json command file for each output, and list them all in the project file. Following the naming convention I used for the json command file in this example, the following project file will directly generate 12 different rearrangements of the LUKAS data for Sweden (lines staring with # are not executed):

```
# OSSL LUCAS Sweden extract raw data

# This line is not executed

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-1050_20.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-1050_40.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_460-1050_10.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_460-1050_20.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_460-1050_40.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-1750_10.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-1750_20.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-1750_40.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-2500_10.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-2500_20.json

/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-import/import_ossl-LUCAS_spectra_sweden_nir_600-2500_40.json
```
