---
layout: post
title: Extract Soil Line
categories: libspectrodata
excerpt: "Extract the spectral soil line from OSSL data."
tags:
  - OSSL
  - soil line
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-14 11:27'
modified: '2023-08-16 11:27'
comments: true
share: true
---

### Introduction

The soil line concept states that soils typically have a correlated spectral reflectance from dark to light within all (or most) bands. The concept has been widely used for interpreting soils in multi-spectral satellite images. This post describes how to extract and define soil lines from dowloaded and organized (imported) OSSL data.

### Prerequisites

This post requires that you followed processing as outlined in the  posts on [downloading](../spectrodata-OSSL4ML01-download) and [arranging](../spectrodata-OSSL4ML01-arrange) the OSSL spectral data. You must also have access to a Python interpreter with the <span class='package'>matplotlib</span> package installed.  

### OSSL soil line

The soil line extraction is done with the Python script (module) called <span class='module'>OSSL_soilline</span>. The script takes a single, user defined, wavelength band as reference and then compares all other bands with this reference band. It is strongly recommended to use a Near Infra-Red (NIR) band as the reference band.

The soil-lines from dark to light soil are calculated in three different ways:

1. taking the global lowest and highest reflectance of the input bands and compare it with the reference band reflectance value for the sample with this lowest/highest reflectance,
2. applying a sequence of regressions with the reference band as the independent variable and the other bands as a single covariate and then taking the global lowest and highest reflectance of each band and the estimated lowest/highest value of the reference band from the regression, and
3. as in 2), but after removal of outliers by only retaining sample spectra that fall within a user defined distance (defined in standard deviations) of the initial soil line regression.

The soil lines will typically change with each alternative, and with different settings for the removal of outliers.

The panels in figure 1 shows the soil lines for the Swedish OSSL dataset downloaded in a [previous](../spectrodata-OSSL4ML01-download) post.

<figure class="third">
	<a href="../../images/LUCAS_460-1050_10_OLS_original-soillines.png">
  <img src="../../images/LUCAS_460-1050_10_OLS_original-soillines.png" alt="image">
  </a>

  <a href="../../images/LUCAS_460-1050_10_OLS_regression-soillines.png">
  <img src="../../images/LUCAS_460-1050_10_OLS_regression-soillines.png" alt="image">
  </a>

  <a href="../../images/LUCAS_460-1050_10_OLS_distilled-soillines.png">
  <img src="../../images/LUCAS_460-1050_10_OLS_distilled-soillines.png" alt="image">
  </a>

	<figcaption>Figure 1. Soil lines can be extracted using three different approaches (see text)</figcaption>
</figure>

#### Subsampling bias

It is usually straight forward to extract the soil lines from OSSL data. If the dataset is very large, like the LUCAS sampling frame for Europe including 40,000 samples, you can speed up the processing by setting a _samplingFrequency_ (see below). Attempting to extract soil lines for the European wide LUCAS dataset I set _samplingFrequency_ to 50 (only using every 50th sample of the original 40,000). This led to a bias in the retrieved soil lines, figure 2.

<figure class="third">
	<a href="../../images/LUCAS_400-2500_2_OLS_original-soillines_subsample50.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_original-soillines_subsample50.png" alt="image">
  </a>

  <a href="../../images/LUCAS_400-2500_2_OLS_regression-soillines_subsample50.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_regression-soillines_subsample50.png" alt="image">
  </a>

  <a href="../../images/LUCAS_400-2500_2_OLS_distilled-soillines_subsample50.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_distilled-soillines_subsample50.png" alt="image">
  </a>

	<figcaption>Figure 2, example of biased soil lines when subsampling to aggresively; the figure shows the soil lines for the European LUCAS data subsampled to every 50th orignal sample.</figcaption>
</figure>

Setting a less aggresive _samplingFrequency_, the soil lines look better, figure 3. But the process would still benefit from an initial removal of (biased) outliers.

<figure class="third">
	<a href="../../images/LUCAS_400-2500_2_OLS_original-soillines_subsample10.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_original-soillines_subsample10.png" alt="image">
  </a>

  <a href="../../images/LUCAS_400-2500_2_OLS_regression-soillines_subsample10.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_regression-soillines_subsample10.png" alt="image">
  </a>

  <a href="../../images/LUCAS_400-2500_2_OLS_distilled-soillines_subsample10.png">
  <img src="../../images/LUCAS_400-2500_2_OLS_distilled-soillines_subsample10.png" alt="image">
  </a>

	<figcaption>Figure 3, the figure shows the same dataset as shown in figure 2, but with subsampling to every 10th sample; the extracted soil lines are then less biased and with more robust regressions. </figcaption>
</figure>

#### Python Module OSSL_soilline.py

The soil line extraction is done by a single stand-alone python module (script) called <span class='module'>OSSL_soilline.py</span>. The script is available from [GitHub](https://github.com).

TO BE FURTHER DEVELOPED

Running the <span class='module'>OSSL_soilline.py</span> script requires between 1 and 5 parameters:

1. docpath [the local path to the folder where you saved the downloaded OSSL data],
2. createjsonparams [boolean variable that if set to _True_ or _1_ will create a json template file]
3. arrangeddatafolder [defaulted to _arranged-data_]
4. projectfilename [defaulted to _extract_soillines.txt_]
5. jsonpath [defaulted to _json-soillines_]

Parameters 3 to 5 define the subfolders to create and use under the root folder (parameter 1) where you saved the original OSSL download (see post on [Download OSSL data](../spectrodata-OSSL4ML01-download)). You can set your own subfolder names if you want to but then you must give all 5 parameters to run the script.

##### json command file

For extracting the soil lines from a project (or _campaign_) you need to create a json command file. The local path to that file must then be added to the textfile identified with the parameter _projectfilename_ (default: _extract_soilline.txt_). The json command file itself must be located in the subfolder identified with the parameter _jsonpath_ (default: _json-soillines_). You can thus in principle run the script from command files located anywhere, but the default is to keep both the piloting text file, all the json command files, and the input and output data, under one and the same root folder.

###### json command file structure

The soil line extraction parameters required are defined as json commands and have the following structure (scroll down to see a commented version):
```
{
  "verbose": 1,
  "id": "Sweden_LUCAS_460-1050_10_20230808",
  "name": "Sweden_LUCAS_460-1050_10",
  "userId": "thomasg",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignId": "OSSL-LUCAS-SE",
    "campaignShortId": "OSSL-LUCAS-SE",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json",
    "yband": 1025,
    "samplingFrequency": 10
  },
  "model": {
    "regressor": "OLS",
    "distillStd": 1.0
  },
  "plot": {
    "minMaxSoilLines": true,
    "rawRegressionSoilLines": true,
    "distilledRegressionSoilLines": true,
    "colorRamp": "jet",
    "maxScatterBands": 6,
    "figSize": {
      "x": 0,
      "y": 0
    },
    "legend": false,
    "tightLayout": false,
    "scatter": {
      "size": 50
    },
    "endMembers": {
      "lightSoil": {
        "size": 150,
        "color": "lightgrey"
      },
      "darkSoil": {
        "size": 150,
        "color": "black"
      }
    },
    "text": {
      "x": 0.6,
      "y": 0.2,
      "bandWidth": true,
      "samples": true,
      "text": ""
    }
  },
  "figure": {
    "minMaxSoilLines": true,
    "rawRegressionSoilLines": true,
    "distilledRegressionSoilLines": true
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
    "jsonSpectraDataFilePath": "/path/to/file/with/arranged/OSSLdata", # The json file with the rearranged spectra,
    "yband": 1025, # The band to use as reference for all soil lines
    "samplingFrequency": 10 # Sub sampling frequency of the original data to use for extracing the soil lines (1 = use all data)
  },
  "model": { # Section for defining the model setting for extraction
    "regressor": "OLS", # Regressor to use for identifying the soil line
    "distillStd": 1.0 # The threshold distance from the initial regression to use for retaining/discarding data points for the distilled soil line definition
  },
  "plot": { # Section for defining plot style and layout
    "minMaxSoilLines": true, # whether to screen plot minmax soil lines
    "rawRegressionSoilLines": true, # whether to screen plot raw regression soil lines
    "distilledRegressionSoilLines": true, # whether to screen plot distilled regression soil lines
    "colorRamp": "jet", # matplotlib default color ramp to use for soil lines
    "maxScatterBands": 6, # maximum nr of bands to include in plots with scatterplots
    "figSize": {
      "x": 0,
      "y": 0
    },
    "legend": false, # whether to include legend in plot (not recommended)
    "tightLayout": false, # whether to use tight layout (not recommended)
    "scatter": { # scatter point symbolisation
      "size": 50 # size of scatter points
    },
    "endMembers": { # section for symbolising the soil line end members
      "lightSoil": { # symbolising the light soil end member
        "size": 150, # size of light soil end member symbol
        "color": "lightgrey" # color of light soil end member symbol
      },
      "darkSoil": { # symbolising the dark soil end member
        "size": 150, # size of dark soil end member symbol
        "color": "black" # color of dark soil end member symbol
      }
    },
    "text": { # Section for defining in-plot text (annotation)
      "x": 0.6, # text plot relative x position
      "y": 0.2, # text plot relative y position
      "bandWidth": true, # whether to state bandwidth in text
      "samples": true, # whether to state nr of samples in text
      "text": "" # any other text to add
    }
  },
  "figure": { # Section for defining which soil line plots to save as png files
    "minMaxSoilLines": true, # whether to save minmax soil lines plot
    "rawRegressionSoilLines": true, # whether to save raw regression soil lines plot
    "distilledRegressionSoilLines": true, # whether to save distilled regression plot
  }
}
```

###### Template json command file

You can create a template file by setting the parameter _createjsonparams_ to _True_ when running the Python script. The script will then report back:

```
json parameter file created: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-soillines/template_soilline_ossl-spectra.json
 Edit the json file for your project and rename it to reflect the commands.
 Add the path of the edited file to your project file (extract_soillines.txt).
 Then set createjsonparams to False in the main section and rerun script.
```

The created json command file will look like the examples above. As the Python module is updated when there are changes in OSSL it is recommended that you generate a new template file rather than copying and pasting the commands above.

Once you have edited the command line file, rename it to reflect the rearrangement, in my case to _soillines_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_, Where:

- _sweden_ is the region,
- _nir_ the spectral data that is rearranged,
- _460_ the lower end wavelength of the output spectra,
- _1050_ the upper end wavelength of the output spectra, and
- _10_ the spectral resolution of the output spectra

The full path to the command file then becomes _/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-soillines/soillines_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_.

I then create the project file _extract_soillines.txt_ (in the subfolder _arranged-data_) and enter the full path to my command file:

```
/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-soillines/soillines_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
```

###### Running the project file

You can now run the Python script _OSSL_soilline.py_ with a single command, _docpath_, where _docpath_ points to the folder where you downloaded the OSSL data and that now should contain the project file (_extract_rawdata.txt_) and the json command file (_import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json_) linked via the project file. The terminal will report the following:

```
Processing /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/extract_rawdata.txt
    jsonObj: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json/import_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
        VISNIR extraction parameters saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/params-visnir_LUCAS_460-1050_10.json
        VISNIR extracted data saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json
```

As I defined the rearrangement for the Swedish LUKAS NIR spectra, the output from the script includes three json files as reported in the terminal.

```
Processing /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/extract_soillines.txt
    jsonObj: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/json-soillines/soillines_ossl-spectra_sweden-LUCAS_nir_460-1050_10.json
        Reading spectral data file: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json
        SoiLline regressions saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/soillines/json/LUCAS_460-1050_10_OLS_soillines.json
        endMembers saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/soillines/json/LUCAS_460-1050_10_OLS_endMembers.json
        endMembers for ortho modelling saved as: /Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/soillines/json/LUCAS_460-1050_10_OLS_ortho-endMembers.json
```

If you set the parameters for generating png image files to true the script will also generate the requested images. In my system, the subfolder <span class='file'>visnir</span> now contains a subfolder called <span class='file'>soillines</span>, with the following content:

```
.
|____soillines
| |____images
| | |____LUCAS_460-1050_10_OLS_original-soillines.png
| | |____LUCAS_460-1050_10_OLS_regression-soillines.png
| | |____LUCAS_460-1050_10_OLS_distilled-soillines.png
| |____json
| | |____LUCAS_460-1050_10_OLS_endMembers.json
| | |____LUCAS_460-1050_10_OLS_soillines.json
| | |____LUCAS_460-1050_10_OLS_ortho-endMembers.json
```

###### endMembers.json

The output file _*endMembers.json_ lists the dark soil and light soil spectral end members. The file lists the end members from the three different soil extraction approaches, as illustrated in the excerpt below. Note how the Random Mean Square Error (RMSE) decreases from the "minmax", via the "rawRegression" to the "distillRegression" approaches for soil line retrieval.

```
{
  "verbose": 1,
  "id": "Sweden_LUCAS_460-1050_10_20230808",
  "name": "Sweden_LUCAS_460-1050_10",
  "userId": "thomasg",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignId": "OSSL-LUCAS-SE",
    "campaignShortId": "OSSL-LUCAS-SE",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json",
    "yband": 1025
  },
  "model": {
    "regressor": "OLS",
    "distillStd": 1.0
  },
  "content": "soil spectral endMembers",
  "endMembers": {
    "minmax": {
      "465": {
        "darkSoil": 0.029314,
        "lightSoil": 0.328138,
        "rmse": 0.0511
      },
      ...
      ...
      "1045": {
        "darkSoil": 0.07030199999999986,
        "lightSoil": 0.6535560000000054,
        "rmse": 0.0031
      }
    },
    "rawRegression": {
      "465": {
        "darkSoil": 0.029314,
        "lightSoil": 0.328138,
        "rmse": 0.0493
      },
      ...
      ...
      "1045": {
        "darkSoil": 0.07030199999999986,
        "lightSoil": 0.6535560000000054,
        "rmse": 0.0016
      }
    },
    "distilledRegression": {
      "465": {
        "darkSoil": 0.034277999999999996,
        "lightSoil": 0.30857799999999996,
        "rmse": 0.0241
      },
      ...
      ...
      "1045": {
        "darkSoil": 0.23008999999999968,
        "lightSoil": 0.5794780000000017,
        "rmse": 0.0007
      }
    }
  }
}
```

###### soillines.json

Also the output file _*soillines.json_ lists the result from the three different approaches of soil line extraction.

```
{
  "verbose": 1,
  "id": "Sweden_LUCAS_460-1050_10_20230808",
  "name": "Sweden_LUCAS_460-1050_10",
  "userId": "thomasg",
  "importVersion": "OSSL-202308",
  "campaign": {
    "campaignId": "OSSL-LUCAS-SE",
    "campaignShortId": "OSSL-LUCAS-SE",
    "campaignType": "laboratory",
    "theme": "soil",
    "product": "diffuse reflectance",
    "units": "fraction",
    "geoRegion": "Sweden"
  },
  "input": {
    "jsonSpectraDataFilePath": "/Users/thomasgumbricht/docs-local/OSSL/Sweden/LUCAS/arranged-data/visnir/data-visnir_LUCAS_460-1050_10.json",
    "yband": 1025
  },
  "model": {
    "regressor": "OLS",
    "distillStd": 1.0
  },
  "endMemberLibrary": {
    "minmax": {
      "nSamples": 4121,
      "soilLines": [
        {
          "465": {
            "m": 1.0443,
            "c": 0.2328,
            "r2": 0.6272,
            "rmse": 0.0511,
            "regression": "refband[1025] = 1.044*band[465] + 0.233"
          },
          ...
          ...
          "1045": {
            "m": 0.9984,
            "c": -0.0014,
            "r2": 0.9987,
            "rmse": 0.0031,
            "regression": "refband[1025] = 0.998*band[1045] + -0.001"
          }
        }
      ]
    },
    "rawRegression": {
      "nSamples": 4121,
      "soilLines": {
        "465": {
          "m": 1.2373,
          "c": 0.2163,
          "r2": 0.6525,
          "rmse": 0.0493,
          "regression": "refband[1025] = 1.237*band[465] + 0.216"
        },
        ...
        ...
        "1045": {
          "m": 1.0019,
          "c": -0.0053,
          "r2": 0.9996,
          "rmse": 0.0016,
          "regression": "refband[1025] = 1.002*band[1045] + -0.005"
        }
      }
    },
    "distilledRegression": {
      "nSamples": 2178,
      "soilLines": {
        "465": {
          "m": 1.3305,
          "c": 0.2017,
          "r2": 0.8872,
          "rmse": 0.0241,
          "regression": "refband[1025] = 1.331*band[465] + 0.202"
        },
        ...
        ...
        "1045": {
          "m": 1.0045,
          "c": -0.0062,
          "r2": 0.9999,
          "rmse": 0.0007,
          "regression": "refband[1025] = 1.005*band[1045] + -0.006"
        }
      }
    }
  }
}
```

###### ortho-endMembers.json

The last output file, _*ortho-endMembers.json_, contains the same information as _*endMembers.json_ and _*soillines.json_ but reformatted to use by subsequent scripts.
