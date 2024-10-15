# ECEN 528

## CONTENT FOR MIDTERM 1

### C++ CONCURRENCY
#### Mutex (RAII: Resourse Acquisition Is Initialization)
- there is a correct way to manage mutexes, using lock_guards -> automatically unlock mutexes at end of scope
- unique_lock -> the mutex can be unlocked and relocked, but mutex is still unlocked at end of scope or on exception

#### Condition Variables
- used along with a mutex & a lock guard
- example: given a buffer with several users that write & read to it concurrently
    - in the event that the buffer fills, *TONS* of CPU time will be burned in the process of continually trying to acquire mutex, and then continually checking to see if the buffer is full
    - the condition variable arbitrates that by notifying writers and readers of the status of the buffer

#### Synchronous vs. Asynchronous Tasks
- Asynchronous function calls can run in parallel to the main thread
- Promises & Futures are a thing:
    - i fell asleep for this
- Async()
    - asleep through this as well 

#### POSIX Threads
- 

#### OpenMP
- Directive based programming
    - can sprinkle compiler directives throughout code so that it can be compiled in linear or parallel fashion
    - can use the macro \_OPENMP to have conditionally compiled code
- based on fork-join parallelism philosophy
- NOTE: processes have separate virtual memory spaces (this is a speed constraint), whereas threads have a shared virtual memory space
- the challenge with OpenMP programming is balancing several different factors
    - load distribution
    - shared/private variables
    - 
- an openMP construct is defined as follows
```
            -  Directive ->         #pragma omp parallel
          / 
construct|     Structured block     { 
          \                             code goes here
            -                       }
```
