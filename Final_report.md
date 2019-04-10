# Project 1 pintos Final Report
## 车良宇 11612228
## email 11612228@mail.sustc.edu.cn
## The difference from design document
When I was writing the design document,My knowledge of the pintos was not enough. Therefore, I make many mistakes in design document. Here, I will show the difference between design document and the final program. 
### task I
- In this task, we need change the function (`void timer_sleep (int64_t ticks)`) 's waiting mode form busy waiting to blocking waiting.   
- In design document, I decided to blcok the current thread in timer_sleep().   
- In final pinots, I add a variable `int64_t wake_tick` in `struct thread` which record the the wake tick. And invoke `thread_sleep(int64_t wake_tick)` method when calling the `timer_sleep` to block the current thread. Next, because `thread_tick` is working on each tick, I rewrite `thread_tick(void)` to wake the sleep threads up by invoke `thread_unblock()`. 
### task II
- In task II, we need to implement priority scheduling, get resources by priority and  priority donation for the thread when a lock is acquired from a lower thread. In order to implement priority scheduling, it is essential to use priority queues.   
- In C language, we can call `list_insert_ordered` method to insert the element orderly and `list_sort` to make a list be a priority queue. Therefore, I make those three lists `ready_list`,semasphare's `waiter` and thread's`locks` be priority queues. 
- In the design document, when `thread_unblock()` is called, `thread_yield()` is called if it determines that the current thread is not idle, and that the priority of the current thread is less than that of the unblocked thread. But, it make a error in `alarm_priority` test. It will make the main thread reboot. Therefore, I don't call the `thread_yield()` in `thread_block()` method in final pinots. 
- But removing `thread_yield()` from `thread_unblock()` also cause another problem. Thread preemption does not occur immediately after calling `sema_up()`, resulting in the `priority-donate-sema` test where main thread finished eariler than the child threads. 
- So, I invoke `thread_yield()` in the end of `sema_up()` in final pintos.
- Because `lock` is based on `semsphore`, `lock_acquire()` calls `sema_down()` to block the thread, and `lock_release()` calls `sema_up()` to unblock the thread. Because `sema_up` call the `thread_yield()`,  `lock_release()` need to call the `sema_up()` in the last line to make sure high-priority threads preempt immediately after calling `lock_release()`.


### task III
- In this task, we need to implement MLFQS algorithm to calculate the priority of threads. First, we need floating point calculation. I use the existing `fixed_point.h` code on website. Then, recent_cpu, load_avg, and thread priority are computed using the formulas while threads are being created or `thread_tick()`.
## Reflection on the project
Sadly, I pushed by DDL on this project. This project was officially started two days before DDL, while this report was written at 10:00 PM on 10th. I think the reasons why this project is finished in such a hurry are as follows.
- First, the fear of this unfamiliar task caused me to delay working on it. 
- Second, some thread knowledge that they don't understand until the last week, such as busy waiting, semasphore, locks and so on.
- Thirdly, the online tutorials are too difficult to understand.
- Last, after writing the design report, there were exams and innovative experiments.
## Improvemnt
The good new is that I basically completed the project and understood the knowledge of busy waiting, thread scheduling and priority transmission. 
I encountered many bugs and successfully fixed bugs. I exchanged programming experience with my classmates and became familiar with the use of Unix.
If I had more time I would like to learn more about the MLQFS algorithm and the use of GDB.
