===========================================================
The LSDCosmoBasin toolkit
===========================================================

The LSDCosmoBasin toolkit is designed to automate calculation of basinwide erosion rates determined estimated from the concentration of in situ cosmogenic nuclides.
Currently 10Be and 26Al are supported. 

The toolkit requires:

  * Data on cosmogenic samples.
  * A file containing filenames of the topographic data, and optional filenames for shielding rasters.
  * A parameter file.
  
The toolkit then produces
  * A csv file that contains results of the analysis.
  * A text file that can be copied into the `CRONUS online calculator <http://hess.ess.washington.edu/math/al_be_v22/al_be_erosion_multiple_v22.php>`_ for data comparison.

Getting your raster into the correct format
=============================================================================================

You will need to get data that you have downloaded into a format LSDTopoToolbox can understand,

One of the things you will need is a raster or rasters containing topographic data for the basins from which sediment was sampled for cosmogenic analyis. 
You can download topographic data from a variety of sources (see :ref:`Data_sources`), 
and this data will come in a variety of formats and with a variety of `projections <http://en.wikipedia.org/wiki/Map_projection>`_.

For our purposes, you will need a `georeferenced <http://en.wikipedia.org/wiki/Georeference>`_ raster that is in a projected coordinate system. 
We recommend using a UTM coordinate system with the WGS 1984 coordinate system.
You can read more about projections and coordinate systems here: :ref:`Converting_data_in_ArcMap` and here: :doc:`gdal_notes`.

The format you **MUST** convert your data to is the **ENVI bil** format: this is a binary format (so that is saves space) 
that has georeferencing (so the cosmogenic program can determine the correct latitude and longitde of your DEM).  

The best way to get rasters into ENVI bil format is with GDAL (read this: :ref:`gdal_translations`).

If you are on a Windows computer you can get GDAL through the open source GIS `QGIS <http://www2.qgis.org/en/site/>`_ (see :doc:`Converting_data_in_QGIS`).



Compiling the comsogenic program
===========================================

The cosmogenic program is distributed as source code. You will need to `compile <http://en.wikipedia.org/wiki/Compiler>`_ this code.
We use the ``g++`` compiler. It is available on virtually all linux distributions. 

If you are on windows, you can get the ``g++`` compilier by installing `cygwin <https://www.cygwin.com/>`_. 
You will have to go into the ``devel`` menu in cygwin to get ``g++``, ``make`` and ``gdb`` when you install. 

When you download the code, you will get a directory with the objects (their names start with LSD). 

Within the directory with the objects, there will be two additional directories:

  - driver_functions
  - TNT
  
The ``TNT`` folder contains routines for linear algebra, and it is made by NIST, you can happily do everything in 
LSDTopoToolbox without ever knowing anything about it, but if you want you can read what it is here: `TNT homepage
<http://math.nist.gov/tnt/>`_.

  #. First, go into the driver function folder
  
  #. Next, type::
  
       make -f Basinwide_CRN.make 
    
     in a `terminal <http://askubuntu.com/questions/38162/what-is-a-terminal-and-how-do-i-open-and-use-it>`_.  
  
  #. You will get a bunch of messages but at the end your code will have 
     compiled. If it has compiled successfully the final message should look 
     like::

      g++ -Wall -O3 Basinwide_cosmogenic_analysis.o 
      ../LSDMostLikelyPartitionsFinder.o ../LSDChiNetwork.o ../LSDIndexRaster.o       
      ../LSDRaster.o ../LSDShapeTools.o ../LSDFlowInfo.o ../LSDJunctionNetwork.o 
      ../LSDIndexChannel.o ../LSDChannel.o ../LSDIndexChannelTree.o 
      ../LSDStatsTools.o ../LSDBasin.o ../LSDParticle.o ../LSDCRNParameters.o 
      ../LSDCosmoData.o ../LSDRasterInfo.o -o Basinwide_CRN.exe 


Setting up your data directories
===========================================

  Before you can run the code, you need to set up some data structures. 
  
    #. You can keep your topographic data separate from your cosmogenic data, if you so desire. 
       You'll need to know the directory paths to these data.  
  
    #. In a single folder (again, it can be separate from topographic data),
       you must put a i) *parameter file*, a *cosmogenic data file*, and a *raster filenames file* .
       
    #. These three files must have the same **prefix**, and each have their own extensions. 
    
         * The *parameter file* has the extension: `.CRNParam`.
         * The *cosmogenic data file* has the extension `_CRNData.csv`.
         * The *raster filenames file* has the extension `_CRNRasters.csv`.
         
    #. For example, if the prefix of your files is `Chile_data_analysis`, 
       then your three data files will be `Chile_data_analysis.CRNParam`,
       `Chile_data_analysis_CRNData.csv`, and `Chile_data_analysis_CRNRasters.csv`.
       
    #. If the files do not have these naming conventions, the code **WILL NOT WORK**! 
       Make sure you have named your files properly. 
       
The parameter file
-----------------------------------------

  This must have the extension *.CRNParam*.
  
  The parameter file could be empty, in which case parameters will just take default values. 
  However, you may set various parameters. The format of the file is::
  
    parameter_name: parameter_value
    
  So for example a parameter file might look like::
  
    min_slope: 0.0001
    source_threshold: 12
    search_radius_nodes: 1
    threshold_stream_order: 1
    theta_step: 30
    phi_step: 30
    Muon_scaling: Braucher
    write_toposhield_raster: true
    write_basin_index_raster: true
    
  In fact all of the available parameters are listed above, and those listed above are default values.
  The parameter names are not case sensitive. The parameter values are case sensitive. 
  These parameters are as follows:
  
    * min_slope: The minimum slope between pixels used in the filling function.
    * source_threshold: The number of pixels that must drain into a pixel to form a channel.
      NOTE this  makes little difference, as the channel network only plays a role in
      setting channel pixels to which cosmo samples will snap. This merely needs to be
      set to a low enough value that ensures there are channels associated with each 
      cosmogenic sample. 
    * search_radius_nodes: The number of pixels around the location of the cosmo location to search for
      a channel. The appropriate setting will depend on the difference between the 
      accuracy of the GPS used to collect sample locations and the resolution of the DEM. 
      If you are using a 30 or 90m DEM, 1 pixel should be sufficient. More should be used
      for LiDAR data. 
    * threshold_stream_order: The minimum stream or which the sample snapping routine considers a 'true' channel. 
    * theta_step: Using in toposhielding calculations. This is the step of azimuth (in degrees) over which shielding and 
      shadowing calculations are performed. `Codilean (2005) <http://onlinelibrary.wiley.com/doi/10.1002/esp.1336/abstract>`_
      recommends 5, but it seems to work without big changes
      differences with 15. An integer that must be divisible by 360 (although if not the code will force it to 
      the closest appropriate integer). 
    * phi_step: Using in toposhielding calculations. This is the step of inclination (in degrees) over which shielding and 
      shadowing calculations are performed. `Codilean (2005) <http://onlinelibrary.wiley.com/doi/10.1002/esp.1336/abstract>`_
      recommends 5, but it seems to work without big changes
      differences with 10. An integer that must be divisible by 360 (although if not the code will force it to 
      the closest appropriate integer).
    * Muon_scaling: The scaling scheme for muons. Options are "Braucher", "Schaller" and "Granger". If you give
      the parameter file something other than this it will default to Braucher scaling. These scalings take values
      reported in `COSMOCALC <http://www.ucl.ac.uk/~ucfbpve/cosmocalc/>`_ as described by 
      `Vermeesch 2007 <http://onlinelibrary.wiley.com/doi/10.1029/2006GC001530/abstract>`_.
    * write_toposhield_raster: If true this writes a toposhielding raster if one does not exist. Saves a bit of time
      but will take up some space on your hard disk!
    * write_basin_index_raster For each DEM this writes an LSDIndexRaster to file with the extension ``_BASINS`` that
      has each of the basins that have been found for CRN analysis listed by basinID.
      
The cosmogenic data file
-------------------------------------------

  This must have the extension *_CRNData.csv*.
  
  This is a ``.csv`` file: that is a comma separated value file. It is in that format to be both excel and 
  `pandas <http://pandas.pydata.org/>`_ friendly. 
  
  The first row is a header that names the columns, 
  after that there should be 7 columns (separated by commas) and unlimited rows. The seven columns are::
  
    sample_name, sample_latitude, sample_longitude, nuclide, concentration, AMS_uncertainty, standardisation
    
  * The sample name should not start with a number or have spaces. 
  * The latitude and longitude should be in decimal degrees. Negative latitude indicates southern hemisphere.
  * Nuclide can be either **"Be10"** or **"Al26"**. Any other option will be rejected. 
  * Concentration is in atoms/gram
  * AMS uncertainty is also in atoms/gram
  * Standardisation is the name of the standards used in the AMS measurements. This is not always so easy to find
    in published papers!! The defaults are "07KNSTD" for 10Be and "KNSTD" for 26Al. These seem to be used by many
    people after 2007 when Kuni Nishiizumi made them available (or at least that is when he published the paper). 
    If the samples are from before 2007 and you don't know the standard use, you should use "KNSTD" for 10Be and 26Al. 
    There are many more standards floating around, but the Nishiizumi one seem the most widely used. 
    The options are (take a deep breath), for 10Be::
        
        "07KNSTD", "KNSTD", "NIST_Certified", "LLNL31000", "LLNL10000", "LLNL3000", "LLNL1000"
        "LLNL300", "NIST_30000", "NIST_30200", "NIST_30300", "NIST_30600", "NIST_27900"
        "S555","S2007", "BEST433", "BEST433N", "S555N", "S2007N"
      
    And for 26Al::
    
        "KNSTD", "ZAL94", "SMAL11", "0", "ZAL94N", "ASTER", "Z92-0222"
        
  An example file would look like this::
    
    Sample_name,Latitude,Longitude,Nuclide,Concentration,Uncertainty,Standardisation
    LC07_01,-32.986389,-71.4225,Be10,100000,2500,07KNSTD
    LC07_04,-32.983528,-71.415556,Be10,150000,2300,07KNSTD
    LC07_06,-32.983028,-71.415833,Al26,4000,2100,KNSTD
    LC07_08,-32.941333,-71.426583,Be10,30000,1500,07KNSTD
    LC07_10,-33.010139,-71.435389,Be10,140000,25000,07KNSTD
    LC07_11,-31.122417,-71.576194,Be10,120502,2500,07KNSTD

The raster names file
------------------------------------------------

  This must have the extension *_CRNRasters.csv*.
  
  This file is a csv file that has as many rows as you have rasters that cover your CRN data. 
  Each row can contain between 1 and 4 columns. 
  
  * The first column is the **FULL** path name to the Elevation raster and its prefix (that is, without the `.bil`, e.g.::
  
    /home/smudd/basin_data/Chile/CRN_basins/Site01/Site_lat26p0_UTM19_DEM
    
  * The next column is **either** a full path name to a snow shielding raster **or** a snow shielding effective depth. 
    Both the raster and the single value should have units of g/cm^2 snow depth. If there is no number here
    the default is 0. 
    
  * The next column is **either** a full path name to a self shielding raster **or** a self shielding effective depth. 
    Both the raster and the single value should have units of g/cm^2 shielding depth. If there is no number here
    the default is 0. 
    
  * The next column is the **FULL** path to a toposhielding raster. If this is blank the code will run topographic
    shielding for you. Note: topographic shielding is the rate limiting step in the cosmo analysis. 
    
  A typical file might will look like this::
  
    /home/smudd/basin_data/Site01/Site01_DEM,0,0,/home/smudd/basin_data/Site01/Site01_DEM_TopoShield
    /home/smudd/basin_data/Site02/Site02_DEM,5,10
    /home/smudd/basin_data/Site03/Site03_DEM,5,/home/smudd/basin_data/Site03/Site03_DEM_SelfShield
    /home/smudd/basin_data/Site04/Site04_DEM,/home/smudd/basin_data/Site04/Site04_DEM_SnowShield,/home/smudd/basin_data/Site04/Site04_DEM_SelfShield
    /home/smudd/basin_data/Site05/Site05_DEM
    /home/smudd/basin_data/Site06/Site06_DEM,/home/smudd/basin_data/Site06/Site06_DEM_SnowShield


Running the driver function
===========================================

Okay, now you are ready to run the driver function. You'll need to run the function from the directory where the compiled code is located 
(``extract_typical_metrics.exe``), but it can work on data in some arbitrary location. 


  1. You run the code with the command ``./Basinwide_CRN.exe pathname_of_data file_prefix``
  
    * The pathname_of_data is just the path to where your data is stored, so for example I put some data into a folder with the path
      ``/home/smudd/basin_data/Chile/CRN_data_analysis/``. **IMPORTANT** You *MUST* remember to put a ``/`` at the end of your pathname. 
    
    * The filename is the **PREFIX** of the files you need for the analysis (that is, without the extension). 


The output files
===========================================

There are two output files. Both of these files will end up in the ``pathname`` that you designated when calling the program. 

The first is called ``file_prefix_CRNResults.csv`` and the second is called ``file_prefix_CRONUSInput.txt``
where file_prefix is the prefix you gave when you called the program. 

So, for example, if you called the program with:: 

  ./basinwide_CRN.exe /home/smudd/basin_data/Chile/CRN_data_analysis/ My_Chile_Data
  
The outfiles will be called::

  My_Chile_Data_CRNResults.csv
  My_Chile_Data_CRONUSInput.txt
  
The ``_CRONUSInput.txt`` is formatted to be cut and pasted directly into the CRONUS calculator. 
The file has some notes (which are pasted into the top of the file)::

    ->IMPORTANT nuclide concentrations are not original!
      They are scaled to the 07KNSTD!!
    ->Scaling is averaged over the basin for snow, self and topographic shielding.
    ->Snow and self shielding are considered by neutron spallation only.
    ->Pressure is an effective pressure that reproduces Stone scaled production
      that is calculated on a pixel by pixel basis.
    ->Self shielding is embedded in the shielding calculation and so
      sample thickness is set to 0.
  
The ``_CRNResults.csv`` is rather long. It contains the following data in comma separated columns::

      basinID     (A unique identifier for each CRN sample)
      sample_name
      nuclide     (either 10Be or 26Al)
      latitude
      longitude
      concentration (atms/g)
      concentration_uncert (atoms/g)
      erosion rate g_percm2_peryr
      erosion rate AMS_uncert g_percm2_peryr
      muon_uncert g_percm2_peryr
      production_uncert g_percm2_peryr
      total_uncert g_percm2_peryr
      AvgProdScaling dimensionless
      AverageTopoShielding dimensionless
      AverageSelfShielding dimensionless
      AverageSnowShielding dimensionless
      AverageCombinedScaling dimensionless (this is averaged production scaling times toposhielding)
      outlet_latitude
      OutletPressure hPa
      OutletEffPressure hPa (pressure needed to get basin averaged production scaling)
      centroid_latitude
      CentroidPressure hPa
      CentroidEffPressure (pressure needed to get basin averaged production scaling)
      ErosionRate_COSMOCALC_in_g_percm2_peryr (assumes 2650 kg/m^2): The erosion
          rate you would get if you took production weighted scaling and used
          cosmocalc. 
      ErosionRate_COSMOCALC_mmperkyr (assumes 2650 kg/m^2): The erosion
          rate you would get if you took production weighted scaling and used
          cosmocalc. 
      ErosionRate_in_mmperkyr (to check against cosmocalc, assumes 2650 kg/m^2)
      ErosionRate_totaluncertainty_in_mmperkyr

