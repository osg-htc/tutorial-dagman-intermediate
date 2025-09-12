# Retrying Failed Nodes

Now you may be wondering: can't DAGMan just retry a failed node,
instead of waiting for me to resubmit the rescue DAG?

If you know that a node will occasionally fail through no fault of your own, 
and simply want DAGMan to try that node again, then you can tell DAGMan to
automatically retry that node again. To do so, define a `RETRY` statement :

```
RETRY your_node N
```

where you should replace `your_node` with the name of the 
node that experiences intermittent failures and `N` with the number of times
that you want DAGMan to automatically retry that node. 

If a node with a `RETRY` defined fails for any reason,
DAGMan will automatically retry that node, up to `N` times.

You can also apply the `RETRY` statement to all nodes in the DAG with

```
RETRY ALL_NODES N
```

where you replace `N` with the number of automatic retries.

> Note that the number of automatic retries should generally be kept low
> (ideally at 3 or less).
> If your job repeatedly fails, it is better to troubleshoot the underlying cause
> of the failure instead of increasing the value of the `RETRY` statement.

## Exercise

First, examine the contents of `retry.dag`. 

* What are the nodes in the DAG?
* How is the `RETRY` statement used?

Next, examine the contents of `fragile.sub`. 

* What is the executable?
* What arguments are passed to the executable?
* What files will be generated when this job is submitted?

In this example, the `retry.dag` has only one node (`fragile`) that runs
`fragile.sh`. The job for the `fragile` node passes the current number of retries 
(`$(RETRY)` in the `.sub` file) to the executable `fragile.sh`. The executable will 
only exit successfully if the number `2` is passed as an input. 

Now run `retry.dag` without modification:

```
$ condor_submit_dag retry.dag
```

The DAG should run to completion. 

Examine the output files of the `fragile` job. Can you tell how many times
the job ran? Confirm your answer by examining the contents of
`retry.dag.dagman.out`.

In practice, if you knew for sure that the job would fail the first two times,
you should instead adjust your code so that doesn't happen!
More likely, though, is that your job has some small chance of failing,
in which case the `RETRY` statement will come in handy.

* For more information on the `RETRY` command, see 
  [DAGMan Node Retries Documentation](https://htcondor.readthedocs.io/en/latest/automated-workflows/node-pass-or-fail.html#retrying-failed-nodes).

