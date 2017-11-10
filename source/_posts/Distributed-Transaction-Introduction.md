title: "Distributed Transaction Introduction"
date: 2017-11-10
categories:
- 内核
---

***Two Phases Commit, Nested Transaction***

Phase I: voting 

Phase II: make decision and report issue

***Well Known design ideas about distributed system***
- distributed consensus amount a few replica;
- consistent client cache to reduce server load;
- timely notification of update;
- a fimiliar file system interface;

***How to ensure that only one process(replace with worker, server, agent) across of a fleet of servers acts on a resource?***
- ensure only one server can write we need a single entity to act on a resource;
- ensure that one server can perform a particular action;
- ensure that there is a single master that processes all writes(e.g., Google's use of chubby for leader election in GFS, map reduce etc);

***Coordination between distributed system***
- You have to elect a leader across a set of distributed processes(leader election);
- detect the failure(failure dectection);
- change the appropriate group membership and elect a new leader;

***A classic way to solve this problem is to use some form of system that can bring consensus***
- a single server which can act as the source of truth
  - not good idea for obvious reason of availability or durability;
- a system that implements a concensus algorithum like Paxos;
- build a form of lead election on top of a strongly consistent datastore;

***What is Paxos***
Paxos is the gold standard in concensus algorithum which is the concensus protocol designed to reach an agreement across a family of unreliable distributed processes. 

***How can I use Paxos algorithum to solve the problem of coordination?****
- **Build a concensus library**: this is a library that implements Paxos that application can consume.
  - elect a leader to execute a workflow is passed as a transaction into the library;
- **Lock service**: Lock service is a form of concensus service that converts the problem of reaching concensus to handing out locks.
  - Basically a distributed set of processes compete to acquire a lock and whoever gets the lock has the "baton" to execute a workflow.
  - Google: chubby;
  - Zookeeper;
  
***Using a strongly consistent datastore to build lock service***

One of the common tricks we have seen being used in the past is to emulate a lock service behaviour using a strongly consistent datastore.
- Basically a strongly consistent datastore that is replicated, hihgly available and durable can be used to persist the information on who is holding it, for how long etc.
- failure detection issue:
  - Traditional lock service also provides a simple library that the clients can use to heartbeat with the service.
  
Reference: <https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html>
