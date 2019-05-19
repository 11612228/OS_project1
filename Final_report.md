# Project 2 pintos Final Report
## 车良宇 11612228
## email 11612228@mail.sustc.edu.cn
## Reflection on the project
- First of all, I would like to thank Dr. Tang for extending the DDL to May 19th, so that I have enough time to finish this project independently. Due to some reasons, I did not find a suitable teammate, so I reluctantly chose to do it alone.
- In this project, I finished task 1、task 2 and task 3 on my own. I passed 77 tests, but failed in multi-oom、syn-read and syn-write. 
- Next, I will outline the changes in the different task below.

### Basic Running
- For keeping the main process from terminating before it's children, I modified `process_wait(tid)`(in `process.c`) and `struct thread` (in `thread.h`). Define the 'struct child' to record the status of the child process. For `struct thread`, I add the `list children` to store its children. `process_wait(tid)` waits for the child process with the designated tid to finish before continue execution. 
- 
### task I Argument Passing
In task I, there are four functions that need to be modified. They are `process_execute()` , `start_process()` , `load` and `setup_stack()`.

### task II Process Control Syscalls

### task III File Operation Syscalls 



## Problem
- In multi-oom, when I print some data then it will exit correctly, but without printf, it will meet load fail and cannot exit correctly.
- In syn-read and syn-write, when I add a line of print for test, it will work well. But if without print, it will load error.
- Those tests show that this program may have race conditions.

## Code style
In code style, I try to keep the same as the original codes, but maybe due to lack of knowledge and experience, it is not a  specification. 

## Interpretability
- I think most of the code is understandable and simple.
- I have comments on a lot of complex code, a small part of which are refer from others I also don't fully understand. 
## Comments
The code I left behind has been commented out.
## Reusable functions
I made reuse functions to reduce duplication of code.
## Link list
I haven't re-implement the algorithm for list, I think the original method is enough.
## Improvemnt
The good new is that I basically completed the project and understood the knowledge of busy waiting, thread scheduling and priority transmission. 
I encountered many bugs and successfully fixed bugs. I exchanged programming experience with my classmates and became familiar with the use of Unix.
If I had more time I would like to learn more about the MLQFS algorithm and the use of GDB.

## Reference

