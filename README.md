# Design Document

## Task 1: Efficient Alarm Clock

### 1.Data structures and functions

#### Modified Structs
##### `struct thread`(threads/thread.h)
- add `int64_t block_tick` to thread.
- The `thread_block` is 0 when the thread is initialized.
#### New Functions
##### `void thread_block(void)`(threads/thread.c)
- Call this method to make the current thread's `status` as `THREAD_BLOCKED` and toggle the current thread.
##### `void thread_foreach (thread_action_func *func, void *aux)`(threads/thread.c)
- The incoming `blcok_thread_check ()` passed as a parameter to `thread_foreach()`.

#####  `void blocked_thread_check (struct thread *t, void *aux UNUSED)`
- Each time this method is called, the thread's block_tick is subtracted by one.
- The `thread_unblock()` method is called to unblock if block_tick equals 0.

##### `void thread_unblock (struct thread *t)`
- `thread_unblock()` puts the thread in the ready queue to continue running:

##### `void blocked_thread_check (struct thread *t, void *aux UNUSED)`(threads/thread.c)

#### Modified Functions
##### `timer_sleep()`(devices/timer.c)
- take out busy-wait.
- The current thread's `block_tick` is assigned as `ticks`.
- Call `thread_block` method.

##### `static void timer_interrupt (struct intr_frame * args UNUSED)`(devices/timer.c)
- Add `void thread_foreach()` function inside `timer_interrupt`.



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
