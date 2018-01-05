# DataPipes by Example

## What is DataPipes?
DataPipes is a lightweight cross-platform app specifically built to orchestrate the flow of data between systems on commodity hardware, which is becoming ever more vital in today’s modern data architecture. This includes the ability to interact with multiple data sources, such as collecting structured or unstructured data from systems that use a variety of data extraction procedures and protocols as well as loading data into disparate systems. DataPipes captures additional activity metrics and metadata during execution to provide lineage and visibility over the flow of data. A common application of DataPipes is to enable the flow of data from on-premise systems and/or cloud services to a data lake repository such as Actio’s FlowHUB. DataPipes can then subsequently be used to cleanse, aggregate, transform and load the data into disparate systems to support the intended business use case.

## How does DataPipes work?
DataPipes is written in Scala and runs on the JVM, allowing for it to be deployed on Windows, Linux and macOS systems. PipeScript is a human readable DSL (domain specific language) that captures how to orchestrate the flow of data between systems. PipeScript can be saved in [HOCON](https://github.com/lightbend/config) format. DataPipes interprets and executes PipeScript which can be read from your local file system or retrieved  via an API call to get up-to-date instructions.

## DataPipes Concepts
When using DataPipes there are a few fundamental concepts that will help anyone using it for the first time. These include DOMs, DataSets, Tasks, Pipelines and Events.

### DOMs
DOMs are immutable objects that are generated by Tasks and flow through Pipes. They store data the Task has extracted and/or processed and any events that have occurred such as exceptions. Each DOM generated by a Task is appended to a single global DOM, so that Tasks potentially have access to DOMs produced by other Tasks. The DOM can be visualised as follows:

<!--[note: DOMs are immutable objects that flow through Pipes and Tasks {bg:cornsilk}]

[DOM]++-successful/failed>[DataSet]
[DOM]++-events *>[Events]
[DOM]++-child *>[DOM]-->

![alt text](http://yuml.me/diagram/scruffy/class/[note:%20DOMs%20are%20immutable%20objects%20that%20flow%20through%20Pipes%20and%20Tasks%20{bg:cornsilk}],%20,%20[DOM]++-successful%2Ffailed>[DataSet],%20[DOM]++-events%20*>[Events],%20[DOM]++-child%20*>[DOM])

### DataSets
DataSets are hierarchical data structures used by DataPipes internally. Elements of the data structure can be accessed using expressions within tasks. DataSets can be defined by the following data types: 

<!--
[note: DataSets contain typed hierarchical data{bg:cornsilk}]

[<<DataSet>>]^[String]
[<<DataSet>>]^[Date]
[<<DataSet>>]^[Numeric]
[<<DataSet>>]^[Boolean]
[<<DataSet>>]^[Record]
[<<DataSet>>]^[Empty]
[<<DataSet>>]^[Array]
[Record]<>-fields*[<<DataSet>>]
[Array]<>-items*[<<DataSet>>]
-->

![alt text](http://yuml.me/diagram/scruffy;dir:TB/class/[<<DataSet>>]^[Record],%20[<<DataSet>>]^[Array],%20[Record]++-%20%20%20%20%20%20fields%20*[<<DataSet>>],%20[Array]++-%20%20%20%20%20%20items%20*[<<DataSet>>],%20[<<DataSet>>]^[String;Date;Numeric;Boolean;Empty])

![alt text](http://yuml.me/97665715.png)

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

### Events
Tasks may generate Events, such as when the task started and completed, or if any exceptions occurred during processing. They essentially capture activity throughout the life cycle of a Task. This also includes capturing metrics. Events can be visualised as follows:

![alt text](http://yuml.me/diagram/scruffy/class/[Event|time:%20timestamp]<>-data>[DataSet],%20[Event]-created_by%201%20%20%20%20%20>[Task])

### Tasks
Tasks perform some operation, usually with a side-effect, on an incoming DOM to produce one or more outgoing DOMs.

![alt text](http://yuml.me/diagram/scruffy/activity/(start)->(Task)->(end))

Tasks at a high level can be of either extract, transform or load type:

* Extractors - interact with DataSources to extract a stream of DOMs
* Transformers - transform the incoming DOMs
* Loaders - interact with DataSources to load data into them

Tasks go through a life cycle in order of:

1. Initialise
2. Process incoming DOMs, optionally generate further DOMs
3. Finalise

### Pipelines
Pipes coordinate and direct the flow of data between tasks and other pipes. They can for example connect extractors and transforms together and similarly transforms to loaders to create a simple ETL pipeline.

![alt text](http://yuml.me/diagram/scruffy/activity/(start)->(Extract)->(Transform)->(Load)->(end))

## Command Line Interface
The command line options for DataPipes are as follows:

```shell
$ datapipes -c <filename> [options]... [vmargs]...

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
$ datapipes -c ./examples/helloworld.conf
```

Once DataPipes completes, view the file output.txt