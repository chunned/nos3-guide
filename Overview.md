# [Background](https://github.com/nasa/nos3/wiki)

NOS3 is a FOSS 3U CubeSat simulator developed by NASA that uses a collection of Linux tools to simulate commercial-off-the-shelf (COTS) hardware used in real CubeSats. The simulator is primarily written in C++ using [Boost](https://www.boost.org/). 

A description of the NOS3 repository directory structure can be found [here](https://github.com/nasa/cFS).

The NOS3 [architecture](https://github.com/nasa/nos3/wiki/NOS3-Architecture) is displayed in the following diagram:
![](/images/architecture.png)

**[NOS Engine](https://github.com/nasa/nos3/wiki/NOS-Engine)** is a message-passing middleware that emulates hardware [busses](https://en.wikipedia.org/wiki/Bus_(computing)). The engine itself contains nodes and busses, where nodes are endpoints capable of sending/receiving messages and busses are groups of nodes. Nodes in the same bus can communicate with each other, but not with nodes that are grouped in other busses. NOS Engine also facilitates interaction between the nodes and the flight simulation software. 

The **[Ground System](https://github.com/nasa/nos3/wiki/NOS3-Ground-Systems)** is the main way you interact with NOS3. Two options are supported, Ball Aerospace's [COSMOS 4](https://ballaerospace.github.io/cosmos-website/) and OpenC3's [COSMOS 5](https://docs.openc3.com/docs). COSMOS 4 is more lightweight and easier, but older and not actively developed. COSMOS 5 is more intensive and harder to use, but has a modern web interface and is under active development. 

*Note: The ground system files can be found in `github-nos3/gsw`* after [installation](Install.md).

**[42](https://github.com/nasa/nos3/wiki/NOS3-Running-Executables-and-Windows#42)** is used to simulate the dynamics and motion of the spacecraft. Time progression is driven through NOS Time Driver and 42 outputs various environmental data to the simulators via NOS3 Engine busses. 

[cFS](https://github.com/nasa/cFS) is a framework for developing flight software. It is platform independent, meaning it can be written and tested in Linux, then later compiled for actual CubeSat hardware. Note that [specific modifications have been made for the NOS3 project](https://github.com/nasa/cFS).


# Space Packet Protocol
SPP is the protocol used for all communication to and from the flight system. The protocol specification can be found [here](https://public.ccsds.org/Pubs/133x0b2e1.pdf).

![](/images/SPP_packet.png)

![](/images/SPP_primary_header.png)

The primary header specifies the application that our message is destined for, and the secondary header allows us to specify the command passed to that application.

![](/images/SPP_secondary_header.png)

COSMOS needs to be aware of the commands and telemetry structures in order to interpret them. An [example](https://github.com/nasa/nos3/wiki/NOS3-Ground-Systems#packet-formatting) can be seen below:

![](/images/COSMOS-CNT-Structure.png)

# [Components](https://github.com/nasa/nos3/wiki/NOS3-Components,-Repository-and-Directory-Structure#components-the-foundation-of-customization)

One of the fundamental concepts of NOS3 is the component, which allows the simulator to be customized. The spacecraft is made up of a core codebase, plus components that can be swapped in/out as desired. Each component is a [cFS](https://github.com/nasa/cFS) application in the `fsw` subdirectory of that component. The `gsw` subdirectory contains the command and telemtry tables needed for COSMOS to control the component application. 

Often, components are simulated hardware components. In such cases, the appropriate NOS3 hardware simulator will be found in the `sims` subdirectory for that component.

**[List of Components](https://github.com/nasa/nos3/wiki/NOS3-Component-Directory)**