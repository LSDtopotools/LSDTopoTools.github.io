==================================================================
Installing Dependencies and Compiling - External Users
==================================================================

The documentation available on this page is for users outwith the University of Edinburgh, or those wishing to run the code on their own laptops away from the University. It contains notes on how to set up some of the required libraries used by some parts of the code.

Our code is designed to be run in the Linux operating environment. 
You can actually get your windows computer to act a bit like a Linux terminal by downloading the Cygwin package (see appendix). 
If you work on a Mac you can also get Linux because the Mac operating system is built on top of Linux: 
to get there you need to call the 'terminal' application.

Basic Requirements
-------------------
Essentials
^^^^^^^^^^

#. C++ compiler (we have tested it on ``gcc``/``g++``)

#. ``make`` 

TNT
^^^
http://math.nist.gov/tnt/overview.html

The most common features of the code are not too heavily dependent on external libraries, however. One that is required for all analyses is the **TNT (Template Numerical Toolkit)** library, this is used for the storage of array-like data. However this is distributed with the code and you do not need to install it separately. All the required files are in the ``./TNT`` folder. 


Requirements for LSDRasterSpectral
----------------------------------
Fast Fourier Transform Library
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
http://www.fftw.org/

Any analyses that use the RasterSpectral objects, which includes the LSDRasterModel, require the **fast fourier transform libraries**. In the source code, you will find ``#include`` statements for these libraries, and corresponding library flags in the makefile: ``-lfftw3``. In the RasterSpectral source files, we assume that you will have a fast fourier transform folder in your toplevel LSDTopoTools directory.

You can download FFTWv3 here: http://www.fftw.org/download.html

Requirements for LSDRasterModel
-------------------------------

RasterModel is dependent on RasterSpectral, so the above requirements apply for the model as well (fftw3). 

Boost
^^^^^
In addition, it uses the popular C++ library, **Boost**. Boost can be installed in a number of ways. In the source code, we treat it like a normal ``#include`` header file and the compiler should be able to find the necessary headers in the standard linux locations such as ``/usr/lib/boost`` or some variant of that.

Boost can be installed in many linux distros simply by using the ``yum`` or ``apt-get`` commands. You need the ``devel`` flavour. For example::

  yum install boost-devel
  
Would work in Fedora Linux and install boost in a sensible place where the complier would look for the boost libraries and headers. 

MTL
^^^
MTL is the *Matrix Template Library*. This is also used in the LSDRasterModel object. It can be downloaded from the MTL4 site here: http://www.simunova.com/node/145 and it should be placed in the ``boost/numeric/mtl`` subdirectory, wherever you have boost installed on your system. 

Requirements for LSDSwath (and LSDCloudBase)
---------------------------------------------

OKay, now things get a little more complicated because you want to use the Swath Profile tools or the LSDCloudBase object. These objects are dependent on a set of libraries used for analysing point cloud data, namely ``pcl`` (Point Cloud Library) and ``libLAS``, a library for working with LAS format data. At Edinburgh, these are installed on the cluster computers, but probably not if you are working externaly, e.g. on your own laptop.

You will also need the ``CMake`` utility as the driver files associate with these objects do not use the standard makefiles. 

PCL
^^^
Before you can install PCL however, it itself is dependent on some other things...

#. blas (blas-devel)
#. eigen3 (eigen-devel)
#. flann
#. libLAS

PCL, blas-devel, flann, eigen3 etc. can be installed in linux using the yum or apt-get commands in many distributions::

  yum install blas-devel flann-devel eigen3-devel pcl-devel
  
You will need to check the exact names of these packages in your package repository manager first.

If you can't install using the above method, you will have to do a manual install following the instruction on the relevant websites. The PCL website is a good place to start for guidance. 

After installing these, you may run into the further problem that the location of the libraries on your system are not where the compiler thinks they are, because the installation folders are named by the version number, e.g. ``/pcl-1.7.2`` rather than just ``/pcl``. You can get around this by creating symbolic links to these folders. From the include directory on your system, (mine was /usr/include/), type::

  ln -s /usr/include/pcl-x.x.x /usr/include/pcl
  
and the same goes for the ``eigen`` library::

  ln -s /usr/include/eigen3 /usr/include/Eigen
  
Right, we are nearly ready to go!

Except for the last library dependency...
  
libLAS
^^^^^^
http://www.liblas.org/

If you thought **pcl** was a faff, **libLAS** takes the biscuit. It isn't included in most linux distro package repositories, so you have to install it manually. (Do not confuse it with ``Blas`` or ``libblas``, which ARE in the linux pacakge repositories but have nothing to do with the libLAS that we want.)

First of all, it is dependent on the ``libgeotiff-devel`` library, so go ahead and install that using yum or apt-get::

  yum install libgeotiff-devel
  
It can be a good idea to update the library paths in linux at this point::

  sudo ldconfig
  
Great. Now libLAS is also dependent on another package called **laszip**. The developers thought it would be too easy to simply include this as one package/download, so you first have to install this as well before we can even get started on the actual libLAS package. 

Get it here:  http://www.laszip.org/

Once you've unzipped it, in the top level directory run::

  ./configure
  make
  sudo make install
  
They should be installed in the ``/usr/local/include/`` directory. Which is bad, because they need to be in their own directory. So you have to root into the ``/usr/local/include/``, create a ``laszip`` directory, and copy the laszip header files into this directory.

Magic. Now, we can install libLAS. It uses cmake to install itself. So in the libLAS directory, ``mkdir`` a ``build`` folder, ``cd`` into that, and run::

  ccmake -G "Unix Makefiles" ../ 

A terminal dialogue opens. Make sure it has found the GEOTIFF directory. If not, you will need to find where the geotiff (see above) stuff was installed to, and enter the full path in the GEOTIFF box. 

Now look at the options below (we are still running ccmake here). Turn all the optional settings to ``OFF``, it just makes things easier and less likely to go wrong during compilation. Hit configure. Save and exit. 

Now run::

  make
  sudo make install
  
Hopefully, cmake should do its thing and install libLAS for you. If not, open a large bottle of whisky and repeat the above steps to check you haven't missed anything. I find a peaty single malt works best here.
  
  

  
  
  
  
      
  
  
  



    

