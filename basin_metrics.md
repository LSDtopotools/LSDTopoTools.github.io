---
layout: page
title: Extracting Hillslope Lengths
---

=====================================================================
Extracting Hillslope Lengths
=====================================================================

This page gives an overview of how to use the hillslope length driver (LH_Driver.cpp)
and it's companion (LH_Driver_RAW.cpp) to quickly generate hillslope length data for a
series of basins, along with other basin average metrics within a larger DEM file.

For appliactions considering landscapes at geomorphic (millenial) timescales use the main
driver, for event scale measurements use the RAW driver. All instructions on this page will
work for either driver. For convenicence it will refer only to LH_Driver.cpp but either driver
can be used.

This code is used to to produce the data for Grieve et al. (in prep.)  

Quick guide
=================================================

Here is a quick overview of how to set up and run the code, if you have done it before:

#. Generate a channel head file for the landscape.
#. Get the window size for the surface fitting routines.
#. Compile the code with: ``make -f LH_Driver.make``
#. Run the program ``LH_Driver.out`` using the path (with trailing slash), the filename prefix, window radius, basin order, critical slope and a switch to write rasters if desired as arguments.
#. Analyse the resulting data files using python.

Overview
=================================================

This driver file will combine several LSDRaster Functions in order to generate
as complete a range of basin average and hillslope length metrics as possible. The tool will
generate:

#. A HilltopData file with metrics calculated for each hilltop pixel which can be routed to a stream pixel.
#. A file containing basin averaged values of hillslope lengths and other standard metrics.
#. An optional collection of trace files, which can be processed to create a shapefile of the trace paths across the landscape. These can be enabled by setting a flag inside the driver on line 141.  

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

Floodplain Mask (Optional)
-------------
A binary mask of floodplains which can be used to ensure that analysis only occurs on the
hillslopes. Should be named <prefix>_FloodPlain.flt


Generating Channel Heads
-------------------------------

Channel heads can be extracted using the steps outlined in :doc:`channel_heads`
and the resulting channel head file should be named <prefix>_CH.flt and be placed
in the same directory as the DEM.

Getting The Window Size
-------------------------------

The surface fitting window size can be constrained  using the steps outlined in
:doc:`window_size`. This should be performed to ensure the correct parameter values are selected.


Compile The Driver
=================================================

The code is compiled using the provided makefile, LH_Driver.make and the command:

``make -f LH_Driver.make``

Which will create the binary file, LH_Driver.out to be executed.

Run The Code
=================================================

The driver is run with six arguments:

#. The data path where the Channel head file and DEM is stored, and the output data will be written.
#. The filename prefix, without an underscore.
#. Radius in spatial units of kernel used in surface fitting. Selected using :doc:`window_size`.
#. The `Strahler order <http://en.wikipedia.org/wiki/Strahler_number>`_ of basins to be extracted.
#. Critical Slope, Used to filter out hilltops with too steep a slope and is used in the E* R* calculations.
#. Switch to exclude floodplains from analysis [Requires a floodplain mask] 0 == do not exclude floodplains and 1 == exclude floodplains
#. Switch to write rasters. 0 == do not write rasters and 1 == write rasters

The syntax on a unix machine is as follows:

``./LH_Driver.out <path to data files> <Prefix> <Window Radius> <Stream order> <Floodplain Switch> <Switch to write rasters>``

And a complete example (your path and filenames may vary):

``./LH_Driver.out /home/s0675405/DataStore/LH_tests/ Oregon 6 2 1 0``

Analysing The Results
=================================================

The final outputs are stored in a series of files, which are written to the data
folder supplied as an argument.

**<Prefix>_Paper_Data.txt**

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

These files can be analysied using a series of python routines located `here <https://github.com/sgrieve/LH_Paper_Plotting/tree/master/Analysis_Code>`_
and the original figures for Grieve et al. (in prep) can be reproduced using code stored
`here <https://github.com/sgrieve/LH_Paper_Plotting/tree/master/Plotting_Code>`_.

**<Prefix>_HilltopData.csv**

This file contains hillslope metrics calculated for every hilltop pixel in the
dataset which was routed successfully to a stream pixel. The file format is as follows:

``X,Y,hilltop_id,S,R,Lh,BasinID,StreamID,HilltopSlope,DivergentCountFlag,PlanarCountFlag,E_Star,R_Star,EucDist``

#. The first 2 columns are the map coordinates of the hilltop pixel
#. hilltop_id is the value of the hilltop pixel.
#. S is the slope calculated as relief/hillslope length.
#. R is the relief, the change in elevation between the hilltop and the channel
#. Lh is the hillslope flow length.
#. BasinID is the junction outlet number of the basin the hilltop is within.
#. StreamID is the value of the stream pixel reached by the trace.
#. HilltopSlope is the gradient of the pixel hwere the trace started.
#. DivergentCountFlag is the count of divergent pixels crossed. **Depreciated**
#. PlanarCountFlag - Count of planar cells crossed **Depreciated**
#. E_Star - E* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. R_Star - R* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. EucDist - Eculidean length of the trace from hilltop to channel

These files can be analysied using a series of python routines located `here <https://github.com/sgrieve/LH_Paper_Plotting/tree/master/Analysis_Code>`_
and the original figures for Grieve et al. (in prep) can be reproduced using code stored
`here <https://github.com/sgrieve/LH_Paper_Plotting/tree/master/Plotting_Code>`_.

**<easting>_<northing>_trace.txt**

If the trace creation switch is set to true a **large** number of files fill be created
with this filename in a given directory. These files follow the format:

``easting northing DivergentCountFlag length PlanarCountFlag E_Star R_Star EucDist``

#. easting - The easting of the trace
#. northing - The northing of the trace
#. DivergentCountFlag - Count of non-planar cells crossed **Depreciated**
#. length - Cumulative length of the trace
#. PlanarCountFlag - Count of planar cells crossed **Depreciated**
#. E_Star - E* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. R_Star - R* value from `Roering (2007) <http://www.uoregon.edu/~jroering/outgoing/2007RoeringReliefEPSL.pdf>`_.
#. EucDist - Eculidean length of the trace from hilltop to channel

These files can be processed using the accompanying python script, trace_process_1_1.py, to produce a
shapefile of the traces, this file can be found `here <https://github.com/sgrieve/LH_Paper_Plotting/tree/master/Analysis_Code>`_.

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
#. Hillshade
