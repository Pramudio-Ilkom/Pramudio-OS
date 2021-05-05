# Compilation Test

## Image
![Compile Test](./usertests/1_Compilation_Test.jpg)

## Makefile
```c
CS333_PROJECT ?= 0
PRINT_SYSCALLS ?= 0
CS333_CFLAGS ?= -DPDX_XV6
```
changed into
```c
CS333_PROJECT ?= 1
PRINT_SYSCALLS ?= 0
CS333_CFLAGS ?= -DPDX_XV6
```

# System Call Tracing

## Image
![System Call Tracing](./usertests/2_System_Call_Tracing.jpg)

## syscall.c
```c
if(num > 0 && num < NELEM(syscalls) && syscalls[num]) {
    curproc->tf->eax = syscalls[num]();
```
added
```c
if(num > 0 && num < NELEM(syscalls) && syscalls[num]) {
    curproc->tf->eax = syscalls[num]();
    #ifdef PRINT_SYSCALLS
      cprintf("%s -> %d \n", syscallnames[num], curproc->tf->eax);
    #endif
```

## Makefile
```c
CS333_PROJECT ?= 1
PRINT_SYSCALLS ?= 0
CS333_CFLAGS ?= -DPDX_XV6
```
changed into
```c
CS333_PROJECT ?= 1
PRINT_SYSCALLS ?= 1
CS333_CFLAGS ?= -DPDX_XV6
```

# Conditional Compilation

## First Condition
```c
CS333_PROJECT ?= 0
PRINT_SYSCALLS ?= 0
CS333_CFLAGS ?= -DPDX_XV6
```
![Conditional Compilation 0](./usertests/3_Conditional_Compilation_0.jpg)

## Second Condition
```c
CS333_PROJECT ?= 1
PRINT_SYSCALLS ?= 0
CS333_CFLAGS ?= -DPDX_XV6
```
![Conditional Compilation 1](./usertests/3_Conditional_Compilation_1.jpg)

# Date System Call

## Image
![Date System Call](./usertests/4_Date.jpg)

## Makefile
```c
ifeq ($(CS333_PROJECT), 1)
CS333_CFLAGS += -DCS333_P1
CS333_UPROGS += #_date
endif
```
removed #
```c
ifeq ($(CS333_PROJECT), 1)
CS333_CFLAGS += -DCS333_P1
CS333_UPROGS += _date
endif
```

## user.h
```c
#ifdef CS333_P1
int date(struct rtcdate*);
#endif // CS333_P1
```
added bellow
```c
int halt(void;
```

## usys.S
```c
SYSCALL(date)
```
added bellow
```c
SYSCALL(halt)
```

## syscall.h
```c
#define SYS_date	SYS_halt+1
```
added bellow
```c
#define SYS_halt	SYS_close+1
```

## syscall.c
```c
#ifdef CS333_P1
extern int sys_date(void);
#endif
```
added in extern section


```c
#ifdef CS333_P1
[SYS_date]	sys_date,
#endif
```
added on
```c
static int (*syscall[])(void)
```


```c
#ifdef CS33_P1
[SYS_date]	"date",
#endif
```
added in
```c
static char *syscallnames[]
```

## sysproc.c
```c
int
sys_date(void)
{
  struct rtcdate *d;
  if(argptr(0, (void*)&d, sizeof(struct rtcdate)) <0)
    return -1;
  else{
    cmostime(d);
    return 0;
  }
}
```
added entirely in sysproc.c

# Process Information

## Image
![Process Information](./usertests/5_Process_Information.jpg)

## proc.c
```c
#elif defined(CS333_P1)
void
procdumpP1(struct proc *p, char *state_string)
{
  cprintf("TODO for Project 1, delete this line and implement procdumpP1() in proc.c to print a row\n");
  return;
}
#endif
```
changed into
```c
#elif defined(CS333_P1)
void
procdumpP1(struct proc *p, char *state_string)
{
  int sekarang = ticks - (p -> start_ticks);
  cprintf("%d\t%s\t\t%d,%d\t%s\t%d\t", p->pid, p->name, sekarang/1000 , sekarang%1000, states[p->state], p->sz);
  return;
}
#endif
```

## proc.h
```c
uint start_ticks;
```
added in
```c
struct proc
```