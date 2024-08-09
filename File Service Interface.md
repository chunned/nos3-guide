*Part of [[cFE Application Developers Guide]]*

# File Service Interface
- File Service API is mostly concerned with handling the standard file header
- For details on creating, opening, or closing files, or obtaining, manipulating, and writing data to a file, see [[OS Abstraction Layer Library]]

## Standard File Header
```c
typedef struct
{
    uint32  ContentType;           /* Identifies the content type (magic #=’cFE1’) */
    uint32  SubType;               /* Type of ContentType, if necessary */
    uint32  Length;                /* Length of this primary header */
    uint32  SpacecraftID;          /* Spacecraft that generated the file */
    uint32  ProcessorID;           /* Processor that generated the file */
    uint32  ApplicationID;         /* Application that generated the file */

    uint32  TimeSeconds;           /* File creation timestamp (seconds) */
    uint32  TimeSubSeconds;        /* File creation timestamp (sub-seconds) */

    char    Description[32];       /* File description */
} CFE_FS_Header_t;
```
- `ContentType`
	- A magic number that identifies the file as conforming to cFE standard header type
	- At time of writing, magic number is `0x63464531` (`cFE1` in ASCII)
- `SubType`
	- Indication of the contents/format of the file
	- Examine `cfe_fs.h` to determine what subtypes are allowed; some are already used by cFE
	- When reading a file, apps should verify the SubType before processing data
- `Length`
	- Specifies the size of `CFE_FS_Header_t`; can be used to determine version of header type, and where user data is relative to the beginning of the file
- `SpacecraftID`, `ProcessorID`, `ApplicationID`
	- Automatically filled by cFE File Services routines when creating a cFE file
	- Help identify how/where file was created
- `TimeSeconds`, `TimeSubSeconds`
	- Spacecraft time when header was created
- `Description`
	- Brief ASCII description of file contents

## Accessing and Modifying the Standard File Header
- `CFE_FS_ReadHeader`
	- Reads contents of the header of a specified file and returns as a predefined data structure
- `CFE_FS_WriteHeader`
	- Populates the given header data structure with `SpacecraftID`, `ProcessorID`, `ApplicationID`, `TimeSeconds`, and `TimeSubSeconds`
		- These are obtained from [[Executive Services Interface|ES]] and Time Services
	- Developer only needs to specify `SubType` and `Description` fields
	- After function writes the header to the file, the file pointer is pointing to the first byte past the standard header
- `CFE_FS_UpdateHeaderTime`
	- Takes a file and sets `TimeSeconds` and `TimeSubSeconds` equal to current time (obtained from `CFE_TIME_GetTime`)
- `CFE_FS_SetHeaderTime`
	- Lets developer set creation time equal to a time specified using the `CFE_TIME_SysTime_t` format

## Utility Functions
![[Pasted image 20240713171613.png]]