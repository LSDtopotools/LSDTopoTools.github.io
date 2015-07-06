.. _gdal-notes-top:
  
====================================================================================
Data preparation 2: Notes for using GDAL to manipulate files for LSDTopoToolbox
====================================================================================

`GDAL (the Geospatial Data Abstraction Library) <http://www.gdal.org/>`_ is a popular software package for manipulating geospatial data. 
GDAL allows for manipulation of geospatial data in the Linux operating system, and for most operations is much faster than GUI-based GIS systems (e.g., ArcMap). 
Here we give some notes on common operations in GDAL that one might use when working with LSDTopoTools.
Much of these operations are carried out using `GDAL's utility programs <http://www.gdal.org/gdal_utilities.html>`_.

Finding out what sort of data you've got
============================================= 

One of the most frequent operations in GDAL is just to see what sort of data you have. The tool for doing this is ``gdalinfo`` which is run with the command line::

  $ gdalinfo filename.ext

where ``filename.ext`` is the name of your raster. 

This is used mostly to:

  i) See what projection your raster is in.
  ii) Check the extent of the raster. 

This utility can read Arc formatted rasters but you need to navigate into the folder of the raster and use the ``.adf`` file as the filename.
There are sometimes more than one ``.adf`` files so you'll just need to use ``ls -l`` to find the biggest one. 

.. _gdal_translations:

Translating your raster into something that can be used by LSDTopoToolbox
=============================================================================

Say you have a raster but it is in the wrong format (LSDTopoToolbox at the 
moment only takes ``.bil``, ``.flt`` and ``.asc`` files) and in the wrong 
projection. 
Note: *LDSTopoToolbox performs many of its analyses on the basis of projected coordinates.* 

You will need to be able to both change the projection of your rasters and change the format of your raster.
The two utilities for this are:

  * `gdalwarp <http://www.gdal.org/gdalwarp.html>`_
  * `gdaltranslate <http://www.gdal.org/gdal_translate.html>`_


Changing raster projections with gdalwarp
------------------------------------------    

  The preferred coordinate system is WGS84 UTM coordinates. 
  For convert to this coordinate system you use ``gdalwarp``. 
  The coordinate system of the source raster can be detected by gdal, so you use the flag ``-t_srs`` to assign the target coordinate system.
  Details about the target coordinate system are in quotes, you want::
  
    '+proj=utm +zone=XX +datum=WGS84'  
  
  where ``XX`` is the UTM zone. `Here is a map of UTM zones <http://www.dmap.co.uk/utmworld.htm>`_. 
  For example, if you want zone 44 (where the headwaters of the Ganges are), you would use::
  
    '+proj=utm +zone=XX +datum=WGS84'
    
  Put this together with a source and target filename::
  
    $ gdalwarp -t_srs '+proj=utm +zone=XX +datum=WGS84' source.ext target.ext
    
  so one example would be::
  
    $ gdalwarp -t_srs '+proj=utm +zone=44 +datum=WGS84' diff0715_0612_clip.tif diff0715_0612_clip_UTM44.tif
    
  note that if you are using UTM and you are in the southern hemisphere, you should use the ``+south`` flag::
  
    $ gdalwarp -t_srs '+proj=utm +zone=19 +south +datum=WGS84' 20131228_tsx_20131228_tdx.height.gc.tif Chile_small.tif
    
  There are several other flags that could be quite handy (for a complete list see `here <http://www.gdal.org/gdalwarp.html>`_). 
  
  * ``-of`` `format`: This sets the format to the selected format. This means you can skip the step of changing formats with ``gdal_translate``. 
    We will repeat this later but the formats for `LSDTopoTools` are
    
      * `AAIGrid` for ASCII files. These files are huge so try not to use them.
      * `EHdr` for ESRI float files. This used to be the only binary option but GDAL seems to struggle with it and it doesn't retain georeferencing.
      * `ENVI` for ENVI rasters. **This is the preferred format**. 
        GDAL deals with these files well and they retain georeferencing. We use the extension `bil` with these files. 
    
    So, for example, you could output the file as::
    
      $ gdalwarp -t_srs '+proj=utm +zone=44 +datum=WGS84' -of ENVI diff0715_0612_clip.tif diff0715_0612_clip_UTM44.bil
      
    Or for the southern hemisphere:
    
      $ gdalwarp -t_srs '+proj=utm +zone=19 +south +datum=WGS84' -of ENVI 20131228_tsx_20131228_tdx.height.gc.tif Chile_small.bil
  
  * ``-tr`` `xres yres`: This sets the x and y resolution of the output DEM. It uses nearest neighbour resampling by default. 
    So say you wanted to resample to 4 metres::
    
      $ gdalwarp -t_srs '+proj=utm +zone=44 +datum=WGS84' -tr 4 4 diff0715_0612_clip.tif diff0715_0612_clip_UTM44_rs4.tif
      
    IMPORTANT: LSDRasters assume square cells so you need both x any y distances to be the same
      
  * ``-r`` `resampling_method`: This allows you to select the resampling method. The options are: 

    * `near`: nearest neighbour resampling (default, fastest algorithm, worst interpolation quality).
    * `bilinear`: bilinear resampling.
    * `cubic`: cubic resampling.
    * `cubicspline`: cubic spline resampling.
    * `lanczos`: Lanczos windowed sinc resampling.
    * `average`: average resampling, computes the average of all non-NODATA contributing pixels. (GDAL >= 1.10.0)
    * `mode`: mode resampling, selects the value which appears most often of all the sampled points. (GDAL >= 1.10.0)   
    
    So for example you could do a cubic resampling with:: 
    
     $ gdalwarp -t_srs '+proj=utm +zone=44 +datum=WGS84' -tr 4 4 -r cubic diff0715_0612_clip.tif diff0715_0612_clip_UTM44_rs4.tif 
     
  * ``-te`` `<x_min> <y_min> <x_max> <y_max>`: this clips the raster. You can see more about this below in :ref:`Clip-using-gdal`.
  
  * **UTM South**: If you are looking at maps in the southern hemisphere, you need to use the ``+south`` flag::
  
      $ gdalwarp -t_srs '+proj=utm +zone=44 +south +datum=WGS84' -of ENVI diff0715_0612_clip.tif diff0715_0612_clip_UTM44.bil   
     
Changing raster format with gdal_translate
--------------------------------------------
  
  Suppose you have a raster in UTM coordinates (`click here for UTM zones <http://www.dmap.co.uk/utmworld.htm>`_) but it is not in ``.flt`` format. 
  You can change the format using ``gdal_translate`` (note the underscore). 
  
  `gdal_translate <http://www.gdal.org/gdal_translate.html>`_ recognises many file formats (`see list here <http://gdal.org/formats_list.html>`_), 
  but for LSDTopoTools you want either:
  
    * The *ESRI .hdr labelled* format, which is denoted with ``EHdr``. 
    * The *ENVI .hdr labelled* format, which is denoted with ``ENVI``. ENVI files are preferred since they work better with GDAL and retain georeferencing.       
  
  To set the file format you use the ``-of`` flag, an example would be::
  
    $ gdal_translate -of ENVI diff0715_0612_clip_UTM44.tif diff0715_0612_clip_UTM44.bil 
    
  Where the first ``filename.ext`` is the source file and the second is the output file. 


.. _Clip-using-gdal: 


Potential filename errors
--------------------------

  It appears that GDAL considers filenames to be case-insensitive, which can cause data management problems
  in some cases. The following files are both considered the same::
  
    Feather_DEM.bil feather_dem.bil
  
  This can result in an ESRI ``*.hdr`` file overwriting an ENVI ``*.hdr`` file and causing the code to fail to 
  load the data. To avoid this ensure that input and output filenames from GDAL processes are unique.    


Clipping rasters with gdal
================================================

You might also want to clip your raster to a smaller area. This can sometimes take ages on GUI-based GISs. 
An alternative is to use ``gdalwarp`` for clipping::

  $ gdalwarp -te <x_min> <y_min> <x_max> <y_max> input.tif clipped_output.tif 
  
Since this is a ``gdalwarp`` operation, you can add all the bells and whistles to this, such as:

  * changing the coordinate system,
  * resampling the DEM,
  * changing the file format. 
  
The main thing to note about the ``-te`` operation is that the clip will be in the coordinates of the source raster (``input.tif``). 
You can look at the extent of the raster using ``gdalinfo``.
    
    
  




