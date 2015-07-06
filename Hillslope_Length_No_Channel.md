=====================================================================
Extracting Hillslope Lengths Without A Channel Network
=====================================================================

This page gives an overview of how to use the probabilistic hillslope length driver (LH_Prob.cpp) 
to quickly generate hillslope length data for a series of basins, along with 
other basin average metrics within a larger DEM file, where no constraint on the 
channel network is present.  

Quick guide
=================================================

Here is a quick overview of how to set up and run the code, if you have done it before:

#. Compile the code with: ``make -f LH_Prob.make``
#. Run the program ``LH_Prob.out`` using the path (with trailing slash), the filename prefix, window radius, basin order, critical slope, drainage threshold and a switch to write rasters if desired as arguments.
#. Analyse the resulting data files using python. 

Overview
=================================================

This driver file will combine several LSDRaster Functions in order to generate 
as complete a range of basin average and hillslope length metrics as possible.
A channel network will be extracted from the given threshold area and only hillslopes 
which terminate in streams of the two highest orders in the landsacpe are recorded. The tool will
generate:

#. A HilltopData file with metrics calculated for each hilltop pixel which can be routed to a high order stream pixel.
#. A file containing basin averaged values of hillslope lengths and other standard metrics. 

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

The code is compiled using the provided makefile, LH_Prob.make and the command:

``make -f LH_Prob.make`` 

Which will create the binary file, LH_Prob.out to be executed.

Run The Code
=================================================

The driver is run with seven arguments: 

#. The data path where the Channel head file and DEM is stored, and the output data will be written.
#. The filename prefix, without an underscore.
#. Radius in spatial units of kernel used in surface fitting.
#. The `Strahler order <http://en.wikipedia.org/wiki/Strahler_number>`_ of basins to be extracted.
#. Critical Slope, Used to filter out hilltops with too steep a slope and is used in the E* R* calculations.
#. The threshold area used to extract the channel network, in pixels. 
#. Switch to write rasters. 0 == do not write rasters and 1 == write rasters

The syntax on a unix machine is as follows:

``./LH_Prob.out <path to data files> <Prefix> <Window Radius> <Stream order> <Critical Slope> <Threshold> <Switch>``

And a complete example (your path and filenames may vary):

``./LH_Prob.out /home/s0675405/DataStore/LH_tests/ Oregon 6 2 1.1 5000 0``

Analysing The Results
=================================================

The final outputs are stored in a series of files, which are written to the data 
folder supplied as an argument.

**<Prefix>_prob_<Threshold>_Paper_Data.txt** 

This file contains all of the basin average values for each basin in the following format:

``BasinID HFR_mean HFR_median HFR_stddev HFR_stderr HFR_Nvalues HFR_range HFR_min HFR_max SA_binned_LH SA_Spline_LH LH_Density Area Basin_Slope_mean Basin_Slope_median Basin_Slope_stddev Basin_Slope_stderr Basin_Slope_Nvalues Basin_Slope_range Basin_Slope_min Basin_Slope_max Basin_elev_mean Basin_elev_median Basin_elev_stddev Basin_elev_stderr Basin_elev_Nvalues Basin_elev_Range Basin_elev_min Basin_elev_max Aspect_mean CHT_mean CHT_median CHT_stddev CHT_stderr CHT_Nvalues CHT_range CHT_min CHT_max EStar RStar HT_Slope_mean HT_Slope_median HT_Slope_stddev HT_Slope_stderr HT_Slope_Nvalues HT_Slope_range HT_Slope_min HT_Slope_max HT_relief_mean HT_relief_median HT_relief_stddev HT_relief_stderr HT_relief_Nvalues HT_relief_range HT_relief_min HT_relief_max``

#. BasinID =  Unique ID for the basin.
#. HFR_mean = Mean hilltop flow routing derived hillslope length.
#. HFR_median = Median hilltop flow routing derived hillslope length. 
#. HFR_stddev = Standard deviation of hilltop flow routing derived hillslope length.
#. HFR_stderr = Standard error of hilltop flow routing derived hillslope length.
#. HFR_Nvalues = Number of values used in hilltop flow routing derived hillslope length.
#. HFR_range = Range of hilltop flow routing derived hillslope length.
#. HFR_min = Minimum hilltop flow routing derived hillslope length.
#. HFR_max = Maximum hilltop flow routing derived hillslope length. 
#. SA_binned_LH = Hillslope length from binned slope area plot.
#. SA_Spline_LH = Hillslope length from spline curve in slope area plot.
#. LH_Density = Hillslope length from drainage density.
#. Area = Basin area.
#. Basin_Slope_mean = Mean basin slope.
#. Basin_Slope_median = Median basin slope.
#. Basin_Slope_stddev = Standard deviation of basin slope.
#. Basin_Slope_stderr = Standard error of basin slope.
#. Basin_Slope_Nvalues = Number of basin slope values.
#. Basin_Slope_range = Range of basin slopes.
#. Basin_Slope_min = Minimum basin slope.
#. Basin_Slope_max = Maximum basin slope.
#. Basin_elev_mean = Mean basin elevation.
#. Basin_elev_median = Median basin elevation.
#. Basin_elev_stddev = Standard deviation of basin elevation.
#. Basin_elev_stderr = Standard error of basin elevation.
#. Basin_elev_Nvalues = Number of basin elevation values. 
#. Basin_elev_Range = Range of basin elevations.
#. Basin_elev_min = Minimum basin elevation.
#. Basin_elev_max = Maximum basin elevation.
#. Aspect_mean = Mean aspect of the basin. 
#. CHT_mean = Mean hilltop curvature of the basin.
#. CHT_median = Median hilltop curvature of the basin.
#. CHT_stddev = Standard deviation of hilltop curvature of the basin.
#. CHT_stderr = Standard error of hilltop curvature of the basin.
#. CHT_Nvalues = Number of hilltop curvature values used. 
#. CHT_range = Range of hilltop curvatures.
#. CHT_min = Minimum hilltop curvature in the basin.
#. CHT_max = Maximum hilltop curvature in the basin. 
#. EStar = E* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. RStar = R* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. HT_Slope_mean = Mean slope calculated using (relief/hillslope length).
#. HT_Slope_median = Median slope calculated using (relief/hillslope length).
#. HT_Slope_stddev = Standard deviation of slope calculated using (relief/hillslope length).
#. HT_Slope_stderr = Standard error of slope calculated using (relief/hillslope length).
#. HT_Slope_Nvalues = Number of slope values calculated using (relief/hillslope length).
#. HT_Slope_range = Range of slopes calculated using (relief/hillslope length).
#. HT_Slope_min = Minimum slope calculated using (relief/hillslope length).
#. HT_Slope_max = Maximum slope calculated using (relief/hillslope length).
#. HT_relief_mean = Mean relief.
#. HT_relief_median = Median relief.
#. HT_relief_stddev = Standard deviation of relief.
#. HT_relief_stderr = Standard error of relief. 
#. HT_relief_Nvalues = Number of relief values used. 
#. HT_relief_range = Range of reliefs.
#. HT_relief_min = Minimum relief.
#. HT_relief_max = Maximum relief.

**<Prefix>_prob_<Threshold>_HilltopData.txt**

This file contains hillslope metrics calculated for every hilltop pixel in the 
dataset which was routed successfully to a high order stream pixel. The file format is as follows:

``X Y hilltop_id S R Lh BasinID StreamID``

#. The first 2 columns are the map coordinates of the hilltop pixel
#. hilltop_id is the value of the hilltop pixel.
#. S is the slope calculated as relief/hillslope length.
#. R is the relief, the change in elevation between the hilltop and the channel
#. Lh is the hillslope flow length.
#. BasinID is the junction outlet number of the basin the hilltop is within.
#. StreamID is the stream order of the termination point of the trace.

**Output Raster Data**

If the output switch is set to ``1`` a series of rasters representing intermediate
processing steps will be written to the supplied path. These files are as follows:

#. Filled DEM
#. Slope
#. Aspect
#. Curvature    
#. Stream Network 
#. Basins 
#. Hilltop Curvature
#. Hillslope Length
#. Hillslope Gradient 
#. Relief
    