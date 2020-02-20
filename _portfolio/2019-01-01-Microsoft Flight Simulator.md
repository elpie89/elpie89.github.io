---
layout: portfolioitem
title: Microsoft Flight Simulator
preview: flightsim/flightsim_preview.jpg
---
<!--more-->

[Microsoft Flight Simulator - Official Project Page](https://www.flightsimulator.com/)

[Microsoft Flight Simulator - Asobo Page](https://www.asobostudio.com/games/microsoft-flight-simulator)

Microsoft Flight Simulator is the oldest IP of Microsoft (1976), older than the Windows operating system.
In 2019, at E3, Microsoft announced the new Microsoft Flight Simulator that will be released in 2020.
The project is developed by Asobo Studio and I was lucky enough to be part of the development team, with whom, at the time of writing, I'm still working.
The technologies used innovate the industry, such as the intensive use of data taken from satellite imagery (Bing Maps), aided by artificial intelligence through Microsoft Azure.

<iframe width="425" height="310" src="https://www.youtube.com/embed/ReDDgFfWlS4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<iframe width="425" height="310" src="https://www.youtube.com/embed/BTETsm79D3A" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Since the product will be released with a MarketPlace, it comes with an SDK. The SDK will allow third-party developers to integrate new aircraft, airports, missions, etc., ensuring the participation of the community and the longevity of the game.
For this reason, Asobo Studio has chosen to use the glTF format, to facilitate easy transmission between the various 3d software applications and its proprietary engine. 
 
glTF is short for GL Transmission Format, a common JSON standard for exchanging 3D information that allows interoperability between programs. In practice, although still young and not completely adopted by the industry, it is currently the only open-source format for this purpose that is extensible and (more or less) complete. It is not my goal here to explain the benefits of glTF and why this is, in my opinion, a big step forward for the entire 3d industry, but I’m very proud to be part of it.

Accepting to work on this project I also agreed to move on to tasks much less tech-art and more towards the tool development part. I wanted to accept this challenge because this way I could move out of my comfort zone:
From Unity to Asobo’s custom engine
From Maya to 3ds Max
From C# to Python and C++
From shaders to tool development
My main task was to anticipate artists' needs by observing the pipeline used, proposing tools for 3dsmax to increase productivity and speed up asset integration. To do this, Asobo had chosen to extend the BabylonJS Exporter, a Microsoft-owned project already able to export glTF files from 3ds Max.

[BabylonJS EXPORTERS project](https://github.com/BabylonJS/Exporters)

With over 100 commits to my credit, I contributed to the development of this plugin, helping the BabylonJS community while giving the internal and external developers of the flight sim team all the improvements, tools, and ideas that such an impressive production requires. Among the most important things for the development pipeline are the support for Xref, 3ds Max Containers and the possibility to separate animations from model data.

It is also my responsibility to create tools in 3ds Max. For this task, I opted for a pipeline very different from what it is classically done other companies. Instead of writing tools in MaxScript, only Python(PYMXS) has been used, with the purpose of being able to write an SDK that is adaptable and maintainable over time, using the logic of separation of the UI from the functional code. Taking advantage of Qt Designer and in general from all improvements provided by the Qt API.
  
To summarize my responsibilities:
- Integration of an asset reference system based on 3dsmax Containers and Xref that can export to the glTF format
- Development of a set of tools aimed to increase productivity in the creation of animations and LODs
- Development of a python SDK for 3ds Max tools
- Creating a tool to update the productivity tools, called MaxToolUpdater
A 3ds Max plugin to manage and facilitate the update of scripts and plugins installed on the developer's Desktop, with Perforce integration. This improved a pipeline that required daily updates of exporters and other tools.
- Documentation for some of the developed tools
- Publication, in the Microsoft Flight Simulator SDK, of more than 10 glTF extensions used by FlightSimExporter, with JSON-schemas. 
