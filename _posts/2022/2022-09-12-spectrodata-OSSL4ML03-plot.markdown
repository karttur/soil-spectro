---
layout: post
title: Plot OSSL data
categories: libspectrodata
excerpt: "Plot OSSL data spectra and first derivative."
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

### Introduction

This post is a manual for how to plot OSSL data downloaded and organized as otlined in the previous posts of this blog. The plotting is done using a Python script and the <span class='package'>matplotlib</span> package.

### Prerequisites

This post requires that you followed processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [arranging](../spectrodata-OSSL4ML01-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the <span class='package'>matplotlib</span> package installed.  

### Plot OSSL

The python package <span class='package'>matplotlib</span> have almost endless possibilities for designing data plots. Only a few of the functions are predefined in the module <span class='module'>OSSL_plot.py</span>. If you want to develop your own plot functions for OSSL spectral data plots you are free to use the <span class='module'>OSSL_plot.py</span> as a starting point. As with the previous modules, the parameters required by for plotting are fed trough a <spanc alss='file'>json<'/span'> command file.

#### Python Module OSSL_plot.py

The plotting of OSSL data is done by a single stand-alone python module (script) called <span class='module'>OSSL_plot.py</span>. The script is available from [GitHub](https://github.com).

TO BE FURTHER DEVELOPED

Running the <span class='module'>OSSL_plot.py</span> script requires between 1 and 5 parameters:

1. docpath [the local path to the folder where you saved the downloaded OSSL data],
2. createjsonparams [boolean variable that if set to _True_ or _1_ will create a json template file]
4. arrangeddatafolder [defaulted to _arranged-data_]
5. projectfilename [defaulted to _plot_spectra.txt_]
6. jsonpath [defaulted to _json-plots_]

Parameters 3 to 5 simply define the subfolders to create and use under the rootfolder (parameter 1) where you saved the original OSSL download (see post on [Download OSSL data](../spectrodata-OSSL4ML01-download)). You can set your own names if you want to but then you must give all 6 parameters to run the script.

##### json command file

For each plot you need to create a json command file. The local path to that file must then be added to the textfile identified with the parameter _projectfilename_ (default: _plot_spectra.txt_). The json command file itself must be located in the subfolder identified with the parameter _jsonpath_ (default: _json-plots_).

You can plot either the raw spectra or the first derivate (change of spectra between sequential wavelengths) or both together as subplots in the same figure (figure 1).

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

	<figcaption>Plots of raw spectral signal, derivates of spectral signals and both raw and derivate signals together (see text).</figcaption>
</figure>

###### json command file structure

The json command file that defines the plotting of the OSSL data must have the following structure (scroll down to see a commented version):
```
{
  "verbose": 1,
  "id": "Sweden_LUCAS_460-1050_10_20230807",
  "name": "Sweden_LUCAS_460-1050_10",
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
  "input": {
    "jsonSpectraDataFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json",
    "bandjump": 1
  },
  "plot": {
    "figSize": {
      "x": 8,
      "y": 8
    },
    "screenDraw": true,
    "savePng": true,
    "supTitle": "auto",
    "raw": {
      "apply": true,
      "axisLabel": {
        "x": "Wavelength (nm)",
        "y": "reflectance"
      },
      "yLim": {
        "yMin": 0.1,
        "yMax": 0.5
      },
      "title": {
        "x": 0,
        "y": 0,
        "title": "Original spectra"
      },
      "text": {
        "x": 0.02,
        "y": 0.8,
        "bandWidth": true,
        "samples": true,
        "skipStep": true,
        "text": ""
      }
    },
    "derivatives": {
      "apply": true,
      "axisLabel": {
        "x": "Wavelength (nm)",
        "y": "derivate"
      },
      "yLim": {
        "yMin": 0,
        "yMax": 0
      },
      "title": {
        "x": 0,
        "y": 0,
        "title": "Derivates"
      },
      "text": {
        "x": 0.6,
        "y": 0.8,
        "bandWidth": false,
        "samples": false,
        "skipStep": false,
        "text": "Derivate"
      }
    },
    "xLim": {
      "xMin": 465,
      "xMax": 1045
    },
    "colorRamp": "jet",
    "maxSpectra": 500,
    "legend": false,
    "tightLayout": true,
    "scatter": {
      "size": 50
    }
  }
}
```

Here is the same command file, but with comments (you can not have comments in a json file so the structure below is just for explanations):

```
{
  "verbose": 1, # The verbosity during the script execution
  "id": "unique id", # For now you have to give an id
  "name": "intuitive name", # The name should be something that reveals the content
  "userId": "youruserid - any for now", # For local use you can state anything here
  "importVersion": "OSSL-202308", # Keep the import version as that is read by further steps
  "campaign": { # A campaign is a set of related spectra, e.g. regional soil spectra
    "campaignId": "OSSL-LUCAS-SE",
    "campaignShortId": "OSSL", # Expand to indicate what data is used e.g. OSSL-NIR-SWEDEN
    "campaignType": "laboratory", # Usually this is laboratory, but can also be field
    "theme": "soil", # The theme is usually soil, but could be wetland, desert or something else
    "product": "diffuse reflectance", # The type of spectral data
    "units": "fraction", # The units of the spectral data
    "geoRegion": "Sweden" # The geographic region
  },
  "input": { # Defining the input data of the rearrangement
    "jsonSpectraDataFilePath": "/path/to/file/with/arranged/OSSLdata", # The json file with the rearranged spectra
  },
  "plot": { # Section for defining plot style and layout
    "figSize": { # The size of the plot
      "x": 8, # width in inches
      "y": 8 # height in inches
    },
    "screenDraw": true, # whether or not to draw the plot on screen
    "savePng": true, # whether or not to save the plot as a png file
    "supTitle": "auto", # suptitle where auto creates the default project name
    "raw": { # defining plot style and layout for the raw spectra
      "apply": true, # Whether or not to include the raw spectra in the plot
      "axisLabel": {
        "x": "Wavelength (nm)", # x-axis label for raw sepctra
        "y": "reflectance" # y-axis label for raw sepctra
      },
      "yLim": { # Defining the extent of the raw spectra y-axis
        "yMin": 0.1, # minimum y-axis value [0 = automatic]
        "yMax": 0.5 # maximum y-axis value [0 = automatic]
      },
      "title": { # title for raw spectra plot/subplot
        "x": 0, # Title x position [0 = automatic]
        "y": 0, # Title y position [0 = automatic]
        "title": "Original spectra" # Title text
      },
      "text": { # Additional text for the raw spectra plot
        "x": 0.02, # x position of additional text
        "y": 0.8, # y position of additional text
        "bandWidth": true, # whether or not to include the bandwidth in the additional text
        "samples": true, # whether or not to include the nr of samples in the additional text
        "skipStep": true, # whether or not to include the frequeny of bands included in the additional text
        "text": "" # Any other text included in the additional text
      }
    },
    "derivatives": {
      "apply": true,
      "axisLabel": {
        "x": "Wavelength (nm)",
        "y": "derivate"
      },
      "yLim": {
        "ymin": 0,
        "ymax": 0
      },
      "title": {
        "x": 0,
        "y": 0,
        "title": "Derivates"
      },
      "text": {
        "x": 0.6,
        "y": 0.8,
        "bandWidth": false,
        "samples": false,
        "skipstep": false,
        "text": "Derivate"
      }
    },
    "xLim": {
      "xMin": 500,
      "xMax": 1000
    },
    "colorRamp": "jet", # Matplotlib colorramp to use for the spectra
    "maxSpectra": 500, # Maximum number of spectra to include in the plot
    "legend": false, # Whether or not to include a legend
    "tightLayout": true, # Whether or not to apply a tight layout
    "scatter": { # not used in present version
      "size": 50
    }
  }
}
```

###### Template json command file

You can create a template file by setting the parameter _createjsonparams_ to _True_ when running the Python script. The script will then report back:

```
json parameter file created: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-plots/template_plot_ossl-spectra.json
 Edit the json file for your project and rename it to reflect the commands.
 Add the path of the edited file to your project file (plot_spectra.txt).
 Then set createjsonparams to False in the main section and rerun script.
```

The created json command file will look like the examples above. As the Python module is updated when there are changes in OSSL it is recommended that you generate a new template file rather than copying and pasting the commands above.

###### Editing the json command file

To actually run the rearrangement you need to edit the json command file to fit your input data and the output you want. Rename the file to reflect the rearrangement, in my case to _plot_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_, Where:

- _sweden_ is the region,
- _nir_ the spectral data that is rearranged,
- _460_ the lower end wavelength of the output spectra,
- _1050_ the upper end wavelength of the output spectra, and
- _10_ the spectral resolution of the output spectra

The full path to the command file then becomes _/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json/plot_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_.

I then create the project file _plot_spectra.txt_ (in the project root folder) and enter the full path to my command file:

```
/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-plots/plot_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
```

###### Running the project file

You can now run the Python script _OSSL_plot.py_ with a single command, _docpath_, where _docpath_ points to the folder where you downloaded the OSSL data and that now contains the project file (_plot_spectra.txt_) and the json command file (_plot_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_) linked via the project file.

The module generates the requested plot, and then show it on screen and/or saves it as a png file.
