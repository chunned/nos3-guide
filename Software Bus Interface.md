*Part of [cFE Application Developers Guide](./cFE%20Application%20Developers%20Guide.md)*
# Software Bus Interface
- SB is an inter-application message-based communications system
- Main objective is to provide a mechanism for subsystems to send packets without regard to where the packet is routed, and receive packets without knowledge of where they came from 
- When SB message is sent, it will automatically be routed to all nodes subscribing to that message pipe
- Supports one-to-one, one-to-many, and many-to-one
	- Upper limit defined by `CFE_PLATFORM_SB_MAX_DEST_PER_PKT` parameter
- Can also contain commands from flight interface, telemetry data to be sent out over flight interface, or any other inter-application data
- Apps can either poll (non-blocking) or pend (blocking) on pipe messages
## SB Message
- Collection of data treated as a single entity
- Identified and routed on SB using abstracted message ID
- Message API abstracts details of the header, including the message ID
	- Apps should not directly interpret the message ID
## Pipes
- Destinations to which messages are sent
- Queues that can hold SB messages until they are read out and processed by an app
- Each pipe can only be read by one app, but an app may read many pipes
- Apps can either wait (indefinitely, or with a timeout) or perform a check on pipes to determine if an SB message has arrived
### Message Limits and Overflows
- Pipes have a limit of how many messages may be present
- Two types of limits: pipe depth and message limit
	- Pipe depth is specified when pipe is created
		- Restricts the number of SB messages at a pipe
		- If limit is reached, additional messages are rejected (known as an overflow condition)
	- Message limit is specified during message subscription time
		- Restricts the number of messages with a specific message ID that can be present in a pipe at the same time
		- When messages with the specified MID are attempted to be queued when the limit has been reached, it is referred to as an overrun condition
			- Note that this limit is on top of the pipe depth
### Routing of SB Messages
- Routing is seamless
	- When app sends a SB message, SB searches its routing table to identify where the message should be sent
- Any app is capable of sending SB messages, but interrupt/exception handlers should not send SB messages since SB uses OS calls that may me prohibited in some circumstances (i.e. they may be blocking calls)
	- Same goes for receiving messages
- SB messages are received in order they were sent to the pipe

### Creating and Deleting Pipes
- During app initialization, the app must notify cFE of the pipes it requires to receive data by calling `CFE_SB_CreatePipe`
**Creating**
sample_app.h:
```c
/* Define input pipe characteristics */
#define SAMPLE_PIPE_1_NAME    "SAMPLE_PIPE_1"
#define SAMPLE_PIPE_1_DEPTH    (10)
...
typedef struct
{
	...
	CFE_SB_PipeId_t    SAMPLE_Pipe_1; /* Variable to hold Pipe ID */
	...
} SAMPLE_AppData_t;
```
sample_app.c:
```c
SAMPLE_AppData_t     SAMPLE_AppData;
...
{
	int32 Status;
	...
	Status = CFE_SB_CreatePipe( &SAMPLE_AppData.SAMPLE_Pipe_1, /* holds pipe ID */
								SAMPLE_PIPE_1_DEPTH,  /* pipe depth */
								SAMPLE_PIPE_1_NAME);  /* name of pipe */
}
```
- The above creates a pipe called `SAMPLE_PIPE_1` with a depth of 10
	- Note: pipe name is restricted by `OS_MAX_API_NAME` value
	- Pipe names should be prefixed with app's abbreviated name
	- All pipes for a single app must have unique names
- Second parameter specifies the depth of the pipe (maximum number of SB messages that can be queued before overrun condition occurs)
- First parameter returns pipe identifier
	- Important when using other SB API functions, because pipes are not multi-thread safe - other threads (even from the same app) cannot attempt to receive data from the created pipe
	- If multiple threads require access to messages, each thread must create their own pipe and subscribe to the messages
**Deleting**
sample_app.c:
```c
{
	int32 Status;
	...
	Status = CFE_SB_DeletePipe(SAMPLE_Pipe_1); /* Delete a pipe created earlier */
}
```
- Deleting pipes is not a requirement
	- cFE will monitor the resources that each app has created and delete the resources when the app exits
	- This function is basically just for apps that need a temporary pipe
### Message Subscription
sample_msgids.h:
```c
...
/* Define message IDs */
#define SAMPLE_CMDID_1    (0x0123) /* Value depends on selected implementation */
```
sample_app.h:
```c
...
/* Define receive message ID characteristics */
#define SAMPLE_CMDID_1_LIMIT    (10)
...
typedef struct
{
	...
	CFE_SB_PipeId_t    SAMPLE_Pipe_1; /* Holds Pipe ID */
	...
} SAMPLE_AppData_t;
```
sample_app.c:
```c
SAMPLE_AppData_t SAMPLE_AppData;
...
{
	int32 Status;
	...
	Status = CFE_SB_SubscribeEX(
		SAMPLE_CMDID_1,  /* MsgID to receive */
		SAMPLE_AppData.SAMPLE_Pipe_1,  /* Pipe which msg is to be received on */
		CFE_SB_DEFAULT_QOS,  /* Quality of Service settings */
		SAMPLE_CMDID_1_LIMIT  /* Max number to queue */
	);
	...
}
```
- In the above example, the app is requesting all SB messages with an ID of `0x0123` to be routed to `SAMPLE_PIPE_1`
- QoS parameter determines priority and reliability of the specified SB message 
	- Most apps are fine with the default QoS as defined in `CFE_SB_DEFAULT_QOS` macro
	- Others, like attitude control, may require higher QoS to ensure receipt of critical sensor data
	- QoS not currently implemented in current cFE version
- Fourth parameter specifies limits on the number of messages that can be simultaneously queued in the pipe
- SB message IDs are defined in a header file separate from the rest of the app's interface
- Most apps do not care about QoS or message limits
	- Those apps can use `CFE_SB_Subscribe`
	- `CFE_SB_SubscribeEx` allows the QoS and Message Limits to be specified
#### Unsubscribing
sample_app.c:
```c
{
	int32 Status;
	...
	Status = CFE_SB_Unsubscribe(SAMPLE_CMDID_1, /* MsgID to stop receiving */
								SAMPLE_AppData.SAMPLE_Pipe_1); /* Pipe the msg is currently received on */
}
```

###  Creating SB Messages
- For an app to send a SB message, it must first create it
- App should define the data structure of the message, allocate memory, and initialize it with SB message header info 
sample_msgids.h:
```c
...
#define SAMPLE_HK_TLM_MID (0x0321) /* Define SB MID for SAMPLE's HK packet */
...
```
sample_app.h:
```c
/* Type definition - SAMPLE housekeeping */
typedef struct 
{
	CFE_MSG_TelemetryHeader_t TelemetryHeader;
	/* Task command interface counters */
	uint8 CmdCounter;
	uint8 ErrCounter;
} SAMPLE_HkPacket_t; 

typedef struct
{
	...
	/* Task command interface counters */
	uint8 CmdCounter;
	uint8 ErrCounter;
	...
	SAMPLE_HkPacket_t HkPacket; /* Declare instance of Housekeeping packet */
	...
} SAMPLE_AppData_t;
```
sample_app.c:
```c
SAMPLE_AppData_t SAMPLE_AppData; /* instantiate task data */
...
{
	int32 Status;
	...
	Status = CFE_MSG_Init(CFE_MSG_PTR(Sample_AppData.HkPacket.TelemetryHeader), /* Location of SB message data buffer */
	SAMPLE_HK_TLM_MID, /* SB MID associated with the data */
	sizeof(SAMPLE_AppData.HkPacket)); /* Size of buffer */
}
```

- In the above example, developer has allocated space for the SB message header in their structure using the `CFE_MSG_TelemetryHeader_t` type
	- If SB message was to be a command message, `CFE_MSG_CommandHeader_t` would've been used instead
- `CFE_MSG_Init` API call formats the message header appropriately with the given MID and size, and clears the rest of the message

#### Message Header Types
- Message module supports two types of headers: command and telemetry
	- Share the same CCSDS primary header structure, but differ in the secondary header
	- Note that all messages must have a secondary header per CCSDS standard
```c
typedef struct
{
    uint8 FunctionCode; /**< \brief Command Function Code */
                        /* bits shift ---------description-------- */
                        /* 0x7F  0    Command function code        */
                        /* 0x80  7    Reserved                     */

    uint8 Checksum; /**< \brief Command checksum  (all bits, 0xFF)      */
} CFE_MSG_CommandSecondaryHeader_t;

/**
 * \brief cFS telemetry secondary header
 */
typedef struct
{
    uint8 Time[6]; /**< \brief Time, big endian: 4 byte seconds, 2 byte subseconds */
} CFE_MSG_TelemetrySecondaryHeader_t;
```
- The size of these headers can be calculated with `sizeof()`

#### Message Header Information
- Before sending a SB message, the app can set fields in the message header
![[Pasted image 20240712152849.png]]
- Reading message header information
![[Pasted image 20240712152945.png]]
### Sending SB Messages
- After a SB message has been created and its contents set to the appropriate values, the app can send the message on the SB

sample_app.c:
```c
SAMPLE_AppData_t SAMPLE_AppData; /* Instantiate task data */
...
{
	...
	/* Get command execution counters */
	SAMPLE_APP_Data.HkTlm.Payload.CommandErrorCounter = SAMPLE_APP_Data.ErrCounter;
	SAMPLE_APP_Data.HkTlm.Payload.CommandCounter = SAMPLE_APP_Data.CmdCounter;
	
	/* Send housekeeping telemetry packet */
	CFE_SB_TimeStampMsg(CFE_MSG_PTR(SAMPLE_APP_Data.HkTlm.TelemetryHEader));
	CFE_SB_TransmitMsg(CFE_MSG_PTR(SAMPLE_APP_Data.HkTlm.TelemetryHeader), true);
	...
}
```
### Receiving SB Messages
- App calls `CFE_SB_ReceiveBuffer` to receive a message

sample_app.h:
```c
typedef struct
{
	...
	CFE_SB_PipeId_t   CmdPipe;
	...
} SAMPLE_AppData_t;
```

sample_app.c:
```c
{
	...
	CFE_SB_Buffer_t *SBBufPtr;
	...
	while (TRUE)
	{
		/* Wait for next SB message */
		SB_Status = CFE_SB_ReceiveBuffer(&SBBufPtr,
										SAMPLE_AppData.CmdPipe,
										CFE_SB_PEND_FOREVER);
		if (SB_Status == CFE_SB_SUCCESS)
		{
			/* Process SB message */
			SAMPLE_AppPipe(SBBufPtr);
		}
	}
}
```
- App will pend on `SAMPLE_AppData.CmdPipe` until message arrives
- Pointer to the next message in the pipe will be returned by `SBBufPtr`
- App could have set a timeout by putting a numerical argument in place of `CFE_SB_PEND_FOREVER` or could quickly poll the pipe using `CFE_SB_POLL` instead of `CFE_SB_PEND_FOREVER`

### Large Message Performance
- Sometimes large quantities of data need to be passed between apps on the same processors
	- Downside of using `CFE_SB_TransmitMsg` is that the message is copied into the SB buffer
	- If this copy operation is too time-consuming, devs can use the Zero Copy protocol instead
		- App can request a buffer from the SB with `CFE_SB_AllocateMessageBuffer` and then write the message data directly to the buffer so it can be sent directly over the SB without a copy
		- Once the app has formatted and filled the buffer, it can be transmitted with `CFE_SB_TransmitBuffer`
			- The app identifies the apps subscribed to this data and places a pointer to the SB buffer in their pipe(s)
			- **This pointer is no longer valid once the app calls `CFE_SB_TransmitBuffer`**
- [Example](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#68-improving-message-transfer-performance-for-large-messages)

## Best Practices
- Apps should use SB for all inter-app communications
- Pipe depth and message limits are dependent on the entire system
	- Consider both receiving and sending apps
- Apps should always use API functions to read/manipulate message headers
- Apps should maintain command and command error counters in housekeeping telemetry
- Apps should support a no-operation command and a reset counters command
- Every app should have at least one pipe