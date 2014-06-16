---
layout: post
title: "GADGET-2 portlet development (1)"
description: Development of a portlet for the GADGET-2 application		
headline: We're working on a portlet for the easy submission of massive simulations of astrophysical hydrodynamics
modified: 2014-06-13
category: personal
tags: [Astrophysics, simulations, science gateway, portlets, GADGET2]
image: 
  feature: milleniumSim.png
  comments: true
mathjax: 
---
# TL;DR 
We've made some great strides in developing a developer-friendly environment for porting applications to the grid, and the time has come to put a "real" application and its user community to the test. We chose GADGET2, an astrophysical hydrodynamic simulations toolkiit with some nontrivial dependencies for a first attempt. This is a first report back of the work done.

# GADGET-2

GADGET-2 - ***GA****laxies with* ***D****ark Matter and* ***G****as Int****E****rac****T*** - is a smoothed-particle hydrodymanics simulation software which is widely-used in the astrophysics and cosmology community, developed by the Max Planck Institute for Astronomy in Garching. GADGET-2 is a massively-parallel application, which is MPI-aware and designed to run on anything from a user's laptop to the biggest supercomputers in the world. It is so widely used that it forms one of the benchmarks of the cluster competition of the annual Supercomputing conference. 
Since it's so widely used, we are interested in developing a general-purpose science gateway for it, which could allow astrophysicists to submit simulations of their problem area to various computing infrastructures. 

## Design and usage considerations

We first discussed the idea with the SARChI chair in Cosmology at the University of the Western Cape, Romeel Dave, to get some idea of how this application is used *in the wild*. Since it's a cutting-edge research tool, we were told, it's probably unwise to provide it without any kind of context of limitation to the general public. As in the case of the WRF toolkit, many scientifically incorrect conclusions can be drawn from a powerful simulator, if the user isn't careful; just because you can run a simulation doesn't mean it makes sense. While this kind of incorrect or abusive usage would probably be picked up by a serious journal, it would be a bit irresponsible to throw it out there, enabling runs on supercomputers to just anybody. 

We fully understand this point of view and can make the necessary access and submission limitations on the gateway. This is an attractive feature of the gateway, that we have a single point of access, tracking and accountability for applications such as these; even as they open up the infrastructure to many many users in a transparent way, they provide the scientific community interested in these applications to understand better who is running jobs and how. We'll talk more about that in a separate post.

## Co-Design and reaching the goal.

> ***Our goal ?*** *To have as many people running GADGET on as many resources, for as much time as makes sense, in a way that appeals to the user and is acceptable to the infrastructure providers.*

GADGET-2 is one of those applications that is being used by many people in many different ways. What is more, in the process of developing this service, there are a few different role-players, all needing to collaborate with each other : 
 
 * The application porting expert
   : This role is responsible for ensuring that the application will actually execute on the backend infrastructure. This role needs to make sure that the application can be compiled for whatever infrastructure is going to be attached to the science gateway, as well as ensure that the relevant dependencies will be available.
 * The infrastructure administrator
   : The infrastructure administrator (which may be the same person, or many different people, depending on how many infrastructures we want to let our application run on) needs to be aware of the needs of the application, and approve its execution on it.
 * The gateway administrator
   : This role is usually a single one, and could even be delegated to the community itself. This role needs to approve the deployment of the portlet which will provide the user interface to the application, and eventually approve new users of the application after they request access.
 * The portlet developer
   : The portlet developer, in the initial phase, is as important as the user, since they will be implementing the features requested by the user, and act on feedback during [UX](http://en.wikipedia.org/wiki/User_experience)[^UX]
 * The target user or user community
   : Of paramount importance is the actual user. Without the adoption of this work by a significant community, and its continued usage, the effort would be difficult to justify.
<figure>
	<img src="{{site.url}}/images/UX1a1.png">
	<figcaption><title="Six aspects of User Experience to create a valuable design."></figcaption>
</figure>
Acting in isolation, none of these roles could reach the ultimate goal we've set for ourselves; This is part of the reason why SAGrid exists - to stimulate this kind of collaboration and foster co-development of this kind. 


## GADGET-2 and dependencies

GADGET relies on several libraries to be executed : **OpenMPI**, **HDF5**, **GSL**, **FFTW**. These libraries are usually an integral part of the *execution environment*. The users and developers of the portlet can assume that these dependencies will be available on the execution environment, whether that be SAGrid, or some other cloud/grid/hpc infrastructure. We've already worked on the GADGET-2 application integration, and you can see the build status and history in the [SAGrid CI server](http://ci.sagrid.ac.za:8080/view/All/job/GADGET/)

# Typical workflow 
<figure>
	<img src="{{site.url}}/images/gadget workflow.svg">
	<figcaption>Figure 1: Typical GADGET2 workflow. The scientist describes a problem in terms of cosmological or astrophysical initial conditions, then generates the initial conditions with MUSIC, and uses this to execute GADGET with a specified parameter file. </figcaption>

</figure>

Typical usage of GADGET can be seen in figure 1:
  1. **Generate initial conditions**
     : The scientist needs to describe the initial conditions in the ***MUSIC*** `.conf` file. This creates an `ics.dat` file which contains the initial conditions in an internal MUSIC data format. 
  1. **Define simulation parameters** 
     : The scientist then needs to provide the ***GADGET2*** simulation parameters which will be used by GADGET to make the run. These are usually contained in a `.param` file.
  1. **Run the simulation**
     : Once both the initial conditions and the param file have been created or otherwise provided, the simulation can be run. This is usually preceded by a recompilation of GADGET for that particular run.

## GADGET compilation *on the fly*

Much of GADGET's functionality is defined in the Makefile. This means that GADGET needs to be recompiled just before runtime, with the Makefile of the run. Aspects of GADGET which are changed by the Makefile are :

````
#--------------------------------------- Basic operation mode of code
#OPT   +=  -DPERIODIC 
OPT   +=  -DUNEQUALSOFTENINGS
````

... periodic boundary conditions ...
````
#--------------------------------------- Things that are always recommended
OPT   +=  -DPEANOHILBERT
OPT   +=  -DWALLCLOCK   


#--------------------------------------- TreePM Options
#OPT   +=  -DPMGRID=128
#OPT   +=  -DPLACEHIGHRESREGION=3
#OPT   +=  -DENLARGEREGION=1.2
#OPT   +=  -DASMTH=1.25
#OPT   +=  -DRCUT=4.5
````
... options of the TreePM functionality of the code ... 

````
#--------------------------------------- Single/Double Precision
#OPT   +=  -DDOUBLEPRECISION      
#OPT   +=  -DDOUBLEPRECISION_FFTW      


#--------------------------------------- Time integration options
OPT   +=  -DSYNCHRONIZATION
#OPT   +=  -DFLEXSTEPS
#OPT   +=  -DPSEUDOSYMMETRIC
#OPT   +=  -DNOSTOP_WHEN_BELOW_MINTIMESTEP
#OPT   +=  -DNOPMSTEPADJUSTMENT


#--------------------------------------- Output 
#OPT   +=  -DHAVE_HDF5  
#OPT   +=  -DOUTPUTPOTENTIAL
#OPT   +=  -DOUTPUTACCELERATION
#OPT   +=  -DOUTPUTCHANGEOFENTROPY
#OPT   +=  -DOUTPUTTIMESTEP


#--------------------------------------- Things for special behaviour
#OPT   +=  -DNOGRAVITY     
#OPT   +=  -DNOTREERND 
#OPT   +=  -DNOTYPEPREFIX_FFTW        
#OPT   +=  -DLONG_X=60
#OPT   +=  -DLONG_Y=5
#OPT   +=  -DLONG_Z=0.2
#OPT   +=  -DTWODIMS
#OPT   +=  -DSPH_BND_PARTICLES
#OPT   +=  -DNOVISCOSITYLIMITER
#OPT   +=  -DCOMPUTE_POTENTIAL_ENERGY
#OPT   +=  -DLONGIDS
#OPT   +=  -DISOTHERM_EQS
#OPT   +=  -DADAPTIVE_GRAVSOFT_FORGAS
#OPT   +=  -DSELECTIVE_NO_GRAVITY=2+4+8+16
```

# Current status

# Feedback required ! 

# Footnotes and References
[^UX]: *User Experience* - this is the process by which user feedback is requested and incorporated into the design of the product. 
