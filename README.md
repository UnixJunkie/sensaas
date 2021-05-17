## SENSAAS


[![badgepython](https://forthebadge.com/images/badges/made-with-python.svg)](https://www.python.org/downloads/release/python-370/)  [![forthebadge](https://forthebadge.com/images/badges/built-with-science.svg)](https://chemoinfo.ipmc.cnrs.fr/)

**SENSAAS** is a shape-based alignment program which allows to superimpose molecules. It is based on the publication [SenSaaS: Shape-based Alignment by Registration of Colored Point-based Surfaces](https://onlinelibrary.wiley.com/doi/full/10.1002/minf.202000081)

![example](/images/sensaas-core-eg-large_v2.png)

**Documentation**: Full documentation is available at [https://sensaas.readthedocs.io](https://sensaas.readthedocs.io)

**Website**: A web demo is available at https://chemoinfo.ipmc.cnrs.fr/SENSAAS/index.html

**Tutorial**: This video on YouTube XXXX provides a tutorial


## Requirements

SENSAAS relies on the open-source library [Open3D](http://www.open3d.org). The current release of SENSAAS uses **Open3D version 0.12.0 along with Python3.7**.

Visit the following URL for using Python packages distributed via PyPI: [http://www.open3d.org/docs/release/getting_started.html](http://www.open3d.org/docs/release/getting_started.html) or conda: [https://anaconda.org/open3d-admin/open3d/files](https://anaconda.org/open3d-admin/open3d/files). For example, for windows-64, you can download *win-64/open3d-0.12.0-py37_0.tar.bz2*


## Virtual environment for python with conda (for Windows for example)

Install conda or Miniconda from [https://conda.io/miniconda.html](https://conda.io/miniconda.html)  
Launch Anaconda Prompt, then complete the installation:

	conda update conda
	conda create -n sensaas
	conda activate sensaas
	conda install python=3.7 numpy
 
 After downloading the appropriate version of Open3D:
  
 	conda install open3d-0.12.0-py37_0.tar.bz2

(Optional) Additional packages for visualization with PyMOL:

  	conda install -c schrodinger -c conda-forge pymol-bundle
  
Retrieve and unzip SENSAAS repository in your desired folder. See below for running the program **sensaas.py**.

## Linux

Install:

1. Python3.7 and numpy
2. Open3D version 0.12.0 (more information at [http://www.open3d.org/docs/release/getting_started.html](http://www.open3d.org/docs/release/getting_started.html))

(Optional) Install additional packages for visualization with PyMOL:

3. PyMOL (a molecular viewer; more information at [https://pymolwiki.org](https://pymolwiki.org))
  
Retrieve and unzip SENSAAS repository

## MacOS

	Not tested

## Information on the third-party program NSC

NSC is used to efficiently generate point clouds of molecules and to calculate their surfaces. It is written in C and was developed by Frank Eisenhaber who kindly licensed its use in SENSAAS. **Please be advised that the use of NSC is strictly tied to SENSAAS and its code is released under the following [license](https://github.com/SENSAAS/sensaas/blob/main/License_NSC.txt)**. If the NSC license is an issue for your application or if you wish to use NSC independently of
SENSAAS, please contact the author Frank Eisenhaber (email: frank.eisenhaber@gmail.com) who will amicably manage your request.

References :

1. F. Eisenhaber, P. Lijnzaad, P. Argos, M. Scharf, The Double Cubic Lattice Method: Efficient Approaches to Numerical Integration of Surface Area and Volume and to Dot Surface Contouring of Molecular Assemblies, *Journal of Computational Chemistry*, **1995**, 16, N3, pp.273-284.
2. F. Eisenhaber, P. Argos, Improved Strategy in Analytic Surface Calculation for Molecular Systems: Handling of Singularities and Computational Efficiency, 	*Journal of Computational Chemistry*, **1993**,14, N11, pp.1272-1280.


Executables nsc (for Linux) or ncs-win (for windows) are included in this repository. In case they do not work on your system, you may have to compile it using the source file nsc.c in directory src/

**for Windows**:

The current executable nsc-win.exe was compiled by using [http://www.codeblocks.org](http://www.codeblocks.org). Rename the executable as nsc-win.exe because 'nsc-win.exe' is used to set the variable nscexe in the Python script sensaas.py

**for Linux**:

	cc src/nsc.c -lm
	
rename a.out as nsc because 'nsc' is used to set the variable nscexe in the Python script sensaas.py:

	cp a.out nsc
	


## Run Sensaas
To align a Source molecule on a Target molecule, the syntax is:
	
	python sensaas.py sdf molecule-target.sdf sdf molecule-source.sdf slog.txt optim
	
Example:

	python sensaas.py sdf examples/IMATINIB.sdf sdf examples/IMATINIB_mv.sdf slog.txt optim

Here, the source file IMATINIB_mv.sdf is aligned (**moved**) on the target file IMATINIB.sdf (**that does not move**). The output **tran.txt** contains the transformation matrix allowing the alignment of the source file (result in **Source_tran.sdf**). The **slog.txt** file details results with final scores of the aligned molecule (Source) on the last line. In the current example, the last line must look like:

	gfit= 1.000 cfit= 0.999 hfit= 0.996 gfit+hfit= 1.996

There are three different fitness scores but we only use 2 of them, gfit and hfit, to calculate gfit+hfit.

- gfit score estimates the geometric matching of point-based surfaces - it ranges between 0 and 1

- hfit score estimates the matching of colored points representing pharmacophore features - it ranges between 0 and 1

Thus, we calculate a hybrid score = gfit + hfit scores - **gfit+hfit ranges between 0 and 2**

   - A gfit+hfit score close to 2.0 means a perfect superimposition.

   - A gfit+hfit score > 1.0 means that similaries were identified.
   
## Run meta-sensaas.py

**1. If you want to process a sdf file containing several molecules as for example several conformers for Target and/or Source:**

 		python meta-sensaas.py molecules-target.sdf molecules-source.sdf
 
 Here outputs are:
 - the file **bestsensaas.sdf** that contains the best ranked aligned Source
 - the file **catsensaas.sdf** that contains all aligned Sources
 - the file **matrix-sensaas.txt** that contains gfit+hfit scores (rows=Targets and columns=Sources)

You can also select the score type by using the option -s (default is **-s source**):

	python meta-sensaas.py molecules-target.sdf molecules-source.sdf -s mean

here the mean of the score of the target and of the aligned source will be used to rank solutions and to fill matrix-sensaas.txt


**2. If you want to repeat in order to find alternate alignments when they exist (eg: aligning a fragment on a large molecule):**

  		python meta-sensaas.py molecules-target.sdf molecules-source.sdf -r 100
 
 here 100 alignments of the source will be generated and clustered. Outputs are:
 
 - file **sensaas-1.sdf** with the best ranked alignemnt - it contains 2 molecules: first is Target and second the aligned Source
 - file **sensaas-2.sdf** with the second best ranked alignemnt - it contains 2 molecules: first is Target and second the aligned Source
 - ...
 - file **cat-repeats.sdf** that contains all aligned Sources

Example:

	 python meta-sensaas.py examples/VALSARTAN.sdf examples/tetrazole.sdf -r 100

- sensaas-1.sdf contains the self-matching superimposition
- sensaas-2.sdf contains the bioisosteric superimposition
- sensaas-3.sdf contains the geometric-only superimposition

## Visualization 

You can use any molecular viewer. For instance, you can use PyMOL if installed (see optional packages):

	pymol examples/IMATINIB.sdf examples/IMATINIB_mv.sdf Source_tran.sdf 

or after executing meta-sensaas.py with the repeat option:
	
	pymol examples/VALSARTAN.sdf examples/tetrazole.sdf sensaas-1.sdf


## Licenses
1. SENSAAS code is released under [the 3-Clause BSD License](https://opensource.org/licenses/BSD-3-Clause)

2. NSC code is released under the following [license](https://github.com/SENSAAS/sensaas/blob/main/License_NSC.txt)

## Copyright
Copyright (c) 2018-2021, CNRS, Inserm, Université Côte d'Azur, Dominique Douguet and Frédéric Payan, All rights reserved.

## Reference
[Douguet D. and Payan F., SenSaaS: Shape-based Alignment by Registration of Colored Point-based Surfaces, *Molecular Informatics*, **2020**, 8, 2000081](https://onlinelibrary.wiley.com/doi/full/10.1002/minf.202000081). doi: 10.1002/minf.202000081
   
