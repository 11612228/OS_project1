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

##### `setup_stack()`(userprog/process.c)
- Place command and args into stack.
- esp = 4 - (length of command and args)%4 to make sure the next start address a multiple of four.
- Push NULL into stack as argv[argc]
- Implemented arguments separation by traversing each char for checking. If it is ' ' or '\0' and next char isn't ' ' or '\0' then push the address into stack and argc plus one. Replace the ' ' to '\0'. 
- Push argc into stack.
- Push 0 into stack as the return address.

### Synchronization
Task 1 does not need to consider synchronization

### Rationale
- Strtok_r () separates command and arguments, but I can still get command inside args with pg_round_down(). The traversal char  is a powerful way of dealing with two Spaces condition.
- Check if page is null

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
##### `exec()`(userprog/syscall.c)
- Make sure the command is valid
- Call process_execute() to execute the command and get the correspone thread's tid
- Waits for the child of the current process to end
- return the tid

#### Modified Functions
##### `init_thread`(threads/thread.c)
Initialize `lock_child`,`cond_child`, `children`, `child_load_status`.
##### `process_wait()`(userprog/process.c)
Iterate through children to see if there is a running child waiting for the child to finish.
##### `process_execute()`(userprog/process.c)
Initializes the `child` and push it in the `children` of current thread.
##### `start_process()`(userprog/process.c)
Wake up the parent and set the corresponding child's `child_load_status`.
##### `process_exit()`(userprog/process.c)
- free current thread's pagedir
- free current thread's children
- Wake up the parent and set the corresponding child's `child_load_status = -1`. 

### Synchronization
- Ensure that syscall is not executed concurrently by using `sys_lock`.
- The parent process needs to wait for the child process to finish executing before it can execute
### Rationale
- Check that tid is valid

### task III File Operation Syscalls 
### Data Structure
#### New Structs
##### `struct open_file`(userprog/syscall.h)
Encapsulate fd, file and elem for traversal.
##### `struct list open_files`(userprog/syscall.h)
Hold all open_file.

#### Modified Structs
##### `struct thread`(threads/thread.h)
- Add member `file *exec_file` to hold the executable file to implement write disable while file is executing.

### Algorithm
#### New Functions
##### `create()`(userprog/syscall.c)
- The execution needs to get `sys_lock`
- Verify that `file_name` is valid.
- Call `filesys_create ()`.
##### `remove()`(userprog/syscall.c)
- This function is like `create()`

##### `open()`(userprog/syscall.c)
- The execution needs to get `sys_lock`
- Call `filesys_open()` and get the file.
- Encapsulate the file as a open_file and push into open_files.

##### `read()`(userprog/syscall.c)
- When size is greater than PGSIZE, the buffer increases by one PGSIZE at a time until it equals size.
- Exits if the buffer virtual page is not valid during the loop
- The execution needs to get `sys_lock`
- Find the corresponding open_file for fd in open_files.
- Call `filesys_read()`.

##### `write()`(userprog/syscall.c)
- This function is like `read()`.

##### `close()`(userprog/syscall.c)
- The execution needs to get `sys_lock`
- Find the corresponding open_file for fd in open_files.
- Remove the open_file from open_files 
- Call `filesys_close()`.
- Free the open_file.

##### `seek()`(userprog/syscall.c)
- The execution needs to get `sys_lock`
- Find the corresponding open_file for fd in open_files
- Call `filesys_seek()`.
##### `tell()`(userprog/syscall.c)
- This function is like `seek()`

##### `filesize()`(userprog/syscall.c)
- This function is like `seek()`


#### Modified Functions

##### `syscall_init()`(userprog/process.c)
Initialize `open_files`.

##### `process_exit()`(userprog/process.c)
- write enable for its `exec_file`

### Synchronization
- The race condition is prevented by make `exec_file` write disable while the thread is executing.
### Rationale
- Check whether the file is not null.
- Check that command is valid.
- Check the buffer is not null.

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
I made reuse functions to reduce duplication of code, like `get_opened_file`, `close_opened_file`. But the list iterating method is not making reuse function.

### Link list
I haven't re-implement the algorithm for list, I think the original method is enough.

### Conclusion
This project is very difficult. At the beginning, I had no idea how to start work with it. By constantly looking up information on the Internet, reading other people's code, gradually understand it. Finally, I would like to thank the student assistants for their patient answers, and Siyi Ding's help.
## Reference
- codyjack/OS-pinots https://github.com/codyjack/OS-pintos.git
- CSCI 350: Pintos Guide Written by: Stephen Tsung-Han Sher August 26, 2018
