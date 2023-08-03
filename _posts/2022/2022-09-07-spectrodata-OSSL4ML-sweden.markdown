---
layout: post
title: Arranging OSSL data
categories: libspectrodata
excerpt: "Downloading and organizing OSSL data for Machine Learning modeling in Python, using Sweden as an example"
tags:
  - OSSL
  - api
  - explorer
  - organizing
  - Sweden
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-07 11:27'
modified: '2023-07-02 11:27'
comments: true
share: true
---

### Introduction

This post builds on the two previous posts in this blog: [Open Soil Spectral Library (OSSL)](../spectrodata-OSSL-intro) and [OSSL explorer API](../spectrodata-OSSL-api-explorer). In this post you will access selected OSSL data and organize that data into a single matrix that can be imported to Python for Machine Learning modelling. The data used as an example is Near-InfraRed (NIR) OSSL data over Sweden. You can change the study area by selecting any other geographical region, select via soil textures (attributes) or source dataset as outlined in the [previous](../spectrodata-OSSL-api-explorer) post.  

### Prerequisites



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

<figure>
<img src="../../images/whitespace.png">
</figure>
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

####

Arranging the OSSL data into a single table relies on the site uuid identifier for linking the data. In the present version of the OSSL, the uuid column of each table (csv file) is called _id.layer_uuid_txt_. The headers are hardcoded in the script and if the headers change you need to edit directly in the code.
