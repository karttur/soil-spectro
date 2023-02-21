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
date: '2022-09-04 11:27'
modified: '2022-09-04 11:27'
comments: true
share: true
---

### Introduction

This post is a quick manual for how to access data from the [Open Soil Spectral Library (OSSL)](https://explorer.soilspectroscopy.org) via the OSSL [API explorer](https://explorer.soilspectroscopy.org). A short general introduction and links to other OSSL recourses can be found in the [previous](../spectrodata-OSSL-intro) post.

### OSSL API

The OSSL online API contains two entries, 1) an engine for entering data into the database and 2) an [Explorer](https://explorer.soilspectroscopy.org) for searching and retrieving data.

#### OSSL Explorer

Open the [OSSL Explorer](https://explorer.soilspectroscopy.org). The application might take a while for the data to load.

<figure>
<img src="../../images/OSSL_explorer_001_load.png">
<figcaption>OSSL loading data on startup.</figcaption>
</figure>

When loaded the OSSL explorer opens with the main window showing Geographic distribution of data. The main view is, however, partly covered and you have to option to scroll over the latest set of tweets related to OSSL. Close the tweets window to continue. If you want to see the tweets again, click the little twitter bird in the top menu.

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
In the lower left corner of the map (Below South America if you have the global default view) you can see the number of samples and spectra available. The spectral data is divided into visible (V or VIS) plus Near InfraRed (NIR) (summed as VNIR), and Mid InfraRed (MIR).

![data-selection](../../images/OSSL_explorer_008_data-selection.png)
{: .pull-right}
In the _Data selection_ window you can add search requests both related to geographic regions (interactively on the map or by stating a country), and to different soil and sample attributes. The map below shows the result when searching for data in country = Uganda.

<figure>
<img src="../../images/OSSL_explorer_009_country-uganda.png">
<figcaption>Datasets returned with search set to nation = Uganda.</figcaption>
</figure>

##### Data selection

In the _Data selection_ window you can directly enter search criteria for _Attributes_ and _Dataset_. Both refer to nominal string categories (i.e. names of soil texture and name of data acquisition program) and you need to have a-priori knowledge about the datasets to enter (or select) the appropriate variable.

If you click the <span class='button'>Soil properties</span> button in the Data selection window, the main view will switch from _Geography_ to _Soil properties_.

In the _Soil properties_ view_ you can select and view the distribution of up to 4 different properties for the present geographic search.

<figure>
<img src="../../images/OSSL_explorer_011_country-uganda-soil-properties-distribution.png">
<figcaption>Exploring the distribution of soil properties in the Soil properties view.</figcaption>
</figure>

![data-selection](../../images/OSSL_explorer_012_download-now-btn.png)
{: .pull-right}
When you have fine-tuned your search you can download the selected dataset by clicking the <span class='button'>Download now</span> button.

##### OSSL Explorer download

Data downloaded from the [OSSL Explorer](https://explorer.soilspectroscopy.org) is deliverad as a <span class='file'>zip</span> file. When unzipped, 4 files are created, in alphabetic oder:

- mir.data.csv
- soillab.data.csv
- soilsite.data.csv
- visnir.data.csv

The coding of the data is available at [https://soilspectroscopy.github.io/ossl-manual/030-database_description](https://soilspectroscopy.github.io/ossl-manual/030-database_description). Note that the coding has changed substantially over the past months, and before using any metadata documentation you need to make sure that you have data and metadata that corresponds.

###### soilsite.data.csv (version valid 20220907, outdated)

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.location_olc_c | location id | pit or plot |
| 1 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 2 | observation.ogc.schema.title_ogc_txt | dataset origin organisation | - |
| 3 | observation.ogc.schema_idn_url | dataset origin url | - |
| 4 | observation.date.begin_iso.8601_yyyy.mm.dd | first observation date |   | - |
| 5 | observation.date.end_iso.8601_yyyy.mm.dd | last observation date | - |
| 6 | location.address_utf8_txt | postal address (often missing) | - |
| 7 | location.country_iso.3166_c | country code (often missing) | - |
| 8 | location.method_any_c | location determination | usually GPS |
| 9 | surveyor.title_utf8_txt | name of observer | - |
| 10 | surveyor.contact_ietf_email | observer email | - |
| 11  | surveyor.address_utf8_txt | observer postal address | -  |
| 12  | longitude_wgs84_dd | longitude in DD | - |
| 13 | latitude_wgs84_dd | latitude in DD | - |
| 14 | location.error_any_m | position error in m  | - |
| 15 | dataset.title_utf8_txt | dataset title | - |
| 16 | dataset.owner_utf8_txt |  dataset owner | - |
| 17 | dataset.code_ascii_c | dataset code/abbreviation | - |
| 18 | dataset.address_idn_url | dataset url | - |
| 19 | dataset.license.title_ascii_txt | dataset license | - |
| 20 | dataset.license.address_idn_url | license url | - |
| 21 | dataset.doi_idf_c | doi | dataset Digital Object Identifier |
| 22 | dataset.contact.name_utf8_txt | dataset contact person | - |
| 23 | dataset.contact_ietf_email | dataset contact person email  | - |
| 24 | id.project_ascii_c | dataset project |   |
| 25 | id.user.site_ascii_c | dataset project site id | - |
| 26 | pedon.taxa_usda_c | soil type  | - |
| 27 | pedon.completeness_usda_uint8 | ? | - |
| 28 | layer.sequence_usda_uint16 | ? | - |
| 29 | layer.type_usda_c | ? | - |
| 30 | layer.field.label_any_c | ? | - |
| 31 | layer.upper.depth_usda_cm | sample min depth (cm)| - |
| 32 | layer.lower.depth_usda_cm | sample max depth (cm)| - |
| 33 | horizon.designation_usda_c | soil horizon name | - |
| 34 | horizon.designation.discontinuity_usda_c | ? | - |
| 35 | layer.structure.type_usda_c | ? | - |
| 36 | layer.structure.grade_usda_c | ? | - |
| 37 | layer.texture_usda_c | ? | - |
| 38 | id.layer_local_c | layer id | - |
| 39 | id_mir | Boolean indicator (TRUE/FALSE) | - |
| 40 | id_vis | Boolean indicator (TRUE/FALSE) | - |

###### soillab.data.csv

See [https://soilspectroscopy.github.io/ossl-manual/database.html#soillab-table](https://soilspectroscopy.github.io/ossl-manual/database.html#soillab-table)

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 1 | id.layer_local_c | layer id | database link |
| 2 | sample.doi_idf_c | sample Digital Object Identifier  | cf dataset.doi_idf_c in soilsite|
| 3 | sample.contact.name_utf8_txt | sample contact person | - |
| 4 | sample.contact.email_ietf_email | sample contact person email | - |
| 5 | acid.tea_usda4b2_cmolkg | pH? | unclear |
| 6 | al.dith_usda.4g1_wpct | Al, dithionite-citrate extractable | ? |
| 7 | al.kcl_usda.4b3_cmolkg | Al, KCl extractable | - |
| 8 | al.ox_usda.4g2_wpct | Al, ammonium oxalate extractable| ? |
| 9 | bsat_usda.4b4_wpct | Base saturation | - |
| 10 | bd.clod_usda.3b1_gcm3 | Bulk density clod | - |
| 11 | bd.od_usda.3b2_gcm3 | Bulk density fine earth, Oven-Dry | - |
| 12 | ca.ext_usda.4b1_cmolkg | Ca, NH4OAc Extractable | - |
| 13 | c.tot_usda.4h2_wpct | Total C | - |
| 14 | caco3_usda.4e1_wpct  | CaCO3 (weight percent) | - |
| 15 | cec.ext_usda.4b1_cmolkg | Cation Exchange Capacity | ? |
| 16 | clay.tot_usda.3a1_wpct | Clay content (weight percent)?  | - |
| 17 | gyp_usda.4e2_wpct | Corrected Gypsum (weight percent) | - |
| 18 | ecec_usda.4b4_cmolkg | Effective Cation Exchange Capacity | - |
| 19 | ec.w_usda.4f1_dsm | Electric conductivity | ? |
| 20 | oc_usda.calc_wpct | Organic Carbon (Weight percent)? | - |
| 21 | fe.dith_usda.4g1_wpct | Fe, dithionite-citrate extractable ? | - |
| 22 | fe.kcl_usda.4b3_mgkg | KCl extractable Fe (mg/kg)? | - |
| 23 | fe.ox_usda.4g2_wpct | Fe, ammonium oxalate extractable | - |
| 24 | mg.ext_usda.4b1_cmolkg | extractable Mg | - |
| 25 | n.tot_usda.4h2_wpct | total N | - |
| 26 | ph.kcl_usda.4c1_index | KCl pH ? | - |
| 27 | ph.h2o_usda.4c1_index | water extraced pH | - |
| 28 | ph.cacl2_usda.4c1_index | pH ? | - |
| 29 | ph.naf_usda.4c1_index | pH ? | - |
| 30 | p.ext_usda.4d6_mgkg | Extractable P | - |
| 31 | p.olsn_usda.4d5_mgkg | P ? | - |
| 32 | k.ext_usda.4b1_cmolkg | Extractable K ? | - |
| 33 | sand.tot_usda.3a1_wpct | Weight percent sand | - |
| 34 | wpg2_usda.3a2_wpct | ? | - |
| 35 | silt.tot_usda.3a1_wpct | Weight percent silt | - |
| 36 | na.ext_usda.4b1_cmolkg | Extractable Na | - |
| 37 | s.tot_usda.4h2_wpct | ? | - |
| 38 | sum.bases_4b4b2a_cmolkg | ? | - |
| 39 | wr.33kbar_usda.3c1_wpct | ? | - |
| 40 | wr.1500kbar_usda.3c2_wpct | ? | - |
| 41 | al.meh3_usda.4d6_wpct | ? | - |
| 42 | as.meh3_usda.4d6_mgkg | ? | - |
| 43 | ba.meh3_usda.4d6_mgkg | ? | - |
| 44 | ca.meh3_usda.4d6_mgkg | ? | - |
| 45 | cd.meh3_usda.4d6_wpct | ? | - |
| 46 | co.meh3_usda.4d6_mgkg | ? | - |
| 47 | cr.meh3_usda.4d6_mgkg | ? | - |
| 48 | cu.meh3_usda.4d6_mgkg | ? | - |
| 49 | p.meh3_usda.4d6_mgkg | ? | - |
| 50 | k.meh3_usda.4d6_mgkg | ? | - |
| 51 | na.meh3_usda.4d6_mgkg | ? | - |
| 52 | mg.meh3_usda.4d6_mgkg | ? | - |
| 53 | fe.meh3_usda.4d6_mgkg | ? | - |
| 54 | pb.meh3_usda.4d6_mgkg | ? | - |
| 55 | zn.meh3_usda.4d6_mgkg | ? | - |
| 56 | mo.meh3_usda.4d6_mgkg | ? | - |
| 57 | si.meh3_usda.4d6_mgkg | ? | - |
| 58 | sr.meh3_usda.4d6_mgkg | ? | - |

###### visnir.data.csv

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.scan_uuid_c | sample id (1 per depth) | database link |
| 1 | id.scan_local_c | layer id | database link |
| 2 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 3 | id.layer_local_c | ? | - |
| 4 | model.name_utf8_txt | ? | - |
| 5 | model.code_any_c | spectrometer model | - |
| 6 | method.light.source_any_c | light source model | - |
| 7 | method.preparation_any_c | sample preparation | - |
| 8 | scan.file_any_c | ? | - |
| 9 | scan.date.begin_iso.8601_yyyy.mm.dd | first date of scan | - |
| 10 | scan.date.end_iso.8601_yyyy.mm.dd | last date of scan | - |
| 11 | scan.license.title_ascii_txt | scan license | - |
| 12 | scan.license.address_idn_url | license url | - |
| 13 | scan.contact.name_utf8_txt | scanner name | - |
| 14 | scan.contact.email_ietf_email | scanner email | - |
| 15 | scan_visnir.350_pcnt | reflectance (%) @ 350 nm | - |
| 16 | scan_visnir.352_pcnt | reflectance (%) @ 352 nm | - |
| ...  | scan_visnir.xx_pcnt | reflectance (%) @ xx nm  | - |
| 1091 | scan_visnir.2500_pcnt | reflectance (%) @ 2500 nm | - |

###### mir.data.csv

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.scan_uuid_c | scan id (1 per depth) | database link |
| 1 | id.scan_local_c | scan local id | database link |
| 2 | id.layer_uuid_c | layer id (1 per depth) | database link |
| 3 | id.layer_local_c | ? | - |
| 4 | model.name_utf8_txt | ? | - |
| 5 | model.code_any_c | spectrometer model | - |
| 6 | method.light.source_any_c | light source model | - |
| 7 | method.preparation_any_c | sample preparation | - |
| 8 | scan.file_any_c | ? | - |
| 9 | scan.date.begin_iso.8601_yyyy.mm.dd | first date of scan | - |
| 10 | scan.date.end_iso.8601_yyyy.mm.dd | last date of scan | - |
| 11 | scan.license.title_ascii_txt | scan license | - |
| 12 | scan.license.address_idn_url | license url | - |
| 13 | scan.contact.name_utf8_txt | scanner name | - |
| 14 | scan.contact.email_ietf_email | scanner email | - |
| 15 | scan_mir.600_abs | absolut spectra @ 600 nm | - |
| 16 | scan_mir.602_pcnt | absolut spectra @ 602 nm | - |
| ...  | scan_mir.xx_pcnt | absolut spectra @ xx nm  | - |
| 1716 | scan_mir.4000_pcnt | absolut spectra @ 4000 nm | - |
