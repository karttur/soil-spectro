---
layout: post
title: OSSL explorer API
categories: libspectrodata
excerpt: "A short introduction to the Open Soil Spectral Library (OSSL)"
tags:
  - OSSL
  - api
  - explorer
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-05 11:27'
modified: '2023-07-02 11:27'
comments: true
share: true
---

### Introduction

This post is a quick manual for how to access data from the [Open Soil Spectral Library (OSSL)](https://soilspectroscopy.org) via the OSSL [API explorer](https://explorer.soilspectroscopy.org). A short general introduction and links to other OSSL recourses can be found in the [previous](../spectrodata-OSSL-intro) post.

### OSSL API

The online API contains two entries, 1) an [Engine](https://engine.soilspectroscopy.org) for entering data into the database and 2) an [Explorer](https://explorer.soilspectroscopy.org) for searching and retrieving data.

#### OSSL Explorer

Open the [OSSL Explorer](https://explorer.soilspectroscopy.org). The application might take a while for the data to load.

<figure>
<img src="../../images/OSSL_explorer_001_load.png">
<figcaption>OSSL loading data on startup.</figcaption>
</figure>

When loaded, the OSSL explorer opens with the main window showing Geographic distribution of data. The main view is, however, partly covered and you have to option to scroll over the latest set of tweets related to OSSL. Close the tweets window to continue. If you want to see the tweets again, click the little twitter bird in the top menu.

<figure>
<img src="../../images/OSSL_explorer_002_tweet.png">
<figcaption>Many tweets contain links to interesting applications.</figcaption>
</figure>

There is also an information window. Close it when finished with it. If, for any reason, you want to read the information again, click the little info symbol in the top menu.

<figure>
<img src="../../images/OSSL_explorer_003_info+disclaimer.png">
<figcaption>OSSL information window.</figcaption>
</figure>

![map-layer-icon](../../images/OSSL_explorer_005_map-layer-icon.png)
{: .pull-right}
To change the background map, click the map layer icon in the top right corner of the map area.

<figure>
<img src="../../images/OSSL_explorer_006_map-layer-change.png">
<figcaption>Change the background map.</figcaption>
</figure>

![nr-datasets](../../images/OSSL_explorer_007_nr-datasets.png)
{: .pull-left}
In the lower left corner of the map (Below South America if you have the global default view) you can see the number of samples and spectra available. The spectral data is divided into visible (V or VIS) plus Near InfraRed (NIR) (summed as VNIR), and Mid InfraRed (MIR). The number of geographic data points in September 2022 was 114619, with NIR and MIR contributing roughly equal sites.

![data-selection](../../images/OSSL_OSSL_explorer_007b_nr-datasets-20230802.png)
{: .pull-left}
On august 1, 2023 the number of total samples are instead reported to be 88867. A novel dataset has then also been added - the NIR NeoSpectra encompassing 1760 sample sites over the USA.
[NeoSpectra](https://www.si-ware.com) is an advanced handheld NIR field spectrometer (1350 - 2500 nm) developed by the company [SI-ware](https://www.si-ware.com). At time of writing this post a [NeoSpectra scanner is prized at $6950](https://shop.si-ware.com) excluding accessories.

![data-selection](../../images/OSSL_explorer_008_data-selection.png)
{: .pull-right}
##### Data selection

In the _Data selection_ window you can add search requests both related to geographic regions (interactively on the map or by stating a country), to different soil and sample attributes and to different datasets.

##### Geospatial selection

![geospatial-selection](../../images/OSSL_explorer_008a_geospatial-selection.png)
{: .pull-left}
If you select the _Geospatial option_ in the Data Selection window you have the options to draw a region on the map or select a nation. The map below shows the result when searching for data in country = Uganda.

<figure>
<img src="../../images/OSSL_explorer_009_country-uganda.png">
<figcaption>Datasets returned with search set to nation = Uganda.</figcaption>
</figure>


##### Attributes selection

![attribute-selection](../../images/OSSL_explorer_008b_attributes-selection.png)
{: .pull-left}
The <span class='button'>Attributes</span> selection option can be used for selection soil textures. You can chose the soil texture class to select from a drop-down menu by clicking the text field for Soil texture. Search multiple classes by sequentially clicking the options in the drop-down menu. The classes are not globally harmonized thus the classification depends on the geographical region.
<figure>
<img src="../../images/whitespace.png">
</figure>

##### Dataset selection

![dataset-selection](../../images/OSSL_explorer_008c_dataset-selection.png)
{: .pull-left}
In a similar manner you can also select the source dataset(s) to search.
<figure>
<img src="../../images/whitespace.png">
</figure>

##### Soil properties selection

The last option is to select a subset of data using soil properties criteria. When you click the <span class='button'>Soil properties</span> button in the Data selection window, the main view will switch from _Geography_ to _Soil properties_. In the _Soil properties_ view you can select and view the distribution of up to 4 different properties for the present geographic search.

<figure>
<img src="../../images/OSSL_explorer_011_country-uganda-soil-properties-distribution.png">
<figcaption>Exploring the distribution of soil properties in the Soil properties view.</figcaption>
</figure>

![data-selection](../../images/OSSL_explorer_012_download-now-btn.png)
{: .pull-right}
When you have fine-tuned your search you can download the selected dataset by clicking the <span class='button'>Download now</span> button (or restart the selection by clicking _Clear Selection_).

##### OSSL Explorer download

Data downloaded from the [OSSL Explorer](https://explorer.soilspectroscopy.org) is delivered as a <span class='file'>zip</span> file. When unzipped, 4 files are created, in alphabetic order:

- mir.data.csv [MIR spectral scans]
- neon.data.csv [NeoSpectra spectral scans]
- soillab.data.csv [soil laboratory (wet chemistry) data]
- soilsite.data.csv [sample site data]
- visnir.data.csv [VIS-NIR spectral scans (excluding NeoSpectra)]

The coding of the data is available at [https://soilspectroscopy.github.io/ossl-manual/030-database_description](https://soilspectroscopy.github.io/ossl-manual/030-database_description). Note that the coding is changing from time to time, and before using any metadata documentation you need to make sure that you have data and metadata that corresponds.

###### soilsite.data.csv

The [main manual page](https://soilspectroscopy.github.io/ossl-manual/030-database_description#sites-table) lists all the site data in text format; the same data is also  replicated in [tabular form](https://github.com/soilspectroscopy/ossl-manual/blob/main/tabular/ossl_level0_names_soilsite.csv).

###### soillab.data.csv

The soil laboratory (wet chemistry) data is available in two versions: level 0 and level 1. Level 0 contains all the data whereas level 1 is more restricted. The [main page](https://soilspectroscopy.github.io/ossl-manual/030-database_description#soillab-table) lists both versions indicating which are only available at level 0. The tabular version is divided in two separate tables [level 0](https://github.com/soilspectroscopy/ossl-manual/blob/main/tabular/ossl_level0_names_soillab.csv) and [level 1](https://github.com/soilspectroscopy/ossl-manual/blob/main/tabular/ossl_level1_names_soillab.csv).

###### visnir.data.csv

The visible to near-inrared (visnir) spectral data is also available in [text format](https://soilspectroscopy.github.io/ossl-manual/030-database_description#visnir-table) and in [tabular form](https://github.com/soilspectroscopy/ossl-manual/blob/main/tabular/ossl_level0_names_visnir.csv).

###### mir.data.csv

The visible to near-inrared (visnir) spectral data is also available in [text format](https://soilspectroscopy.github.io/ossl-manual/030-database_description#mir-table) and in [tabular form](https://github.com/soilspectroscopy/ossl-manual/blob/main/tabular/ossl_level0_names_mir.csv).
