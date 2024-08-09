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


See next: [cFE App Developer's Guide](./cFE%20Application%20Developers%20Guide.md)