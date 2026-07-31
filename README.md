# Journey Of Learning PySpark

[Tutorials (Project Based Learning)](https://towardsdatascience.com/pyspark-for-beginners-mastering-the-basics/)
[FreeCodeCamp: PySpark Tutorial](https://www.youtube.com/watch?v=_C8kWso4ne4)

## What is PySpark?
- Python api for Apache spark
    - computing frame work that can efficiently process very large amount of data
    - achieves this by spreading out the computations across multiple machines (clusters)
- Simplifies the complexity of distributed systems
    - you can still write code that is similar to python, without having to manage threads memory or network communication(between clusters)
    - Spark allows the user to focus on how to use the data
- Spark does not need to be ran on an actual cluster of compute
    - instead when pyspark is run locally, it simulates how a cluster works using multiple cores
    - the locally developed code can then be deployed to an actual cluster with little to no changes

## How Pyspark Works
### 1. Clusters
- group of servers that are networked together and can help each other
- there is one machine that is the driver or coordinator(orchestrator): 
    - that tells others what tasks to complete
- the rest of the machines are called workers(executors) that perform computations on chunks of data
    - once a worker is done with their work, they tell the driver
    - where the driver can assign them more work
- as datasets grow in size, more workers can be added to the cluster, reducing the runtime without changes to the code

### 2. Spark dataframe
DataFrame API: main way data is used with spark
- table of data made of rows and columns
    - similar to tables in databases or data frames
- using spark data frames you can easily:
    - filter rows
    - select columns
    - grouping data
    - joining tables
    - calculating summaries
- spark automatically determines the most efficient way to run the data frame operation
    - and executes them in parallel across the cluster
- spark handles splitting the data, coordination, and recovering when something goes wrong
- spark data frames can handle very large datasets, even if they can't fit on a single computer while still being simple with a familiar interface

### 3. Lazy vs Eager Evaluation
Eager evaluation(used by most python data libraries): 
- when an operation is ran, it is instantly executed, with the next one afterwards
    - each operation runs as soon as it is called, can be inefficient for larger datasets

Lazy evaluation(PySpark):
- instead of executing data transformations, it first builds an optimized plan and only runs when the action is triggered
    - waits until all operations are called before executing anything
    - saves processing power in case some steps are made redundant 

#### Example: 10-million-record dataframe
- Process:
    - *add* a new column called `X`
    - *filter* the data where half of the records are removed
    - *perform aggregation* on the data, so `X` has the max value of another value in the row 
    - *print* out the row with the max value of `X`

- Eager evaluation:
    - *adds* 10 million new rows by creating a new version of the old dataset
    - *filters* all 10 million rows, with 5 million deletions, has to write another 5 million rows data set
    - *calculates* the max value for each row, and updates the 5 million rows data set
    - *prints* the top row

This wastes a lot of time and compute resources since the 10 million added rows are cut in half in the next step, so halve the compute power and time was just wasted

- Lazy evaluation(PySpark):
Doesn't do any work when the steps are defined, instead builds a Logical Plan(DAG - Directed Acyclic Graph) that does the work
    - when the final step of actually calling for the row with the max value of `X`
    - Spark sees the filter, and instead of adding `X` to 10 million rows, it moves the filtering to the beginning
    - adds only `X` and aggregates the remaining 5 million rows
    - ends up not processing 5 million records, saving 50% of the compute and time