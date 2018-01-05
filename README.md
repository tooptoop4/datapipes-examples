# DataPipes by Example

## What is DataPipes?
DataPipes is a lightweight cross-platform app specifically built to orchestrate the flow of data between systems on commodity hardware, which is becoming ever more vital in today’s modern data architecture. This includes the ability to interact with multiple data sources, such as collecting structured or unstructured data from systems that use a variety of data extraction procedures and protocols as well as loading data into disparate systems. DataPipes captures additional activity metrics and metadata during execution to provide lineage and visibility over the flow of data. A common application of DataPipes is to enable the flow of data from on-premise systems and/or cloud services to a data lake repository such as Actio’s FlowHUB. DataPipes can then subsequently be used to cleanse, aggregate, transform and load the data into disparate systems to support the intended business use case.

## How does DataPipes work?
DataPipes is written in Scala and runs on the JVM, allowing for it to be deployed on Windows, Linux and macOS systems. PipeScript is a human readable DSL (domain specific language) that captures how to orchestrate the flow of data between systems. DataPipes interprets and executes PipeScript. It can read PipeScript instances from your local file system or retrieve up-to-date instructions via an API call.

## DataPipes Concepts
When using DataPipes there are a few fundamental concepts that will help anyone using it for the first time. These include DataSets, Tasks, Pipelines and Events.

### DOMs
DOMs are immutable objects that are generated by Tasks and flow through Pipes. They store data the Task has extracted and/or processed and any events that have occurred such as exceptions. A task may generate successfull data as well as unsuccessfull data. The DOM can be visualised as follows:

![alt text](http://yuml.me/diagram/scruffy/class/edit/[note: DOMs flow through Pipes and Tasks {bg:cornsilk}], , [DOM]++-successful%2Ffailed>[DataSet], [DOM]++-events *>[Events], [DOM]++-child *>[DOM])

### DataSets
DataSets are hierarchical data structures used by DataPipes internally. Elements of the data structure can be accessed using expressions within tasks. DataSets can be defined by the following data types: 

* String, Numeric, Date, Boolean, Record, Array or Empty

These data structures can be thought of as json structures, such as the following:

```json
{
  "person": {
    "firstName": "John",
    "lastName": "Smith",
    "address": {
      "addr1": "George St.",
      "addr2": "Sydney",
      "postcode": 2000
    },
    "phoneNumbers": [
      "98765432",
      "87654321",
      "76543212"
    ]
  }
}
```

### Tasks
Tasks perform some operation, usually with a side-effect, on an incoming DataSet to produce an outgoing DataSet.

![alt text](http://yuml.me/diagram/scruffy/activity/(start)->(Task)->(end))

Tasks at a high level can be of either extract, transform or load type:

* Extractors - query data sources to create a stream of data
* Transformers - transform the incoming stream of data
* Loaders - push the incoming stream of data to data sources


### Pipelines
Pipes coordinate and direct the flow of data between tasks and other pipes. They can for example connect extractors and transforms together and similarly transforms to loaders to create a simple ETL pipeline.

![alt text](http://yuml.me/diagram/scruffy/activity/(start)->(Extract)->(Transform)->(Load)->(end))

### Events


## Command Line Interface
The command line options for DataPipes are as follows:

```shell
$ ./run -c <filename> [options]... [vmargs]...

```

options:
* **-p, --pipe**
    This is used to specify which pipe to execute in the configuration file. The default is the startup pipe specified in the configuration file.
* **-s, --service**
    The parameter will run DataPipes as a long running service, listening on a predefined port specified in the configuration file.

vmargs:
* **-Dkey=val**
    Command-line arguments used to substitue values in the configuration file.


## Hello World

To run the Hello World example, run the following command:

```shell
$ ./run -c ./examples/helloworld.conf
```

Once DataPipes completes, view the file output.txt