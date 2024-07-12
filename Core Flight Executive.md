# Introduction

See also: [Acronyms & Terms](./Acronyms%20&%20Terms.md)

The Core Flight Executive (cFE) is the application development and runtime environment for the Core Flight System. The core services provided by cFE include:
- Software bus (SB) messaging
- Time
- Events/alerts
- Executive (startup/runtime)
- Table services

cFE is one of the abstraction layers used in cFS. These abstraction layers allow applications to be reused across missions and embedded systems. The cFE layer runs on top of the OS Abstraction Layer (OSAL) and the Platform Support Package (PSP) layer. The PSP layer is for interfacing with the hardware platform hosting the flight software. 

The cFE repository can be found [here](https://github.com/nasa/cFE). The source documentation for the information on this page can be found [here](https://github.com/nasa/cFE/blob/gh-pages/cfe-usersguide.pdf). A word of warning: it's a 1200 page PDF.


# [[Executive Services]]
Executive Services (ES) is one of the five core cFE components, and the primary interface to the underlying OS. This component is responsible for starting/restarting cFE and cFE applications, as well as logging errors and performance data, and providing a persistent memory store for cFE apps. 

The ES task interfaces include the Ground Interface (for commands/telemetry) and the API. The ES uses the OSAL to interface with the OS and the PSP layer to interface with the hardware platform. 

ES functionality includes:
- Software reset
- Application and child task management
- Basic filesystem
- Performance data collection
- Critical data store
- Memory pool
- System log
- Shell command