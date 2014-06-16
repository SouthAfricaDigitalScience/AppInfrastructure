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

Much of GADGET's functionality is defined in the Makefile, or in parameters passed to the compiler. This means that GADGET needs to be recompiled just before runtime, with the Makefile of the run, which has to be consistent of course with both the initial conditions as well as the parameter file provided. 

## Input validation and UI

Since GADGET will be compiled on the target infrastructure (grid site, cloud site, HPC centre) that the portal submits it to, it will be too late to change anything and prevent jobs from being aborted if the input parameters and initial conditions are in conflict with the GADGET run parameters. This means that the portlet needs to validate input ***before*** submitting to the infrastructure, by making as many sanity checks on the configuration as possible. 

This can be done by restricting the input format available to the user, showing them only Y/N boxes where a binary toggle is applicable, providing warnings that parameters which will result in long run, parameters which are not cosmologically or astrophysically appropriate, etc. We've made some decent progress towards implementing this (thanks @ccarrubba and @PrimoFurno) - a screenshot of a recent build is shown below in Figure 2.

<figure>
	<img src="https://github.com/SouthAfricaDigitalScience/GADGET-portlet/blob/master/screenshots/screenshotGadgetInterface.png">
	<figcaption>Figure 2: GUI under development for guiding the user through the creation of input parameter files </figcaption>
</figure>

# Current status

We are in a period of intense development right now and should be until the end of week 25, which is the date for the first beta of the project. To keep up to date with the project, you can 
  
  1. Check the Agile Dashboard : https://waffle.io/southafricadigitalscience/gadget-portlet, where you can see the state of development
  1. Check Github project page : https://github.com/SouthAfricaDigitalScience/GADGET-portlet, where you can find information about the project, wiki, etc.
  1. Check the build status : http://ci.sagrid.ac.za:8080/view/All/job/GADGET2-portlet/
  1. Check this blog :)
  
A brief summary of the state as of Friday 13/06 is given below.
 
## GADGET-2 build status
The application itself, as well as its dependencies are passing build tests on our [continuous integration](http:ci.sagrid.ac.za:8080) server. In order to actually execute the code, it of course needs to be on a site which can do so; we plan to do this by putting GADGET and dependencies in our [CVMFS](cernvm.cern.ch/portal/filesystem) repository at apprepo.sagrid.ac.za (this is almost finished). We still need to enable promotion of build artifacts from Jenkins to CVMFS, but this will be done as soon as we can solve the CVMFS mounting issue. Sites will then be able to mount CVMFS easily, and GADGET will be available without further intervention. 

One outstanding task is the definition of a functional test for GADGET, which would be a job which 
  
  1. Uses MAGIC to create an initial condition file
  1. Compiles GADGET with a specified default setup
  1. makes a short run of GADGET to check whether it can actually be executed. 
  
## Portlet development status
This is currently being implemented by @ccarrubba as a series of input forms, which will guide the novice user through the options available, allowing them to have a graphical means to generate their config and input files. Validation will be performed on these parameters (which is currently being done by @vale-anna.

We will be making available a dev machine on which to actually deploy the WAR which is created by Jenkins during the portlet build phase, to provide the end users a "live" system with which to interact. 

# Feedback required ! 

At this point, we need some feedback ! Are we doing the right thing ? Are these features desirable ? Should the interface be simpler or more feature-rich ? 

As we discussed above, in our interactions with astronomers at the University of the Western Cape (who are our primary contact for this project), we have been told that such a portlet could be very useful to postgraduate and undergraduate students who are getting to grips with GADGET. We think that an "intelligent" UI with a verbose and graphical interface could help them understand how GADGET works, and speed up their usage of it. 

However, when it comes to "serious" research with GADGET, we expect that forcing the user to go through the process of inserting and changing values every run might be somewhat tedious. Therefore, we think of providing two different interfaces and asking the user up front if they are an "expert" or "novice", to determine their interface. Of course, since we are using federated identity management for AuthN/AuthZ, we can also assign these roles to users *a-priori* and expose the relevant interface to them behind the scenes. 

What we hope for in the long run is that this service could be handed over to the research community interested, allowing them to manage the usage of it themselves - they're the experts, after all ! 

For this to work, we need direct and constant feedback from the user. So... 

  1. If you already have a Github account, [open an issue](https://github.com/SouthAfricaDigitalScience/GADGET-portlet/issues/new?title=%22New feature request for GADGET portlet%22&labels=requested) (tag it "requested")
  1. Comment right here with Disqus - make sure to mark your comment "Feature Request"
  1. If you're hardcore, fork the project and send us a pull request
  1. ... or just drop a mail to bbecker@csir.co.za

Till next time ! 
  
# Footnotes and References
[^UX]: *User Experience* - this is the process by which user feedback is requested and incorporated into the design of the product. 
