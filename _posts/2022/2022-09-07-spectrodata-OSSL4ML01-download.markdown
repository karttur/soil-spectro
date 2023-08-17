---
layout: post
title: Download OSSL data
categories: libspectrodata
excerpt: "Download OSSL data over a selected region to use for Machine Learning modelling, the post uses Sweden as an example"
tags:
  - OSSL
  - api
  - explorer
  - download
  - Sweden
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-07 11:27'
modified: '2023-07-02 11:27'
comments: true
share: true
---

### Introduction

This post builds on the two previous posts in this blog: [Open Soil Spectral Library (OSSL)](../spectrodata-OSSL-intro) and [OSSL explorer API](../spectrodata-OSSL-api-explorer). In this post you will download selected OSSL data  that will then be used in the following posts for Machine Learning modelling. The data used as an example is Near-InfraRed (NIR) OSSL data over Sweden. You can change the study area by selecting any other geographical region, select via soil textures (attributes) or source dataset as outlined in the [previous](../spectrodata-OSSL-api-explorer) post.  

### Select OSSL data

The [previous](../spectrodata-OSSL-api-explorer) post illustrates how to access OSSL data using the [OSSL API Explorer](https://explorer.soilspectroscopy.org). In this example the selection was set to Sweden as illustrated in the figure below.

<figure>
<img src="../../images/OSSL_explorer_020_select-sweden.png">
<figcaption>Datasets returned with search set to nation = Sweden.</figcaption>
</figure>

For most country searches you can select sub-regions, in the example of Sweden (figure above) the data is divided into counties.

![se-dataset-selection](../../images/OSSL_explorer_021_se-dataset-selection.png)
{: .pull-left}
You can also select the dataset(s) to extract from the selected region by clicking the <span class='button'>Dataset</span> button in the Data selection window. The Swedish OSSL data stem from three different datasets, ICRAF-ISRIC, LUCAS-WOODWELL.SSL and LUCAS.SSL. [LUCAS](https://land.copernicus.eu/imagery-in-situ/lucas) is a European wide soil survey that is repeated every three years. The ICRAF-ISRIC dataset focuses on the Mid-Infrared  (MIR) spectral regions and will not be used, also because it only contains 5 sample sites in Sweden. The LUCAS-WOODWELL.SSL only contains a single sample site over Sweden, including or omitting it will not really influence the modelling.

### Select NeoSpectra data

If you want to analyse and evaluate the predictive power of the [NeoSpectra](https://www.si-ware.com) advanced handheld NIR field spectrometer mentioned in the [previous](../spectrodata-OSSL-api-explorer) post, select the NEOSPECTRA.SSL as Dataset from the global (or USA) geographical region.

<figure>
<img src="../../images/OSSL_explorer_022_select-neospectra.png">
<figcaption>Datasets returned with search set to dataset = NEOPSECTRA.SLL.</figcaption>
</figure>

### Download OSSL data

Download your selected OSSL data by clicking the <span class='button'>Download now</span> button. The data will be compressed to a <span class='file'>zip</span> and then download. Expand the <span class='file'>zip</span> file and you should get five <span class='file'>csv</span> files:

- mir.data.csv [MIR spectral scans]
- neon.data.csv [NeoSpectra spectral scans]
- soillab.data.csv [soil laboratory (wet chemistry) data]
- soilsite.data.csv [sample site data]
- visnir.data.csv [VIS-NIR spectral scans (excluding NeoSpectra)]

### Arranging OSSL data

In the [next](../spectrodata-OSSL4ML02-arrange/) post you will use a python script to arrange (import) the data from the <span class='file'>csv</span> files into a combined <span class='file'>json</span> file. To keep your data in order I suggest that you create a dedicated folder (directory) under your user where you keep the OSSL data that you download. In my system I have a folder called <span class='file'>OSSL</span> and in that root folder I create subfolders with regional and/or thematic names indicating the OSSL data that I downloaded. I save the downloaded file, that is always called <span class='file'>data.zip</span>, in that folder, then I unzip the file and the 5 <span class='file'>csv</span> files will end up in a subfolder called <span class='file'>data</span>. Do not rename the downloaded and unzipped files if you want to use the Pythons cripts in the following posts. My OSSL folder now looks like this:

```
.
|____Sweden
| |____LUCAS
| | |____data.zip
| | |____data
| | | |____visnir.data.csv
| | | |____neon.data.csv
| | | |____soillab.data.csv
| | | |____mir.data.csv
| | | |____soilsite.data.csv
|____Uganda
| |____data.zip
| |____data
| | |____visnir.data.csv
| | |____soillab.data.csv
| | |____mir.data.csv
| | |____soilsite.data.csv
|____USA
| |____NeoSpectra
| | |____data.zip
| | |____data
| | | |____visnir.data.csv
| | | |____neon.data.csv
| | | |____soillab.data.csv
| | | |____mir.data.csv
| | | |____soilsite.data.csv
```
