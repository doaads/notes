[..](../soi.md)

/usr/src/kernel/proc.h

> dodać do tabeli proc

```c
int priority
```

oraz na początku pliku

```c
#define INTER_P 0
#define COMP_P 1
#define IO_P 2
```

1. choice_index = 0, choice[ci] = 0


TODO: Image corrupted. Transfer kernel files from 
- /usr/src/kernel
- /usr/include/minix/com.h
- /usr/src/mm
- /usr/src/fs

to new image

replace cproc in while loop in /usr/src/kernel/proc.c sched() with nproc
