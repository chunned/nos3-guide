- Enabling the radio
    - Use the Command Sender to send the `TO_ENABLE_OUTPUT` packet to the target `CFS_RADIO`

- [Understanding all of the windows](https://github.com/nasa/nos3/wiki/NOS3-Running-Executables-and-Windows)
    After running `make launch`, many applications/windows are launched. The above link will describe what each one is for. A simplified explanation is included below:

| Window | Purpose |
| ---- | ---- |
| NOS3 Flight Software | The flight software that would execute on a spacecraft SBC (modified to use software components instead of hardware). |
| NOS Time Driver | Provides time-ticks to cFS, 42, and components |
| RW SIM | Reaction wheel simulator |
| GPS SIM | | 
| 42 | Spacecraft motion simulator (multiple windows) |
| NOS Engine Server | Standalone NOS Engine; has basic functionality (list buses, data nodes, time clients, time sender, terminate). |

