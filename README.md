Download Link: https://assignmentchef.com/product/solved-ece650-homework1-implementation-of-malloc-library
<br>



For this assignment, you will implement your own version of several memory allocation functions from the C standard library (actually you will have the chance to implement and study several different versions as described below). Your implementation is to be done in C code.

The C standard library includes 4 malloc-related library functions: malloc(), free(), calloc(), and realloc(). In this assignment, you only need to implement versions of malloc() and free():

void *malloc(size_t size);        void free(void *ptr);

Please refer to the man pages for full descriptions of the expected operation for these functions. Essentially, malloc() takes in a size (number of bytes) for a memory allocation, locates an address in the program’s data region where there is enough space to fit the specified number of bytes, and returns this address for use by the calling program. The free() function takes an address (that was returned by a previous malloc operation) and marks that data region as available again for use.

The submission instructions at the end of this assignment description provide specific details about what code files to create, what to name your new versions of the malloc functions, etc.

As you work through implementing malloc() and free(), you will discover that as memory allocations and deallocations happen, you will sometimes free a region of memory that is adjacent to other also free memory region(s).  <strong>Your implementation is <em>required</em> to coalesce in this situation by merging the adjacent free regions into a single free region of memory. Similarly, it is also required to split free regions if the ideal free region is larger than requested size. </strong>

<strong>Hint: </strong>For implementing malloc(), you should become familiar with the sbrk() system call. This system call is useful for: 1) returning the address that represents the current end of the processes data segment (called program break), and 2) growing the size of the processes data segment by the amount specified by “increment”.

void *sbrk(intptr_t increment);

<strong>Hint:</strong> A common way to implement malloc() / free() and manage the memory space is to <strong><u>keep a</u> <u>data structure that represents a list of free memory regions</u></strong>. This collection of free memory ranges would change as malloc() and free() are called to allocate and release regions of memory in the process data segment. You may design and implement your malloc and free using structures and state tracking as you see best fit.

<strong>In this assignment, you will develop a malloc implementation and study different allocation policies. In Homework 2, you will make this implementation thread-safe.</strong>

<h1>Study of Memory Allocation Policies</h1>

Your task is to implement 2 versions of malloc and free, each based on a different strategy for determining the memory region to allocate.  The two strategies are:

<ol>

 <li><strong>First Fit</strong>: Examine the free space tracker (e.g. free list), and allocate an address from the first free region with enough space to fit the requested allocation size.</li>

 <li><strong>Best Fit</strong>: Examine all of the free space information, and allocate an address from the free region which has the smallest number of bytes greater than or equal to the requested allocation size.</li>

</ol>

The following picture illustrates how each strategy would operate (assuming free regions are traversed in a left to right order) for a malloc() request of 2 bytes:




<h1>Requirement: Malloc implementations</h1>

To implement your allocation strategies, you will create 4 functions:

//First Fit malloc/free void *ff_malloc(size_t size); void ff_free(void *ptr);

//Best Fit malloc/free void *bf_malloc(size_t size); void bf_free(void *ptr);

Note, that in all cases, a policy to minimize the size of the process’s data segment should be used. In other words, if there is no free space that fits an allocation request, then sbrk() should be used to create that space. Moreover, when a request for allocation is received, <strong>only the requested size of memory must be allocated </strong>(with the exception that the remaining free space is too small to keep track of). However, you do not need to perform any type of garbage collection (e.g. reducing the size of the process’s data segment, even if allocations at the top of the data segment have been freed).

On free(), your implementation is required to merge the newly freed region with any currently free adjacent regions. In other words, your bookkeeping data structure should not contain multiple adjacent free regions, as this would lead to poor region selection during malloc. Since we are managing the memory space by ourselves, there is no need to call the original free() function.

<h1>Requirement: Performance study report</h1>

In addition to implementing these malloc functions, you are tasked to conduct a performance study of the malloc() performance with different allocation policies.  Several programs for experimentation will be provided that perform malloc() and free() requests with different patterns (e.g. frequencies, sizes). The metrics of interest will be: 1) the run-time of the program as the implementation of different allocation policies may result in different amounts of memory allocation overhead, and 2) fragmentation (i.e. the amount of allocated data segment space divided by total data segment space).  In order to capture #2, you should also implement two additional library functions:

unsigned long get_data_segment_size(); //in bytes unsigned long get_data_segment_free_space_size(); //in bytes The functions must include the spaced used for metadata also.

get_data_segment_size() = entire heap memory (this includes memory used to save metadata) get_free_space_segment_size() = size of the “free list” = (actual usable free space + space occupied by metadata) of the blocks in your free list

Then, using these functions, you can use the included test programs as well as test programs of your own creation to evaluate and compare the algorithms.

<h1>The Starter Kit</h1>

A starter kit is included in a file on Sakai in a folder under Resources: <strong>homework1-kit.tgz</strong>

This archive can be retrieved on the command-line using wget if hosted on a website or downloaded to a local computer and transferred to the VM using scp for *nix/Mac systems or by using a file transfer app on Windows (Mobaxterm for Windows has built-in file transfer with drag and drop). It can then be extracted using “tar xvzf homework1-kit.tgz”.

The kit includes:

<ul>

 <li><strong>Makefile</strong>: A sample Makefile for libmymalloc.so.</li>

 <li><strong>general_tests/</strong>: General correctness test, see README.txt for details.</li>

 <li><strong>alloc_policy_tests/</strong>: Allocation policy test cases, see README.txt for details.</li>

 <li><strong>alloc_policy_tests_osx/</strong>: Same thing adapted for MacOS.</li>

</ul>

<em>NOTE: Mac OS is <u>not</u> the grading platform; these files are provided as a convenience. Additionally, you may need to change #include “time.h” to #include “sys/time.h”</em>

<h1>Testing your system</h1>

<strong><u>tl;dr: Write your own tests!</u></strong> At least a basic print data structure and minimal testing can help you catch several bugs.

Code is provided for minimal testing, but the provided materials will not exhaustively evaluate the correctness of your implementation. It is recommended to create your own test software that uses your library, both to aid during development and to ensure correctness in a variety of situations. If you’d like a general introduction to developing test cases, see <a href="https://www.csc.ncsu.edu/courses/csc216-common/Heckman/lectures/04_Testing.pdf">Software Testing by</a> <a href="https://www.csc.ncsu.edu/courses/csc216-common/Heckman/lectures/04_Testing.pdf">Sarah Heckman</a><a href="https://www.csc.ncsu.edu/courses/csc216-common/Heckman/lectures/04_Testing.pdf">.</a>

For debugging make sure to use tools such as gdb and valgrind. Please contact TAs and get help if you are not comfortable with using these tools. Also, please use the relevant online resources that may help you. Make sure to check for any errors in valgrind as these may be uncaught bugs in your program.

<h2>A         refresher     on       gdb     in        emacs</h2>

<ul>

 <li>In your Makefile(s), change -O3 to -ggdb3. -O3 is an optimization flag and -ggdb3 is a debugging flag. -ggdb3 is required to let gdb add information to the compiled binary that will help it pinpoint exact line numbers, function names, etc.</li>

 <li>Here is a link to how you may use gdb in emacs: <a href="https://www.youtube.com/watch?v=Vn1kDWVxq4s">AoD_1: Debugging with GDB in Emacs</a> You may have a better reference video, please feel free to share that with others.</li>

 <li>In emacs open any file in the directory where you have the binary you need to run. e.g. if it is mymalloc_test from general_tests, navigate to that directory and open a file.</li>

 <li>Then run M-x gdb</li>

 <li>Then type (r) for run.</li>

 <li>Your code ends in a seg-fault–If you have followed all the above steps, gdb in emacs already shows you the line on which the seg-fault has occurred.</li>

 <li>You can then run (bt) for backtrace which will show you which series of function calls led you here, e.g. line number in main test file which resulted in this function call.</li>

 <li>Please consult this GDB cheat sheet <a href="http://users.ece.utexas.edu/~adnan/gdb-refcard.pdf">GDB QUICK REFERENCE GDB Version 4</a> for more. <strong>Valgrind </strong></li>

</ul>

Some flags that may be useful with valgrind:

valgrind -v –leak-check=full –track-origins=yes ./program