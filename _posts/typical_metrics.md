===========================================================
A simple first example: extracting typical metrics
===========================================================

Here we explain how to run a simple driver: extract_basin_metrics.cpp

This driver extracts the following derived rasters from a user supplied .flt raster

  - A filled raster, with ``_fill`` in the filename
  - A hillshade raster, with ``_hs`` in the filename
  - A slope raster, with ``_slope`` in the filename. This is calculated using a polynomial fit.
  - A curvature raster, with ``_curv`` in the filename. This is calculated using a polynomial fit.
  - A tangential curvature raster, with ``_tan_curv`` in the filename. This is calculated using a polynomial fit.
  - A contributing pixel raster, with ``_CP`` in the filename. This is calculated using D8 with the Fastscape (Braun and Willett, Geomorphology 2013) algorithm.
  - A DInfinity drainage area raster, with ``_DinfDA`` in the filename. Calculated based on the D-infinity algorithm (Tarbonton, 1997, WRR).
  - A flow distance raster, with ``_FD`` in the filename. Derived from D8 flow routing. 

Getting your raster into the correct format
===========================================

You will need to get data that you have downloaded into a format LSDTopoToolbox can understand, namely the `.flt` format. 
  
  You can do this with arcmap; (read this: :ref:`Converting_data_in_ArcMap`), or with GDAL (read this: :ref:`gdal_translations`). 

  You can also do it with `QGIS <http://www.qgis.org/en/site>`_.

Compiling the driver function
===========================================

You will need to download all of the code. This will have a directory with the objects (their names start with LSD). 

Within the directory with the objects, there will be two additional directories:

  - driver_functions
  - TNT
  
The ``TNT`` folder contains routines for linear algebra, and it is made by NIST, you can happily do everything in 
LSDTopoToolbox without ever knowing anything about it, but if you want you can read what it is here: `TNT homepage
<http://math.nist.gov/tnt/>`_.

  #. First, go into the driver function folder
  
  #. Next, type::
  
       make -f extract_typical_metrics.make 
    
     in a `terminal <http://askubuntu.com/questions/38162/what-is-a-terminal-and-how-do-i-open-and-use-it>`_.  
  
  #. You will get a bunch of messages but at the end your code will have compiled. 
  
Running the driver function
===========================================

Okay, now you are ready to run the driver function. You'll need to run the function from the directory where the compiled code is located 
(``extract_typical_metrics.exe``), but it can work on data in some arbitrary location. 

  1. You run the code with the command ``./extract_typical_metrics.exe pathname_of_data name_of_DEM filetype``
  
    * The pathname_of_data is just the path to where your data is stored, so for example I put some data into a folder with the path
      ``/home/smudd/topographic_tools/LSDRaster_local/Data/Chile/lat28p5/``. **IMPORTANT** You *MUST* remember to put a ``/`` at the end of your pathname. 
    
    * The filename is the **PREFIX** of your DEM, so if your DEM is called ``lat_26p5_flt.flt`` then the ``name_of_DEM`` is ``lat_26p5_flt``
      (that is, without the ``.flt`` at the end). 

    * The file type. Right now the only options are ``flt`` and ``asc``. You do not include the full stop (i.e., ``.asc`` will not work).
      
  2. This will write out a bunch of rasters to the folder indicated by ``pathname_of_data``. It choses default parameters for the computations, these are:
      
    * The threshold number of pixels that generate a channel.
    
    * The search radius of the window for polynomial surface fitting. 
    
  3. Alternatively, you can write a file called ``topo_metrics.param`` that sits in the same directory as your DEM and has the ``threshold`` and the ``window_radius`` in plain text, separated by a space. 
    
