# Reusing a Single Submit File for Multiple Nodes

You may be wondering: do I have to use a unique submit file for *every* node?
The answer is no!

Specifically, you can use the `VARS` command to define the variables for 
a particular node, and these variables can be read by the submit file.
The syntax for this looks like:

```
VARS <Node Name> sample="Foo" My.SampleAd="Bar"
```

where first the node is specified (`<Node Name>`) followed by a list of 
`key="value"` pairs to define the variables.

Then, the `key` definition can be referenced in submit file using the typical submit variable syntax, i.e. `$(key)`,
which the submit file will evaluate to `"value"` for that submission.
You can also define a custom job attribute by prefixing your `key` with `My.`, which means that `"value"` will be
available in the job's ClassAds. 

You can also define variables to be used by all nodes with 

```
VARS ALL_NODES <list of key-value pairs>
```

Here "ALL_NODES" is a reserved name that allows you to apply a declaration to all
of the DAGMan nodes.

> For a regular HTCondor job, you can define a variable for use in job submission
> by adding it to the submit file and then calling it with the `$()` syntax.
> For example, 
>
> ```
> sample = Foo
> output = $(sample).out
> ```
>
> will save the standard output file as `Foo.out`. Alternatively, instead of defining `sample` 
> within the submit file, you can define the variable at submission time with
>
> ```
> condor_submit my_submit_file.sub sample="Foo"
> ```
>
> This is effectively what the `VARS` command in the input `.dag` is doing.

## Exercise

In this example, you willl use the `diamond.dag` to submit multiple jobs that each
print out their own message. Since each node is doing the same job but with
a different message, you can avoid writing many submit files by using a single
submit file that takes the message as an argument.

Examine the contents of `message.sub`.

* What variables are being used in the submit file?
* Which variables do you need to define?
* Which variables are referencing built-in submit variables?

Next, example the contents of `diamond.dag`.
* What submit file(s) are the nodes using?
* How will the `VARS` lines change the message for each node?

Run `diamond.dag` without modification:

```
$ condor_submit_dag diamond.dag
```

The DAG should complete successfully on the first try. Examine the output
files in `./output_messages`. Were your predictions of messages for each node correct?

* For more information on the `VARS` command, see the 
  [DAGMan VARS Documentation](https://htcondor.readthedocs.io/en/latest/automated-workflows/dagman-vars.html).

