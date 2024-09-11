---
title: Elasticsearch with Spring Boot
---
# 1. Introduction

## 1.1 What is Elasticsearch

Elasticsearch is a powerful distributed, scalable, real-time search and analytics engine. It enables storage, search, and analysis of lots of data. Some use cases include logging and event management, real-time monitoring, and complex search features.

## 1.2 Why Use Elasticsearch?

• **Speed**: Fast data retrieval with the ability to read and write almost instantly

• **Scalability**: It scales out easily

• **Flexibility**: Advanced search capabilities, including full-text search and complex query types

## 1.3 Basic Concepts and Architecture in Elasticsearch

• **Document**: A basic unit of information in Elasticsearch, similar to a row in a database.

• **Index**: A collection of documents that are somewhat similar, like a database in a relational database system.

• **Node**: A single server that is part of your cluster, storing data and participating in the cluster’s indexing and query capabilities.

• **Cluster**: A collection of nodes that holds your entire data and provides federated indexing and search capabilities across all nodes.

## 1.4 Follow Along The Tutorial

You can follow along with this tutorial via the Github Repository.

# 2. Setting Up the Development Environment

## 2.1. Prerequisites

Before we dive into the coding, ensure you have the following tools installed:

- **Java Development Kit (JDK)**
  
- **Maven**

- **Integrated Development Environment (IDE):** IntelliJ IDEA

## 2.2. Creating a New Spring Boot Project

1. **Use Spring Initializr:** [start.spring.io](https://start.spring.io/).
2. **Configure the Project:**
   - **Project:** Maven Project.
   - **Language:** Java.
   - **Spring Boot:** 3.3.3, the latest stable version.
   - **Group:** `org.jacobwu`.
   - **Artifact:** `elasticsearch-springboot`.
   - **Name:** `elasticsearch-springboot`.
   - **Description:** A demo project for Elasticsearch with Spring Boot.
   - **Packaging:** Jar.
   - **Java Version:** 17.
3. **Add Dependencies:**
   - **Spring Web**
   - **Spring Data Elasticsearch (Access+Driver)**
4. **Generate the Project:** Click the "Generate" button to download the project as a ZIP file. Extract the ZIP file and open it in your IDE.

## 2.3 Elasticsearch Configuration in Spring Boot

Spring Boot simplifies configuration by providing defaults based on the dependencies included in the project. Let’s set up the basic configuration in the `application.properties` file to get our project up and running.

1. **Open `application.properties`:** Located in `src/main/resources`.
2. **Add the following properties:**

```properties
spring.data.elasticsearch.client.reactive.endpoints=localhost:9200  
spring.data.elasticsearch.repositories.enabled=true
spring.elasticsearch.rest.username=elastic  
spring.elasticsearch.rest.password=your_password_here
```

# 3. Creating Entity Object

We start off with `MetricEsDTO.java` located in `src/main/org/jacobwu/elasticsearch_springboot/model/MetricEsDTO.java`. The getters and setters are created via IntelliJ's generators for getters and setters.

```java
package org.jacobwu.elasticsearch_springboot.model;  
  
import org.springframework.data.annotation.Id;  
import org.springframework.data.elasticsearch.annotations.Document;  
import org.springframework.data.elasticsearch.annotations.Field;  
  
import java.time.LocalDateTime;  
import org.springframework.data.elasticsearch.annotations.FieldType;  
  
@Document(indexName = "metrics")  
public class MetricEsDTO {  
  
    @Id  
    private Long id; // The primary key  
  
    @Field(type = FieldType.Keyword)  
    private String key; // A unique key for the metric  
  
    @Field(type = FieldType.Text)  
    private String name; // The name of the metric  
  
    @Field(type = FieldType.Text)  
    private String description; // A description of the metric  
  
    @Field(type = FieldType.Double)  
    private Double value; // The current value of the metric  
  
    @Field(type = FieldType.Date)  
    private LocalDateTime createdAt; // Timestamp when the metric was created  
  
    @Field(type = FieldType.Date)  
    private LocalDateTime updatedAt; // Timestamp when the metric was last updated  
  
    @Field(type = FieldType.Keyword)  
    private String status; // The status of the metric (e.g., active, inactive)  
  
    @Field(type = FieldType.Keyword)  
    private String category; // The category of the metric  
  
    @Field(type = FieldType.Keyword)  
    private String module; // The module to which the metric belongs  
  
    public Long getId() {  
        return id;  
    }  
  
    public void setId(Long id) {  
        this.id = id;  
    }  
  
    public String getKey() {  
        return key;  
    }  
  
    public void setKey(String key) {  
        this.key = key;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public String getDescription() {  
        return description;  
    }  
  
    public void setDescription(String description) {  
        this.description = description;  
    }  
  
    public Double getValue() {  
        return value;  
    }  
  
    public void setValue(Double value) {  
        this.value = value;  
    }  
  
    public LocalDateTime getCreatedAt() {  
        return createdAt;  
    }  
  
    public void setCreatedAt(LocalDateTime createdAt) {  
        this.createdAt = createdAt;  
    }  
  
    public LocalDateTime getUpdatedAt() {  
        return updatedAt;  
    }  
  
    public void setUpdatedAt(LocalDateTime updatedAt) {  
        this.updatedAt = updatedAt;  
    }  
  
    public String getStatus() {  
        return status;  
    }  
  
    public void setStatus(String status) {  
        this.status = status;  
    }  
  
    public String getCategory() {  
        return category;  
    }  
  
    public void setCategory(String category) {  
        this.category = category;  
    }  
  
    public String getModule() {  
        return module;  
    }  
  
    public void setModule(String module) {  
        this.module = module;  
    }  
}
```

**@Document(indexName = “metrics”)** specifies that instances of MetricEsDTO should be stored in the Elasticsearch index named “metrics”

**@Id** marks the id field as the primary key for documents in the Elasticsearch index

**@Field(type = FieldType.\*)** specify the type of each field as it should be treated in Elasticsearch
* Keyword fields are searchable but not analyzed
* Text fields are searchable and analyzed (broken down into searchable terms, enables full-text search)

# 4. Create a Repository Interface

Create a `MetricRepository` in 

```

```

# 5. Download and Start Elasticsearch

## 5.1 Download and Run

1. Download Elasticsearch from [Elasticsearch official download page](https://www.elastic.co/downloads/elasticsearch)
2. Extract the package (Safari should do so automatically)
3. Run Elasticsearch. For mac, the command is `bin/elasticsearch`. Before running, please ensure the ES_JAVA_HOME is set to the jdk locally.

You will see a message similar to the one below:

```
✅ Elasticsearch security features have been automatically configured!

✅ Authentication is enabled and cluster connections are encrypted.

ℹ️  Password for the **elastic** user (reset with `bin/elasticsearch-reset-password -u elastic`):
```

In a new terminal window, run `curl -u elastic -X GET "http://localhost:9200/"`. Use this password from the message earlier to authenticate. You should see `curl: (52) Empty reply from server` since the elasticsearch cluster is currently empty.

## 5.2 Configure

Modify `config/elasticsearch.yml` to disable security for local testing so you won't have to deal with the headache of SSL and certificate authentication. You should run elasticsearch at least once to generate the default configuration settings, before applying the following modifications.

* Set `xpack.security.enabled: false`
* Set `xpack.security.enrollment.enabled: false`
* Set `xpack.security.http.ssl.enabled: false` and comment out any other setting under `xpack.security.http.ssl`
* Set `xpack.security.transport.ssl.enabled: false` and comment out any other setting under `xpack.security.transport.ssl`

The modified `elasticsearch.yml` should look like this.

```yml
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
#cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
#network.host: 192.168.0.1
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Allow wildcard deletion of indices:
#
#action.destructive_requires_name: false

#----------------------- BEGIN SECURITY AUTO CONFIGURATION -----------------------
#
# The following settings, TLS certificates, and keys have been automatically      
# generated to configure Elasticsearch security features on 10-09-2024 23:55:29
#
# --------------------------------------------------------------------------------

# Enable security features
xpack.security.enabled: false

xpack.security.enrollment.enabled: true

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
#  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false
#  verification_mode: certificate
#  keystore.path: certs/transport.p12
#  truststore.path: certs/transport.p12
# Create a new cluster with the current node only
# Additional nodes can still join the cluster later
cluster.initial_master_nodes: ["Jacob"]

# Allow HTTP API connections from anywhere
# Connections are encrypted and require user authentication
http.host: 0.0.0.0

# Allow other nodes to join the cluster from anywhere
# Connections are encrypted and mutually authenticated
#transport.host: 0.0.0.0

#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
```

# 5.3 Verification of Elasticsearch Server

Let's create an index called `test_index` using the curl command. We specify `pretty` to request a more readable and structured output.

```curl
curl -X PUT "http://localhost:9200/test_index?pretty"
```

A response indicating success will display:

```curl
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test_index"
}
```

Let's add a document using this command.

```
curl -X POST "http://localhost:9200/test_index/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "Jacob Wu",
  "age": 30,
  "occupation": "Engineer"
}'
```

A response indicating success will display:

```
{
  "_index" : "test_index",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

Let's retrieve the document using this command.

```
curl -X GET "http://localhost:9200/test_index/_doc/1?pretty"
```

And the entry we inserted should be returned.

We can search for an entry, after all, it is elasticsearch.

```
curl -X GET "http://localhost:9200/test_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "name": "Jacob"
    }
  }
}'
```

And again the entry is returned to us.

Now we can delete the document.

```
curl -X DELETE "http://localhost:9200/test_index/_doc/1?pretty"
```

And finally delet