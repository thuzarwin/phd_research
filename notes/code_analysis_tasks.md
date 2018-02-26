Mon Feb 26 13:25:22 PST 2018

Following meeting with Duncan on Friday I'm writing down questions to
answer with code analysis.

Duncan's use case: people come him with code to make faster. They often
give him the whole data set which may take days to run. It would be neat to
look at the code and then artificially create a data set that is similar to
the large one. This must be similar to generated tests that aim to explore
every branch.

## Outcomes

What are the high level goals?

__Speed__

Take code that runs in 20 minutes and make it run in 10 minutes. There are
many ways to do this: detecting programming mistakes, compilation,
parallelism, dropping in more efficient implementations, etc.

__Memory__

Take code that requires 5 GB, and run it in a way that requires only 2 GB,
or even just 10 MB. This may be possible with streaming computations.

__Data Abstraction__

Take code that runs on a local data frame and evaluate it efficiently on
any tabular data source. The data could be a table in a database, a bunch
of files in a local directory, or a file on Amazon's S3.


## High level Questions / Tasks

Below "run" means evaluate a particular piece of code on a particular data
set.

__Cloud Computing__

If we pay for computation and storage then we can do things based on cost.
We have access to a potentially limitless amount of computing power.
Things we might like to do when evaluating a particular piece of code on a
particular data set:

- Minimize cost
- Minimize time
- Minimize cost such that the computation takes no more than `h` hours
- Minimize time such that the computation costs no more than `d` dollars


## Language independent semantics

The following code performs the same semantic operation:

```{R}
subset(flights, month == 1 & day == 1)      # Base R
flights[month == 1 & day == 1, ]            # data.table
filter(flights, month == 1 & day == 1)      # dplyr
```

Because the semantics are the same it should be possible to represent these
semantics in a way that's independent of the language. Indeed, if the rows
are unique the code above is
equivalent to [selection in relational
algebra](https://en.wikipedia.org/wiki/Selection_(relational_algebra).

The more we know about the semantics of the desired operation the more we
can use this knowledge to evaluate the code in a different way. 

## More Technical

__Reorder code in an efficient way__


- What columns to we need?
- What subsets of the data do we actually need?

If these are known early then we can filter the data early, even at the
source. This saves memory and time for intermediate computations.