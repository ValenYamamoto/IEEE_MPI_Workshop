# Multithreading Programming with MPI
IEEE Technical Workshop 11/12/2020

## Overview
- [Multithreading Programming with MPI](#multithreading-programming-with-mpi)
  - [Overview](#overview)
  - [Background](#background)
  - [Introduction to MPI](#introduction-to-mpi)
  - [General MPI terms](#general-mpi-terms)
  - [Sending and Receiving Data](#sending-and-receiving-data)
      - [Blocking vs. Non-Blocking Send/Receive](#blocking-vs-non-blocking-sendreceive)
      - [Buffering](#buffering)
  - [Collective Communication Routines](#collective-communication-routines)
  - [Examples](#examples)
      - [Hello World](#hello-world)
      - [Hello Master Task](#hello-master-task)
      - [(Non-Blocking) Ping](#non-blocking-ping)
      - [Round Robin](#round-robin)
      - [Monte Carlo](#monte-carlo)
      - [Expensive Computation](#expensive-computation)
  - [Next Steps](#next-steps)
      - [Parallelize an already existing program/algorithm](#parallelize-an-already-existing-programalgorithm)
      - [Do a project](#do-a-project)
      - [Resources/Cool Reads](#resourcescool-reads)
## Background
* Originally, more processing power was accomplished by increasing the clock frequency of the CPU; eventually, the amount of heat this generates and the power consumption becomes too much.
* Companies begin making microprocessors with multiple processors on them; these processors are called cores
* Parallel programming then becomes increasing popular to utilize this new multi-core architecture
* Amdahl's Law - gives the theoretical speedup in latency of the execution of a task at a fixed workload that can be expected of a system whose resources are improved.
  * Can be used to estimate speedup when more threads are added, but really just a reminder that no matter how many threads the work is split up among, the serial portion of your code remains unaffected
![amdahl's law equation](./images/amdahls_law.png) **OR** ![amdahl's serial](./images/amdahls_law_2.png)

## Introduction to MPI
* MPI stands for **Message Passing Interface** and is a message passing library standard
  * the first standardized, vector independent message passing library
* Some different MPI libraries
  * Open MPI - thread safe, open source MPI. Mostly for C, Fortran
  * mpi4py - MPI for Python (What we'll be doing today)
* MPI spawns what it calls processes; ideally one process per core/thread
* General MPI Program Structure
  1. MPI inlude file
  2. declarations
  3. serial code
  4. initialize MPI environment (#include mpi4py in python does steps 1-4)
  5. do work
  6. terminate MPI environment (in mpi4py, steps 6-8 are done when the code stops)
  7. serial code
  8. program ends

## General MPI terms
* MPI uses objects called communicators and groups to define which collection of processes may communicate with each other
* Within each communicator, every process has its own unique, integer identifier called a rank or task id
  * in each communicator, begin at 0 and are continguous
  * rank is used to specify source and destination of messages within the communicator
* Most of the time, the communicator used is MPI_COMM_WORLD (in mpi4py is MPI.COMM_WORLD)
  * This is what you could consider the global communicator, which has all the processes within it.
* Operation to share data with another MPI process is called send; operation to get shared data from another MPI process is called receive (often abbrieviated recv)

## Sending and Receiving Data
* MPI point-to-point operations typically involve message passing between two and only two different MPI tasks.
  * one process performs a send and the data is received by a matching receive operation 
* Many different types of send/receive routines
  * synchronous send
  * blocking send/receive
  * non-blocking send/receive
  * buffered send
  * combined send/receive
  * "ready" send
* Any type of send routine can be paired with any type of receive routine
* Data in transit is held in a system buffer, which is managed entirely by the MPI Library.
#### Blocking vs. Non-Blocking Send/Receive
* a blocking send only returns when it is safe to modify send data for reuse
  * doesn't mean data is received, it can still be sitting in the buffer
* a blocking receive only returns when the data is received and is ready to be used
```C
// Blocking Sends in C
MPI_Send( void* data, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm communicator );
MPI_Recv( void* data, int count, MPI_Datatype datatype, int source, int tag, MPI_Comm communicator, MPI_Status* status );
```
```python
# blocking sends in Python
comm.send( obj, dest, tag=0 )
comm.recv( source=MPI.ANY_SOURCE, tag=MPI.ANY_TAG, status=None )
```
* non-blocking sends and receives return alm ost immediately and don't wait for communication to complete.
  * usually used to overlap computation with communication for performance gain
  * need some kind of wait routine to make sure data is ready to be modified
```C
MPI_Isend( void* data, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm, MPI_Request* req );
MPI_Irecv( void* data, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm, MPI_Request* req );
Waitall( int num, MPI_Request *reqs, MPI_Status *stats );
```
```python
comm.isend( rank, partner )
req = comm.irecv( source=partner, tag=tag )
message = req.wait()
```
#### Buffering
* A system buffer are is reserved to hold data in transit
  * buffer is managed entirely by MPI library and is oblique to the programmer
* MPI guarantees that messages will not overtake each other
  * If message 1 is sent before message 2 to the same task, message 1 will always be received before message 2
  * If receive 1 is posted before receive 2, receive 1 will always receive its value before receive 2 
## Collective Communication Routines
Because we can hahahahahaah

## Examples
Make a copy of Google Colab Notebook at **link here**
#### Hello World
Every task prints Hello World

#### Hello Master Task
Every task (besides the master task) sends a message to the master task

#### (Non-Blocking) Ping
Every task sends and receives data from one partner using non-blocking sends/receives 

#### Round Robin
Every task sends data to an adjacent task

#### Monte Carlo
Calculate Pi

#### Expensive Computation
Put expensive computation in its own task and print a counter in the main task.

## Next Steps
#### Parallelize an already existing program/algorithm
A great way to wrap your head around multithreaded program is take some serial code -- either a project you did before or an existing algorithm -- and parallelize it.
Some interesting algorithms to look into:
* Matrix Multiplication (Lots of publications on this)
    * naive, strassen, cannon's algorithm
* Sorting Algorithms
  * Sleep Sort (not at all practical, but kind of funny)
  * Merge Sort (or any recursive sorting algorithms)
#### Do a project
* Some multicore single-board computers
    * Raspberry Pi 3/4 are quad-core processors
    * ARM Cortex-M4 and Cortex-M0
    * *this is a reminder that IEEE members get 15% Digikey Orders*
* Or you can just use your own computer
  * most desktops/laptops are multicore
* Some project ideas
  * idea 1
  * idea 2
  * idea 3
  * idea 4
#### Resources/Cool Reads
* [MPI Tutorial](https://computing.llnl.gov/tutorials/mpi/) (in C and Fortran)
* [mpi4py docs](https://mpi4py.readthedocs.io/en/stable/)

