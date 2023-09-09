# emacs-async-scheduler
A scheduler for async jobs in emacs that allocates tasks to a fixed number of separate emacs processes

Careless use of asynchronous emacs processes can launch an unbounded number of processes.  This package
provides a generic facility for managing arbitrary sets of asynchronous jobs allocated among a fixed 
number of processes.  

A *job* is a structure that tracks the function to run.  A job optionally specifies callbacks for 
scheduler events:
  * activation and deactivation of the owning job collection
  * the spawning of the process to run the job
  * the end of the process that ran the job
  * the job being aborted, possibly prior to running

A *job collection* is a set of jobs managed together.  Each collection may be assigned a priority.
The job collection implements a user-specified strategy for determining the next job to be scheduled
from the collection at any given time.
The collection may define handlers for the following events:
  * activation of the collection, when the collection is added to the set from which the next scheduled
job will be drawn    
  * deactivation of the collection, when the collection is removed from the active set
  * when the collection becomes empty after having contained some non-zero number of jobs that have run
    since the creation of the collection or the last empty event


A scheduler:
  * maintains the set of currently running jobs, bounded by some constant
  * maintains the set of job collections, partitioned into:
    * the active set from which jobs may be drawn for scheduling
    * the inactive set, for which scheduling has been "paused"
  * implements a specified scheduling strategy for determining which collection
  * notifies jobs and collections of scheduling events

Schedulers use a user-specified procedure for running events.  This allows job collections to use a subordinate
scheduler to coordinate subcollections of jobs.  One use of this facility is to allow a job collection to limit
the number of simultaneous jobs it runs to some number smaller than that available to the system.  In particular,
if some emacs facility wishes to sequentially execute a set of tasks, it may set up a job collection with a
subordinate scheduler having only one slot, containing a single job collection that implements a queue strategy.


