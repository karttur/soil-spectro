---
layout: post
title: Clone the OSSL python package
categories: libspectrosupport
excerpt: "Clone or download the OSSL python package from GitHub"
tags:
  - Python
  - GitHub
  - OSSL
  - clone
  - download
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-09-06 11:27'
modified: '2023-09-06 11:27'
comments: true
share: true
---

The python modules of the OSSL python package are setup for a virtual python environment with the first line of each module pointing towards this environment:
```
#!/Applications/anaconda3/envs/spectraimagine_py38/bin/python3.8
```
This must be edited to reflect your python setup before running the modules.

Before you can run the modules you probably also have to set execution rights on your local machine. In MacOS and Linux you do that with the <span class='terminalapp'>chmod</span> (change mode) command, for example:

 <span class='terminal'>chmod OSSL_import.py 755</span>

### Introduction

The python scripts for [importing](../../libspectrodata/spectrodata-OSSL4ML02-arrange), [plotting](../../libspectrodata/spectrodata-OSSL4ML03-plot) and [modelling](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01) OSSL data are available for cloning or downloading from [GitHub](https://github.com). GitHub is an online service for working with, and sharing, program codes using [Git](https://www.atlassian.com/git/tutorials/what-is-git) version control. You do not need to use GitHub or git as such to access the OSSL python package. You can simply [download the OSSL package from GitHub](https://github.com/karttur/OSSL-pydev) and not bother about neither GitHub nor Git.

But if you want to easily follow any updates in the online available code you should install git/GitHub and then clone (rather than download) the OSSL package. if there are any updates you can later _pull_ these from the online repository and they will immediately take effect in your local clone.

If you want to use Git for cloning and managing codes, you need to either [Install git for command line](https://karttur.github.io/git-vcs/git/git-commandline-install/) or download and install [GitHub Desktop](https://desktop.github.com). I have written a separate ["blog" on using Git](https://karttur.github.io/git-vcs/) to get started.

In this post I will assume that you know how to use <span class='terminalapp'>git</span> or <span class='app'>GitHub Desktop</span> if you want to clone the OSSL python package. If not, I suggest you simply just download the [OSSL-pydev repo](https://github.com/karttur/OSSL-pydev) as described in the next section.

### Download OSSL-pydev from GitHub

To download the OSSL-pydev repository to your local machine, open a browser and navigate to [https://github.com/karttur/OSSL-pydev](https://github.com/karttur/OSSL-pydev). Click the green <span class='button'>Code</span> button to get the options for accessing the repo data and code (figure 1).

<figure>
  <img src="../../images/GitHub_OSSL-pydev_front.png" alt="image">
	<figcaption>Figure 1. Navigate to https://github.com/karttur/OSSL-pydev, click the code button to see the options for accessing the data and code.</figcaption>
</figure>

To download the data directly, just click the _Download ZIP_ options.

When the ZIP file is completely downloaded, move it to the destination where you want to have it. Unzip it.

If you are conversant with using Python and your system is set up with Python, you can jump straight to the set of posts in this blog on the [Open Soil Spectral Library](../../libspectrodata/spectrodata-OSSL-intro) and how to [explore](../../libspectrodata/spectrodata-OSSL-api-explorer), [download](../../libspectrodata/spectrodata-OSSL4ML01-download), [import](../../libspectrodata/spectrodata-OSSL4ML02-arrange), [plot](../../libspectrodata/spectrodata-OSSL4ML03-plot) and [model](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01) the OSSL data.

Remember that the python modules of the OSSL python package are setup for a virtual python environment with the first line of each module pointing towards this environment:
```
#!/Applications/anaconda3/envs/spectraimagine_py38/bin/python3.8
```
This must be edited to reflect your python setup before running the modules.

### Example dataset

To get started you can use the prepared OSSL example dataset available in the GitHub repository [https://github.com/karttur/OSSL-data](https://github.com/karttur/OSSL-data). The example data uses the LUCAS Near Infrared (NIR) dataset over Sweden. Download, move and unzip as described above. To edit the files and paths of the example dataset to fit you machine and local setup see the post on [Run ossl-xspectre modules](../spectrosupport-OSSL-run) or go to the [full series of instructions](../../libspectrodata/).

### Setup a Python development environment

If you are beginner in Python or want to have a Graphical User Interface (GUI) with support for editing codes, proceed with the next two posts in the "Python package" section. The first will guide you through installing and setup [<span class='app'>Anaconda</span>](../libspectrosupport/spectrosupport-OSSL-anaconda) for defining your python environment. The second is an instruction for how to install and setup the Integrated Development Environment (IDE) [<span class='app'>Eclipse</span>](../libspectrosupport/spectrosupport-OSSL-eclipse). After finishing these two posts you should have a working python environment for running the python package _ossl-xspectre_.
