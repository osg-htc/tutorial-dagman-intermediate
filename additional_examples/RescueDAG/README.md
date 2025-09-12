# Dealing with a Failed DAG

You may be wondering: what happens if a node fails as part of
a DAG workflow that DAGMan is managing?

When a node fails, DAGMan will keep doing as much
work as it possibly can before exiting. DAGMan identifies the 
nodes that do not depend on the failed node(s), and will continue 
managing those nodes as normal. When there is no more work to do,
DAGMan will write a rescue file and exit.

The DAGMan rescue file will be named as:

```
<DAG File>.rescue###
```

Where `###` is the rescue file number that increments
with each new rescue. The first such file will use the number 001.

When re-submitting the DAGMan job to HTCondor, DAGMan will read the newest
rescue file (the one with the highest `###` value) and mark all `DONE` nodes as
DONE. These nodes are skipped and DAGMan will start by submitting the work 
remaining work after its previous attempt (i.e. the failed nodes 
and descendants).

You can change the automatic rescue behavior:

1. **Use an older rescue file** by using the `-DoRescueFrom ###` flag, where the
   number matches that of the rescue file you want to use.
1. **Don't use the rescue file** by using the `-force` option.

## Bad DAG Example

This directory contains another Diamond DAG example, but this time
one of the jobs submitted will fail.
Examine the contents of the file and determine the structure of the DAG
and the submit files corresponding to each node, but don't try fixing anything just yet.

Run the DAG without modification:

```
$ condor_submit_dag diamond.dag
```

The DAG will fail and produce the rescue file `diamond.dag.rescue001`.
Inside this rescue file are some comments about the file such as
when it was written, what nodes have failed, and a list of nodes
marked as `DONE`

```
$ cat diamond.dag.rescue001
  ...
  TOP DONE
  LEFT DONE
```

In this example the RIGHT node has failed for some reason.
This is reported in both the rescue file and the `<DAG File>.dagman.out` log file.

The RIGHT node's working directory `right` should provide more information on what went wrong.
If you look at the job's standard error (`.err`) file, it states:

```
ls: invalid option -- z
```

If you look at the `ls.sub` file in the `right` sub-directory, you'll see 
the arguments line is passing an invalid `z` flag as part of `-lz`. 

Change the argument from `-lz` to `-la` in `right/ls.sub` and then
resubmit the DAG workflow. DAGMan should start back up but skip the
DONE nodes `TOP` and `LEFT` and proceed directly to the `RIGHT` node, 
followed by the `BOTTOM` node, and finish successfully.

* For more information, see the HTCondor documentation: 
  [Rescue from a Failed DAG](https://htcondor.readthedocs.io/en/latest/automated-workflows/dagman-resubmit-failed.html#the-rescue-dag).

