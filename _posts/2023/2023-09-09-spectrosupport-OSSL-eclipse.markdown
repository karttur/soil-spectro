---
layout: post
title: Eclipse for PyDev
categories: libspectrosupport
excerpt: "Setup Eclipse as python development environment for the OSSL spectral data processing"
tags:
  - Eclipse
  - Python
  - OSSL
image: ts-mdsl-rntwi_RNTWI_id_2001-2016_AS
date: '2023-09-09 11:27'
modified: '2023-09-09 11:27'
comments: true
share: true
---

### Introduction

To run the python scripts for [importing](../../libspectrodata/spectrodata-OSSL4ML02-arrange), [plotting](../../libspectrodata/spectrodata-OSSL4ML03-plot) and [modelling](../../libspectrodata/spectrodata-OSSL4ML05-mlmodel01) OSSL data as described in other posts of this blog you need to have a python development environment. This post describes how to setup a python environment using [<span class='app'>Eclipse</span>](https://www.eclipse.org). The instructions are shorthand and written using MacOS. I have also published more detailed instructions for [setting up Eclipse for MacOS](https://karttur.github.io/setup-ide/setup-ide/install-eclipse/) and a summary for how to [install Eclipse and Anaconda in Ubuntu20](https://karttur.github.io/setup-ide/blog/ubuntu20-setup-spide/).

### Eclipse

Eclipse is “an open extensible IDE for anything and nothing in particular”, where IDE stands for Integrated Development Environment. We will use Eclipse for testing and developing the python scripts used for processing OSSL data.

Open a browser and go to [Eclipse foundation](https://www.eclipse.org). Look for the (orange) <span class='button'>Download</span> button. You should come directly to the download page for your OS. At time of writing this in September 2023 the latest version of Eclipse is _2023-06_. Select the version that is appropriate for your machine architecture and go ahead and download (figure 1).

<figure>
  <img src="../../images/Eclipse_download-appropriate.png" alt="image">
	<figcaption>Figure 1. Select the Eclipse version that is appropriate for your machine architecture.</figcaption>
</figure>

The later versions of the Eclipse installations packages are all inclusive. The installation package is rather small and should download quickly (figure 2).

<figure>
  <img src="../../images/Eclipse_download-install-pckg.png" alt="image">
	<figcaption>Figure 2. The later versions of the Eclipse installation package includes the Java Runtime Environment required for your OS.</figcaption>
</figure>

![Eclipse_installer_app](../../images/Eclipse_installer_app.png)
{: .pull-right}
Open the installation app that was downloaded and run it - the example to the right shows the MacOS Eclipse installation app icon. Just double click and the installation of <span class='app'>Eclipse</span> itself should start.

It might take a while for the installer to get going (figure 3).

<figure>
  <img src="../../images/Eclipse_install_app_startup.png" alt="image">
	<figcaption>Figure 3. Waiting for the Eclipse installer app to load.</figcaption>
</figure>

When presented with different alternatives to install, chose the **Eclipse IDE for Java Developers** alternative.

<figure>
  <img src="../../images/Eclipse_install_alternatives.png" alt="image">
	<figcaption>Figure 4. Choose the Eclipse IDE for Java Developers when asked for version to install.</figcaption>
</figure>

After selecting the installation version, the next window will summarise your choice and the destination paths of your installation (figure 5).

<figure>
  <img src="../../images/Eclipse_installer_install.png" alt="image">
	<figcaption>Figure 5. Summary of selected installation and destination paths for installation, press Install to start the installation.</figcaption>
</figure>

Click <span class='button'>Install</span>. When the installation is ready, the Eclipse installer window changes and you can launch (or inspect) the <span class='app'>Eclipse</span> installation (figure 6).

<figure>
  <img src="../../images/Eclipse_installer_launch-Eclipse.png" alt="image">
	<figcaption>Figure 6. Launch Eclipse (or open the readme/explore the installation).</figcaption>
</figure>

The first time you start, <span class='app'>Eclipse</span> will ask you to **Select a directory as workspace**. You can usually accept the default, but as I have older versions installed I set a specific workspace for each version as shown in figure 7.

<figure>
  <img src="../../images/Eclipse_select_workspace.png" alt="image">
	<figcaption>Figure 7. Select a directory as workspace.</figcaption>
</figure>

Once you have selected the directory for the workbench, the next window is the **Welcome to the Eclipse IDE for ..**. Before proceeding you have to **Launch the Eclipse Marketplace** (figure 8).

<figure>
  <img src="../../images/Eclipse_Welcome.png" alt="image">
	<figcaption>Figure 8. Eclipse Welcome window - before continuing Launch the Eclipse Marketplace.</figcaption>
</figure>

In the **Eclipse Marketplace** window, enter the search term _pydev_ in the _Find_ field and click the <span class='button'>Go</span> button in the top row. The top result should then be the PyDev extension (figure 9). Click the <span class='button'>Install</span> button for the PyDev extension.

<figure>
  <img src="../../images/Eclipse_Marketplace_find-pydev.png" alt="image">
	<figcaption>Figure 9. In Eclipse Marketplace find the PyDev extension and then click install.</figcaption>
</figure>

 In the sequence of windows that follows (figures 10 to 13) you must confirm, bypass and accept various conditions to follow the installation through.

<figure>
  <img src="../../images/Eclipse_Marketplace_confirm-pydev.png" alt="image">
	<figcaption>Figure 10. In Eclipse Marketplace confirm the installation of the PyDev extension.</figcaption>
</figure>

<figure>
  <img src="../../images/Eclipse_Marketplace_proceed-pydev.png" alt="image">
	<figcaption>Figure 11. In Eclipse Marketplace for MacOS the Marketplace installation of PyDev reports an error, but by accepting to proceed the installation anyway finishes correctly. </figcaption>
</figure>

<figure>
  <img src="../../images/Eclipse_Marketplace_license-pydev.png" alt="image">
	<figcaption>Figure 12. You must accept the license terms in order to proceed with the PyDev extension installation.</figcaption>
</figure>

<figure>
  <img src="../../images/Eclipse_Marketplace_trust-pydev.png" alt="image">
	<figcaption>Figure 13. You must state that you trust the authorities in order to proceed with the PyDev extension installation.</figcaption>
</figure>

Once the PyDev extension is installed, <span class='app'>Eclipse</span> needs to be restarted for the installation to be activated. In the window asking if you want to restart click the button <span class='button'>Restart Now</span> (figure 14).

<figure>
  <img src="../../images/Eclipse_Marketplace_restart-pydev.png" alt="image">
	<figcaption>Figure 14. Restart Eclipse to activate the PyDev extension.</figcaption>
</figure>

![Eclipse_welcome_hide](../../images/Eclipse_welcome_hide.png)
{: .pull-right}
Once <span class='Eclipse'></span> has restarted you will again come to the **Welcome to the Eclipse IDE for ..** window. This time you just bypass (hide) the Welcome window by clicking the little _hide_ icon in the upper right corner (shown to the right).

An <span class='app'>Eclipse</span> project window opens (figure 15).

<figure>
  <img src="../../images/Eclipse_empty_project.png" alt="image">
	<figcaption>Figure 15. Eclipse project window.</figcaption>
</figure>

To continue you must have cloned or downloaded the python OSSL package from GitHub as explained in a [previous post](../spectrosupport-OSSL-pydev-clone).

To import the python OSSL package, either click _Import projects_ in the _Package Explorer_ tab (the left main column), or go via the menu:

<span class='menu'>File -> Import</span>

In the **Import** window, expand the _General_ folder and then select _Projects from Folder or Archive_ as shown in figure 16.

<figure>
  <img src="../../images/Eclipse_projects _from_folder.png" alt="image">
	<figcaption>Figure 16. Expand import options to get the alternative "Projects from Folder or Archive".</figcaption>
</figure>

This should hihglight the (blue) <span class='button'>Next ></span> button, click it to proceed. In the next, **Import Projects** window, select the (white) <span class='button'>Directory...</span> button (figure 17) and navigate to the path where you saved the [clone/download of the OSSL-pydev repo](../spectrosupport-OSSL-pydev-clone). Under that path, select the folder <span class='file'>ossl-xspectre</span> (the folder that is also contains the hidden Eclipse files <span class='file'>.project</span> and <span class='file'>.pydevproject</span>) (figure 17).

<figure>
  <img src="../../images/Eclipse_project_ossl-xspectre.png" alt="image">
	<figcaption>Figure 17. Select the ossl-xspectre package that you cloned/donwloaded in a previous post.</figcaption>
</figure>

Click <span class='button'>Finish</span>. You should then get an error message "Python not configured" (figure 18). What <span class='app'>Eclipse</span> is asking for is the virtual environment that you created in the [previous post](../spectrosupport-OSSL-anaconda).

<figure>
  <img src="../../images/Eclipse_python_not_configured.png" alt="image">
	<figcaption>Figure 18. You have to manually set the python environment to the virtual environment you created with Anaconda in a previous post.</figcaption>
</figure>

In the **Python not configured** window, click the (blue) button <span class='button'>Manual config</span>. In the **Preferences** window that opens, click the button <span class='button'>New</span> at the top of the right column and select the alternative _Browse for python/pypy exe_ (figure 19).

<figure>
  <img src="../../images/Eclipse_preferences.png" alt="image">
	<figcaption>Figure 19. In the Eclipse Preferences window select New in the top right column, then Browse for python/pypy exe and navigate to the virtual python environment you created using Anaconda.</figcaption>
</figure>

Navigate to the [virtual Python environment you created with Anaconda](../spectrosupport-OSSL-anaconda). It is under the Anaconda installation directory, in a subfolder called <span class='file'>envs</span>. Under <span class='file'>envs</span> you should have another subfolder with the name of the environment you created (e.g. "ossl_py38" if you used the suggested name in the post on [Anaconda](../spectrosupport-OSSL-anaconda)). The python/pyp exe file is under the <span class='file'>bin</span> folder and called <span class='file'>python3.8</span>. The relative path under your Anaconda installation should thus be:

<span class='menu'> anaconda3 -> envs -> ossl_py38 -> bin ->python3.8</span>

In my system (MacOS) it looks like this when I have selected the <span class='file'>python3.8</span> exe file (figure 20).

<figure>
  <img src="../../images/Eclipse_select_interpreter.png" alt="image">
 <figcaption>Figure 20. Eclipse selected interpreter with the Anaconda virtual environment created in an earlier post selected.</figcaption>
</figure>

Click <span class='button'>OK</span>, then click <span class='button'>OK</span> again in the **Selection needed** window (figure 21)

<figure>
  <img src="../../images/Eclipse_selection_needed_interpreter.png" alt="image">
 <figcaption>Figure 21. Confirm the interpreter selection in the Selection needed window.</figcaption>
</figure>

You should then get back to the **Preferences** window. Click <span class='button'>Apply and Close</span>.

Finally you get back to <span class='app'>Eclipse</span> project window. In the left column package explorer you should see the pydev package <span class='package'>ossl-xspectre</span>. Expand to see the content by clicking on it, this will first cause a popup window asking for your Default Preferences for PyDev in Eclipse. Accepting the defaults by clicking <span class='button'>OK</span> is recommended.

To run the scripts (python modules) go to the [next post](../spectrosupport-OSSL-run/) that explains how to link the script to OSSL data and edit the command files required.
