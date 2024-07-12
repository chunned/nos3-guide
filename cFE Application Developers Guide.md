[Source](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md)

# Introduction
- [[Core Flight Executive|cFE]] services are the building blocks to create and host FSW applications
- Five core services make up cFE
	- Executive Services (ES)
	- Event Services (EVS)
	- Software Bus (SB)
	- Table Services (TBL)
	- Time Services (TIME)
## Development Environment
- See [application directory structure](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#2-cfe-application-development-environment)
### Header Files
- `<module>_extern_typedefs.h` - macros, data types shared between command/telemetry messages, data files, API functions
- `<module>_msg.h` - command/telemetry message definitions
- `<module>_events.h` - event ID numbers for events generated via Event Services
- `<module_api_typedefs.h` - macros, data types used by the component API
- Need to include the relevant files, but only need to include the files which you actually need to perform tasks that your app does.
	- e.g. if an app needs to interpret a file generated by ES, only `cfe_es_extern_typedefs.h` is required
		- **To send commands or interpret telemetry, `cfe_es_msg.h` may be used**
- A header with no extra suffix provides the complete runtime API definition for the component
	- `cfe_es.h` - executive service
	- `cfe_evs.h` - event service
	- `cfe_fs.h` - file service
	- etc.
	- These headers provide function prototypes; if you're referencing data types or macros, you should include the more targeted header file
- **An all-inclusive header is also provided: `cfe.h`**

Remaining sections are split into their own files:
# [[cFE Application Architecture]]

# [[Executive Services Interface]]

# [[Software Bus Interface]]

# [[Event Services Interface]]

# [[Table Service Interface]]