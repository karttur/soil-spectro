---
layout: post
title: Anaconda
categories: libspectrosupport
excerpt: "Setup Anaconda for defining python environment for the OSSL spectral data processing"
tags:
  - Anaconda
  - Python
  - OSSL
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-09-07 11:27'
modified: '2023-09-17 11:27'
comments: true
share: true
---

### Introduction

To run the python scripts for [importing](../../libspectrodata/spectrodata-OSSL4ML02-arrange), [plotting](../../libspectrodata/spectrodata-OSSL4ML03-plot) and [modelling](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01) OSSL data as described in other posts of this blog you need to have a python environment. This post describes how to setup install and setup [<span class='app'>Anaconda</span>](https://www.anaconda.org) for defining virtual python environments. The instructions are shorthand and written using MacOS. I have also published more detailed instructions for [install Anaconda for MacOS](https://karttur.github.io/setup-ide/setup-ide/install-anaconda/) and a summary for how to [install Eclipse and Anaconda in Ubuntu20](https://karttur.github.io/setup-ide/blog/ubuntu20-setup-spide/).

[<span class='app'>Anaconda</span>](https://www.anaconda.org) is a cross-platform solution for developing virtual python environments. There are also several other solutions available online. You can simply do a google seach on your operating system (_MacOS_, _Linux_, _Windows_) and _python virtual environment_).

### Conda, Anaconda and miniconda

Conda is the core python package manager for both Anaconda and miniconda. Conda is run from a <span class='app'>Terminal</span> window. The difference between [<span class='app'>Anaconda</span>](https://anaconda.org) and [<span class='app'>miniconda</span>](https://docs.conda.io/projects/miniconda/en/latest/) is that Anaconda comes with a more extensive default library and a Graphical User Interface (Navigator). If you are new to Python or setting up environments for Python for the first time, I recommend that you install [<span class='app'>Anaconda</span>](https://anaconda.org).

### Anaconda

[Anaconda](https://anaconda.org) is a free platform for scientific Python. To download Anaconda, visit the [Anaconda homepage](https://www.anaconda.com/download), and look for the download link. In the download page, find the distribution (e.g. Windows, Linux, MacOS). For the OSSL python project you need python 3.8, and thus you should chose the latest Anaconda version for python 3. If there are no versions to chose from, the default in September 2023 is 3, so just go ahead and press the <span class='button'>Download</span> button.

Once downloaded just start the installer and follow the instructions. You might need to accept some license terms or override some security warnings - dependent on your OS. The default destination of the installation is your local user directory. If you want to change the installation destination you can do that as part of the installation process.

From hereon this post will only rely on <span class='app'>terminal</span> commands.

Open a <span class='app'>terminal</span> window and check that <span class='terminalapp'>conda</span> was installed:

<span class='terminal'>% conda -V</span>

```
% conda -V
conda 4.13.0
```

Before setting up the virtual environment for the OSSL package, update both <span class='terminalapp'>conda</span> and [<span class='app'>Anaconda</span>].

<span class='terminal'>% conda update conda</span>

Dependent on your version and OS, there might be updates or not. The following excerpt shows the kind of responses you can get. Once the update is checked and prepared, you get the question whether to proceed or not. Enter _y_ (default) in the terminal to proceed, _n_ to stop.

```
% conda update conda
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

 environment location: /Applications/anaconda3

 added / updated specs:
   - conda

The following packages will be downloaded:

 ...
 ...

The following NEW packages will be INSTALLED:
 ...
 ...

The following packages will be REMOVED:
 ...
 ...

The following packages will be UPDATED:
 ...
 ...

The following packages will be SUPERSEDED by a higher-priority channel:
 ...
 ...

Proceed ([y]/n)?
```

<span class='terminal'>% conda update anaconda</span>

The terminal window reports what can be updated, and again you can chose to proceed or stop the update.

```
% conda update anaconda
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /Applications/anaconda3

  added / updated specs:
    - anaconda

The following packages will be downloaded:
  ...
  ...

The following packages will be UPDATED:
  ...
  ...

Proceed ([y]/n)? y
```

Your terminal indicates which conda environment that is active in the parenthesis "()" that is shown on the text line of the command line, for example like this:

```
(base) your.user@machine ~ %
```

_base_ is the default conda python environment. But we want to create a custom virtual environment where we can [plot](../../libspectrodata/spectrodata-OSSL4ML03-plot) and [model](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01) the OSSL spectra and wet laboratory data.

To create the conda virtual python environment for the OSSL project you can either install the required packages from scratch or download a predefined environment (<span class='file'>.yml</span>) file. In either case the packages that are required, but not a part of the core conda python 3 environments, include:

- matplotlib,
- numpy,
- pandas, and
- scikit-learn

#### Create virtual environment

To setup a new virtual environment from using <span class='terminalapp'>conda</span> you can either
- define default packages in a file called .condarc,
- directly specify packages as part of the creation,
- use an exported configuration (yml) file, or
- set up a default environment and add individual packages afterwards

The last alternative, to install individual packages as a post-process, is not recommended. As the packages are not installed simultaneously, <span class='terminalapp'>conda</span> can not assure the coherence between the installed packages.

Of the four additional packages required, <span class='package'>numpy, pandas and scikit-learn</span> are available in the [Anaconda package list](https://docs.anaconda.com/free/anaconda/reference/packages/pkg-docs/). <span class='package'>matplotlib</span> is instead available via the conda-forge repository.

##### Default packages (.condarc)

To set a default environment you need to have a <span class='file'>.condarc</span> configuration file in you personal folder. For in-depth details on the syntac of <span class='file'>.condarc</span> see the conda page on [Managing environments](https://conda.io/activation).

Create or open the <span class='file'>.condarc</span> file in your home directory. How to do that depends on your OS. In MacOS you can use the terminal text editor <span class='terminalapp'>pico<span>:

<span class='terminal'>% pico .condarc</span>

Then edit the content of <span class='file'>.condarc</span> thus:

```
create_default_packages:
  - matplotlib
  - numpy
  - pandas
  - scikit-learn
channels:
  - conda-forge
  - defaults
```

The [GitHub repo OSSL-pydev](https://github.com/karttur/OSSL-pydev/tree/main/anaconda) contains the <span class='file'>.condarc</span> file above.

Save the edited <span class='file'>.condarc</span> file. Back at the terminal window, create the virtual environment with the following command

<span class='terminal'>conda create \-\-name ossl_py38 python=3.8</span>

The terminal reports the progress and you might need to respond to conflicts. Sticking to default answers to any questions usually solves any conflicts:

```
% conda create --name ossl_py38 python=3.8
  ...
  ...
Solving environment: done

## Package Plan ##

  environment location: /Applications/anaconda3/envs/ossl_py38

  added / updated specs:
    - matplotlib
    - numpy
    - pandas
    - python=3.8
    - scikit-learn

The following packages will be downloaded:

    ...
    ...

The following NEW packages will be INSTALLED:

    ...
    ...

Proceed ([y]/n)? y

Downloading and Extracting Packages
Preparing transaction: done                                                                         
Verifying transaction: done                                                                            
Executing transaction: done                                                                            
#           
# To activate this environment, use
#     $ conda activate ossl_py38
#
# To deactivate an active environment, use
#     $ conda deactivate   
```

##### Direct specification

An alternative to setting default packages using <span class='file'>.condarc</span>, is to directly specify the additional packages to bring along while creating the virtual environment:

<span class='terminal'>conda create \-n ossl_py38a python=3.8 matplotlib numpy pandas scikit-learn</span>

The reporting in the terminal will be similar as when installing using default packages defined in <span class='file'>.condarc</span>.

##### From an exported configuration (yml) file

The [GitHub repo OSSL-pydev](https://github.com/karttur/OSSL-pydev) contains an exported [conda virtual environment](https://github.com/karttur/OSSL-pydev/tree/main/anaconda) (yml) file. If you [cloned or downloaded the repo](../spectrosupport-OSSL-pydev-clone) you should have the file <span class='file'>spectra-ossl_from-history_py38.yml</span> on your local machine. To set up a new virtual environment using an exported environment make sure the yml file is in the active path of the <span class='app'>Terminal</span> and then type:

 <span class='terminal'>conda env create \-f spectra-ossl_from-history_py38.yml</span>

The reporting in the terminal will be similar as when installing using default packages defined in <span class='file'>.condarc</span>.

#### Post-installation of individual packages

If you need to do a post-installation, this last section describes how to do it. For adding a package to a particular virtual environment you first have to activate it:

<span class='terminal'>conda activate ossl_py38</span>

Then your installation commands will add the package to that particular environment.

##### matplotlib

<span class='package'>matplotlib</span> is not available in the default Anaconda repositories, but need to be installed from conda-forge. The terminal command for [installing matplotlib using conda](https://anaconda.org/conda-forge/matplotlib) is:

<span class='terminal'>conda install -c conda-forge matplotlib</span>

##### numpy

The <span class='pacakge'>numpy</span> is available from the [default Anaconda repository](https://anaconda.org/anaconda/numpy) and can thus be installed with the command:

<span class='terminal'>conda install -c anaconda numpy</span>

##### pandas

Also <span class='pacakge'>pandas</span> is available from the [default Anaconda repository](https://anaconda.org/anaconda/pandas):

<span class='terminal'>conda install -c anaconda pandas</span>

##### scikit-learn

Also <span class='pacakge'>scikit-learn</span> is available from the [default Anaconda repository](https://anaconda.org/anaconda/scikit-learn):

<span class='terminal'>conda install -c anaconda scikit-learn</span>
