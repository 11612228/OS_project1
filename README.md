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
Turn the current thread into a `THREAD_BLOCKED` state, record the time to be blcok with block_tick, and join the thread state check by using the OS itself to perform a time break for each tick. Each time check for the thread's block_tick minus one, unblock the thread if block_tick is 0 and put it in the ready queue.

### 3.Synchronization
Disable interrupts when the `thread_unblock (struct thread *t)`functions are called.


### 4.Rationale
The original timer_sleep() implementation is busy waiting and consume CPU resources. This change avoids busy waiting by blocking the thread, then detecting it in `timer_interrupt` and waking the thread.

## Task 2: Priority Scheduler
### 1.Data structures and functions

#### New Structs
##### `struct lock`
- the member variables:
```
struct list_elem elm;
int max_priority;
```
#### Modified Structs
##### `struct thread`(threads/thread.h)
- Add the following member variables:
```
int base_priority;
struct list locks;
struct lock *lock_waiting;
```
#### New Functions
##### `bool thread_cmp_priority (const struct list_elem *a, const struct list_elem *b, void *aux UNUSED)`
- Compares the priority of thread a and thread b, and returns true if a>b.
##### `bool lock_cmp_priority (const struct list_elem *a, const struct list_elem *b, void *aux UNUSED)`
- A function that can sort the lock list.
##### `void list_insert_ordered (struct list *list, struct list_elem *elem,list_less_func *less, void *aux)`
- A function can insert the threads sequentially into the ready queue.
##### `void thread_remove_lock(struct lock *lock)`
- Remove the `lock` from the `locks`
- Call `thread_update_priority(thread_current())`to update the current thread's priority.
##### `void thread_update_priority (struct thread *t)`
- If the thread's `locks` is not empty and `lock[0]`'s priority is greater than the thread's `base_priority`, then update the `max_priority`.
#### Modified Functions
##### `void thread_unblock (struct thread *t)`(threads/thread.c)
- Call `list_insert_ordered()` instead of `list_push_back()` to ensure that the ready queue is the priority queue.
##### `void thread_yield (void)`(threads/thread.c)
- Call `list_insert_ordered()` instead of `list_push_back()` to ensure that the ready queue is the priority queue.
##### `void thread_init(void)`(threads/thread.c)
- Initalize thread's priority and locks.
- Call `list_insert_ordered()` instead of `list_push_back()` to ensure that the ready queue is the priority queue.
##### `void thread_set_priority (int new_priority)`(threads/thread.c)
- Update the `current_thread->base_priority`.
- Record the old_priority.
- Call `thread_yield ()`
##### `tid_t thread_create (const char *name,int priority,thread_func *function, void *aux)`(threads/thread.c)
- After calling thread_unblock(t)，add the following code
```
if (thread_current ()->priority < priority)
   {
     thread_yield ();
   }
```
##### `void lock_acquire (struct lock *lock)`
- Add a line of code that can recursive priority donation before P operation.
- Undoing donations after becoming the holder of the lock.
##### `void lock_release (struct lock * lock)`
- Call `thread_remove_lock (lock)`
##### `void cond_signal (struct condition *cond, struct lock *lock UNUSED)`
- Change condition queue to priority queue.
##### `void sema_up (struct semaphore *sema)`
- Implement a semaphore `wait_queue` as a priority queue
##### `void sema_down (struct semaphore *sema)`
- Implement a semaphore `wait_queue` as a priority queue
### 2.Algorithms
The main algorithm to realize priority donation is as follows:
#### Lock_acquire
When a thread tries to acquire a lock, it should look at the current lock holder if it has to wait. If the current lock holder's priority is lower than its, the `max_priority` of the lock holder is updated to the priority of the thread.
#### Lock_release
When we release a thread's lock, we remove the lock it hold. Then, we access the remaining highest priority lock and assign its priority to the thread.
### 3.Synchronization
Disable interrupts when the `thread_update_priority` and `thread_set_priority` functions are called.
### 4.Rationale
Call `list_insert_ordered` function to make the normal queue a priority queue.Thus reduce the original code changes and additions.
## Task 3: Multi-level Feedback Queue Scheduler

### 1.Data structures and functions
#### Global variable
##### `fixed_t load_avg`(threads/thread.c)
- Initialized to `FP_CONST (0)` in the `thread_start()` function.
#### Modified Structs
##### `struct thread`(threads/thread.h)
- Add the following member variables:
```
int nice;
fixed_t recent_cpu;
```
#### Modified Functions
##### `static void timer_interrupt (struct intr_frame *args UNUSED)`
- Call `thread_mlfqs_increase_recent_cpu_by_one ()`, `thread_mlfqs_update_load_avg_and_recent_cpu ()` or `thread_mlfqs_update_priority (thread_current ())` to compute and update the priority of the thread at regular intervals.
##### `void thread_mlfqs_increase_recent_cpu_by_one (void)`
- `recent_cpu`plus one.
##### `void thread_mlfqs_update_load_avg_and_recent_cpu (void)`
- Compute load_avg and recent_cpu and update thread.
##### `void thread_mlfqs_update_priority (struct thread *t)`
- Update thread priority.
##### Some get and set methods

### 2.Algorithms
```
priority= PRI_MAX - (recent_cpu/ 4) - (nice*2)
recent_cpu= (2*load_avg)/(2*load_avg+ 1) *recent_cpu+nice
load_avg= (59/60)*load_avg+ (1/60)*ready_threads
```
Through the above three formulas, the priority of threads is dynamically calculated and modified every once in `ticks % TIMER_FREQ == 0`.
### 3.Synchronization
Synchronization problem can occur when calculating the priority value for each thread. To solve this problem, we disable interrupts while processing it.
### 4.Rationale
Multi-level feedback queue scheduling algorithm can not only make high-priority jobs get response but also make processes complete quickly.
## Design Document Additional Questions
It depends on exact implementation details like:
start load_avg set to 0
ready_threads at any time-step set to 0
TIMER_FREQ = 100
A's nice = 0; B's nice = 10; C's nice = 20;
order to calculate load_avg, recent_cpu, priority is recent_cpu -> load -> priority

timer ticks | R(A) | R(B) | R(C) | P(A) | P(B) | P(C) | thread to run
------------|------|------|------|------|------|------|--------------
0 |   0.0 |   0.0 |   0.0 |  63.0 | 43.0 |  23.0 | A
4 |   4.0 |   10.0 |   20.0 |  62.0 | 43.0 |  23.0 | A
8 |   8.0 |   10.0 |   20.0 |  61.0 | 43.0 |  23.0 | A
12 |   12.0 |   10.0 |   20.0 |  60.0 | 43.0 |  23.0 | A
16 |   16.0 |   10.0 |   20.0 |  59.0 | 43.0 |  23.0 | A
20 |   20.0 |   10.0 |   20.0 |  58.0 | 43.0 |  23.0 | A
24 |   24.0 |   10.0 |   20.0 |  57.0 | 43.0 |  23.0 | A
28 |   28.0 |   10.0 |   20.0 |  56.0 | 43.0 |  23.0 | A
32 |   32.0 |   10.0 |   20.0 |  55.0 | 43.0 |  23.0 | A
36 |   36.0 |   10.0 |   20.0 |  54.0 | 43.0 |  23.0 | A

