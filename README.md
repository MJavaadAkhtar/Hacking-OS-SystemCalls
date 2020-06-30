# HackingSystemCalls

The goal of this project is to learn more about system calls and how to use synchronization mechanisms. For this project, i wrote very basic kernel module that intercepts system calls and monitors processes on demand.

### Implementation Detail

1. Since the number of system calls is rather small (~300), and for performance reasons, I maintained the system call information in an array. Each array element will contain information, as described in the mytable struct: 
```C
typedef struct {
    asmlinkage long (*f)(struct pt_regs);
    int intercepted;
    int monitored;
    int listcount;
    struct list_head my_list;
} mytable;
``` 

2. I used linked list for storing information about the monitored processes; using an array of fixed size is entirely inadequate (because the search time will be the same as a linked list, the implementation complexity will be the same, but the size of the array will limit the number of entries).

3. The system call table is exported by the v`oid* sys_call_table[NR_syscalls]`, present in one of the kernel source files from the VM image on CDF.

4. Since the 2.6 kernel is preemptive, I protected access to shared data. I used __spinlocks__ for this purpose.

5. I used the system call number 0 for `MY_CUSTOM_SYSCALL`. 

6. Logging the system call will be done using the `log_message` macro, defined in the `interceptor.h` header file.

7. For testing, i used the tester programs. Remember to run the tester using sudo privileges in the VM (if someone wants to use it). When you are ready to test these, use the `test_intercept.c` tester. To test all commands (both related to intercepting and to monitoring), you can use the `test_full.c` tester