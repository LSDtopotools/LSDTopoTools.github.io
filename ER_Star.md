=====================================================================
Generating E*R* data
=====================================================================

These instructions will take you through the steps needed to generate E*R* data for
a landscape, and use that data to constrain the critical gradient following
the techniques published in Hurst et al. (2012) and Hurst et al. (2013).

Quick guide
=================================================

Here is a quick overview of how to set up and run the code, if you have done it before:

#. Generate hillslope length data using LH_Driver.cpp.
#. Compile the code with: ``make -f E_STAR_R_STAR.make``
#. Run the program ``E_STAR_R_STAR.out`` using the path (with trailing slash), the filename prefix and the minimum patch area in pixels as arguments.
#. Analyse the resulting data files using python. 

Overview
=================================================

This driver file will combine several LSDRaster Functions in order to generate E*R*
data for a landscape. This methodology matches the techniques published in Hurst et al. (2012) and Hurst et al. (2013) 
This code will produce:

#. A csv file containing the hilltop patch averaged values needed to calculate E*R* values.
#. A csv file containing raw needed to calculate E*R* values.  

Warning
=================================================

This code is for research purposes and is under continuous development, so we cannot guarantee a bug-free experience!

Before You Start
=================================================
See the other docs on this site for help with loading data into the correct format 
and general help with using this suite of software: :doc:`float`

Input Data
=================================================

Input Topographic Data
-------------
The 4 input rasters are all generated using LH_Driver.cpp, and this code must be run before 
E*R* data can be generated. The files should be in the data directory in the following format:

#. <prefix>_Relief.flt 
#. <prefix>_HFR_LH.flt - Hillslope Length 
#. <prefix>_Slope.flt
#. <prefix>_CHT.flt - Hilltop curvature  

Compile The Driver
=================================================

The code is compiled using the provided makefile, E_STAR_R_STAR.make and the command:

``make -f E_STAR_R_STAR.make`` 

Which will create the binary file, E_STAR_R_STAR.out to be executed.

Run The Code
=================================================

The driver is run with three arguments: 

#. The data path where the input raster files are stored, and the output data will be written.
#. The filename prefix, without an underscore.
#. The minimum patch area in pixels.

The syntax on a unix machine is as follows:

``./E_STAR_R_STAR.out <path to data files> <Prefix> <Minimum patch area>``

And a complete example (your path and filenames may vary):

``./E_STAR_R_STAR.out /home/s0675405/DataStore/ER_tests/ Oregon 10``

The Output Data
=================================================

The final outputs are stored in two csv files and a raster file, which are written to the data 
folder supplied as an argument.

**<prefix>_E_R_Star_Patch_Data.csv** 

This file contains all of the patch average values that are needed to calculate E*R* values
The file is in the following format:

``Final_ID,lh_means,lh_medians,lh_std_devs,lh_std_errs,cht_means,cht_medians,cht_std_devs,cht_std_errs,r_means,r_medians,r_std_devs,r_std_errs,s_means,s_medians,s_std_devs,s_std_errs``

#. Final_ID = Patch ID value.
#. lh_means = Mean hillslope length.
#. lh_medians = Median hillslope length.
#. lh_std_devs = Standard deviation of hillslope length.
#. lh_std_errs = Standard error of hillslope length.
#. cht_means = Mean hilltop curvature.
#. cht_medians = Median hilltop curvature.
#. cht_std_devs = Standard deviation of hilltop curvature.
#. cht_std_errs = Standard error of hilltop curvature.
#. r_means = Mean relief.
#. r_medians = Median relief.
#. r_std_devs = Standard deviation of relief. 
#. r_std_errs = Standard error of relief. 
#. s_means = Mean slope.
#. s_medians = Median slope.
#. s_std_devs = Standard deviation of slope.
#. s_std_errs = Standard error of slope. 

These files can be analysied using a series of python routines detailed below and 
located `here <https://github.com/sgrieve/>`_. 

**<prefix>_E_R_Star_Raw_Data.csv** 

This file contains all of the hilltop data for every valid pixel in the landscape.
The file is in the following format:

``i,j,LH,CHT,Relief,Slope``

#. i = Northing of data point in raster coordinates.
#. j = Easting of data point in raster coordinates.
#. LH = Hillslope length.
#. CHT = Hilltop curvature.
#. Relief = Relief.
#. Slope = Slope.

These files can be analysied using a series of python routines detailed below and 
located `here <https://github.com/sgrieve/>`_. 

**Output Raster Data**

The code also writes a raster file of the hilltop patches, ``<prefix>_Patches.flt``, to allow the user to inspect the 
spatial averaging which has been performed.

Using Python To Analyse The Data
=================================================
    
The latest version of the python scripts which accompany this analysis driver can be found `here <https://github.com/sgrieve/>`_
and provide a complete framework to go from the output data files to a series of figures
utilizing the different methods for producing E*R* data, along with a best fit critical slope
value for the nonlinear flux law proposed by Roering et al. (1999).  

Plotting The Segments
-----------------------

[explain python script here]

Plotting The Raw Data
--------------------

[explain python script here]

Binning the Raw Data
--------------------

[explain python script here]

Getting A Best Fit Critical Slope
---------------------------------

[explain python script here]

Choose how to constrain the fit:

#. raw data
#. segments
#. binned data