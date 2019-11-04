# depanalyze
Distributed Analysis Engine

A simple implementation micro-batching map-reduce system to analyze live data.


## Implementation
The system consists of three main types of nodes;
* *coordinater* - Master node that coordinates all the batches, moves the system between different logical timestamps/epoch (each batch is a different epoch). Master-election algorithm; unknown.
* *ingestor* - Worker node that ingests the data, and aggregates it locally. If the node goes down 
* *aggregator* - Post-processor node that aggregates the local snapshots of individual ingestor nodes for each epoch. The coordinator can act as the aggregator. 


## Limitations
* The main focus of this system is high-availability. This means that if an ingestor node goes down, the data is lost for that epoch.
* Loadbalancing is not performed by the system itself for ingest.
