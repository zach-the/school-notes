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

for construct:
- splits up loop iterations
- by default, there is a barrier at the end of the ``omp for``

**add something about #nowait clause**

```
#pragma omp parallel    |
#pragma omp for         | #pragma omp parallel for
for (I=0;I<N;I++)
{
    STUFF();
}
```

in order to be made parallel, a loop must have canonical 'shape'

in order to avoid race conditions/variable collisions, can use 
```
#pragma omp parallel for private(j)
```
here, ``private(j)`` clause makes it so that each thread gets its own copy of j

**Schedulign Options**
the ``schedule`` clause affects how loop iterations are assigned to threads in the team
- ``schedule(static[,chunk])``
    - assignment of iterates to threads is done statically, doesn't change
    - blocks of iterations of size 'chunk' are *assigned* to each thread
    - when 'chunk' is not given, nearly eaqual size chunks are formed
- **finish notes on scheduling**
> example: ``#pragma omp parallel for private(j) schedule(static,2)``  
> additional considerations:


with perfectly nested rectangular loops, we can parallelize multiple loops in the nest with the ``collapse`` clause

***KNOW THE DATA-SHARING CLAUSES, WHAT THEY MEAN, AND HOW TO USE THEM***
***SPECIFICALLY, PRIVATE, FIRSTPRIVATE, LASTPRIVATE***

Synchronization Constructs
```
#pragma omp barrier
#pragma omp critical(name)
#pragma omp atomic
```
watch out for atomics ON THE EXAM (atomics are not like critical sections)
critical is similar to mutex (read the docs)

``Reduction`` **Clause**
- ``reduction(op : list)``
- inside a parallel or work-distribution cosntruct
    - local copy of each scalar variable is made adn initialized depending on the operation (``op``)
    - updates occur using local copy
    - local copies reduced into a single value, then combined with orig. global value
- variables in ``list`` must be shared in the enclosing parallel region


