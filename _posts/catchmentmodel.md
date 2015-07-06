==================================================================
Landscape Modelling with LSDTopoTools: LSDCatchmentModel
==================================================================

In addition to LSDRasterModel, LSDTopoTools features an implementation of a catchment-scale landscape evoltion model with a coupled hydrological module. The implementation is heavily based on the CAESAR-Lisflood model. It is designed to be integrated with the LSDTopoTools package, but can be run on its own as a standalone modelling environment. 

What does this model do?
========================
It is designed to model shorter term landscape processes and evolution within indivdual catchments. The key features are a hydrologically non-steady state flow routing component. In other words, the model explicity calculates 2D water flow dynamics from rainfall input across the landscape using a simplified version of the shallow water equations. Most other LEMs calculate water fluxes based on some simplified parameterisation involving drainage area. The model timestep is typically much shorter than many LEMs and so while there is theorertically no limit to the length of timescale you wish to simulate, bear in mind that long runs over thousands of years with complex rainfall distributions will take a *long* time. The model was designed with simulating landscape response to individual storms, seasonal changes, with holocene landscape evolution as a suggested upper limit in terms of time scale.

It is designed to simulate individual catchments, with water flowing out at a single edge cell(s), so preparation of your DEM should reflect this requirement. 

**Why not just use the original, GUI version of CAESAER-Lisflood?** 

You can do if you want to! If you are happy to run on Windows, and would prefer to use a model in GUI-mode then CAESAR-Lisflood is a good choice for this. This branch of the model is designed to be cross platform, though with an emphasis on running on a linux environment, and hopefully extendable to running on a supercomputer if the resources are available to you. One of the drawbacks to the original model is that you need a dedicated Windows box to do your simulations, you can't send them off to a cluster computer type facility. 

With this version you can also perform topographic analysis within the same LSDTopoTools environment, switching easily between modelling and topographic analysis.

Also note that this version of the model does not yet implement some of the more specialised features in CAESAR-Lisflood, such as dune modelling, river reach modelling, and so on.

How does LSDCatchmentModel fit in with LSDRasterModel?
======================================================
It should compliment the LSDRasterModel, which is better suited to large-scale, topographic evolution over long geological time periods, and has a much faster implicit solver based on Jean Braun's FastScape algorithm.

References:

Coulthard, T. J., Neal, J. C., Bates, P. D., Ramirez, J., Almeida, G. A., & Hancock, G. R. (2013). *Integrating the LISFLOOD‐FP 2D hydrodynamic model with the CAESAR model: implications for modelling landscape evolution.* Earth Surface Processes and Landforms, 38(15), 1897-1906.

Bates, P. D., Horritt, M. S., & Fewtrell, T. J. (2010). *A simple inertial formulation of the shallow water equations for efficient two-dimensional flood inundation modelling.* Journal of Hydrology, 387(1), 33-45.

Brief Usage Guide
=================
The model runs from the command line/terminal/console. You specify the model executable name (CatchmentModel.out) followed by the path name to the parameter file and the parameter file itself. The model will print out updates to the terminal window regularly, keeping you updated to the stage it is at and if there are any errors. The DEM of your catchment must be present in the same folder as your parameter file and must be correctly formatted.

DEM preparation
===============
Currently, you will have to prepare your own DEM as a separate stage in the workflow. (Using whichever GIS tool you like, or preferably our own software!). As mentioned before, the DEM should be set up so that one side of the catchment will act as the flow exit point. This means the lowest elevations of you model should be on these edge cells to allow water to flow outwards, otherwise you will get unrealistic pooling of water and flood the entire catchment. *There should be no NODATA values between the edge cells and the farthest cells of the DEM file.* This is very important for the model to work correctly. 


Model Parameters
================
You can see an example input file in CatchmentModel.params. More info on what each of these do will come soon.
