=====================================================================
Selecting A Window Size for Surface Fitting
=====================================================================

These instructions will take you through the steps needed to identify the correct 
window radius to use in the surface fitting routines, following
the techniques published in Roering et al. (2010) and Hurst et al. (2012).

Quick guide
=================================================

Here is a quick overview of how to set up and run the code, if you have done it before:

#. Compile the code with: ``make -f PolyFitWindowSize.make``
#. Run the program ``PolyFitWindowSize.out`` using the path (with trailing slash), the filename prefix and the minimum patch area in pixels as arguments.
#. Analyse the resulting data files using python. 

Overview
=================================================

This driver file will run the surface fitting routines at a range of window sizes up to 100 meters.
It then calculates the inter quartile range, standard devaiation and for each surface and outputs these data
as a text file.

This code will produce:

#. A csv file containing the surface statistics for each window size.

Warning
=================================================

This code is for research purposes and is under continuous development, so we cannot guarantee a bug-free experience!

Before You Start
=================================================
See the other docs on this site for help with loading data into the correct format 
and general help with using this suite of software: :doc:`float`

Input Data
=================================================

Raw DEM
-------------
The raw DEM to be analysed should be named <prefix>_DEM.flt 

Compile The Driver
=================================================

The code is compiled using the provided makefile, PolyFitWindowSize.make and the command:

``make -f PolyFitWindowSize.make`` 

Which will create the binary file, PolyFitWindowSize.out to be executed.

Run The Code
=================================================

The driver is run with three arguments: 

#. The data path where the input raster files are stored, and the output data will be written.
#. The filename prefix, without an underscore.
#. The input file format. Must be either ``bil``, ``flt`` or ``asc``.

The syntax on a unix machine is as follows:

``./PolyFitWindowSize.out <path to data files> <Prefix> <file format>``

And a complete example (your path and filenames may vary):

``./PolyFitWindowSize.out /home/s0675405/DataStore/Final_Paper_Data/NC/ NC_DEM flt``

The Output Data
=================================================

The final outputs are stored in a plain text file, which is written to the data 
folder supplied as an argument.

**<prefix>_Window_Size_Data.txt** 

This file contains the data needed to select the correct window size. The file is in the following format:

``Length_scale Curv_mean Curv_stddev Curv_iqr``

#. Length_scale = Window size.
#. Curv_mean = Mean curvature for the landscape.
#. Curv_stddev = Standard deviation of curvature for the landscape.
#. Curv_iqr = Inter quartile range of curvature for the landscape.. 

These files can be analysied using a series of python routines detailed below and 
located `here <https://github.com/sgrieve/GeneralAnalysis>`_. 

Using Python To Select A Window Size
=================================================
    
The latest version of the python scripts which accompany this analysis driver can be found `here <https://github.com/sgrieve/GeneralAnalysis>`_
and provide a complete framework to select a window size for surface fitting.

Set the ``path``, ``filename`` and ``outpath`` variables on lines 29-31 to point to your data, generated from the c++ driver. 
If you want to save the figure, toggle the commented lines:
  
#.  plt.show()
#.  #plt.savefig(outpath+'WindowSizeFig.png')

Run the code and look at the generated graph. Using Roering et al (2010) Figure 1 and 
Hurst et al. (2012) Figure 4 for guidance on how to interpret the results.
