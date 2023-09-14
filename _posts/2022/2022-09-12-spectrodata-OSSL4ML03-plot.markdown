---
layout: post
title: Plot OSSL data
categories: libspectrodata
excerpt: "Plot OSSL spectra and wet laboratory soil data."
tags:
  - OSSL
  - api
  - explorer
  - arrange
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-12 11:27'
modified: '2023-07-05 11:27'
comments: true
share: true
---

This post is the third in a series on organising and analysing data from the Open Soil Spectral Library (OSSL). To run the scripts used in this post you need to setup a Python environment, and clone or download the python scripts from a [GitHub repository (repo)](https://github.com/karttur/OSSL-pydev/), as explained in the post [Clone the OSSL python package](../../libspectrosupport/spectrosupport-OSSL-clone).

### Introduction

This post is a manual for how to explore, by plotting, the OSSL data downloaded and organized as otlined in the previous posts of this blog. The plotting is done using a Python script and the <span class='package'>matplotlib</span> package. With it you can plot both the spectra and the laboratory data.

### Prerequisites

This post requires that you followed the processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [importing](../spectrodata-OSSL4ML02-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the <span class='package'>matplotlib</span> package installed. If you want to have an example structure of command files, access the [OSSL-data repo](https://github.com/karttur/OSSL-data/). This post is more of a stand-alone explanation of how to setup processing using the python module <span class='module'>OSSL_plot</span>; the post [Run ossl-xspectre modules](../../libspectrosupport/spectrosupport-OSSL-run) instead starts from the structure of a prepared example of [OSSL-data](https://github.com/karttur/OSSL-data/), also accessible from GitHub.

### Plot OSSL

The python package <span class='package'>matplotlib</span> has almost endless possibilities for designing data plots. Only a few of the functions are predefined in the module <span class='module'>OSSL_plot.py</span>. If you want to develop your own plot functions for OSSL data you are free to use the <span class='module'>OSSL_plot.py</span> as a starting point.

#### Spectral plots

You can plot either the raw spectra or the first derivate (change of spectra between sequential wavelengths) or both together as subplots in the same figure (figure 1). In the json command file you can set the number of spectra to show, the colour ramp and the ranges of the plot axis. You can also set the information text that appear in each plot or subplot.

<figure class="third">
	<a href="../../images/LUCAS_460-1050_10_spectra.png">
  <img src="../../images/LUCAS_460-1050_10_spectra.png" alt="image">
  </a>

  <a href="../../images/LUCAS_460-1050_10_derivative.png">
  <img src="../../images/LUCAS_460-1050_10_derivative.png" alt="image">
  </a>

  <a href="../../images/LUCAS_460-1050_10_spectra+derivative.png">
  <img src="../../images/LUCAS_460-1050_10_spectra+derivative.png" alt="image">
  </a>

	<figcaption>Figure 1. Plots of raw spectral signal, derivates of spectral signals and both raw and derivate signals together (see text).</figcaption>
</figure>


#### Feature plots

The laboratory data ("features") can be plotted as histograms and box-whisker (box) plots. You can plot individual features or combine all selected features in a single plot (figure 2). You can define individual colours for each feature, as exemplified in figure 2.

<figure class="half">
  <a href="../../images/LUCAS_SE_histogram_all-features.png">
  <img src="../../images/LUCAS_SE_histogram_all-features.png" alt="image">
  </a>  
	<a href="../../images/LUCAS_SE_boxwhisker_all-features.png">
  <img src="../../images/LUCAS_SE_boxwhisker_all-features.png" alt="image">
  </a>
	<figcaption>Figure 2. Plots of laboratory data features, left panel shows histogram distribution and right panel shows box-whisler plots.</figcaption>
</figure>

#### Python Module OSSL_plot.py

Running the <span class='module'>OSSL_plot.py</span> script is similar to running the [import script](../spectrodata-OSSL4ML02-arrange), and requires specifications of the paths and names of 1) the OSSL data and 2) the command files that define what you want to plot and some minimal layout options:

- **rootpath**: full path to folder with a downloaded OSSL zip file; parent folder to  "sourcedatafolder", "arrangeddatafolder", and "jsonfolder"
- **sourcedatafolder**: subfolder under "rootpath" with the exploded content of the OSSL zip file (default = "data")
- **arrangeddatafolder**: subfolder under "rootpath" where the imported (rearranged) OSSL data will be stored
- **jsonfolder**: subfolder under "rootpath" where the json plot parameter files must be located
- **projFN**: the name of an existing txt file that sequentially lists json plot parameter files to run, must be directly under the "arrangeddatafolder"
- **createjsonparams**: if set to true the script will create a template json file and exit

##### json specification file

All of the paths and names listed above must be specified in a json file, and the local path to this json file is the only parameter that is required when running the <span class='module'>OSSL_plot.py</span> script. The json file for plotting the data over Sweden that were [downloaded](../spectrodata-OSSL4ML01-download) and then [imported](../spectrodata-OSSL4ML02-arrange) looks like this:

```
{
  "rootpath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-plots",
  "projFN": "plot_spectra.txt",
  "createjsonparams": false
}
```

The paths/names of the OSSL data are those that you set when you downloaded and exploded in the [download](../spectrodata-OSSL4ML01-download) post. Before you can plot any data you must create 1) a json command file defining how to plot the OSSL data, and 2) a text file that specifies the name of this json command file. The reason that the direct link to the command file is not given is that the project text file can link to any number of json command files. You can thus run multiple plot designs for one and the same dataset, or run plots for multiple datasets using a single project file and a single run.

The first time you use the script you must copy or create and then edit the json command files. The script can generate a template command file for you, or you can download an example (the data over Sweden used in the previous posts) from a [GitHub repo](https://github.com/karttur/OSSL-data). To generate a template set the _rootpath_ and change the parameter _createjsonparams_ to _true_.

```
{
  "rootpath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS",
  "sourcedatafolder": "data",
  "arrangeddatafolder": "arranged-data",
  "jsonfolder": "json-plots",
  "projFN": "plot_spectra.txt",
  "createjsonparams": true
}
```

##### Run script

To run the script, open a <span class='app'>terminal</span> window. Change directory (<span class='terminalapp'>cd</span>) to where you downloaded the <span class='module'>OSSL_plot.py</span> script (or give the full path).

Before you can run the script you probably have to set the script to have execution rights on your local machine. In MacOS and Linux you do that with the <span class='terminalapp'>chmod</span> (change mode) command:

 <span class='terminal'>chmod OSSL_plot.py 755</span>

 Then you can run the script with the full, local path to the json file [above](#json-specification-file) as the only parameter:

<span class='terminal'>python OSSL_plot.py \"/Users/thomasgumbricht/docs-local/OSSL/plot_spectra.json\"</span>

With the parameter _createjsonparams_ set to _true_ the script will report that a template file was created:

```
json parameter file created: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-plots/template_plot_ossl-spectra.json
 Edit the json file for your project and rename it to reflect the commands.
 Add the path of the edited file to your project file (plot_spectra.txt).
 Then set createjsonparams to False in the main section and rerun script.
```

###### json command file structure

The json command file that defines the plotting of the OSSL data have the following structure (scroll down to see a commented version):
```
{
  "verbose": 1,
  "id": "id_as_from_import",
  "name": "name_as_from_import",
  "userId": "youruserid - any for now",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignId": "OSSL-region-etc",
    "campaignShortId": "OSSL-xyz",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": ""/path/to/file/with/ossl/arranged_data.json",
  },
  "spectraPlot": [
    {
      "apply": true,
      "savePng": true,
      "screenDraw": true,
      "legend": false,
      "maxSpectra": 500,
      "colorRamp": "jet",
      "supTitle": "auto",
      "tightLayout": true,
      "singles": {
        "figSize": {
          "x": 8,
          "y": 6
        }
      },
      "duals": {
        "figSize": {
          "x": 8,
          "y": 8
        }
      },
      "xLim": {
        "xMax": 1045,
        "xMin": 465
      },
      "raw": {
        "apply": true,
        "axisLabel": {
          "x": "Wavelength (nm)",
          "y": "reflectance"
        },
        "text": {
          "bandWidth": true,
          "samples": true,
          "skipStep": true,
          "text": "",
          "x": 0.02,
          "y": 0.8
        },
        "title": {
          "title": "Original spectra",
          "x": 0,
          "y": 0
        },
        "yLim": {
          "yMax": 0.6,
          "yMin": 0
        }
      },
      "derivatives": {
        "apply": true,
        "axisLabel": {
          "x": "Wavelength (nm)",
          "y": "derivate"
        },
        "text": {
          "bandWidth": false,
          "samples": false,
          "skipStep": false,
          "text": "Derivate",
          "x": 0.6,
          "y": 0.8
        },
        "title": {
          "title": "Derivates",
          "x": 0,
          "y": 0
        },
        "yLim": {
          "yMax": 0,
          "yMin": 0
        }
      }
    }
  ],
  "featurePlot": {
    "bins": 10,
    "boxWhisker": true,
    "histogram": true,
    "savePng": true,
    "screenDraw": true,
    "singles": {
      "apply": true,
      "figSize": {
        "x": 8,
        "y": 6
      }
    },
    "columns": {
      "apply": true,
      "figSize": {
        "x": 8,
        "y": 8
      },
      "ncolumns": 3
    },
    "targetFeatureSymbols": {
      "caco3_usda.a54_w.pct": {
        "color": "whitesmoke",
        "label": "CaCo3",
        "unit": "percent"
      },
      "cec_usda.a723_cmolc.kg": {
        "color": "seagreen",
        "label": "Cation Exc. Cap.",
        "unit": "mol*kg-1"
      },
      "cf_usda.c236_w.pct": {
        "color": "sienna",
        "label": "Crane fraction",
        "unit": "percent"
      },
      "clay.tot_usda.a334_w.pct": {
        "color": "tan",
        "label": "Clay cont.",
        "unit": "percent"
      },
      "ec_usda.a364_ds.m": {
        "color": "dodgerblue",
        "label": "Electric cond.",
        "unit": "ms*m'-1"
      },
      "k.ext_usda.a725_cmolc.kg": {
        "color": "lightcyan",
        "label": "Potassion (K)",
        "unit": "mol*kg-1"
      },
      "n.tot_usda.a623_w.pct": {
        "color": "darkcyan",
        "label": "Nitrogen (N) [tot]",
        "unit": "percent"
      },
      "oc_usda.c729_w.pct": {
        "color": "dimgray",
        "label": "Organic carbon (C)",
        "unit": "percent"
      },
      "p.ext_usda.a274_mg.kg": {
        "color": "firebrick",
        "label": "Phosphorus (P)",
        "unit": "mg*kg-1"
      },
      "ph.cacl2_usda.a481_index": {
        "color": "lemonchiffon",
        "label": "pH (CaCl)",
        "unit": "pH"
      },
      "ph.h2o_usda.a268_index": {
        "color": "lightyellow",
        "label": "pH (H20)",
        "unit": "pH"
      },
      "sand.tot_usda.c60_w.pct": {
        "color": "orange",
        "label": "Sand cont.",
        "unit": "percent"
      },
      "silt.tot_usda.c62_w.pct": {
        "color": "khaki",
        "label": "Silt cont.",
        "unit": "percent"
      }
    },
    "targetFeatures": [
      "n.tot_usda.a623_w.pct",
      "oc_usda.c729_w.pct"
    ]
  }
}
```

Here is the same command file, but with comments (you can not have comments in a json file so the structure below is just for explanations):

```
{
  "verbose": 1, # The verbosity during the script execution
  "id": "id_as_from_import", # use the same id as reported by the import script
  "name": "name_as_from_import", # use the same name as reported by the import script
  "userId": "youruserid - any for now", # For local use you can state anything here
  "importVersion": "OSSL-202308",
  "campaign": { # Unique campaign identifier that indicate what data is used e.g. OSSL-NIR-SWEDEN
    # Comment use the same setting as in the import script
    "campaignId": "OSSL-region-etc",
    "campaignShortId": "OSSL-xyz",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": "/path/to/file/with/ossl/arranged_data.json",
  },
  "spectraPlot": [
    {
      "apply": true, # whether or not to plot the spectra
      "savePng": true, # whether or not to save spectra plot as png
      "screenDraw": true, # whether or not to draw spectra plot on screen
      "legend": false, # whether or not to include legend
      "maxSpectra": 500, # maximum number of spectra to include in plot
      "colorRamp": "jet", # matplolib default colorramp to apply for spectra
      "supTitle": "auto", # Super (top level) title
      "tightLayout": true, # whether or not to apply tight layout
      "singles": { # Size of single plots (spectra or derivative)
        "figSize": {
          "x": 8,
          "y": 6
        }
      },
      "duals": { # Size of dual plots (spectra and derivative)
        "figSize": {
          "x": 8,
          "y": 8
        }
      },
      "xLim": { # x-axis range of spectral plot
        "xMax": 1045,
        "xMin": 465
      },
      "raw": {
        "apply": true, # whether or not to include reflectance spectra in plot
        "axisLabel": { # axis labels for reflectance spectra
          "x": "Wavelength (nm)",
          "y": "reflectance"
        },
        "text": { # text to add to reflectance plot
          "bandWidth": true, # include bandwidth in text
          "samples": true, # include nr of samples in text
          "skipStep": true, # include how many spectra of the total that are in the plot in text
          "text": "", # additional text
          "x": 0.02, # relative x-position of text
          "y": 0.8 # relative y-position of text
        },
        "title": { # title and title position of reflectance spectra
          "title": "Original spectra",
          "x": 0,
          "y": 0
        },
        "yLim": { # y-axis range of reflectance spectra  (0 = automatic)
          "yMax": 0.6,
          "yMin": 0
        }
      },
      "derivatives": {
        "apply": true, # whether or not to include spectra derivatives in plot
        "axisLabel": { # axis labels for spectra derivative
          "x": "Wavelength (nm)",
          "y": "derivate"
        },
        "text": { # text to add to reflectance plot
          "bandWidth": true, # include bandwidth in text
          "samples": true, # include nr of samples in text
          "skipStep": true, # include how many spectra of the total that are in the plot in text
          "text": "", # additional text
          "x": 0.02, # relative x-position of text
          "y": 0.8 # relative y-position of text
        },
        "title": { title and title position of spectra derivative
          "title": "Derivates",
          "x": 0,
          "y": 0
        },
        "yLim": { # y-axis range of spectra derivate (0 = automatic)
          "yMax": 0,
          "yMin": 0
        }
      }
    }
  ],
  "featurePlot": { # define the feature plot(s)
    "bins": 10, # nr bins for histograms
    "boxWhisker": true, # whether or not to include box-plots
    "histogram": true, # whether or not to include histogrames
    "savePng": true, # whether or not to save feature plots as png
    "screenDraw": true, # whether or not to draw feature plots on screen
    "singles": {  
      "apply": true, # whether or not to include single feature plots
      "figSize": { # size of single feature plots
        "x": 8,
        "y": 6
      }
    },
    "columns": {
      "apply": true, # whether or not to include multi-feature plots
      "figSize": { # size of multi feature plots
        "x": 8,
        "y": 8
      },
      "ncolumns": 3 # nr of colums to use in mulit-feature plots
    },
    "targetFeatureSymbols": { # library of symbols, labels and units for the features
      "caco3_usda.a54_w.pct": {
        "color": "whitesmoke",
        "label": "CaCo3",
        "unit": "percent"
      },
      "cec_usda.a723_cmolc.kg": {
        "color": "seagreen",
        "label": "Cation Exc. Cap.",
        "unit": "mol*kg-1"
      },
      "cf_usda.c236_w.pct": {
        "color": "sienna",
        "label": "Crane fraction",
        "unit": "percent"
      },
      "clay.tot_usda.a334_w.pct": {
        "color": "tan",
        "label": "Clay cont.",
        "unit": "percent"
      },
      "ec_usda.a364_ds.m": {
        "color": "dodgerblue",
        "label": "Electric cond.",
        "unit": "ms*m'-1"
      },
      "k.ext_usda.a725_cmolc.kg": {
        "color": "lightcyan",
        "label": "Potassion (K)",
        "unit": "mol*kg-1"
      },
      "n.tot_usda.a623_w.pct": {
        "color": "darkcyan",
        "label": "Nitrogen (N) [tot]",
        "unit": "percent"
      },
      "oc_usda.c729_w.pct": {
        "color": "dimgray",
        "label": "Organic carbon (C)",
        "unit": "percent"
      },
      "p.ext_usda.a274_mg.kg": {
        "color": "firebrick",
        "label": "Phosphorus (P)",
        "unit": "mg*kg-1"
      },
      "ph.cacl2_usda.a481_index": {
        "color": "lemonchiffon",
        "label": "pH (CaCl)",
        "unit": "pH"
      },
      "ph.h2o_usda.a268_index": {
        "color": "lightyellow",
        "label": "pH (H20)",
        "unit": "pH"
      },
      "sand.tot_usda.c60_w.pct": {
        "color": "orange",
        "label": "Sand cont.",
        "unit": "percent"
      },
      "silt.tot_usda.c62_w.pct": {
        "color": "khaki",
        "label": "Silt cont.",
        "unit": "percent"
      }
    },
    "targetFeatures": [ # list of the features to include in the plot
      "n.tot_usda.a623_w.pct",
      "oc_usda.c729_w.pct"
    ]
  }
}
```

###### Editing the json command file

To actually run the rearrangement you need to edit the json command file to fit your input data and the output you want. Rename the file to reflect the rearrangement, in my case to _plot-OSSL-LUCAS-SE_nir_460-1050_10.json_, Where:


- _LUCAS_ is the OSSL source dataset,
- _SE_ is the region,
- _nir_ the spectral data that is rearranged,
- _460_ the lower end wavelength of the output spectra,
- _1050_ the upper end wavelength of the output spectra, and
- _10_ the spectral resolution of the output spectra

The full path to the command file on my machine then becomes _/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-plots/plot-OSSL-LUCAS-SE_nir_460-1050_10.json_.

I then create the project file _plot_spectra.txt_ (in the project root folder) and enter the full path to my command file:

```
/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-plots/plot-OSSL-LUCAS-SE_nir_460-1050_10.json
```

###### Running the project file

You can now run the Python script _OSSL_plot.py_ with a single command, the path to the [json specification file](#json-specification-file). The json specification file points to the arranged data and then plot project file (a simple text file) that in turn points the json command file. The project text file can point to any number of json command files and they will be executed in sequence.

The module generates the requested plots, and shows them on screen and/or saves them as a png files as requested in the json command file. For instance [figure 1](#spectral-plots) and [figure 2](#feature-plots) above were generated in this way.
