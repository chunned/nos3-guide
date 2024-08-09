*Part of [cFE Application Developers Guide](./cFE%20Application%20Developers%20Guide.md)*

# Time Service Interface (TIME)
- This API maintains and provides access to timing information
- Definitions for the API are found in `cfe_time.h`

## Time Formats
- TIME manages time as two 32-bit integers
	- First represents the number of seconds
	- Second represents the number of `2^(-32)` seconds 
```c
typedef struct {
      uint32      Seconds;      /* Number of seconds */
      uint32      Subseconds;   /* Number of 2^(-32) subseconds */
} CFE_TIME_SysTime_t;
```

- For further information on interacting with time values, see [the full documentation](https://github.com/nasa/cFE/blob/main/docs/cFE%20Application%20Developers%20Guide.md#102-time-values)
