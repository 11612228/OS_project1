# Project 2 pintos Final Report
## 车良宇 11612228
## email 11612228@mail.sustc.edu.cn
- For keeping the main process from terminating before it's children, I modified `process_wait(tid)`(in `process.c`) and `struct thread` (in `thread.h`). Define the 'struct child' to record the status of the child process. For `struct thread`, I add the `list children` to store its children. `process_wait(tid)` waits for the child process with the designated tid to finish before continue execution.
## task I Argument Passing
### Data Structure
None
### Algorithm
#### Modified Functions
##### `process_execute()`(userprog/process.c)
- In process_execute (), I split the the file_name into command and arguments by strtok_r().
- Then, I create a new thread named by the command, whose function is start_process and aux is arguments.

##### `load`(userprog/process.c)
- File_close (file) is called only when file is Null.

##### `setup_stack()`(userprog/process.c)
- Implemented arguments separation by traversing each chars to check they are ' ' or '\0'. In addition, I need to consider double spaces.
- Push command and arguments into the stack.
- Calculate the argc.

### Synchronization
Task 1 does not need to consider synchronization

### Rationale
Strtok_r () separates command and arguments, but I can still get command inside args with pg_round_down(). The traversal char  is a powerful way of dealing with two Spaces condition.

### task II Process Control Syscalls
#### New Structs
##### `struct child`(threads/thread.h)
- Struct child is used to record the tid of the child process, wait state and exit state.
##### `lock sys_lock`(userprog/syscall.h)
Ensure that syscall is not executed concurrently.
##### `typedef int pid_t`(userprog/syscall.h)
##### Modified Structs
##### `struct thread`(threads/thread.h)
- Add members `lock lock_child` and `cond_child` to implement parent thread waits for the child threads.
- Add member `list children` to hold the children.
- Add member `int child_load_status` to hold the child load status.
####
### Algorithm
#### New Functions
##### `thread_get_by_id()`(threads/thread.c)
Iterate through `all_list` for getting the thread whose tid is the designated tid.
##### `halt()`(userprog/syscall.c)
Call `shut_down_power_off()`.
##### `wait()`(userprog/syscall.c)
Call `process_wait()`.
##### `exit()`(userprog/syscall.c)
- Printf(”%s: exit(%d)\n”, thread_current()−>name, exit_code) to pass the test.
- Iterate through the children of the parent process of this process and set the is_exit=true, exit status = exit_code of the corresponding child.
- Call `thread_exit()`
##### `process_wait()`(userprog/process.c)
Iterate through children to see if there is a running child waiting for the child to finish.
##### `process_execute()`(userprog/process.c)
Initializes the `child` and push it in the `children` of current thread.
##### `start_process()`(userprog/process.c)

##### `process_exit()`(userprog/process.c)



#### Modified Functions
##### `init_thread`(threads/thread.c)
Initialize `lock_child`,`cond_child`, `children`, `child_load_status`.
#####

### Synchronization

### Rationale

### task III File Operation Syscalls 
### Data Structure
#### New Structs
##### `struct open_file`(userprog/syscall.h)
Encapsulate fd, file and elem for traversal.
##### `struct list open_files`(userprog/syscall.h)
Hold all open_file.

##### Modified Structs
##### `struct thread`(threads/thread.h)
- Add member `file *exec_file` to hold the executable file to implement write disable while file is executing.

### Algorithm
#### New Functions
##### `create()`(userprog/syscall.c)
##### `remove()`(userprog/syscall.c)
##### `open()`(userprog/syscall.c)
##### `filesize()`(userprog/syscall.c)
##### `read()`(userprog/syscall.c)
##### `write()`(userprog/syscall.c)
##### `seek()`(userprog/syscall.c)
##### `tell()`(userprog/syscall.c)

#### Modified Functions




### Synchronization

### Rationale

## Question
### Reflection on the project
- First of all, I would like to thank Dr. Tang for extending the DDL to May 19th, so that I have enough time to finish this project independently. Due to some reasons, I did not find a suitable teammate, so I reluctantly chose to do it alone.
- In this project, I finished task 1、task 2 and task 3 on my own. I passed 77 tests, but failed in multi-oom、syn-read and syn-write. 

### Problem
- In multi-oom, when I print some data then it will exit correctly, but without printf, it will meet load fail and cannot exit correctly.
- In syn-read and syn-write, when I add a line of print for test, it will work well. But if without print, it will load error.
- Those tests show that this program may have race conditions.

### Code style
In code style, I try to keep the same as the original codes, but maybe due to lack of knowledge and experience, it is not a  specification. 

### Interpretability
- I think most of the code is understandable and simple.
- I have comments on a lot of complex code, a small part of which are refer from others I also don't fully understand. 

### Comments
The code I left behind has been commented out.

### Reusable functions
I made reuse functions to reduce duplication of code.

### Link list
I haven't re-implement the algorithm for list, I think the original method is enough.

### Conclusion
This project is very difficult. At the beginning, I had no idea how to start work with it. By constantly looking up information on the Internet, reading other people's code, gradually understand it. But there are still parts of the process that are not understood. 

## Reference
- codyjack/OS-pinots https://github.com/codyjack/OS-pintos.git
- CSCI 350: Pintos Guide Written by: Stephen Tsung-Han Sher August 26, 2018
