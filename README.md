# Design Document

## Task 1: Efficient Alarm Clock

### 1.Data structures and functions

#### Added Structs
##### `list * sleep_list`(devices/timer.c)


#### Modified Structs

##### `struct thread`(threads/thread.h)
- add `int64_t block_tick` to thread.


#### Added Functions
##### `thread_sleep(int64_t ticks)`(threads/thread.c)
- /
##### `void thread_foreach(void);`(threads/thread.c)
- /
##### `void blocked_thread_check (struct thread *t, void *aux UNUSED)`(threads/thread.c)
#### Modified Functions
##### `timer_sleep()`(devices/timer.c)
- Do not use busy waiting

##### `void timer_init (void)`(devices/timer.c)
- /
##### `static void timer_interrupt (struct intr_frame * args UNUSED)`(devices/timer.c)
- /


### 2.Algorithms

### 3.Synchronization

### 4.Rationale

## Task 2: Priority Scheduler

### 1.Data structures and functions
#### Added Structs

#### Modified Structs

#### Added Functions
#### Modified Functions


### 2.Algorithms

### 3.Synchronization

### 4.Rationale

## Task 3: Multi-level Feedback Queue Scheduler

### 1.Data structures and functions
#### Added Structs

#### Modified Structs

#### Added Functions
#### Modified Functions

### 2.Algorithms

### 3.Synchronization

### 4.Rationale

## Design Document Additional Questions
