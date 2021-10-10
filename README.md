# Linux-Project
- 中央大學 106 年度：Linux 作業系統 Project

## Project 1: Linux Kernel Code Trace
- Following the style used in linuxLecture_3_9-2.ppt, linuxLecture_3_9-3.ppt, linuxLecture_3_9-4.ppt, and linuxLecture_3_9-5-1.ppt, to write a power point file (85 points) and a report (25 points) to describe the    following topics.
    - segamentation unit, page unit of X64. Following the style used in linuxLecture_3_9-2.ppt and linuxLecture_3_9-2.ppt to make your ppt files.
    - How does Linux kernel 4.0 use the segmentation unit and page unit in the 64 bit mode of X64? Following the style used in linuxLecture_3_9-2.ppt and linuxLecture_3_9-2.ppt to make your ppt files.
    - How does Linux kernel 4.0 create the kernel address space translation table entry template that will be used by every process in the system. Following the style used in linuxLecture_3_9-5-1.ppt to make your ppt files.
    - The size of Linux kernel 4.0.
    - The relationship between the linear addresses in the kernel address space and physical addresses. Following the style used in linuxLecture_3_9-5-1.ppt to make your ppt files.

## Project 2: 新增自定義System Call - Check PID Namespace Level
- Write a new system call get_ns_level() so that a process can use it to get the level of its PID name space. The return value of this system is the level of the PID name space of the process executing this system call.
``` c
//prototype of the new system call is as follows:     
int get_ns_level() 
```

- Write a system call int get_pdps_of_pid_at_ancestor_ns(struct pdps_with_pid_at_ancestor_ns * , long) with the following properties.
``` c
 struct pdps_with_pid_at_ancestor_ns
{
    long process_descriptor_pointer;  //used to store the process descriptor pointer of a process with local PID specified as  
                                      //the second parameter of this system call           
};           

//prototype of the new system call is as follows: 
int get_pdps_of_pid_at_ancestor_ns(struct pdps_with_pid_at_ancestor_ns * , long) 
```

- What follows is a code excerpt that you need to use in your program
``` c
 struct pdps_with_pid_at_ancestor_ns twenty_results[20];
    long    pid;
    int     result,k;

    result = get_pdps_of_pid_at_ancestor_ns(twenty_results, pid);

    if(k != -1)        
        for(k=0; k<result; k++)
            printf("At level %d, process descriptor pointer %u has local PID %d\n", k, twenty_results[k], pid);
        printf("\n");
    else
        printf("No Ancestor PID name has a process with local PID %d\n", pid);
```
- This system call has a return value. The return value is either -1, 0, or a positive value.
- Return value -1 means that the system call is not executed successfully. Otherwise, the return value represents the number of PID name spaces that have a process with local PID specified as the second parameter of the system call and are ancestor PID name spaces of the PID name space of the process executing this system call.
- The first parameter is a pointer which points to a memory area storing the results of this system call. The data type of this pointer is struct pdps_with_pid_at_ancestor_ns * which is described in the above code excerpt.
- If the level of the PID name space of the process that executes this system is greater than 19 or equal to 0, then return -1 and do not store any result at the first parameter.
- Otherwise,
    - For each PID name space which has level i and is an ancestor name space of the PID name space of the process executing this system call, at element [i] of the first parameter, store the process descriptor pointer of the process in this PID name space and with local PID specified as the second parameter of this system call.
    - If an ancestor PID name space with level j does not have a process with local PID specified as the second parameter of this system call, store 0 at element [j] of the first parameter.
    - The return value of the above result is the level of the PID name space of the process executing this system call.
- Hint
    - Inside the Linux kernel, you need to use function copy_to_user() to copy data to a user address buffer.
    - You may need to add a new system call to do project 2.
        - Check the "Referenced Material" part of the Course web site to see how to add a new system call in Linux.