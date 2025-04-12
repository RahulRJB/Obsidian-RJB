
# Asynchronous Programming


DATE:  11-04-25


Tags: [[Notes/python|python]]

# References:
https://www.youtube.com/watch?v=t5Bo1Je9EmE


# Content:

**Synchronous** - 
- Everything is happening sequentially
- ![[Attachments/Pasted image 20250411174210.png]]`foo()` has to complete executing before running `print('tim')`
- The next operation waits for the current one to complete. Performance is often tied to processor clock speed. Fast processor -> Fast runs


**Asynchronous Programming** - 
- Execution of code in a non-blocking manner. While one task is waiting for an I/O-bound operation (like network requests or file operations), the program can execute other code.
- **Co-routine:** A special type of function defined with the async keyword. It is a wrapped version of theCalling a co-routine does not directly execute its code but returns a co-routine object.
- **async Keyword:** Used to define a co-routine function.
- **await Keyword:** Used inside an async function to pause the execution of the current co-routine and wait for another awaitable object (like another co-routine, a future, or a task) to complete. This allows the event loop to run other tasks.
- **Event Loop:** The core of asyncio. It manages and schedules the execution of co-routines and handles I/O operations. You typically start the event loop using asyncio.run().
- **Task:** Represents a concurrently running co-routine. You can create a task using asyncio.create_task() and it gets scheduled to run on the event loop.
- **Future:** An object that represents the result of an asynchronous operation that may or may not be complete yet. Tasks are a type of future. It acts as a placeholder for a value that will be available in the future.



