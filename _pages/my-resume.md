---
layout: single
title: Resume
permalink: /my-resume/
redirect_from:
  - /resume/
author_profile: true
classes: wide
---

## Summary

Detail-oriented Data Engineer with 3+ years of experience designing scalable ETL and streaming pipelines on AWS. Strong in modular system design, automated CI/CD, and observability. Proven ability to transform business needs into resilient, production-ready data solutions.

## Technical Skills

- **Languages:** Java, Python, Scala, SQL  
- **Cloud & Infra:** AWS (Lambda, EMR, Redshift, Glue, S3, EventBridge, CloudFormation), Terraform  
- **Data & ETL:** Apache Spark, Kinesis, DynamoDB, Athena, Matillion  
- **CI/CD & Testing:** GitHub Actions, Maven, JUnit, Mockito, Guice DI  
- **Monitoring:** CloudWatch, Datadog, PagerDuty  

## Professional Experience

<div class="timeline">
  <div class="timeline-item">
    <span class="timeline-marker"></span>
    <div class="timeline-content">
      <h3>Blackline Safety — Data Engineer</h3>
      <p class="timeline-date">June 2023 – Present · Edmonton, AB</p>
      <ul>
        <li>Built reusable Matillion + Lambda component that parses a custom SQL DSL and orchestrates data movement via S3, Kinesis, Redshift — improving feed onboarding by 70%.</li>
        <li>Refactored serverless transformation engine using Maven and Guice DI; introduced SQS/DynamoDB DLQs and automated CI/CD via CloudFormation.</li>
        <li>Migrated EMR CDC jobs to Spark Structured Streaming with optimized cluster configs — reducing latency by 30% and cost by 20%.</li>
        <li>Automated Glue Catalog + Athena table creation; deployed Redshift Serverless with cross-cluster sharing for self-service analytics.</li>
        <li>Developed SFTP-based reporting platform with query validation, Datadog alerting, and EventBridge scheduling with DLQ replay logic.</li>
        <li>Created GitHub Actions pipeline to sync customer config with DynamoDB using checksum validation, ensuring integrity.</li>
        <li>Led schema migrations and backfills (e.g. EXO8 gas, gamma data); built validation logic to resolve data mismatches.</li>
        <li>Managed production on-call rotation and authored runbooks and observability dashboards in Datadog and CloudWatch.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <span class="timeline-marker"></span>
    <div class="timeline-content">
      <h3>Scotiabank — Data Engineer</h3>
      <p class="timeline-date">June 2023 – Present</p>
      <ul>
        <li>Developed scalable PII detection and anonymization framework using Spark (Scala + Python) across Parquet, ORC, JSON, HBase, etc.</li>
        <li>Optimized Spark workloads via Spark UI and execution plans, achieving 70% performance gains on 500+ production tables.</li>
        <li>Deployed framework post-UAT and integrated Ranger policies for secure PII masking in production systems.</li>
        <li>Assisted HDP to CDP migration with issue debugging and platform compatibility fixes.</li>
        <li>Automated ingestion pipelines with Python, Bash, and built unit-tested delivery workflows.</li>
        <li>Documented pipelines for audits and collaborated with compliance on data privacy initiatives.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <span class="timeline-marker"></span>
    <div class="timeline-content">
      <h3>Scotiabank — Data Analyst Intern</h3>
      <p class="timeline-date">Jan 2023 – May 2023</p>
      <ul>
        <li>Automated 5+ manual business processes using Python and VBA.</li>
        <li>Created interactive PowerBI dashboards for data-driven leadership decisions.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <span class="timeline-marker"></span>
    <div class="timeline-content">
      <h3>American Express — Lead Analyst, Business Intelligence</h3>
      <p class="timeline-date">Sep 2018 – Sep 2020</p>
      <ul>
        <li>Refactored Hive SQL queries to improve runtime by 50%; migrated logic to PySpark, increasing efficiency by 60%.</li>
        <li>Optimized jobs using broadcast joins and Scala UDFs, and automated reports using Python and Bash.</li>
        <li>Mentored junior analysts and supported transition to Spark-based workloads.</li>
      </ul>
    </div>
  </div>

  <div class="timeline-item">
    <span class="timeline-marker"></span>
    <div class="timeline-content">
      <h3>Mu‑Sigma Business Solutions — Trainee Associate, Data Analytics</h3>
      <p class="timeline-date">Nov 2016 – Sep 2018</p>
      <ul>
        <li>Built HiveQL pipelines and SQL scripts to analyze clickstream behavior and KPIs.</li>
        <li>Created reusable data extractors to automate Excel/PowerBI reporting processes.</li>
        <li>Maintained data dictionaries, technical docs, and logic maps for ETL flows.</li>
      </ul>
    </div>
  </div>
</div>

## Education

- **Lambton College, Toronto, CA** — Cloud Computing for Big Data (GPA: 3.67/4.0, President Award recipient), Sep 2021 – May 2023  
- **National Institute of Technology, India** — Bachelor of Technology in Computer Science, May 2012 – May 2016
