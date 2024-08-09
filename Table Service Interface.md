*Part of [[cFE Application Developers Guide]]*

# Table Service Interface
- A table is a related set of data values
	- Equivalent to a C structure
	- Can be loaded and dumped as a single unit 
	- Used in flight code to give ground operators the ability to update constants used in the flight software without needing to patch the software itself

## Terminology
- **Table**
	- A contiguous block of memory containing parameters (usually static) that an application requires
		- These parameters are configuration values that mayu change over the course of a mission
- **Active/inactive** tables
	- Active table = table that an app can obtain a pointer to and can access stored data
	- Inactive table = complete copy of the active table that can be operated on by either ground or stored commands
		- When modifications have been made to the inactive table, TS can switch the contents of the active table
- **Single/double buffered** tables
	- When table is registered, app can decide whether to implement it as a single or double buffered table
	- Single uses the least amount of memory; modifications are performed in a shared Inactive Table Buffer
		- Disadvantage is that the app could be delayed while the table is being updated
	- Double buffered requires a dedicated Inactive Table Buffer of the same size as the Active Table Buffer
		- Advantage is the switch between inactive and active tables is quick and does not block the application
- **Loading/Activating** a table
	- Operators and applications can load the contents of a table image with values specified in a file or in a block of memory
- **Dumping** a table
	- Operator can command TS to make a Table Dump File
	- Current contents of the Active Table Image are written to on-board filesystem
	- Same format as a Table Load file and can be later used as a Load image
-
## Registering Tables
- Applications can request a table image to be created with `CFE_TBL_Register`
	- cFE responds with an app-unique Table Handle
- See [code example](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#82-registering-tables)

