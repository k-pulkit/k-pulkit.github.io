---
title: "Exploding Spark Event Logs on AWS EMR"
categories:
    - aws
    - dataengineering
header: 
  teaser: /assets/images/collections/common/spark-articles.png
tags:
    - aws
    - emr
    - spark
    - streaming
    - dataengineering
excerpt: How to reduce size of spark event logs on EMR for streaming applications.
---

## Understanding the problem
If you're running Apache Spark on AWS EMR, you might have run into a nasty surprise: compacted event logs that keep ballooning in size, even when they're supposed to be shrinking.  We recently tackled this issue, and the culprit turned out to be a sneaky pair of custom Spark listeners. In this post, we'll break down why this happens and how we solved it with a custom filter.

[^EMR]: Elastic Map Reduce

For streaming spark applications, you can turn on eventlogs that help to recreate spark UI when the application fails or completes
{: .notice--info}

## The Problem: Compacted Event Logs Gone Wild

While monitoring our production Spark jobs, we noticed that the compacted event logs for several streaming jobs were growing at an alarming rate. Compaction is meant to reduce log size, but even after compaction, the overall size and number of logs kept creeping up.

Diving into the event logs, we found that events related to two specific classes were piling up:

* `org.apache.spark.sql.execution.ui.SparkListenerEffectiveSQLConf`
* `org.apache.spark.sql.execution.ui.SparkListenerQueryExecutionMetrics`

## The Root Cause: Missing Compaction Filters

These two listeners aren't native to the Spark version we were using (3.2.1). They're part of Amazon EMR's modified Spark SQL implementation.  The problem? AWS hadn't implemented the necessary logic to clean up these events during compaction. They were just accumulating like digital dust bunnies.

## Our solution

To fix this, we needed to create a custom filter to exclude these events during compaction. Here's the high-level approach we took:

* **Identify the Target Events**: We pinpointed the specific event types we wanted to exclude.
* **Write the Filter**: We crafted a filter to remove those events from the compaction process.
* **Testing in Dev**: We thoroughly tested the filter in our development environment to ensure it worked as expected and didn't break the Spark History UI.
* **Deploy to Production**: Once confident, we rolled out the filter to our production environment.

## Under the Hood

To make this fix a reality, we created a new implementation for Spark's EventFilterBuilder interface. This interface is how Spark dynamically loads custom filters using its service loader mechanism.

Here's a breakdown of our solution:

```scala
package org.apache.spark.deploy.history
// ... (other imports)

// Custom Event Filter Builder
private[spark] class CustomEventFilterBuilder extends EventFilterBuilder with Logging {
  override def createFilter(): EventFilter = new CustomEventFilter
}

// Custom Event Filter
private[spark] class CustomEventFilter extends EventFilter with Logging {
  // Exclude filter
  def acceptFn: PartialFunction[SparkListenerEvent, Boolean] = {
    case e: SparkListenerEvent if List("SparkListenerQueryExecutionMetrics", "SparkListenerEffectiveSQLConf").contains(Utils.getSimpleName(e.getClass)) => false
  }
  override def statistics(): Option[FilterStatistics] = None
}
```

## How it Works

##### Service Loader Magic: 
The key to this working is a special file we added to our project called **META-INF/services/org.apache.spark.deploy.history.EventFilterBuilder**. This file contains the fully qualified class name of our *CustomEventFilterBuilder*. When Spark starts up, it automatically discovers this file and loads our filter builder.

##### Builder Does Its Thing: 
When it's time to filter events, Spark calls the `createFilter()` method on our *CustomEventFilterBuilder*. This simply returns a new instance of our *CustomEventFilter*.

##### Filter in Action: 
The *CustomEventFilter* takes over. Its `acceptFn` function checks each event's class name. If the class name matches one of the events in our excludedEvents list (in our case, *SparkListenerQueryExecutionMetrics* and *SparkListenerEffectiveSQLConf*), the event is rejected during compaction.

## Why This Approach is Awesome

* **Simple and Elegant**: The code itself is straightforward, making it easy to understand and maintain.
* **Minimal Disruption**: We're not modifying Spark's core code. Our custom filter neatly integrates into the existing framework.
* **Targeted Filtering**: We have precise control over which events to exclude, ensuring we don't accidentally impact other Spark functionalities.
* **Extensible**: You can easily modify the excludedEvents list to target different events based on your specific requirements.
This custom filter solved our event log woes, dramatically reducing their size and improving the stability of our Spark cluster. If you're facing a similar issue, this approach might just be your saving grace!

---

#### Optional - Why Service Loaders Are So Cool:

Imagine you're building a complex software application, and you want to make it flexible and extensible. You want to allow other developers (or even yourself in the future) to add new features or customize behavior without modifying the core codebase.  This is where service loaders come in handy.

In essence, a service loader is a mechanism that allows a Java application to discover and load implementations of specific interfaces at runtime.  Think of it as a way for the main application to say, "Hey, I need something that can do X. Is there anyone out there who can help?"

##### How Service Loaders Work:

* **Interface**: The core application defines an interface that outlines the contract for a particular functionality. This interface acts like a blueprint for what the extension should do.
* **Implementations**: Developers create separate components that provide concrete implementations of the interface.
* **Service File**: Each implementation includes a special configuration file (*usually in the `META-INF/services` directory*) that lists the fully qualified class names of the implementation classes.
* **Discovery**: The core application uses the *service loader* mechanism to scan its classpath at runtime. It looks for these configuration files and automatically loads the listed implementation classes.
* **Usage**: The core application can then use the loaded implementations, seamlessly extending its capabilities without any hardcoded dependencies.

```java
// In the core application, defining the interface
package translator;
public interface MessageTranslator {
    String translate(String message);
}

// In a separate module create implementation of the interface
public class EnglishToFrenchTranslator implements MessageTranslator {
    @Override
    public String translate(String message) {
        // ... translation logic ...
        return "Translated " + message;
    }
}

// Service File: META-INF/services/translator.MessageTranslator
translator.EnglishToFrenchTranslator

// Usage in application
// In the core application
ServiceLoader<MessageTranslator> loader = ServiceLoader.load(MessageTranslator.class);
for (MessageTranslator translator : loader) {
    System.out.println(translator.translate("Hello")); 
}

```

In our Spark example, the service loader helped us seamlessly integrate our custom filter, demonstrating the power of this elegant design pattern.




