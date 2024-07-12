*Part of [[cFE Application Developers Guide]]*
# Application Architecture

## Software-Only Application
- Does not directly communicate with hardware
- Receives messages via SB, manipulates the data, and issues TLM/CMD messages
- Examples
	- Stored Command, Scheduler (SCH) applications
- *Should never directly interface with any piece of hardware, or the underlying OS*
	- Function calls to functions outside the application's code should be either to the cFE APIs, or the OSAL

## Hardware-Servicing Applications
- Communicates directly with a piece of hardware
- Should follow the below device driver model
![[cFE_Application_Developers_Guide_image17 1.png]]
## Best Practices
- Use the sample app to generate your component according to the template
- All files should begin with component name as prefix to ensure unique filenames
- See [provided tables](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#421-cfs-application-template) for description of how each file should be named/what the names mean
- Note the "IMPORTANT" note about the INTERFACE scope
	- Basically, be careful when changing any of the files classified as having the INTERFACE scope, because those changes need to be made to all other cFE apps
- Table and message definitions are split between `defs.h` and `struct.h` files
- `defs.h` contains definitions coupled to the app source code
	- Should contain the payload structure definitions (content of commands consumed/produced by the app)
	- Also any constants needed to support those definitions
- `struct.h` contains definitions coupled to the message-passing interface
	- Should contain the structures that are actually passed via the SB/messaging interface