---
layout: post
title: OSSL VISNIR extracter
categories: libspectrodata
excerpt: "Data to extract for modeling from OSS VISNIR"
tags:
  - OSSL
  - soil
  - spectroscopy
  - Vis
  - NIR
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2022-09-06 11:27'
modified: '2022-09-06 11:27'
comments: true
share: true
---

### Introduction

This post lists the columns to extract for modeling.

###### soilsite.data.csv

| Column nr | Column id | tg interpretation | Comment |
| 1 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 12  | longitude_wgs84_dd | longitude in DD | - |
| 13 | latitude_wgs84_dd | latitude in DD | - |
| 31 | layer.upper.depth_usda_cm | sample min depth (cm)| - |
| 32 | layer.lower.depth_usda_cm | sample max depth (cm)| - |
| 40 | id_vis | Boolean indicator (TRUE/FALSE) | - |

###### soillab.data.csv

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 1 | id.layer_local_c | layer id | database link |
| 12 | ca.ext_usda.4b1_cmolkg | exchangeable Ca ? | - |
| 13 | c.tot_usda.4h2_wpct | total C ?  | - |
| 14 | caco3_usda.4e1_wpct  | CaCO3 (weight percent)? | - |
| 15 | cec.ext_usda.4b1_cmolkg | Cation Exchange Capacity | ? |
| 16 | clay.tot_usda.3a1_wpct | Clay content (weight percent)?  | - |
| 18 | ecec_usda.4b4_cmolkg | Effective Cation Exchange Capacity | - |
| 19 | ec.w_usda.4f1_dsm | Electric conductivity ? | ? |
| 20 | oc_usda.calc_wpct | Organic Carbon (Weight percent)? | - |
| 22 | fe.kcl_usda.4b3_mgkg | KCl extractable Fe (mg/kg)? | - |
| 24 | mg.ext_usda.4b1_cmolkg | extractable Mg | - |
| 25 | n.tot_usda.4h2_wpct | total N | - |
| 27 | ph.h2o_usda.4c1_index | water extraced pH | - |
| 30 | p.ext_usda.4d6_mgkg | Extractable P | - |
| 32 | k.ext_usda.4b1_cmolkg | Extractable K ? | - |
| 33 | sand.tot_usda.3a1_wpct | Weight percent sand | - |
| 35 | silt.tot_usda.3a1_wpct | Weight percent silt | - |
| 36 | na.ext_usda.4b1_cmolkg | Extractable Na | - |

###### visnir.data.csv

| Column nr | Column id | tg interpretation | Comment |
| 0 | id.scan_uuid_c | sample id (1 per depth) | database link |
| 1 | id.scan_local_c | layer id | database link |
| 2 | id.layer_uuid_c | sample id (1 per depth) | database link |
| 3 | id.layer_local_c | ? | - |
| 15 | scan_visnir.350_pcnt | reflectance (%) @ 350 nm | - |
| 16 | scan_visnir.352_pcnt | reflectance (%) @ 352 nm | - |
| ...  | scan_visnir.xx_pcnt | reflectance (%) @ xx nm  | - |
| 1091 | scan_visnir.2500_pcnt | reflectance (%) @ 2500 nm | - |
