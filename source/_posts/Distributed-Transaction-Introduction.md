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
