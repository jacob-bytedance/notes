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