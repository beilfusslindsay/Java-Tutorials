# Adding a Global Secondary Index (GSI) to [DynamoDB](https://aws.amazon.com/dynamodb/?nc2=h_ql_prod_db_ddb) Using [Java](https://dev.java/) and [Dagger](https://dagger.io/)

<br>
This tutorial will guide you through adding a Global Secondary Index (GSI) to a DynamoDB table using Java and Dagger for dependency injection. We will automate the creation of a table and GSI without using static methods, hardcoded variables, or a traditional main method. We will touch on hot partitions, their implications on performance, and how to manage them effectively. Finally, we will also cover how to achieve the same setup manually using the AWS Console for a visual understanding of the process.

<br>
By the end of this guide, you will have a scalable and clean implementation for managing DynamoDB GSIs programmatically.



## Why Use a Global Secondary Index?
<br>

A Global Secondary Index (GSI) allows querying a DynamoDB table on a different attribute than the table’s primary key. This is particularly useful when your access patterns require efficient lookups on non-primary key attributes. For example:
- Your DynamoDB table has a primary key of userId
- You need to query users based on their email

<br>
A GSI can be created with email as the partition key, enabling efficient lookups by email without scanning the entire table.

<br>

**Table vs GSI**

<br>

| Base Table       | Partition Key | Attributes             | 
|---------|----------------|--------------------------------|
| userId  | user123        | name, email, status            | 

| GSI (EmailIndex) | Partition Key | Attributes (Projected) | 
|---------|----------------|--------------------------------|
| email   | lindsay@example.com        | name, email, status| 



## Prerequisites

- Java 17+
- AWS SDK for Java v2
- Dagger 2
- An AWS account with permissions for DynamoDB

>Ensure you have a basic understanding of Java and DynamoDB concepts before proceeding.



## Step 1: Creating a Table and GSI in the AWS Console
To understand the structure, let’s manually create a table and GSI in the AWS Management Console. In the DynamoDB console, the workflow will look like:

```AWS Console > DynamoDB > Tables > Create Table > Add GSI```  



### Create a DynamoDB Table

1. Log in to the AWS Console and navigate to DynamoDB.
1. Click **Tables > Create Table**.
1. Define the table properties:
  - Table Name: Users
  - Partition Key: userId (String)
>Set the Capacity Mode to either On-demand or Provisioned based on your use case.

### Add a Global Secondary Index (GSI)

1. After creating the table, open the Indexes tab and click Create Index.
1. Define the GSI properties:
- **Index Name**: EmailIndex
- **Partition Key**: email (String)
- **Projection Type**: ALL
>Once the index is active, it will be available for queries.



**[AWS Management Console](https://console.aws.amazon.com/dynamodb/home) GSI Setup**
| Step                    | Details                         | 
|-------------------------|---------------------------------|
| Define Table Schema  | Partition Key: userId              | 
| Create GSI Index Name:  | EmailIndex, Partition Key: email| 
| Projection | Type: ALL (include all attributes)           | 

## Step 2: Automating Table and GSI Creation

Let’s automate the setup using Java and Dagger.

### Dynamic Configuration

Start by creating a application.yml configuration file to define the table and GSI properties dynamically. This allows us to avoid hardcoded values and ensures flexibility across environments.

```java
dynamodb:
  table:
    name: Users
    primaryKey: userId
    gsi:
      name: EmailIndex
      partitionKey: email
      readCapacity: 5
      writeCapacity: 5
```

## Step 3: Setting Up Dependency Injection with Dagger

### DynamoDB Client Module

Use a Dagger module to provide the DynamoDB client. This ensures that a singleton [DynamoDbClient](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/dynamodb/DynamoDbClient.html) is available for injection throughout the application. Creating a new DynamoDB client for each request in your application is generally not recommended. 


```java
import dagger.Module;
import dagger.Provides;
import software.amazon.awssdk.services.dynamodb.DynamoDbClient;
import software.amazon.awssdk.regions.Region;
import javax.inject.Singleton;

@Module
public class DynamoDbModule {
    @Provides
    @Singleton

    DynamoDbClient provideDynamoDbClient() {
        return DynamoDbClient.builder()
                .region(Region.US_EAST_1) // Update the region as needed
                .build();
    }
}
```


### Step 4: Automating Table Creation

Create a service to handle the creation of the DynamoDB table dynamically:

```java
import org.springframework.stereotype.Service;
import software.amazon.awssdk.services.dynamodb.DynamoDbClient;
import software.amazon.awssdk.services.dynamodb.model.*;

@Service
public class DynamoDbTableService {
    private final DynamoDbClient dynamoDbClient;
    private final DynamoDbTableConfig config;

    public DynamoDbTableService(DynamoDbClient dynamoDbClient, DynamoDbTableConfig config) {
        this.dynamoDbClient = dynamoDbClient;
        this.config = config;
    }

    public void createTable() {
        String tableName = config.getName();
        String primaryKey = config.getPrimaryKey();

        CreateTableRequest request = CreateTableRequest.builder()
                .tableName(tableName)
                .keySchema(KeySchemaElement.builder()
                        .attributeName(primaryKey)
                        .keyType(KeyType.HASH)
                        .build())
                .attributeDefinitions(AttributeDefinition.builder()
                        .attributeName(primaryKey)
                        .attributeType(ScalarAttributeType.S)
                        .build())
                .provisionedThroughput(ProvisionedThroughput.builder()
                        .readCapacityUnits(5L)
                        .writeCapacityUnits(5L)
                       .build())
                .build();
        dynamoDbClient.createTable(request);
    }
}
```


**Execution Flow for Table Creation**
```application.yml (dynamic configuration) > DynamoDBTableConfig (maps config to Java) > DynamoDBTableService (creates table) > Table (created in AWS)```




Step 5: Automating GSI Creation



Next, create a service to automate the creation of the GSI:



@Service

public class DynamoDbGsiService {

    private final DynamoDbClient dynamoDbClient;

    private final DynamoDbTableConfig config;



    public DynamoDbGsiService(DynamoDbClient dynamoDbClient, DynamoDbTableConfig config) {

        this.dynamoDbClient = dynamoDbClient;

        this.config = config;

    }



    public void createGsi() {

        GlobalSecondaryIndexUpdate gsiUpdate = GlobalSecondaryIndexUpdate.builder()

                .create(CreateGlobalSecondaryIndexAction.builder()

                        .indexName(config.getGsi().getName())

                        .keySchema(KeySchemaElement.builder()

                                .attributeName(config.getGsi().getPartitionKey())

                                .keyType(KeyType.HASH).build())

                        .projection(Projection.builder()

                                .projectionType(ProjectionType.ALL).build())

                        .provisionedThroughput(ProvisionedThroughput.builder()

                                .readCapacityUnits(config.getGsi().getReadCapacity())

                                .writeCapacityUnits(config.getGsi().getWriteCapacity()).build())

                        .build())

                .build();



        dynamoDbClient.updateTable(UpdateTableRequest.builder()

                .tableName(config.getName())

                .globalSecondaryIndexUpdates(gsiUpdate)

                .build());

    }

}



Step 6: Automating Execution



Use Spring’s ApplicationRunner to automatically execute the table and GSI creation when the application starts:



import org.springframework.boot.ApplicationRunner;

import org.springframework.context.annotation.Bean;

import org.springframework.context.annotation.Configuration;



@Configuration

public class DynamoDbInitializer {

    private final DynamoDbTableService tableService;

    private final DynamoDbGsiService gsiService;



    public DynamoDbInitializer(DynamoDbTableService tableService, DynamoDbGsiService gsiService) {

        this.tableService = tableService;

        this.gsiService = gsiService;

    }



    @Bean

    public ApplicationRunner runner() {

        return args -> {

            tableService.createTable();

            gsiService.createGsi();

        };

    }

}



Visual: Complete Workflow



+--------------------+         +------------------------+

| DynamoDbTableConfig|         | DynamoDbGsiConfig     |

| (Dynamic Config)   |         | (Dynamic Config)      |

+--------------------+         +------------------------+

         |                             |

         v                             v

+--------------------+         +------------------------+

| DynamoDbTableService|       | DynamoDbGsiService     |

| (Table Creation)    |       | (GSI Creation)         |

+--------------------+         +------------------------+

         \                             /

          \                           /

           v                         v

        +-------------------------------+

        | DynamoDB Table & GSI Created  |

        | (Fully Automated Setup)       |

        +-------------------------------+



Hot Partitions: What You Need to Know



What Are Hot Partitions?



Hot partitions occur when a large number of requests target the same partition in DynamoDB, leading to performance bottlenecks.



Visual: Hot Partition Example



Partition Key Request Count

user123 10 requests/sec

user456 5000 requests/sec



Wrapping Up



In this tutorial, we:

1. Set up a DynamoDB table and GSI manually in the AWS Console.

2. Automated the process using Java, Dagger, and Spring.

3. Discussed hot partitions and strategies to prevent them.



This approach ensures flexibility, scalability, and clean code for managing DynamoDB resources. Would you like to explore advanced topics like monitoring or error handling? Let me know!
