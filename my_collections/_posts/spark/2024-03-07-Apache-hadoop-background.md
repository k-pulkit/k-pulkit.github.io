---
title: "Apache Hadoop - Background"
categories:
    - dataengineering
tags:
    - hadoop
    - learn
    - history
    - dataengineering
header: 
  teaser: /assets/images/collections/pages/spark-articles.png
excerpt: In this piece of text, we focus on understanding ...
---

> Technologies come and go, but data is here to grow!

## Introduction
Data is at the core of every application, driving decision-making and improving user experiences. Over time, organizations have continually invested in better ways to collect, analyze, and utilize data. This evolution has led to the development of various technologies, each building upon the successes and shortcomings of its predecessors.

* Excel
* Oracle SQL, Microsoft SQL Server
* Hadoop
* Spark

Each evolution aimed to solve a specific outcoming of previous generation of solutions.

## The Big Data Problem
![image-right]({{ site.url }}{{ site.baseurl }}/assets/images/collections/pages/spark-articles-big-data-problems.png){: .align-left}
With the advent of the internet and mobile devices, the volume and variety of data exploded. Data came in various formats, including json, xml, doc, mp4, avro, among others. This increase in data volume and velocity posed a significant challenge for traditional data processing systems, such as relational database management systems (RDBMS).

These together, <mark>three Vs</mark>, contitute what we call the big data problem. 

Event though RDBMS have evolved over many decades to meet the every changing industry demands, but they have not been designed to handle big data problems.

*[RDBMS]: Relational Database Management Systems

### Distributed Computing
To address the scalability, availability, and cost challenges posed by big data, distributed computing systems emerged. These systems, like Apache Hadoop and later Apache Spark, utilize clusters of machines to distribute and process data in parallel. This approach allows for efficient processing of large volumes of data while ensuring fault tolerance and scalability.

Apache Spark, in particular, has gained popularity for its speed and ease of use, becoming a preferred choice for many data engineers and analysts. By leveraging in-memory processing and a rich set of APIs, Spark has become a powerful tool for processing and analyzing large-scale data.

Hadoop like sytem have the following features - 
1. Scalability: _If the demand rises, say now we have double the amount of data, how easy is it for us to store, process and serve the same._
2. High Availability: _Is the system fault tolerant, what happens if there is a hardware failure, software, network or power failure! Does it keep working._
3. Cost: _Is the solution expensive, how cost effective is it to scala or to increase availability?_

## Basic background: Hadoop
In simplicity, hadoop consists of 3 main layers: <br />
![image-right]({{ site.url }}{{ site.baseurl }}/assets/images/collections/pages/spark-articles-hadoop-1.png){: .align-right}
1. YARN - Cluster operating system, responsible for managing resources of the cluster. 
2. HDFS - Forms the storage layer. Data stored is split, replicated and distributed across cluster making it reliable and fault tolerant.
3. Map / Reduce - Allows users to write Java programs to process the data stored on hadoop.

> Just like windows makes laptop hardware usable, cluster os allows to run applications like spark on a cluster

*[cluster]: Group of physical or virtual servers that make up the distributed cluster
*[HDFS]: hadoop file system

The point is hadoop was designed to allow working with cluster of computers easier. Today's distributed processing engines are so powerful that they totally abstract away the complexity and so we can pretty much write programs in the same as we would do for a single computer.

### Hadoop ecosystem
The core hadoop consist of Yarn, HDFS and Map/Reduce. Over years the innovate big data community has developed several other tools that are designed to work in hadoop and making it better. Some examples are:
* Hive: Data warehousing and SQL-like query language for Hadoop.
* Hbase: Distributed, scalable, NoSQL database for Hadoop.
* Sqoop: Tool for transferring data between Hadoop and structured databases.
* Oozie: Workflow scheduler for managing Hadoop jobs.

### Hadoop architecture
To understand the core architecture of Hadoop, we have to understand 3 compoenents which are HDFS (storage layer), YARN (os layer) and Map-Reduce (compute framework). The key thing to remember is that hadoop is written in java, so each component, lets say HDFS, will have it daemon java process run on all machines to manage the distributed storage. For explanation purpose, we will assume we have a 5 node cluster.

### HDFS (Hadoop File System)
Hadoop File System is the hadoop solution to distributed storage. HDFS is open-source and it got its inspiiration from Google's white paper on GFS or Google File System. 

The below diagram shows the basic architecture of HDFS. In this diagram we can see a cluster of 5 nodes, one master and 4 slaves. This is known as the master-slave architecture, you can think as master as the coordiator and the slaves as workers following orders from master.

<figure style="max-width: 590px" class="align-center">
  <div class="image-wrapper">
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/collections/pages/spark-article-hdfs-arch.png" alt="">
  </div>
  <figcaption>HDFS architecture</figcaption>
</figure> 

HDFS consists of 2 type of services (which as java processes running on nodes under the hood ). These services are:
1. Name Node (runs on master)
2. Data Node (runs on workers)

#### Name node
Name node runs on the master node and is reponsible for book-keeping. Whenever you read or write data to HDFS, name node is going to store and track meta data about the data stored on data nodes. So, think of name node as the librarian and data-node as the shelves where actual information is stored away.

#### Data node
Data node is where the actual data being written to HDFS is stored. When you want to store a big file say 1GB file on HDFS, it is not stored as a single file. The file is broken down into what is called blocks, and these blocks are stored in data nodes. The blocks stored are also replicated to ease data recovery if one of the nodes fail. This is controlled by replication factor.

Default block size in HDFS is 128MB
{: .notice--info}

#### What exactly is stored in name node?
As mentioned before, name node stores the metadata about original data. To make the idea concrete, consider an example of file metadata stored by name node.

```bash
- NAMENODE
    FILE - example.txt
    DIRECTORY - /data
    FILE_SIZE - 1GB
    FILE BLOCKS - 
      - BLOCK1
        BLOCK_ID - e3245243
        BLOCK_LOCATION - DATANODE1/disk1/data/rufrebv34343223.txt
      ...
      - BLOCKN
        BLOCK_ID - e3245243
        BLOCK_LOCATION - DATANODE1/disk1/data/rufrebv34343223.txt
```
In case you have a hadoop cluster up and running, you can fetch above info by running `hdfs fsck hdfs://file -location -blocks -files`.

#### How reading from HDFS works?
Say you made a request to hdfs to read contents of a file using command `hdfs dfs -cat hdfs://<filepath>`. Then the following will happen:
1. HDFS client on your machine connects to namenode
2. Name node responds back with metadata about where different blocks are stored
3. HDFS client connects to the data nodes directly to get the respective blocks
4. HDFS client uses metadata about file to assemble the blocks in right order and create the file

#### How writing to HDFS works?
Say you decide to write a file to HDFS, so you issue command `hdfs dfs -copyFromLocal localpath hdfspath`. When you issue this command:
1. Your HDFS client connects to namenode for the write operation
2. Name node responds back redirections to data nodes
3. HDFS client splits the file into blocks and sends write requests to data nodes
4. Once the client receive acknowledgment that all blocks were written it passes the info the name node
5. Name node persists the meta data for file usage

### Common HDFS commands
Even though, your data is stored in a distributed fashion, working with HDFS is as easy as interacting with your local file system. Some useful commands are:
* List files - `hdfs dfs -ls <path>`
* Copy files - `hdfs dfs -copyFromLocal <local-src> <hdfs-dest>`
* Change file permissions - `hdfs dfs chmod 777 <path>`
* Rename files - `hdfs dfs -mv <src> <dest>`
* List contents - `hdfs dfs -cat <file>`
* Get disk usage - `hdfs dfs -du -s -h /user/test`

## Hadoop YARN
In the previous sections, we talked about HDFS which forms the storage layer of hadoop ecosystem. Once you have the data in place, you may want to run some computation on it and make use of it. This is where YARN comes into picture. Just like HDFS has 2 kind of java processes (namenode and datanode), YARN follows a similar master slave architecture. The master and slave components of YARN are - 
1. Resource Manager
2. Node Manager










