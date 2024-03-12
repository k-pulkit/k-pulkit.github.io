---
title: "Spark Hive JDBC Issues"
categories:
    - dataengineering
tags:
    - debugging
    - spark
    - remotehive
    - hivejdbc
    - dataengineering
header: 
  teaser: /assets/images/collections/pages/spark-articles.png
excerpt: If you have ever tried to connect to Hive-1.2 JDBC and faced issues then this post is for you. I am going to mention the issues I faced in my use-case.
---

## Scenario
Spark is the most widely used framework for big data processing. Since, its launch, spark has gone through so many iterations and releases and the same is true for Hive. However, one of the challenges with hive is backward compatibility of its APIs. If you have an production environment setup with latest hive and hadoop, and you want to connect it to say Hive-1.2 then it is going to be big struggle. How to make it work? 

## JDBC to the rescue
Incompatibility between `Spark>2.4` and `Hive<2.0` arises due to significant changes in their configurations and protocols. For instance, `Hive<2.0` uses __hive-service.jar__ for Thrift protocol, while the latter adopts __hive-rpc-service.jar__. These alterations result in backward incompatibility, posing challenges when integrating Spark with older versions of Hive.

> Backward compatibility in software: where ancient relics and cutting-edge tech awkwardly high-five each other

If you have worked with hive before, then it is possible to connect to hive through different protocols, one of which is JDBC.
Hive JDBC enables programs written in Java or other languages to interact with Hive for executing queries and manipulating data in Hive tables.

*[JDBC]: Java Database Connectivity

## What Jars are required to connect to Hive-JDBC-1.2 ?
Spark will require a couple of Jars, that contain the relevant Java code to connect to Hive via JDBC protocol. The Jars required are as follows :-
1. __Hive-JDBC-Driver__ - Version `1.2.1` of this driver. This contains the driver class `org.apache.hive.jdbc.driver.HiveDriver` that can be used to make connection to Hive.
2. __Hive-Shims-common-1.2.1, Hive-Shims-0.23-1.2.1__ - Hive shims is used by hive jdbc driver to authenticate the user, typically with Kerberos which is very common in production hadoop environments.
3. __Libthrift-0.9.3__ - Under the hood, thrift protocol is in used and this jar provide the required classes
4. __hive-service-1.2__ - For hive `<2.0`, the hive-service jar is where all the thrift protocols are defined for communication with the hiveserver2
5. __hive-serde-1.2.1__ - This is the serialization and deserialization classes used by hive 1.2


## Modification to existing Jars

Using Hive-JDBC-1.2 drivers with `spark-2.4.8` comes with its own challenges. We will discuss them so you do not have to re-invent the wheel.

There is issues with 2 specific Jars that make them incompatible to be directly used with `spark>2.4`. What are these errors that you will come across if you do not alter the source code.

**ERROR 1** - Unknown Hadoop version 3[^1][^2]
{: .notice--danger}

[^1]: https://support.tibco.com/s/article/How-to-resolve-Illegal-Hadoop-Version-Unknown-expected-A-B-format
[^2]: https://stackoverflow.com/questions/62938360/unrecognised-hadoop-major-version-1-2-1-error-hive-and-impala-jdbc-connection

This error originates from the hive-shims-common jar file, class [ShimLoader.java](https://github.com/apache/hive/blob/release-1.2.1/shims/common/src/main/java/org/apache/hadoop/hive/shims/ShimLoader.java). 

~~~java
// Lines 159 to 176
public static String getMajorVersion() {
    String vers = VersionInfo.getVersion();

    String[] parts = vers.split("\\.");
    if (parts.length < 2) {
      throw new RuntimeException("Illegal Hadoop Version: " + vers +
          " (expected A.B.* format)");
    }

    switch (Integer.parseInt(parts[0])) {
    case 1:
      return HADOOP20SVERSIONNAME;
    case 2:
      return HADOOP23VERSIONNAME;
    default:
      // NEEDS TO BE ALTERED TO RETURN : HADOOP23VERSIONNAME;
      throw new IllegalArgumentException("Unrecognized Hadoop major version number: " + vers);
    }
}
~~~
By altering the logic and `cheat-compiling`, we can make the hive shims work on unsupported version of hadoop. This may be necessary when the data being accessed via JDBC is in a hadoop cluster with version lower or incompatible with the cluster where spark is running.

**ERROR 2** - Unknown method HiveStatement.setQueryTimeout 3[^3]
{: .notice--danger}

[^3]: https://community.progress.com/s/article/setquerytimeout-is-not-working-with-apache-hive-jdbc-driver

This error originates from the hive-jdbc jar file, when `spark>2.4` calls the `setQueryTimeout` method on the `HiveStatement` object.
The solution to this problem is to likewise remove the exception line in file [HiveStatement.java](https://github.com/apache/hive/blob/release-1.2.1/jdbc/src/java/org/apache/hive/jdbc/HiveStatement.java).

~~~java
// Line 737 to 740
  public void setQueryTimeout(int seconds) throws SQLException {
    // return 1
    throw new SQLException("Method not supported");
  }
~~~
By altering the logic and `cheat-compiling`, we can make this error go away and finally out hive1.2 jars will be ready to be used with `spark2.4`.

**Note** - You can use commands `mvn clean install && mvn clean package` to create jar from source.
{: .notice--info}

## Spark command to use the jars
Now that we have the required jars, we are ready to fire-up spark-shell and connect to hive via JDBC.
~~~bash
# Assuming you have the required jars in hive1.2jars directory
cd hive1.2jars
spark-shell \
--master yarn \
--jars hive-jdbc.jar,hive-shims-common.jar,hive-shims-0.23.0.jar,hive:libthrift.jar,hive-serde.jar \
--conf spark.driver.extraClassPaths=hive-jdbc.jar:hive-shims-common.jar:hive-shims-0.23.0.jar:hive:libthrift.jar:hive-serde.jar \
--conf spark.executor.extraClassPaths=hive-jdbc.jar:hive-shims-common.jar:hive-shims-0.23.0.jar:hive:libthrift.jar:hive-serde.jar \
--conf spark.driver.userClassPathFirst=true \
--conf spark.executor.userClassPathFirst=true
~~~

In addition to the above settings, you will also need to provide the executors with the login information via `jaas.conf`[^jaas-sample] file. It is a known issue for hive-jdbc not requesting the `hiveserver2 service token` when used in client or cluster modes.

[^jaas-sample]: https://querysurge.zendesk.com/hc/en-us/articles/115001218863-Setting-Up-a-Hive-Connection-with-Kerberos-using-Apache-JDBC-Drivers-Windows-

~~~bash
# You need to create jaas.conf for serviceName="hiveserver2"
--files ./jaas.conf \
--conf spark.executor.extraJavaOptions=-Djavax.security.auth.useSubjectCredsOnly=false -Djava.security.auth.login.config=./jaas.conf \
--conf spark.security.credentials.hiveserver2.enables=true
~~~

If you have any further comments on the solution, please let me know. I hope my article helps you solve any relevant bugs and connect to hive via JDBC successfully.

---

##### Footnotes



