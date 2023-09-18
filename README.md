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

A *workstream* is a set of jobs managed together.  Each collection may be assigned a priority.
The workstream implements a user-specified strategy for determining the next job to be scheduled
from the workstream at any given time.
The workstream may define handlers for the following events:
  * activation of the workstream, when the workstream is added to the set from which the next scheduled
job will be drawn
  * deactivation of the workstream, when the workstream is removed from the active set
  * when the workstream becomes empty after having contained some non-zero number of jobs that have run
    since the creation of the workstream or the last empty event


A scheduler:
  * maintains the set of currently running jobs, bounded by some constant
  * maintains the set of workstreams, partitioned into:
    * the active set from which jobs may be drawn for scheduling
    * the inactive set, for which scheduling has been "paused"
  * implements a specified scheduling strategy for determining which
    workstream the next job will be chosen from
  * notifies jobs and collections of scheduling events

Schedulers use a user-specified procedure for running events.  This allows a workstream to use a subordinate
scheduler to coordinate subworkstreams.  One use of this facility is to allow a workstream to limit
the number of simultaneous jobs it runs to some number smaller than that available to the system.  In particular,
if some emacs facility wishes to sequentially execute a set of tasks, it may set up a workstream with a
subordinate scheduler having only one slot, containing a single subworkstream that implements a queue strategy.


