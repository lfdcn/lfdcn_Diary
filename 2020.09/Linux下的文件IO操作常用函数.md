## open

> 打开文件

### 使用的头文件

```c
#inlcude <sys/types.h>
#inlcude <sys/stat.h>
#include <fcntl.h>
```

### 声明

```C
int open(const char *pathname, int flags);
int open(const char *pathname,int flags,mode_t mode);

int creat(const char *pathname,mode_t mode);

int openat(int dirfd,const char *pathname,int flags);
int openat(int dirfd,const char *pathname,int flags,mode_t mode);
```

## close

> 关闭文件

### 使用的头文件

```C
#include <unistd.h>
```

### 声明

```C
int close(int fd);
```

## write

> 写文件

### 使用的头文件

```C
#include <unistd.h>
```

### 声明

```C
ssize_t write(int fd,const void *buf,size_t count);
```

## read

> 读取文件

### 使用的头文件

```C
#include <unistd.h>
```

### 声明

```C
ssize_t read(int fd,void *buf,size_t count);
```



## lseek

> 操控光标

### 使用的头文件

```C
#include <sys/types.h>
#include <unistd.h>
```

### 声明

```C
off_t lseek(int fd,off_t offset,int whence);
```

## mmap/munmap

> 映射内存

### 使用的头文件

```C
#include <sys/mman.h>
```

### 声明

```C
void *mmap(void *addr,size_t length,int prot,int flags,int fd,off_t offset);
int munmap(void *addr,size_t length);
```

