# scratch_notes
A place to note down my collected knowledge

## Garbage collection and reason why don't assign too many cores for a spark executor
Garbage collection (GC) is a form of automatic memory management. It's a process that attempts to reclaim garbage, or memory occupied by objects that are no longer in use by the program. In the context of Spark and JVM (Java Virtual Machine), these "objects" are pieces of data in memory that your application has created during its execution but are not needed anymore.

GC operates in a "stop-the-world" manner. This means that while garbage collection is happening, your application stops executing. GC can take some time, depending on the number of objects to deallocate and the total size of your memory pool. The larger the memory pool, the more time it takes to scan and collect the garbage, which can lead to substantial pauses in the execution of your application.

Now, let's map it to the scenario you provided:

In a Spark application, an executor is a distributed agent responsible for executing tasks. When an executor runs a Spark task, it creates objects in memory. Over time, some of these objects become obsolete or unused (garbage) and need to be cleaned up. If an executor is set up with a large memory pool (like 64GB+), it means there can be a large number of objects (and potentially a lot of garbage).

When GC kicks in to clean up these unused objects, it needs to scan the entire memory pool. If the memory pool is very large (like in this case), the GC process can take a long time, causing the executor to pause its processing for the duration. These long GC pauses slow down the overall execution of your Spark job, leading to inefficient processing and longer completion times.

This is why it's often a good practice to balance the number of cores and memory per executor in such a way that you have a larger number of smaller executors (in terms of memory) rather than a small number of large executors. This way, GC operates more frequently but on smaller memory pools, causing shorter GC pauses and making your Spark job run more smoothly.

Let's take a simple example to understand this:

Consider a library (executor) with a large number of books (objects). The librarian (Garbage Collector) has the task to find all the old books (garbage) which are not borrowed (used) anymore. If the library is very big (large memory pool), it will take the librarian a lot of time to scan all the books, leading to the library being closed (stop-the-world pause) for a long period. On the other hand, if there are multiple small libraries, the librarian can finish the task quicker for each library, reducing the overall time the libraries are closed. This is analogous to how GC works in Spark executors.
