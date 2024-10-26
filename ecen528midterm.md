#### 1: Know Flynn's Taxonomy
Flynn's Taxonomy distinguishes computer architectures based on the concept of *Instruction* and *Data* streams
- each dimension can be classified as Single or Multiple
- "stream" refers to a sequence or flow of either instructions or data resulding from porcessor operation
- DOES NOT TAKE INTO CONSIDERATION THE COMPUTERS STRUCTURE    
SISD: single instruction stream, single data stream  
SIMD: single instruction stream, multiple data stream  
MISD: multiple instruction stream, single data stream  
MIMD: multiple instruction stream, multiple data stream

#### 2: Be able to contrast memory architecture types (shared, distributed, hybrid)
Shared:
- processors operate independently but share the same memory resources
- changes in memory made by one processor are visible to all others
- UMA: uniform memory access
    - identical processors
    - equal access time to memory
    - may or may not be cache coherent
- NUMA: non-uniform memory access
    - often made by linking SMPs
    - one SMP can directly access another
    - memory access across links is slower
    - may or may not be cache coherent

Distributed:
- processors(nodes) each have their own local memory and operate independently
- nodes are connected via a communication network, by sending and receiving messages

Hybrid:
- shared memory is attached to a GPU/CPU
- distributed memory is connected by networking multiple shared memory machines (nodes)
- a node only knows about its own memory
- network communications necessary to move data from node to node

#### 3: Know what constitutes Simultaneous Multithreading
- several critical components of the processor are duplicated
- some other functional units are shared
- can alternate between threads and do some operations concurrently to save time
- the OS will schedule tasks on the different logical processors

#### 4: Indicate the different ways CUDA blocks can communicate
threads within a block:
- shared memory
- atomic operations
- barrier synchronization

threads in *different* blocks:
- do not interact

#### 5: Know the usage and meaning of CUDA keywords (``__device__``, ``__global__``, etc.)
- ``__global__`` defines a kernel function. it also must return ``void``
- ``__device__`` defines a function that the host cannot call
- ``__host__`` defines code that the device cannot call. most of the time, not necessary to specify
- ``__restrict__`` 

#### 6: Know how CUDA blocks are scheduled to run on a Streaming Multiprocessor (SM)
- threads are assigned to SM's in block granularity
- note: each block can execute in any order relative to others
- 1536 threads per block
- 8 blocks per SM
- each SM manages/schedules thread execution
- each warp has 32 threads, threads in a warp execute in SIMD

#### 7: Given a CUDA block size, determine the maximum number of threads resident on an SM
- max number of blocks * block size
- max number of threads per sm

#### 8: Given a CUDA block size and count, determine the number of warps
- find the number of threads, ceiling divide by 32

#### 9: Determine the reduction of memory bandwidth achieved for matrix multiply with tiling
- without tiling: 1 FLOP / load
- with tiling: n FLOPS / load

#### 10: Know what is meant by control divergence
- when individual threads in a warp take different paths

#### 11: Be able to calculate the number of warps with control divergence
- control divergence happens horizontally, not vertically

#### 12: Know under what conditions memory accesses are coalesced
- when memory accesses are parallel to the warps, or perpendicular to the threads, accesses are coalesced

#### 13: Know the difference between the Nsight tools and how they can be used
- Systems
    - workload-level performance (analyze application algorithm system-wide)
    - broad brush performance analysis (whole computer)
- Compute
    - detailed cuda kernel performance
    - precise analysis of GPU/device
- Graphics
    - debug/optimize graphics workloads
- NVTX
    - NVIDIA tools extension library

#### 14: Know what a convolution mask is and methods for caching it in constant memory
- the convolution mask is the the array of values with which data is convoluted
- can be stored in device memory using ``__constant__ float_dM[MAX_MASK_WIDTH];``
    - then it's used in device code as if it were a global variable
- can also cache the mask using ``const __restrict__``
    - ex: ``__global__ void convolution_kernel(...vars... const float __restrict__ *M) {...}``

#### 15: Know the difference between strong and weak scaling
strong scaling **(Amdahl)**:
- the total problem size stays fixed as more processors are added
- this aims to see how parallelizable a problem is

weak scaling **(Gustafson)**:
- the total problem size is proportional to the number of processors used
- this aims to see how well a problem can scale in general

variables for formulas:
- *f*: sequential fraction of program 
- *p*: num processors
- *n*: input size

#### 16: Be able to apply parallel performance laws and equations to a problem description
cost of a parallel program: *Cp = p \* Tp(n)*  
speedup: *Sp = T\*(n) / Tp(n)*  
efficiency: *Ep = T\*(n) / (p \* Tp(n)*  
amdahl's law: *Sp = 1/(f + (1-f)/p))*  
gustafson's law: *Sp = f + (1-f)p*  

#### 17: Be familiar with the usage and features of the Roofline model
roofline model:
- flat part is based on perfect usage of compute resources and clock frequency (peak fp performance)
    - how many numbers can you crunch
- slanted part is based on bandwidth and operational intensity (peak memory bandwidth)
    - bandwidth: how fast can you access data
    - op intensity: how much do you use data after it's accessed
- changes to increase performance
    - increasing bandwidth will increase the slope of the slanted line
    - increasing compute capabilities (clock cycle, more cores) will elevate the flat line
    - increasing operational intensity (more efficient code) will move your actual results to the right, meaning less strain on memory bandwidth

#### 18: Be familiar with the levels of parallelism and what limits parallelism
Instruction-Level Parallelism
- multiple, *independent* instructions of a program can be executed in parallel
Data Parallelism
- same *independent* sequence of operations applied to multiple elements of a larger data structure
Functional (Task) Parallelism
- *independent* program parts can be executed in parallel (separate blocks, loops, or funciton calls)
- loop parallelism: *independent* loop iterations can be executed in parallel
    - depending on circumstances, loop parallelism could be implemented at all 3 levels
**TAKEAWAY: dependencies limit parallelism**

#### 19: Given an instruction sequence, identify the type of dependency

#### 20: Be able to identify parallel programming models (e.g., Threads, Message Passing, Data Parallel, etc.)
**PROGRAMMING MODELS HAVE ABSOLUTELY NOTHING TO DO WITH HARDWARE**  
Shared Memory Segments (without threads)
- many smaller components of physically distributed memory are combined to create the illusion of a large shared memory model
- different processes read and write to the shared memory asynchronously (locks & other stuff necessary to prevent race conditions and deadlocks)

Threads
- a single 'heavyweight' process can have multiple 'lightweight', concurrent execution paths

Distributed Memory/Message Passing
- a set of tasks that have their own local memory during computation-
- tasks exchange data through communications, which usually requires handshaking/reciprocative operations

Data Parallel
- in this model, most of the parallel work focuses on performing operations on a data set. the data set is split up into several chunks, which are operated on in parallel

Hybrid
- a hybrid model combines several of the above. a common example is a hybrid shared-distributed memory model

Single Program Multiple Data
- all tasks execute their copy of the same program, on different sets of data

Multiple Program Multiple Data
- several programs or processes do different tasks on different data, or share some data

#### 21: Be able to identify parallel programming patterns (e.g., Fork-Join, Producer-Consumer)
creation of processes / threads
- threads are created, either statically or dynamically

fork-join
- a parent thread spawns n number of threads, then waits for its children to return at a join
- each tine in the fork is identical

parbegin-parend
- kinda like fork-join: except each tine is a different task
- ``pragma omp sections`` & ``pragma omp section``

SIMD, SPMD
- SIMD = data parallelism in the strong sense. exact same instructions applied to data *at the same time, in lock step* (graphics processing would be a good example)
- SPMD = same program is being executed on all of the data, but might be happening asynchronously

master-slave
- one master which onctrols execution of the program. the master thread will spawn worker threads, and assign each of them work

client-server
- kinda like multiple program multiple data
- distributed computing
- client will go to server, reqeust work, then client will do work and report back

pipelining
- data moves through the threads, with each thread acting as a stage in the pipeline instead of threads moving through the data

task pools
- tasks are stored in a pool, and then a fixed number of threads work together to slowly chip away at the tasks
- threads can add additional work to the pool.

producer-consumer
- there are producer and consumer threads. 
    - producer threads produce data which are used as input by consumer threads
    - a common data structrue, like a fixed-length buffer is used to move data elements between producer and consumer threads

#### 22: Know the usage and meaning of the C++ concurrency classes and methods studied in class
``std::thread``:
- pass the execution code (a callable) to the constructor
    - function pointer
    - function object
    - lambda expression
- lots of OS overhead to spawn threads
- threads begin execution immediately upon construction and have no return value
- class members
    - id [type]
    - thread
    - joinable
    - get_id
    - hardware_concurrency [static]
    - join
    - detach  
``std::atomic_flag`` is a thing  
``std::atomic<int> myAtomic(0)`` would initialize an atomic int to the value 0
mutexes are a thing   
- have to avoid deadlock: when two threads are waiting for each other to release their locks
- class members
    - mutex
    - lock
    - try_lock (attempts mutex lock, without blocking)
    - unlock
    - also wanna use unique_lock  
**I HAVE NO IDEA HOW RAII AND CONDITION VARIABLES WORK. I NEED TO LEARN HOW THEY WORK LATER**
- RAII philosophy is a good idea when it comes to mutexes, so you don't lose them
- condition variables must be used with a mutex, they are used to help make the use of mutex locks smoother

