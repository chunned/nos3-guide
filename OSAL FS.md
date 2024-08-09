# OS Abstraction Layer - File System

- File system API is a wrapper around a selection of POSIX file APIs

```c
OsStatus = OS_mkfs((void *)RamDiskMemoryAddress, "/ramdev0", "RAM", CFE_PLATFORM_ES_RAM_DISK_SECTOR_SIZE,
CFE_PLATFORM_ES_RAM_DISK_NUM_SECTORS);
```

- Above will mount a filesystem in the spacecraft's RAM
- On the spacecraft, will appear as `RAM:0`
- On the VM host, will be accessible at `/mnt/ram`
- Mapping of the virtual device to a regular directory is accomplished via
  - OS_mkfs call
  - OS_mount call
  - BSP-specific (Board Support Package) volume table which maps virtual devices to real devices/underlying filesystems

  Volume table has the following fields:
- Device name can be any unique string
  - Common values: ramdisk1, flash1, volatile1, etc.
- Physical device name
  - Implementation-specific; for a file system based implementation, should be the mount point on the root file system where the volume will be mounted
    - Common mount points are /mnt, /tmp, /home/<user>/
- Volume type
  - FS_BASED: use existing file system
  - RAM_DISK: uses a RAM disk
  - FLASH_DISK_FORMAT: uses a flash disk, to be formatted before use
  - FLASK_DISKM_INIT: uses a flash disk with existing format that just needs to be initialized before use
  - EEPROM: for EEPROM/PROM based systems
- Volatile flag
  - Indicates the disk is a volatile (RAM) disk, or a non-volatile disk (content persists after reboot)
- Free flag
  - Internal flag; should be FALSE or zero
- Is Mounted Flag
  - Internal flag that should be set to FALSE or zero
- Volume Name, Mount Point Field
  - Internal flags that should be set to a space character
- Block Size Field
  - Records the block size of the device; does not need to be set by the user
==Where is this table defined?==

**Note**
- OSAL uses abstracted FDs
- The FDs returned from OS_create and OS_open will ionly work with other OS_ functions

