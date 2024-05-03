[Source - cFE Application Developers Guide](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md)

# Acronyms

| **Acronym** | **Description**                                     |
  |:------------|:----------------------------------------------------|
  | AC          | Attitude Control                                    |
  | ACE         | Attitude Control Electronics                        |
  | ACS         | Attitude Control System                             |
  | API         | Application Programming Interface                   |
  | APID        | CCSDS Application ID                                |
  | BSP         | Board Support Package                               |
  | CCSDS       | Consultative Committee for Space Data Systems       |
  | CDH, C&DH   | Command and Data Handling                           |
  | CDS         | Critical Data Store                                 |
  | cFE         | Core Flight Executive                               |
  | cFS         | Core Flight System                                  |
  | CM          | Configuration Management                            |
  | CMD         | Command                                             |
  | CPU         | Central Processing Unit                             |
  | CRC         | Cyclic Redundancy Check                             |
  | EDAC        | Error Detection and Correction                      |
  | EEPROM      | Electrically Erasable Programmable Read-Only Memory |
  | ES          | Executive Service                                   |
  | EVS         | Event Service                                       |
  | FC          | Function Code                                       |
  | FDC         | Failure Detection and Correction                    |
  | FS          | File Service                                        |
  | FSW         | Flight Software                                     |
  | HW, H/W     | Hardware                                            |
  | ICD         | Interface Control Document                          |
  | I/O         | Input/Output                                        |
  | MET         | Elapsed Time                                        |
  | MMU         | Memory Management Unit                              |
  | OS          | Operating System                                    |
  | OSAL        | Operating System Abstraction Layer                  |
  | PID         | Pipeline ID                                         |
  | PKT         | Packet                                              |
  | PSP         | Platform Support Package                            |
  | QoS         | Quality of Service                                  |
  | RAM         | Random-Access Memory                                |
  | SB          | Software Bus                                        |
  | SDO         | Solar Dynamics Observatory                          |
  | ST5         | Space Technology Five                               |
  | STCF        | Spacecraft Time Correlation Factor                  |
  | SW, S/W     | Software                                            |
  | TAI         | International Atomic Time                           |
  | TBD         | To Be Determined                                    |
  | TBL         | Table                                               |
  | TID         | Application ID                                      |
  | TLM         | Telemetry                                           |
  | UTC         | Coordinated Universal Time                          |

# Glossary of Terms
  | **Term**                                | **Definition**                                                                                                                                                   |
  |:----------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
  | Application (App)                       | A set of data and functions that is treated as a single entity by the cFE.                                                                                       |
  | Application ID                          | A processor unique reference to an Application.                                                                                                                  |
  | Application Programming Interface (API) | A set of routines, protocols, and tools for building software applications.                                                                                      |
  | Board Support Package (BSP)             | A collection of user-provided facilities that interface an OS and the cFE with a specific hardware platform. The BSP is responsible for hardware initialization. |
  | Child Task                              | A separate thread of execution that is spawned by an Application’s Main Task.                                                                                    |
  | Command                                 | A SB Message defined by the receiving Application. Commands can originate from other onboard Applications or from the ground.                                    |
  | Core Flight Executive (cFE)             | A runtime environment and a set of services for hosting FSW Applications.                                                                                        |
  | Critical Data Store                     | A collection of data that is not modified by the OS or cFE following a Processor Reset.                                                                          |
  | Cyclic Redundancy Check                 | A polynomial based method for checking that a data set has remained unchanged from one time period to another.                                                   |
  | Developer                               | Anyone who is coding a cFE Application.                                                                                                                          |
  | Event Data                              | Data describing an Event that is supplied to the cFE Event Service. The cFE includes this data in an Event Message.                                              |
  | Event Filter                            | A numeric value (bit mask) used to determine how frequently to output an application Event Message defined by its Event ID (see definition of Event ID below).   |
  | Event Format Mode                       | Defines the Event Message Format downlink option: short or long.                                                                                                 |
  | Event ID                                | A numeric literal used to uniquely name an Application event.                                                                                                    |
  | Event Message                           | A data item used to notify the user and/or an external Application of a significant event.                                                                       |
  | Event Message Counter                   | A count of the number of times a particular Event Message has been generated since a Reset or since the counter was cleared via a Command.                       |
  | Event Message Port                      | A display device that is used to display Event Messages in a test environment.                                                                                   |
  | Event Type                              | A classification of an Event Message.                                                                                                                            |
  | FIFO                                    | First In First Out - A storage device that implies the first entry in is the first entry out.                                                                    |
  | Hardware Platform                       | The target hardware that hosts the FSW.                                                                                                                          |
  | Interface Control Document              | A document that describes the software interface, in detail, to another piece of software or hardware.                                                           |
  | I/O Data                                | Any data being written to and/or read from an I/O (input/output) port.                                                                                           |
  | Local Event Log                         | An optional Critical Data Store containing Event Messages that are generated on the same processor on which it resides.                                          |
  | Log                                     | A collection of data that an application stores that provides information to diagnose and debug FSW problems.                                                    |
  | Main Task                               | The thread of execution that is started by the cFE when an Application is started.                                                                               |
  | Memory Data                             | Any data being written to and read from memory. No structure is placed on the data and no distinction as to the type of memory is made.                          |
  | Message ID                              | An identifier that uniquely defines an SB message.                                                                                                               |
  | Mission                                 | A particular implementation of cFE FSW for a specific satellite or set of satellites.                                                                            |
  | Memory Management Unit                  | A piece of hardware that manages virtual memory systems.                                                                                                         |
  | MsgId-to-Pipe Limit                     | The maximum number of messages of a particular Message ID allowed on a Pipe at any time.                                                                         |
  | Network                                 | A connection between subsystems used for communication purposes.                                                                                                 |
  | Network  Queue                          | A device that stores messages and controls the flow of SB Messages across a Network.                                                                             |
  | Operational Interface                   | The Command and Telemetry interface used to manage the cFE and/or Applications.                                                                                  |
  | Operator                                | Anyone who is commanding the FSW and receiving the FSW telemetry.                                                                                                |
  | Pipe                                    | A FIFO device that is used by Application’s to receive SB Messages.                                                                                              |
  | Pipe Depth                              | The numbers of SB Messages a Pipe is capable of storing.                                                                                                         |
  | Pipe Overflow                           | An error that occurs when an attempt is made to write to a Pipe that is completely full of SB Messages.                                                          |
  | Platform                                | See “Hardware Platform” above.                                                                                                                                   |
  | Processor Reset                         | The processor resets via the execution of its reset instruction, assertion of its reset pin, or a watchdog timeout.                                              |
  | Power-on Reset                          | The processor initializes from a no-power state to a power-on state.                                                                                             |
  | Quality of Service (QoS)                | Quality of Service has 2 components, Priority and Reliability.                                                                                                   |
  | Request                                 | The act of an Application invoking a cFE service that resides on the same processor as the Application.                                                          |
  | Routing Information                     | Any information required to route SB Messages locally or remotely.                                                                                               |
  | Software Bus                            | An inter-Application message-based communications system.                                                                                                        |
  | SB Message                              | A message that is sent or received on the software bus.                                                                                                          |
  | Subscribe                               | The act of requesting future instances of an SB Message to be sent on a particular Pipe.                                                                         |
  | System Log                              | Special “Event Message” log for events that occur when the Event Services are not available.                                                                     |
  | Telemetry                               | A SB Message defined by the sending Application that contains information regarding the state of the Application.                                                |
  | Unsubscribe                             | To request that an SB Message no longer be routed to a particular Pipe.                                                                                          |
  | User                                    | Anyone who interacts with the cFE in its operational state.                                                                                                      |