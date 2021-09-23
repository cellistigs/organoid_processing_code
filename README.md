# CaImAn Postprocessing 
 
This repo contains some information and scripts for the postprocessing of CaImAn data after using NeuroCAAS. 
I can continue to add more scripts here as useful for further processing, and if we find a nice pipeline we can move this into a custon NeuroCAAS analysis as well. 

## 09/23/21: Initial Repo Creation

The initial functionality of this repo satisfies three requests:
 1.  A cli command to compile information about usage. 
 2.  Code to open NeuroCAAS CaImAn outputs
 3.  Code for image plotting. 
 
 
### Installation  
Note that 2 and 3 will depend upon having certain packages installed. If you already have caiman installed, you can skip ahead to the following sections.  
If you do not have caiman installed, I highly recommend you do so using a conda virtual environment: see instructions to [install](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html) conda and [create environments](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#) (if you prefer virtualenv, that's fine too). 

Once you have a virtual environment, please activate it and run the following:

```
conda install caiman -c conda-forge
```

Then you should be all set to run scripts. 
 
### 1. Monitoring File Size  
 
In order to see the data usage of files using the AWS CLI, please run the following:  

```
aws s3 ls s3://caiman-ncap-web/{path_to_folder_for_quantification} --summarize --human-readable --recursive 
```

This will list the size of each file contained in the folder you specify, as well as the total usage aggregating across all files in the folder. 

### 2. Opening CaImAn outputs. 

Caiman outputs from NeuroCAAS are stored in two formats: hdf5 files containing the full caiman model, and npz files containing array outputs with the spatial and temporal array outputs as well as residuals. 

In order to work with the hdf5 model stored at path `filename`, you can run the following code:  

```
python
>>> import caiman
>>> trainedmodel = caiman.source_extraction.cnmf.cnmf.load_CNMF(filename, n_processes=1, dview=None)
>>> estimates = trainedmodel.estimates
>>> A = estimates.A ## spatial component
>>> C = estimates.C ## temporal component
>>> b = estimates.b ## background spatial component
...
```

You can see more on the outputs stored in the `estimates` attribute [here](https://caiman.readthedocs.io/en/master/Getting_Started.html#result-interpretation).

The spatial, temporal, and residual components are also stored independently as numpy data arrays. An array stored at path `arrayname` can be accessed with: 

```
python
>>> import numpy as np
>>> A = np.load("arrayname",allow_pickle=True)["arr_0"]
```

The results are then a numpy array. 

### 3. Image plotting 

You can plot the resulting arrays in python using the package `matplotlib`. Matplotlib follows similar syntax to matlab, and should be familiar in terms of figure formatting. 
Given an array `A` (of shape HxW or HxWx3), you can plot it as follows: 

```
.... 
>>> estimates = trainedmodel.estimates
>>> A = estimates.A
>>> import matplotlib.pyplot as plt
>>> plt.imshow(A) 
>>> plt.savefig("name_of_figure.png") ## will save image to a separate file. 
```

This corresponds to plotting the spatial components as an image. For more on matplotlib, please see [here](https://matplotlib.org/stable/tutorials/introductory/images.html#sphx-glr-tutorials-introductory-images-py).
