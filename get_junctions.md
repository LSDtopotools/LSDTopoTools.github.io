=====================================================================
Extracting Junctions of a Defined Stream Order
=====================================================================

This page gives an overview of how to use the JunctionGetter driver 
to extract junctions of a given order for use in other analyses. 

Quick guide
=================================================

Here is a quick overview of how to set up and run the code, if you have done it before:

#. Create a parameter file.
#. Compile the code with: make -f JunctionGetter.make
#. Run the program ``JunctionGetter.out`` using the data path and the filenames of the parameter file.

Overview
=================================================

Simple driver to get the junctions of all non-beheaded basins in a DEM of a
given stream order. The tool will generate:

#. A text file of all valid junction numbers called ``Junctions.txt``

Warning
=================================================

This code is for research purposes and is under continuous development, so we cannot guarantee a bug-free experience!

Before You Start
=================================================
See the other docs on this site for help with loading data into the correct format 
and general help with using this suite of software: :doc:`float`

Compile The Driver
=================================================

The code is compiled using the provided makefile, JunctionGetter.make and the command:

``make -f JunctionGetter.make`` 

Which will create the binary file, JunctionGetter.out to be executed.

Run The Code
=================================================

The driver is run with three arguments: 

#. The data path where the DEM is stored.
#. The name of the DEM file, without the extension. **Must be in flt format.**
#. Stream order of junctions to be extracted.

The syntax on a unix machine is as follows:

``./JunctionGetter.out <path to DEM> <DEM filename> <stream order>``

And an complete example (your path and filenames may vary):

``./JunctionGetter.out /home/s0675405/basin_met_update/data/ Macon_DEM 2``

The Results
=================================================

Junction File 
--------------------------------------------------------

The output junction file follows a simple format of a single junction value on each line, 
with no headers or other data and is stored in the same path as the input DEM.

 .. image:: ./_images/BasinMetrics/junction_file.jpg