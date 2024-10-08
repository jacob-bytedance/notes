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


# 2. Download and Start Elasticsearch

## 2.1 Download and Run

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

## 2.2 Configure

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

# 2.3 Verification of Elasticsearch Server

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

And finally delete the index.

```
curl -X DELETE "http://localhost:9200/test_index?pretty"
```

# 3. Setting Up the Development Environment

## 3.1. Prerequisites

Before we dive into the coding, ensure you have the following tools installed:

- **Java Development Kit (JDK)**
  
- **Maven**

- **Integrated Development Environment (IDE):** IntelliJ IDEA

## 3.2. Creating a New Spring Boot Project

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

## 3.3 Elasticsearch Configuration in Spring Boot

Spring Boot simplifies configuration by providing defaults based on the dependencies included in the project. Let’s set up the basic configuration in the `application.properties` file to get our project up and running.

1. **Open `application.properties`:** Located in `src/main/resources`.
2. **Add the following properties:**

```properties
spring.elasticsearch.rest.uris=http://localhost:9200
spring.elasticsearch.repositories.enabled=true
```

# 4. Creating Entity Object

We start off with `Metric.java` located in `src/main/org/jacobwu/elasticsearch_springboot/model/Metric.java`. The getters and setters are created via IntelliJ's generators for getters and setters.

```java
package org.jacobwu.elasticsearch_springboot.model;  
  
import org.springframework.data.annotation.Id;  
import org.springframework.data.elasticsearch.annotations.Document;  
import org.springframework.data.elasticsearch.annotations.Field;  
  
import java.time.LocalDate;  
import java.time.LocalDateTime;  
import org.springframework.data.elasticsearch.annotations.FieldType;  
  
@Document(indexName = "metrics")  
public class Metric {  
  
    @Id  
    private String id; // The primary key  
  
    @Field(type = FieldType.Keyword)  
    private String key; // A unique key for the metric  
  
    @Field(type = FieldType.Text)  
    private String name; // The name of the metric  
  
    @Field(type = FieldType.Text)  
    private String description; // A description of the metric  
  
    @Field(type = FieldType.Double)  
    private Double value; // The current value of the metric  
  
    @Field(type = FieldType.Date)  
    private LocalDate createdAt; // Timestamp when the metric was created  
  
    @Field(type = FieldType.Date)  
    private LocalDate updatedAt; // Timestamp when the metric was last updated  
  
    @Field(type = FieldType.Keyword)  
    private String status; // The status of the metric (e.g., active, inactive)  
  
    @Field(type = FieldType.Keyword)  
    private String category; // The category of the metric  
  
    @Field(type = FieldType.Keyword)  
    private String module; // The module to which the metric belongs  
  
    public String getId() {  
        return id;  
    }  
  
    public void setId(String id) {  
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
  
    public LocalDate getCreatedAt() {  
        return createdAt;  
    }  
  
    public void setCreatedAt(LocalDate createdAt) {  
        this.createdAt = createdAt;  
    }  
  
    public LocalDate getUpdatedAt() {  
        return updatedAt;  
    }  
  
    public void setUpdatedAt(LocalDate updatedAt) {  
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

# 5. Repository and Service Layers

## 5.1 Creating the Repository

Create a `MetricRepository.java` interface in `src/main/org/jacobwu/elasticsearch_springboot/repository/MetricRepository.java`.

```java
package org.jacobwu.elasticsearch_springboot.repository;  
  
import org.jacobwu.elasticsearch_springboot.model.Metric;  
import org.springframework.data.domain.Page;  
import org.springframework.data.domain.Pageable;  
import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;  
  
/**  
 * @author Jacob Wu */public interface MetricRepository extends ElasticsearchRepository<Metric, String> {  
  
    Page<Metric> findByStatus(String status, Pageable pageable);  
    Page<Metric> findByCategory(String category, Pageable pageable);  
  
}
```

## 5.2 Creating the Service Layer

In `src/main/org/jacobwu/elasticsearch_springboot/service/MetricService.java`, define the service class.

```java
package org.jacobwu.elasticsearch_springboot.service;  
  
import org.jacobwu.elasticsearch_springboot.model.Metric;  
import org.jacobwu.elasticsearch_springboot.repository.MetricRepository;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.data.domain.Page;  
import org.springframework.data.domain.PageRequest;  
import org.springframework.stereotype.Service;  
  
import java.util.Optional;  
  
@Service  
public class MetricService {  
  
    @Autowired  
    private MetricRepository metricRepository;  
  
    public Metric saveMetric(Metric metric) {  
        return metricRepository.save(metric);  
    }  
  
    public Optional<Metric> findById(String id) {  
        return metricRepository.findById(id);  
    }  
  
    public Page<Metric> findByStatus(String status, int page, int size) {  
        return metricRepository.findByStatus(status, PageRequest.of(page, size));  
    }  
  
    public Page<Metric> findByCategory(String category, int page, int size) {  
        return metricRepository.findByCategory(category, PageRequest.of(page, size));  
    }  
  
    public Page<Metric> findAll() {  
        return (Page<Metric>) metricRepository.findAll();  
    }  
  
    public void deleteMetric(String id) {  
        metricRepository.deleteById(id);  
    }  
  
    public void deleteAllMetrics() {  
        metricRepository.deleteAll();  
    }  
}
```

## 5.3 Creating the Controller

Now, let’s accept RESTful API using a controller at `src/main/org/jacobwu/elasticsearch_springboot/controller/MetricController.java`

I recommend adding a `deleteAllMetrics` endpoint as included below in case you mess up. For example, if you specify a data type in Java that is different from the data type in Elasticsearch, you may encounter a server error. You can call `deleteAllMetrics` and start over once you make the necessary data type changes.

**API Endpoints**:
* POST /api/metrics: Creates a new metric
* GET /api/metrics/{id}: Retrieves a metric by its ID
* GET /api/metrics: Retrieves all metrics
* GET /api/metrics/status/{status}: Retrieves metrics filtered by status
* GET /api/metrics/category/{category}: Retrieves metrics filtered by category
* DELETE /api/metrics/{id}: Deletes a metric by its ID
* DELETE /api/metrics/all: Deletes all metrics

```java
package org.jacobwu.elasticsearch_springboot.controller;  
  
import org.jacobwu.elasticsearch_springboot.model.Metric;  
import org.jacobwu.elasticsearch_springboot.service.MetricService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.data.domain.Page;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.*;  
  
import java.util.Optional;  
  
@RestController  
@RequestMapping("/api/metrics")  
public class MetricController {  
  
    @Autowired  
    private MetricService metricService;  
  
    @PostMapping  
    public Metric createMetric(@RequestBody Metric metric) {  
        return metricService.saveMetric(metric);  
    }  
  
    @GetMapping("/{id}")  
    public Optional<Metric> getMetricById(@PathVariable String id) {  
        return metricService.findById(id);  
    }  
  
    @GetMapping  
    public Page<Metric> getAllMetrics() {  
        return metricService.findAll();  
    }  
  
    @GetMapping("/status/{status}")  
    public Page<Metric> getMetricsByStatus(@PathVariable String status,  
                                           @RequestParam(defaultValue = "0") int page,  
                                           @RequestParam(defaultValue = "10") int size) {  
        return metricService.findByStatus(status, page, size);  
    }  
  
    @GetMapping("/category/{category}")  
    public Page<Metric> getMetricsByCategory(@PathVariable String category,  
                                             @RequestParam(defaultValue = "0") int page,  
                                             @RequestParam(defaultValue = "10") int size) {  
        return metricService.findByCategory(category, page, size);  
    }  
  
    @DeleteMapping("/{id}")  
    public void deleteMetric(@PathVariable String id) {  
        metricService.deleteMetric(id);  
    }  
  
    @DeleteMapping("/all")  
    public ResponseEntity<Void> deleteAllMetrics() {  
        metricService.deleteAllMetrics();  
        return ResponseEntity.noContent().build();  
    }  
}
```

## 5.4 Testing Spring Boot Elasticsearch

We can create a metric

```bash
curl -X POST http://localhost:8080/api/metrics \
-H "Content-Type: application/json" \
-d '{
  "id": "1",
  "key": "metric1",
  "name": "Sample Metric",
  "description": "This is a sample metric.",
  "value": 42.0,
  "createdAt": "2024-09-10",
  "updatedAt": "2024-09-10",
  "status": "active",
  "category": "performance",
  "module": "sales"
}'
```

Retrieve a metric by id

```bash
curl -X GET http://localhost:8080/api/metrics/1
```

Retrieve all metrics

```bash
curl -X GET http://localhost:8080/api/metrics
```

Retrieve metrics by status

```bash
curl -X GET http://localhost:8080/api/metrics/status/active
```

Retrieve metrics by category

```bash
curl -X GET http://localhost:8080/api/metrics/category/performance
```

Delete a metric by id

```bash
curl -X DELETE http://localhost:8080/api/metrics/1
```

Delete all metrics

```bash
curl -X DELETE http://localhost:8080/api/metrics/all
```

## 5.5 Adding Mock Metrics

You can use the script below to add 10 metrics for testing. Since we have not yet implemented bulk insert functionality in our Spring Boot application, we can call Elasticsearch server directly to import sample metrics.

```bash
curl -X POST "localhost:9200/_bulk" -H 'Content-Type: application/json' -d'
{ "index" : { "_index" : "metrics", "_id" : "1" } }
{ "id": 1, "key": "metric1", "name": "Sales Revenue", "description": "Total sales revenue generated.", "value": 10000.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "2" } }
{ "id": 2, "key": "metric2", "name": "Customer Satisfaction", "description": "Percentage of satisfied customers.", "value": 89.7, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "customer", "module": "support" }
{ "index" : { "_index" : "metrics", "_id" : "3" } }
{ "id": 3, "key": "metric3", "name": "Net Promoter Score", "description": "Customer likelihood of recommending the company.", "value": 7.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "customer", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "4" } }
{ "id": 4, "key": "metric4", "name": "Churn Rate", "description": "Percentage of customers lost.", "value": 5.2, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "archived", "category": "customer", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "5" } }
{ "id": 5, "key": "metric5", "name": "Lead Conversion Rate", "description": "Percentage of leads converted to customers.", "value": 12.8, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "sales", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "6" } }
{ "id": 6, "key": "metric6", "name": "Average Order Value", "description": "Average value of customer orders.", "value": 450.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "7" } }
{ "id": 7, "key": "metric7", "name": "Page Load Time", "description": "Average time taken for web page to load.", "value": 2.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "performance", "module": "technology" }
{ "index" : { "_index" : "metrics", "_id" : "8" } }
{ "id": 8, "key": "metric8", "name": "Email Open Rate", "description": "Percentage of emails opened by recipients.", "value": 55.4, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "marketing", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "9" } }
{ "id": 9, "key": "metric9", "name": "Return on Investment", "description": "The gain or loss generated on an investment.", "value": 23.7, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "finance" }
{ "index" : { "_index" : "metrics", "_id" : "10" } }
{ "id": 10, "key": "metric10", "name": "Website Traffic", "description": "Number of visitors to the website.", "value": 25000, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "archived", "category": "performance", "module": "marketing" }
'
```

If 10 is not enough for you, you can use the following script to add 20 more, for a total of 30 metrics for testing.

```bash
curl -X POST "localhost:9200/_bulk" -H 'Content-Type: application/json' -d'
{ "index" : { "_index" : "metrics", "_id" : "11" } }
{ "id": 11, "key": "metric11", "name": "Cost Per Acquisition", "description": "Average cost of acquiring a customer.", "value": 320.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "12" } }
{ "id": 12, "key": "metric12", "name": "Bounce Rate", "description": "Percentage of visitors who leave the website without taking action.", "value": 45.8, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "performance", "module": "technology" }
{ "index" : { "_index" : "metrics", "_id" : "13" } }
{ "id": 13, "key": "metric13", "name": "Customer Lifetime Value", "description": "Total value of a customer over their lifetime.", "value": 8500, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "14" } }
{ "id": 14, "key": "metric14", "name": "Click-Through Rate", "description": "Percentage of people who click on a link after viewing it.", "value": 5.7, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "marketing", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "15" } }
{ "id": 15, "key": "metric15", "name": "Gross Profit Margin", "description": "Total revenue minus cost of goods sold, divided by revenue.", "value": 68.3, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "finance" }
{ "index" : { "_index" : "metrics", "_id" : "16" } }
{ "id": 16, "key": "metric16", "name": "Inventory Turnover", "description": "Number of times inventory is sold and replaced over a period.", "value": 8.4, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "performance", "module": "operations" }
{ "index" : { "_index" : "metrics", "_id" : "17" } }
{ "id": 17, "key": "metric17", "name": "Average Session Duration", "description": "Average time users spend on a website per visit.", "value": 3.2, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "performance", "module": "technology" }
{ "index" : { "_index" : "metrics", "_id" : "18" } }
{ "id": 18, "key": "metric18", "name": "Cart Abandonment Rate", "description": "Percentage of users who add items to their cart but don'\''t complete the purchase.", "value": 72.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "sales", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "19" } }
{ "id": 19, "key": "metric19", "name": "Monthly Recurring Revenue", "description": "The monthly revenue generated from subscription-based services.", "value": 15000, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "20" } }
{ "id": 20, "key": "metric20", "name": "Employee Retention Rate", "description": "Percentage of employees who remain in the company over a certain period.", "value": 88.9, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "archived", "category": "performance", "module": "hr" }
{ "index" : { "_index" : "metrics", "_id" : "21" } }
{ "id": 21, "key": "metric21", "name": "Customer Acquisition Cost", "description": "Cost associated with acquiring a new customer.", "value": 400, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "22" } }
{ "id": 22, "key": "metric22", "name": "Social Media Engagement", "description": "Number of interactions with social media posts.", "value": 1500, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "performance", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "23" } }
{ "id": 23, "key": "metric23", "name": "Time to Resolution", "description": "Average time taken to resolve a customer issue.", "value": 3.8, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "customer", "module": "support" }
{ "index" : { "_index" : "metrics", "_id" : "24" } }
{ "id": 24, "key": "metric24", "name": "Net Revenue Retention", "description": "Percentage of revenue retained from existing customers over time.", "value": 105, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "25" } }
{ "id": 25, "key": "metric25", "name": "Monthly Active Users", "description": "Number of unique users who engage with a product in a month.", "value": 20000, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "inactive", "category": "performance", "module": "technology" }
{ "index" : { "_index" : "metrics", "_id" : "26" } }
{ "id": 26, "key": "metric26", "name": "Error Rate", "description": "Percentage of errors generated by an application or system.", "value": 1.2, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "performance", "module": "technology" }
{ "index" : { "_index" : "metrics", "_id" : "27" } }
{ "id": 27, "key": "metric27", "name": "Gross Merchandise Volume", "description": "Total sales volume through a marketplace or eCommerce platform.", "value": 75000, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "pending", "category": "financial", "module": "sales" }
{ "index" : { "_index" : "metrics", "_id" : "28" } }
{ "id": 28, "key": "metric28", "name": "Marketing Qualified Leads", "description": "Number of leads who meet the marketing team'\''s criteria.", "value": 250, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "sales", "module": "marketing" }
{ "index" : { "_index" : "metrics", "_id" : "29" } }
{ "id": 29, "key": "metric29", "name": "Product Defect Rate", "description": "Percentage of products returned due to defects.", "value": 0.8, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "archived", "category": "performance", "module": "operations" }
{ "index" : { "_index" : "metrics", "_id" : "30" } }
{ "id": 30, "key": "metric30", "name": "Customer Response Rate", "description": "Percentage of customers who respond to surveys or emails.", "value": 20.5, "createdAt": "2024-09-10", "updatedAt": "2024-09-10", "status": "active", "category": "customer", "module": "support" }
'
```

After inserting the sample metrics, we can use the commands from 5.4 to verify Spring Boot Elasticsearch behavior.

# 6. Frontend Implementation

Sending and receiving curl requests in the Terminal can be straightforward but it's hard to make sense of the information. We can create a frontend to showcase the various functionality of our metrics repository. Create a file `index.html` inside `src/resources/static`

```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>Metrics Dashboard</title>  
    <style>  
        body {  
            font-family: Arial, sans-serif;  
            background-color: #f7f8fa;  
            margin: 0;  
            padding: 0;  
            color: #333;  
        }  
  
        header {  
            background-color: #2d2f34;  
            color: white;  
            padding: 10px 20px;  
            display: flex;  
            justify-content: space-between;  
            align-items: center;  
        }  
  
        header h1 {  
            margin: 0;  
            font-size: 1.5rem;  
        }  
  
        .container {  
            padding: 20px;  
        }  
  
        .filter {  
            display: flex;  
            gap: 20px;  
            margin-bottom: 20px;  
            justify-content: flex-start;  
            align-items: center;  
        }  
  
        select, button {  
            padding: 10px;  
            border: 1px solid #ddd;  
            border-radius: 5px;  
            background-color: white;  
            cursor: pointer;  
        }  
  
        button {  
            background-color: #1c9cd8;  
            color: white;  
            border: none;  
        }  
  
        button:hover {  
            background-color: #1380a7;  
        }  
  
        .metrics {  
            width: 100%;  
            border-collapse: collapse;  
            background-color: white;  
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);  
        }  
  
        .metrics th, .metrics td {  
            text-align: left;  
            padding: 12px 15px;  
            border: 1px solid #ddd;  
        }  
  
        .metrics th {  
            background-color: #f4f4f4;  
            font-weight: 600;  
            text-transform: uppercase;  
            font-size: 14px;  
            color: #333;  
        }  
  
        .metrics td {  
            font-size: 14px;  
            color: #333;  
        }  
  
        .status.active {  
            color: green;  
            font-weight: bold;  
        }  
  
        .status.inactive {  
            color: orange;  
        }  
  
        .status.archived {  
            color: red;  
        }  
  
        .status.pending {  
            color: blue;  
        }  
  
        .pagination {  
            margin-top: 20px;  
            display: flex;  
            justify-content: center;  
        }  
  
        .pagination button {  
            margin: 0 5px;  
            padding: 8px 12px;  
            border: none;  
            background-color: #f4f4f4;  
            cursor: pointer;  
        }  
  
        .pagination button.active {  
            background-color: #1c9cd8;  
            color: white;  
        }  
  
        .pagination button:hover {  
            background-color: #ddd;  
        }  
  
    </style>  
</head>  
<body>  
  
<header>  
    <h1>Metrics Dashboard</h1>  
</header>  
  
<div class="container">  
    <div class="filter">  
        <label for="status">Filter by Status: </label>  
        <select id="status">  
            <option value="">All</option>  
            <option value="active">Active</option>  
            <option value="inactive">Inactive</option>  
            <option value="archived">Archived</option>  
            <option value="pending">Pending</option>  
        </select>  
        <button onclick="applyFilter('status')">Filter by Status</button>  
  
        <label for="category">Filter by Category: </label>  
        <select id="category">  
            <option value="">All</option>  
            <option value="financial">Financial</option>  
            <option value="customer">Customer</option>  
            <option value="performance">Performance</option>  
            <option value="sales">Sales</option>  
        </select>  
        <button onclick="applyFilter('category')">Filter by Category</button>  
    </div>  
  
    <table class="metrics">  
        <thead>  
        <tr>  
            <th>ID</th>  
            <th>Key</th>  
            <th>Name</th>  
            <th>Description</th>  
            <th>Value</th>  
            <th>Status</th>  
            <th>Category</th>  
            <th>Module</th>  
            <th>Created At</th>  
            <th>Updated At</th>  
        </tr>  
        </thead>  
        <tbody id="metricsTableBody">  
        <!-- Metrics dynamically inserted here -->  
        </tbody>  
    </table>  
  
    <div class="pagination" id="pagination">  
        <!-- Pagination buttons will go here -->  
    </div>  
</div>  
  
<script>  
    async function fetchMetrics(query = "") {  
        const response = await fetch(`http://localhost:8080/api/metrics${query}`);  
        const data = await response.json();  
  
        const metricsTableBody = document.getElementById("metricsTableBody");  
        metricsTableBody.innerHTML = '';  
  
        if (data.numberOfElements > 0) {  
            data.content.forEach(metric => {  
                const row = `  
                        <tr>                            <td>${metric.id}</td>  
                            <td>${metric.key}</td>  
                            <td>${metric.name}</td>  
                            <td>${metric.description}</td>  
                            <td>${metric.value}</td>  
                            <td class="status ${metric.status}">${metric.status}</td>  
                            <td>${metric.category}</td>  
                            <td>${metric.module}</td>  
                            <td>${metric.createdAt}</td>  
                            <td>${metric.updatedAt}</td>  
                        </tr>                    `;  
                metricsTableBody.innerHTML += row;  
            });  
  
            setupPagination(data.totalPages, page);  
        } else {  
            metricsTableBody.innerHTML = '<tr><td colspan="10">No metrics found</td></tr>';  
        }  
    }  
  
    function applyFilter(type) {  
        let query = "";  
        if (type === 'status') {  
            const status = document.getElementById("status").value;  
            query = status ? `/status/${status}` : "";  
        } else if (type === 'category') {  
            const category = document.getElementById("category").value;  
            query = category ? `/category/${category}` : "";  
        }  
        fetchMetrics(query);  
    }  
  
    function setupPagination(totalPages, currentPage) {  
        const paginationDiv = document.getElementById("pagination");  
        paginationDiv.innerHTML = '';  
  
        for (let i = 1; i <= totalPages; i++) {  
            const btn = document.createElement('button');  
            btn.textContent = i;  
            btn.onclick = () => fetchMetrics(i);  
            if (i === currentPage) {  
                btn.classList.add('active');  
            }  
            paginationDiv.appendChild(btn);  
        }  
    }  
  
    window.onload = () => fetchMetrics();  
</script>  
  
</body>  
</html>
```

When you run the application and go to `http://localhost:8080/`, you should see the metrics added earlier in 5.5.

![[img/Elasticsearch with Spring Boot/1.png]]

# 7. 