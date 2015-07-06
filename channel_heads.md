=====================================================================
Calculating channel heads using the DrEICH algorithm
=====================================================================

This document gives you instructions on how to calculate channel head locations for a LiDAR DEM using the DrEICH method.  Before running your analysis you must know
the m/n value for your field site.

Quick guide
=================================================

If you already know more or less what you are doing, but need a quick reminder, here are the steps involved:

#. Perform a chi analysis to get the correct m/n value for your field site - the methodology is explained with the chi analysis tools: :doc:`chi_profiles`
#. Create a parameter file.
#. Compile the code with channel_heads.make and run with channel_heads.out.
#. Convert the float files "DEM_name_CH.flt" and "DEM_name_SO_from_CH.flt" in ArcMap to visualise the channel heads and resulting channel network.

Overview
=================================================

This document gives instructions on how to predict channel head locations from LiDAR data using the LSDTopoToolbox.  This method is described in detail in 
Clubb et al. (2014), available online at http://onlinelibrary.wiley.com/doi/10.1002/2013WR015167/abstract.  This method firstly uses the chi analysis in 
LSDTopoToolbox to find the correct m/n value for the field site in question. It then identifies valleys with a positive curvature to run the chi analysis on - 
these are selected as having 10 or more linked pixels with a curvature greater than 0.1. For each basin identified, the valley outlet - hilltop profile is transformed 
into chi-elevation space.  The channel head is predicted by assuming the chi-elevation profile is made up of two segments: a channel segment, which should be linear; 
and a hillslope segment, whihc should be non-linear. A test value is maximised to search for the best combination of a linear channel segment and a non-linear hillslope segment.
The channel head is then identified as the intersection of these two segments.  A float file is then generated showing the location of the channel head pixels identified using this method.  
A stream network is then generated using these pixels as the sources, which is also output as a float file.

Warning
=================================================

This code is for research purposes and is under continuous development, so we cannot guarantee a bug-free experience!

Before You Start
=================================================

See the other docs on this site for help with loading data into the correct format 
and general help with using this suite of software: :doc:`float`

Getting the correct m/n value
=================================================

In order to use the chi method to predict channel head locations, the correct m/n value must be used for your particular field site. For background to the method of finding m/n, 
and a description of the algorithms, we refer the reader to `Mudd et al. (2014)
<http://onlinelibrary.wiley.com/doi/10.1002/2013JF002981/abstract>`_. 
The method of using LSDTopoToolbox to find the m/n value is described in the documentation elsewhere on this site: :doc:`chi_profiles`

Downloading the DrEICH algorithm
=================================================

The DrEICH algorithm is available for download on CSDMS: http://csdms.colorado.edu/wiki/Download_models
There is a main folder in which the code is stored, with a nested folder called ``driver_functions`` containing the driver needed to run the channel head algorithm.
Two test datasets are provided with the code that you are welcome to use as a starting point. These datasets are both from Ohio: one from the Indian Creek watershed 
and another from the Mid Bailey Run watershed. These are named as ``indian_creek_dem.flt`` and ``bailey_run_dem.flt`` respectively. Two example parameter files (one for each
DEM) are also provided called ``indian_params.driver`` and ``bailey_params.driver``.

Creating a parameter file
=================================================

The parameter file needs to be used as the second argument with the two driver functions for the channel head prediction. This file should be located within
the main folder of the code, NOT within the driver_functions file. It contains the following
parameters:

#. DEM name - the filename of your DEM **without a file extension**.
#. Minimum slope - the minimum slope for filling the DEM, should be ``0.0001``.
#. Number of contributing pixels for channel threshold - will only be used as a starting point for valley identification, should be ``100``.
#. A_0 for chi analysis - should be ``1000``.
#. m/n for chi analysis - should be calculated in previous step.
#. Number of linked pixels used to identify a valley - should be ``10``.

Run the channel head driver
=================================================

To run the channel head prediction algorithm, you first need to navigate to the ``driver_functions`` folder on UNIX. You then compile the code
using the provided makefile ``channel_heads.make`` and the command:

``make -f channel_heads.make`` 

Which will create the binary file, channel_heads.out to be executed.

The driver is run with two arguments: 

#. The data path where the parameter files and DEM is stored (the main folder in which the code is stored, NOT the path to the driver_functions folder.
#. The parameter file.

The syntax on a unix machine is as follows:

``./channel_heads.out <path to data files> <parameter file>``

And an complete example (your path and filenames may vary):

``./channel_heads.out /home/s0923330/LSDRaster/LSDRaster/ indian_params.driver``

This will create 2 float files which can be converted and visualised in ArcMap (for help on this see other pages in this documentation: :doc:`float`).
The file DEM_name_CH.flt identifies each channel head, and the file DEM_name_SO_from_CH.flt generates a stream network from these channel heads.