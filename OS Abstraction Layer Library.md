[Source](https://github.com/nasa/cFE/blob/gh-pages/cfe-usersguide.pdf)

# OS Abstraction Layer Library
## Directory API
```c
/* Open directory */
int32 OS_DirectoryOpen(osal_id_t *dir_id, const char *path)

/* Close directory */
int32 OS_DirectoryClose(osal_id_t dir_id)

/* Rewind an open directory */
int32 OS_DirectoryRewind(osal_id_t dir_id)

/* Read next name in open directory */
int32 OS_DirectoryRead(osal_id_t dir_id, os_dirent_t, *dirent)

/* Make directory */
int32 OS_mkdir(const char *path, uint32 access)

/* Remove directory */
int32 OS_rmdir(const char *path)
```
## File API
- See [OSAL FS](OSAL%20FS.md)
- 