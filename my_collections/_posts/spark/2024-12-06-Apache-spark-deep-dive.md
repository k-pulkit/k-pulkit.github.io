---
title: "Apache Spark Demystified"
categories:
    - dataengineering
tags:
    - in-progress
    - spark
    - rdd
    - partitions
    - coreconcepts
    - dataengineering
header: 
  teaser: /assets/images/collections/common/spark-articles.png
excerpt: Understanding the internals of Apache Spark in-depth
---

## Introduction
In this blog post, I want to discuss the internals of Apache Spark. I will discuss the Spark SQL API and how it works together with Spark Core to allow processing of huge amounts of data. A lot of the my understanding is owed to the amazaing spark summit videos. If you have time, do check them out on youtube.

## Spark Ecosystem
The best way to understand Spark is to understand the spark ecosystem as a whole. Spark has been in development for more than a decade and a lot has changed since its inception. Below diagram explores the different components of the Spark Ecosystem.

<figure style="max-width: 550px" class="align-center">
  <div class="image-wrapper">
    <img src="/assets/images/collections/posts/2024-04-06-Spark-deepdive/ecosystem.png" alt="">
    
  </div>
  <figcaption>Apache Spark Building Blocks</figcaption>
</figure>

To understand the above, let's say you are writing a spark application:
1. __APIs__ - You decide to write your spark application using Scala, which is one of the languages that enables you to write spark code. Likewise, you can write your Spark programs in Python, Java, R. 
2. __Choosing libraries__ - After deciding the language (_API_), you decide to use high level DataFrames instead of low-level RDDs to express your data processing logic. DataFrames are offered as part of the Spark-SQL layer that allows you to write declarative code (of what needs to be created than how it needs to be created)
3. __Executing code__ - When you execute your code, it is converted into low level representations that are interpreted by Spark Core APIs, which is responsible for creating tasks, keeping track of running tasks, collecting results etc.
4. __Resource Manager__ - Spark being the distributed application makes use of external services for resource management. There are different backends available for Apache spark like Yarn, Mesos, Kubernetes and Spark's own Standalone scheduler.
5. __Storage__ - It is where data lives. Spark has seperation of concerns where compute has been decoupled from storage and so you can use spark to process data in variety of data sources like HDFS, HBase, Trino, S3, Cassandra, Cloud Storage etc.

## Spark Core
Spark Core, like the name suggests forms the core of Apache Spark. It implements a distributed data structure known as RDD (Resilient Distributed Dataset). When you think of RDDs think of them as objects that can iterate over your data and transform it. To help you visualize better, we will see how RDD is represented in Spark Core codebase.

PATH : `apache/spark/core/src/main/scala/org/apache/spark/rdd/RDD.scala`
~~~scala
 abstract class RDD[T: ClassTag](
    @transient private var _sc: SparkContext,
    @transient private var deps: Seq[Dependency[_]]
  ) extends Serializable {

    def compute 

  }
~~~















