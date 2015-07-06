==================================================================
Landscape Evolution Modelling with LSDTopoTools
==================================================================

The LSD Topo Toolbox contains a landscape evolution model built on the core components of the LSD software. The model implementation is contained in the LSDRasterModel files. The driver file is `model_driver.cpp`. The landscape model is based on the FastScape algorithm (Braun 2013) and the MUDDPile algorithms. It contains both hillslope and fluvial components, which can be run individually, simulating a single hillside for example, or together, to simulate whole landscape evolution. The model will run from default parameters if none are supplied, but the user can also specify their own choice of parameters for the model run, which are listed below.  

Parameter File
==============
The parameter file is just a plain text file with the following layout

.. code:: python 

	# Template for parameter file
	Run Name:		default_name
	NRows:			100
	NCols:			100
	Resolution:		1
	Boundary code:		bnbn 	North, east, south, west
	# b = base level, p = periodic, n = no flow (default)
	Time step:		50
	End time:		2000
	End time mode:		1	 (if 1, wait for steady state to set the time to count down)
	Uplift mode:		0	 Block uplift
	Max uplift:		0.001
	Tolerance:		0.0001
	Print interval:		5
	#Periodicity:		1000
	
	#####################
	Fluvial:		on
	K:			0.01
	m:			0.5
	n:			1
	K mode:			0	constant
	#K amplitude:		0.005
	
	#####################
	Hillslope:		on
	Non-linear:		off
	Threshold drainage:	-1	(if negative, ignored)
	D:			0.05
	S_c:			30	degrees
	D mode:			0	Constant
	#D amplitude:		0.005
	
	#####################
	Isostasy:		off
	Flexure:		off
	Rigidity:		1000000


Model Domain
============
**Run Name**
	This is the name that will be appened to all output files generated during the model run. The model will check to see if this name has already been used before overwriting existing output files.
**NRows**
	This is the number of rows in the model domain. You can also think of it as the *y* dimension of the model grid.
**NCols**
	This is the number of columns in the model domain. You can also think of it as thee *x* dimension of the model grid.
**Resolution**
	The resolution of the model grid. The size in metres that a single grid cell represents.
**Boundary code**
	This code determines the output sides of the model grid. I.e. which sides are for sediment output and which are not. The *b* represents base level, *n* represents no flow, and *p* represents periodic boundary conditions.
**Time step**
	The model timestep, *dt*
**End time**
	The end time for the model run
**End time mode**
	This sets the model to either run until it reaches steady state (=1), or until a specified time (=0).
**Uplift mode**
	Instructs the model to use block, uniform uplift, or an uplift field.
**Max uplift**
	The uplift rate (m/yr)

**Tolerance**
	This parameter sets the iteration tolerance value in the LSDRasterModel object. The value is related to the implicit solver and when the solution is considered to be 'converged upon' and the numerical soultion solved. (DAV - needs more explanation?)
**Print interval**
	The output file interval.





Fluvial Component
=================
**Fluvial**
	Turns the fluvial component **on** or **off**, with these respective keywords.
**K**
	The *K* paramter used in the many forms of the stream power equation and its derivatives. *K* can also be thought of as the erodibility. Typical values for K are something like 0.0001 to 0.002, but these can vary significantly between different lithologies/environments etc. The default is 0.0002. 
**m**
	The *m* exponent of the stream power law. Typical values of the m/n ratio are between 0.3 and 0.7, but consult the literature if it is availble for your study area. The ratio is related to the concavity of the idealised river profile. The default value of *m* is 0.5. 
**n**
	The *n* exponent of the stream power law. Typical values are around 1.0. (Which is the default value). The above parameters are related to each other in the stream power equation as below:
	
.. math::
	
	I = KA^mS^n
	
where *I* is the incision rate, *A* is the drainage area, and *S* is the slope of the channel. The fluvial component of the model is based on this equation, which is a good approximation in many bedrock mountainous landscapes, though your mileage may vary.

**K mode**
	Sets the K value to be constant (**0** is default, meaning constant).



Hillslope Component
===================
The hillslope component comes in two flavours, a linear model and a non-linear one. 

**Hillslope**
	Turns the hillslope component **on** or **off**
**Non-linear**
	Sets the hillslope law to linear or non-linear. (**on** or **off**)
**D**
	The soil transport coefficient. The *D* value is used in calculating the soil creep functions in the model.
**S_c**
	The critical slope angle. The default is 30 degrees.
	
Running the Model
=================

Once compiled, the model is run using::
	
	./model.out [parameter.file] [run_name]

Screen output should help the user see if the components/parameters have run as expected.

Model Output
============


