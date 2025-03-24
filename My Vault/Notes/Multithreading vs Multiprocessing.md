
# Multithreading vs Multiprocessing


DATE:  24-03-25


Tags: [[Notes/python|python]]

# References:
https://www.youtube.com/watch?v=AZnGRKFUU0c&t=5s



# Content:


People have different understandings of Python's capabilities when it comes to handling multiple tasks simultaneously. 2 common statements: 
	"Python is multi-threaded"
	"Python is not multi-threaded" 
Python is actually multi-threaded, but it is not simultaneously multi-threaded. 
This means that while Python can create and manage multiple threads, only one thread within a single process can execute at any given time.


To understand this better, we first need to differentiate between Processes and Threads:

- Process: A process is an instance of a program running on your operating system1 . Examples include an open window of Firefox or multiple instances of Microsoft Word1 . When a program runs as a process, the operating system allocates a separate memory space for its code and data1 .

•

Thread: A thread is the smallest sequence of instructions that can be managed by the operating system1 . A process has at least one thread1 . This thread has its own registers (small storage locations within the CPU) and a stack (a region of memory used for function calls and local variables)1 . Importantly, a thread has access to all the data within its parent process's memory space1 . A single process can spawn multiple threads, becoming multi-threaded1 . Each of these threads will have its own registers and stack but will share the process's memory1 .

•

Memory Isolation: A crucial distinction is that the data space of one process is not directly accessible to another process1 . If different processes need to share data, they must use mechanisms like queues or pipes1 . Threads within the same process can also use queues and pipes for data sharing, although this might involve some overhead1 .

Concurrency vs. Parallelism

The video touches upon the concepts of concurrency and parallelism2 ....

•

Concurrency: In Python's threading model, threads within the same process are concurrent but not parallel2 .... This means that the execution of multiple threads is interleaved in time. Each thread gets a small slice of execution time, giving the illusion of running simultaneously. However, no two threads from the same process are ever truly executing at the exact same moment2 .... The operating system can switch between these threads, allowing progress to be made on multiple tasks, but not at the same instant within a single process2 .

•

Parallelism: On the other hand, processes in Python can execute truly simultaneously (in parallel), especially on multi-core CPUs2 . As the video illustrates, threads from different processes (process zero and process one in their example) can run at the same time because they belong to separate memory spaces and are managed independently by the operating system2 .

The Global Interpreter Lock (GIL)

The reason Python threads within the same process cannot execute simultaneously is due to a mechanism called the Global Interpreter Lock (GIL)2 .

•

The GIL is a protected global lock within the Python interpreter2 . Only one thread can acquire this lock at any given time2 . By ensuring that only one thread can execute Python bytecode at once, the GIL guarantees thread safety in CPython (the most common Python implementation)2 . Thread safety, in this context, implies that there can be no deadlocks (a situation where two or more threads are blocked indefinitely, waiting for each other to release resources) within the interpreter itself2 .

•

While the GIL simplifies memory management and ensures thread safety for the interpreter's internal state, it prevents simultaneous multi-threading for CPU-bound tasks within a single process2 . This means that if you have a computationally intensive task, using multiple threads in Python will likely not result in a significant speedup on a multi-core processor because only one thread will be actively using a CPU core at any moment2 .... In fact, the overhead of switching between threads might even make the program run slower for purely CPU-bound tasks6 .

•

Interestingly, the GIL can actually improve the performance of single-threaded programs because the interpreter doesn't need to manage fine-grained locking for every object2 .

Impact on I/O-bound vs. CPU-bound Tasks

The GIL's limitation primarily affects CPU-bound tasks, which involve a lot of processing and computation2 .... However, I/O-bound tasks are hardly affected by the GIL2 ....

•

I/O-bound tasks are those where the program spends most of its time waiting for input/output operations to complete, such as reading from or writing to files, network requests, or waiting for user input2 ....

•

When a thread encounters an I/O operation in Python, the interpreter releases the GIL, allowing other threads to execute while the first thread is waiting for the I/O to finish2 . This makes threading an effective way to improve the responsiveness of programs with I/O-bound operations2 .... For example, in a graphical user interface (GUI) application, you can use a separate thread to perform a long-running I/O operation without freezing the main thread responsible for updating the user interface6 .

Experimenting with Threads (CPU-bound Task)

The video demonstrates an experiment with a CPU-bound task using the threading library3 ....

•

A function f is defined that performs an intensive calculation within an infinite loop, incrementing a histogram based on the time elapsed3 . Importantly, this function is single-core limited and not multi-threaded internally3 .

•

Multiple threads are created, each given a deep copy of an array to work on3 .

•

The results show that even with multiple threads (4, 8, and 16) on a multi-core CPU, only one thread executes at any given time4 .... This is visualized in the activity plot where only one horizontal bar (representing a thread doing work) shows activity at any point4 .

•

The CPython interpreter allows a thread to run for a maximum of approximately 15 milliseconds or until it encounters a blocking I/O call before switching to another thread4 ....

•

Increasing the number of threads does not significantly increase the total amount of work done for this CPU-bound task4 .... In the experiment, the total number of function calls remained around two million regardless of whether 4, 8, or 16 threads were used4 .... There was even increased asymmetry in execution time among threads as the number of threads increased, with some threads getting significantly less execution time4 ....

Moving to Multiprocessing (CPU-bound Task)

To overcome the limitations of the GIL for CPU-bound tasks, Python offers the multiprocessing library5 .

•

multiprocessing allows you to create and manage multiple independent processes, each with its own Python interpreter and memory space5 . This means that processes can execute in parallel on multi-core processors, as they are not bound by a single GIL2 ....

•

The video demonstrates modifying the code to use multiprocessing5 .... Key changes include using time.time() for a process-independent timestamp and explicitly returning the result array from each process since processes do not share memory directly5 .

•

A pool of worker processes is created using multiprocessing.Pool, and the starmap function is used to distribute the work (the function f and the data array) to these processes8 . The pool automatically handles sending data to and receiving results from the worker processes8 .

•

The activity plot for multiprocessing shows that each process executes simultaneously, demonstrating true parallelism8 .

•

Using multiprocessing resulted in a significant performance improvement for the CPU-bound task6 .... With four processes, approximately four times the work was done compared to multi-threading8 . With eight processes on an eight-core CPU (with simultaneous multi-threading), the work done was nearly eight times more9 . Even with 16 processes (more than the number of physical cores), a substantial improvement was still observed, although the scaling wasn't perfectly linear due to operating system overhead and the nature of simultaneous multi-threading9 .

•

However, it's important to note that there is overhead associated with creating and managing processes, including the communication of data between processes (packing, sending, and receiving)7 ....

Inter-thread and Inter-process Communication

Both threads and processes might need to communicate and share data1 .

•

Threads: Threads within the same process can directly access the process's shared memory1 . However, this shared access can lead to race conditions and data corruption if not managed properly7 . Mechanisms like locks are used to protect shared data and ensure that only one thread can access or modify it at a time7 . Additionally, queues (from the queue module) can be used for thread-safe communication7 .... While pipes can also be used, they might become corrupted if accessed simultaneously by multiple threads10 .

•

Processes: Since processes have separate memory spaces, they cannot directly access each other's data1 . To share data, they must use inter-process communication (IPC) mechanisms like queues (from the multiprocessing module, which are process-safe), pipes, or shared memory segments managed by the operating system1 .... Queues are generally more robust for simultaneous access by multiple processes but might have a higher setup cost than pipes1 ....

When to Use Threading vs. Multiprocessing

The video provides clear guidelines on when to choose threading or multiprocessing6 :

•

Use Multiprocessing for CPU-limited tasks: If your program involves a significant amount of computation and can be broken down into independent tasks, multiprocessing is generally the way to go as it can leverage multiple CPU cores for true parallelism6 .

•

Use Multithreading for I/O-limited tasks: If your program spends a lot of time waiting for I/O operations, threading can improve responsiveness by allowing other threads to run while one thread is blocked waiting for I/O2 .... Threading is also excellent for GUI applications to keep the interface responsive while background tasks are being performed6 .

Potential Pitfalls and Considerations

•

Overhead: Both threading and multiprocessing introduce some overhead. Creating and managing threads is generally less costly than processes3 . However, the inter-process communication required in multiprocessing can introduce significant overhead, especially if large amounts of data need to be transferred frequently between processes7 .... If the tasks are too short or involve too much data transfer relative to the computation, multiprocessing might not provide a speedup and could even make the program slower7 .

•

Native Multithreading in Libraries: Many performance-critical libraries like NumPy and SciPy are heavily optimized and often utilize simultaneous multi-threading in their underlying C or Fortran implementations6 .... If your CPU-bound task relies heavily on these libraries and they are already using all available cores, you might not see any additional benefit from using Python's multiprocessing on top of them6 ....

•

Thread Safety and Synchronization: When using threads that share data, it is crucial to implement proper synchronization mechanisms (like locks) to prevent race conditions and ensure data integrity7 .... Incorrectly managing shared resources can lead to subtle and hard-to-debug errors.

In summary, Python's threading is concurrent within a single process due to the GIL and is best suited for I/O-bound tasks. Multiprocessing, on the other hand, provides true parallelism by utilizing multiple processes, each with its own interpreter and memory space, making it the preferred choice for CPU-bound tasks that can be parallelized7 . Choosing the right approach depends on the nature of the tasks your program needs to perform and understanding the underlying mechanisms of threading and multiprocessing in Python6 .



