
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

- **Process**: 
	- A process is an instance of a program running on your operating system.
	  *eg.* Open window of Firefox or multiple instances of Microsoft Word . 
	- When a program runs as a process, the operating system allocates a separate memory space for its code and data.
	  
- **Thread**: 
	- Smallest sequence of instructions that can be managed by the operating system. 
	- A process has at least one thread. 
	- Thread has its own registers (small storage locations within the CPU) and a stack (a region of memory used for function calls and local variables). 
	- A thread has access to all the data within its parent process's memory space.
	-  A single process can spawn multiple threads, becoming multi-threaded. Each of these threads will have its own registers and stack but will share the process's memory.

- Memory Isolation: A crucial distinction is that the data space of one process is not directly accessible to another process. If different processes need to share data, they must use mechanisms like queues or pipes. Threads within the same process can also use queues and pipes for data sharing, although this might involve some overhead.
  ![[Attachments/Pasted image 20250324221401.png]]




#### [[Concurrency]] vs. [[Parallelism]]

- Concurrency: 
	- In Python's threading model, threads within the same process are concurrent but not parallel. This means that the execution of multiple threads is interleaved in time. Each thread gets a small slice of execution time, giving the illusion of running simultaneously. However, no two threads from the same process are ever truly executing at the exact same moment. 
	- The operating system can switch between these threads, allowing progress to be made on multiple tasks, but not at the same instant within a single process.
	- ![[Attachments/Pasted image 20250324222057.png]]

- Parallelism: 
	- Processes in Python can execute truly simultaneously (in parallel), especially on multi-core CPUs. As the video illustrates, threads from different processes (process 0 and process 1 in their example) can run at the same time because they belong to separate memory spaces of 2 diff processes and are managed independently by the operating system.


- [[Global Interpreter Lock]] (GIL):
	- The reason Python threads within the same process cannot execute simultaneously is due to a mechanism called the Global Interpreter Lock (GIL).
	- The GIL is a protected global lock within the Python interpreter. Only 1 thread can acquire this lock at any given time. By ensuring that only one thread can execute Python bytecode at once, the GIL guarantees thread safety in CPython (the most common Python implementation). 
	- Thread safety, in this context, implies that there can be no deadlocks (a situation where two or more threads are blocked indefinitely, waiting for each other to release resources) within the interpreter itself.
	- While the GIL simplifies memory management and ensures thread safety for the interpreter's internal state, it prevents simultaneous multi-threading for CPU-bound tasks within a single process. This means that if you have a computationally intensive task, using multiple threads in Python will likely not result in a significant speedup on a multi-core processor because only 1 thread will be actively using a CPU core at any moment.
	- In fact, the overhead of switching between threads might even make the program run slower for purely CPU-bound tasks.
	- Interestingly, the GIL can actually improve the performance of single-threaded programs because the interpreter doesn't need to manage fine-grained locking for every object.


- Impact on I/O-bound vs. CPU-bound Tasks:
	- The GIL's limitation primarily affects CPU-bound tasks, which involve a lot of processing and computation. However, I/O-bound tasks are hardly affected by the GIL.
	- I/O-bound tasks are those where the program spends most of its time waiting for input/output operations to complete, such as reading from or writing to files, network requests, or waiting for user input.
	- When a thread encounters an I/O operation in Python, the interpreter releases the GIL, allowing other threads to execute while the first thread is waiting for the I/O to finish. This makes threading an effective way to improve the responsiveness of programs with I/O-bound operations.
	- For example, in a graphical user interface (GUI) application, you can use a separate thread to perform a long-running I/O operation without freezing the main thread responsible for updating the user interface.
	- ![[Attachments/Pasted image 20250324223456.png]]

- ![[Attachments/Pasted image 20250324223638.png]] 

- Experimenting with Threads (CPU-bound Task): The video demonstrates an experiment with a CPU-bound task using the threading library:
	- A function f is defined that performs an intensive calculation within an infinite loop, incrementing a histogram based on the time elapsed3 . Importantly, this function is single-core limited and not multi-threaded internally.![[Attachments/Pasted image 20250324224328.png]]
	- Multiple threads are created, each given a deep copy of an array to work on.
	- The results show that even with multiple threads (4, 8, and 16) on a multi-core CPU, only one thread executes at any given time. 
	- This is visualized in the activity plot where only one horizontal bar (representing a thread doing work) shows activity at any point.![[Attachments/Pasted image 20250324224351.png]]![[Attachments/Pasted image 20250324224440.png]]
	- Total number of function calls across all threads was around 2 million in both cases. Around the same number of actual function calls were performed despite doubling the number of threads allocated.


- The [[CPython]] interpreter allows a thread to run for a maximum of approximately 15 milliseconds or until it encounters a blocking I/O call before switching to another thread.


- Moving to Multiprocessing (CPU-bound Task):
	- To overcome the limitations of the GIL for CPU-bound tasks, Python offers the multiprocessing library.
	- Multiprocessing allows you to create and manage multiple independent processes, each with its own Python interpreter and memory space. This means that processes can execute in parallel on multi-core processors, as they are not bound by a single GIL.
	- A pool of worker processes is created using multiprocessing.Pool, and the starmap function is used to distribute the work to these processes. The pool automatically handles sending data to and receiving results from the worker processes.
	- The activity plot for multiprocessing shows that each process executes simultaneously, demonstrating true parallelism.![[Attachments/Pasted image 20250324225403.png]]
	- Using multiprocessing resulted in a significant performance improvement for the CPU-bound task.. With 4 processes, approximately four times the work was done compared to multi-threading. With 8 processes on an eight-core CPU (with simultaneous multi-threading), the work done was nearly eight times more. Even with 16 processes (more than the number of physical cores), a substantial improvement was still observed, although the scaling wasn't perfectly linear due to operating system overhead and the nature of simultaneous multi-threading.
	- However, it's important to note that there is overhead associated with creating and managing processes, including the communication of data between processes (packing, sending, and receiving).


- Inter-thread and Inter-process Communication:
	- Both threads and processes might need to communicate and share data.
	- Threads: Threads within the same process can directly access the process's shared memory. However, this shared access can lead to race conditions and data corruption if not managed properly. 
	- Mechanisms like locks are used to protect shared data and ensure that only one thread can access or modify it at a time. 
	- Additionally, queues (from the queue module) can be used for thread-safe communication. While pipes can also be used, they might become corrupted if accessed simultaneously by multiple threads.
	- Processes: Since processes have separate memory spaces, they cannot directly access each other's data. 
	- To share data, they must use inter-process communication (IPC) mechanisms like queues (from the multiprocessing module, which are process-safe), pipes, or shared memory segments managed by the operating system. Queues are generally more robust for simultaneous access by multiple processes but might have a higher setup cost than pipes.


When to Use Threading vs. Multiprocessing

- Use Multiprocessing for CPU-limited tasks: If your program involves a significant amount of computation and can be broken down into independent tasks, multiprocessing is generally the way to go as it can leverage multiple CPU cores for true parallelism.
- Use Multithreading for I/O-limited tasks: If your program spends a lot of time waiting for I/O operations, threading can improve responsiveness by allowing other threads to run while one thread is blocked waiting for I/O. Threading is also excellent for GUI applications to keep the interface responsive while background tasks are being performed.

Potential Pitfalls and Considerations:

- Overhead: Both threading and multiprocessing introduce some overhead. Creating and managing threads is generally less costly than processes. However, the inter-process communication required in multiprocessing can introduce significant overhead, especially if large amounts of data need to be transferred frequently between processes. If the tasks are too short or involve too much data transfer relative to the computation, multiprocessing might not provide a speedup and could even make the program slower.
- Native Multithreading in Libraries: Many performance-critical libraries like NumPy and SciPy are heavily optimized and often utilize simultaneous multi-threading in their underlying C or Fortran implementations. If your CPU-bound task relies heavily on these libraries and they are already using all available cores, you might not see any additional benefit from using Python's multiprocessing on top of them.
- Thread Safety and Synchronization: When using threads that share data, it is crucial to implement proper synchronization mechanisms (like locks) to prevent race conditions and ensure data integrity. Incorrectly managing shared resources can lead to subtle and hard-to-debug errors.

