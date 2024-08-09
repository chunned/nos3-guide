*Part of [cFE Application Developers Guide](./cFE%20Application%20Developers%20Guide.md)*
# Executive Services Interface
- Incorporates the OSAL API
	- OSAL API intent was to provide common interface for all apps, regardless of what RTOS its running on
	- cFE takes advantage of the OSAL API for portability between RTOSes
- Functions starting with `CFE_ES_` are calls to the cFE ES interface; if the prefix is `OS_` its the OSAL API
	- If there are two functions that behave similarly, one from cFE ES API and one from OSAL API, developers should use the cFE ES one
- Application registration
	- Apps are auto-registered with ES before entry point is invoked; no specific registration function to invoke
- App names/IDs
	- Names are mapped to numeric app IDs
		- Simplifies identification for the processor while retaining human-readable names for debugging
	- Two functions available: `CFE_ES_GetAppName` and `CFE_ES_GetAppIDByName`
		- If an app needs to get its own ID it can call `CFE_ES_GetAppID`
		- Note that children processes of a main app process are considered to be the same app; so even if you call the above from one of the children, it'll return the same ID
- Child task control
	- Apps can be multithreaded; each thread is referred to as a Task
	- The first thread started when the app is loaded is called the Main Task
		- Any other threads are Child Tasks
	- Functions provided by ES to control Child Tasks
		- `CFE_ES_CreateChildTask`
			- Spawn a child task; child task is auto-registered with ES before invoking entrypoint
		- `CFE_ES_DeleteChildTask`
		- `CFE_ES_SuspendChildTask`
		- `CFE_ResumeChildTask`
		- All above require the task ID which is returned by `CFE_ES_CreateChildTask`
## Obtaining OS and Platform Info
- There are lots of functions for obtaining OS/platform info, and not all are necessary for cFE developers. The ones of most interest are:
	- `CFE_PSP_GetSpacecraftId` - returns identifier associated with the spacecraft; useful when software is executing on multiple spacecraft
	- `CFE_PSP_GetProcessorId` - returns identifier associated with a specific processor; useful when software may be executing on multiple processors on the same spacecraft
	- `OS_InfoGetTicks` - returns number of microseconds per OS clock tick; useful for compensating for processor time on certain platforms

## OS Queues, Semaphores, and Mutexes
### Queues
- Avoid using the `OS_` queue functions
- They are lower level duplications of SB service pipes
- Usage of these queues limits visibility into data messages passed between applications
	- Would also impose the requirement that all apps must reside on the same processor
- The one exception to this is communication between main task and its child tasks

### Semaphores
- Binary semaphores can be used for app synchronization
	- Binary semaphore is basically a flag that is either available or unavailable
	- When app *takes* a binary semaphore using `OS_BinSemTake`, outcome depends on whether the semaphore is available or not at the time of the call
		- If available, it now becomes unavailable and the app continues executing
		- If unavailable, app is put into a queue of blocked apps and enters pending state while waiting for the semaphore to become available
	- When app *gives* a binary semaphore using `OS_BinSemGive`:
		- If semaphore is already available, the give has no effect
		- If semaphore is unavailable, and no app is waiting to take it, it becomes available
		- If semaphore is unavailable, and apps are pending on its availability, the first app from the queue is unblocked, and the semaphore remains unavailable
- See [Semaphore Functions](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#5721-binary-semaphore-functions)
### Mutex Semaphores
- Provide mutual exclusion for a shared resource in order to protect against multiple apps using the resource at the same time
- Similar to binary semaphore but used in different way
	- When app needs to use shared resource, it must follow a specific protocol:
		- Take the mutex with `OS_MutSemTake`
		- Use the resource
		- Release the mutex with `OS_MutSemGive`
- Only one app can hold the mutex at a time
	- If app tries to take a mutex that isn't in use, it immediately acquires the mutex
	- If app tries to take a mutex that *is* in use, the app pends until the current holder releases it
- Code that executes in between the taking and giving of a mutex is said to be *protected* by the mutex
	- This code should be written in a way that it is immediately clear what is happening in the protected region; should be short as possible, apps should not hold the mutex longer than necessary
		- In particular, avoid operations in the protected section that may cause the app to pend (i.e. receiving a SB packet, taking a semaphore)
- See [Mutex Functions](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#5741-mutex-functions)
## Interrupt Handling
- OSAL no longer handles interrupts due to platform dependencies and incomplete implementation
- May be considered at PSP layer in the future
## Exceptions
- Handlers can be associated with specific exceptions
- `OS_ExcAttachHandler(uint32 ExceptionNumber, void *ExceptionHandler, int32 Param );`
	- Specifies a handler for an exception
- `ExceptionHandler` is a function called when the exception is detected and should have a prototype like `void ExceptionHandler( int32 Param );`
- Can enable/disable exceptions with `OS_ExcEnable( uint32 ExceptionNumber );` and `OS_ExcDisable( uint32 ExceptionNumber );`
## Memory Utilities
- Mempool library provides block memory management APIs and functions for pseudo-dynamic memory allocations
	- Allow apps to allocate memory blocks of variable size and return to the pool for use by other apps
- Mempool functions only manage a block of memory provided to it by the app; does not create the block itself
	- Hence, app must ensure sufficient memory is provided to store the mempool management structures in addition to the actual memory used in the app
- [Full section](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#5101-memory-pool) goes into much more detail
## File System Functions
- OSAL API provides a POSIX standard interface for file system activities
- Functions are broken into three categories
	- Device, Directory, and File routines

- Device functions
	- `OS_mkfs` - make file system on specified device
	- `OS_mount` - mount filesystem to make it accessible
	- `OS_unmount` - unmount previously mounted filesystem
	- `OS_chkfs` - check file system to ensure links are correct
- Directory functions
	- `OS_mkdir` - create directory
	- `OS_opendir` - open directory
	- `OS_closedir` - close directory
	- `OS_readdir` - read directory
	- `OS_rewinddir` - resets file pointer for a directory back to the beginning
	- `OS_rmdir` - remove directory
- File functions
	- `OS_creat` - create file
	- `OS_open` - open file
	- `OS_close` - close file
	- `OS_read` - read file
	- `OS_chmod` - change access rights; may not be supported for embedded systems
	- `OS_stat` - get file statistics (size, last modification time, etc.)
	- `OS_lseek` - move file pointer to a particular location in the file
	- `OS_remove` - delete file
	- `OS_rename` - rename file

## System Log
- ES provide a system log to provide a mechanism of recording events that cannot be issued as Event Messages
- Developers should use the Event Services `CFE_EVS_SendEvent` when possible, however if an event needs to be recorded and EVS can't be used for whatever reason, use `CFE_ES_WriteToSyslog`
	- Prototype: `int32 CFE_ES_WriteToSysLog(const char *pSpecString, ...);`
	- Acts like the C `printf` function and records ASCII string to a buffer
- This function is generally used if the call to `CFE_EVS_Register` fails or if the app is about to exit

## Software Performance Analysis
- Two functions are provided to configure regions for performance tracking
	- `CFE_ES_PerfLogEntry`
	- `CFE_ES_PerfLogExit`
- These functions are typically used to track the performance of an app's main execution loop
	- Can track other sections of code, but must define a unique PerfID for each segment of code that you want to monitor
	- These PerfIDs are usually defined in the `_mission_cfg.h` file
- See example code of performance analysis [here](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#513-software-performance-analysis)