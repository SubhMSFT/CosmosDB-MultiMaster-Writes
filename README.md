# Configuring Azure Cosmos DB Multi-Master Writes via SDK
SDK samples for setting up Azure Cosmos DB - Multi-Master Write Regions across 5 available APIs in one place.

[About Azure Cosmos DB](#about-azure-cosmos-db) 

[Core SQL API](#core-sql-api)

[Mongo DB API](#mongo-db-api)

[Cassandra DB API](#cassandra-db-api)

[Table API](#table-api)

[Gremlin API](#gremlin-api)

[Feedback](#feedback)

[License/Terms of Use](#license--terms-of-use)

## About Azure Cosmos DB
Azure Cosmos DB is a fully managed NoSQL database for modern app development, with SLA-backed speed and availability, automatic and instant scalability, and open-source APIs for MongoDB, Cassandra, and other NoSQL engines. For a more in-depth coverage of Azure Cosmos DB, you should visit the official site here > https://docs.microsoft.com/en-us/azure/cosmos-db/introduction

![Image1](media/azure-cosmos-db.png)

## Core SQL API
Once an account has been created with multiple write regions enabled, you must make two changes in your application to the ```ConnectionPolicy``` for the Cosmos client to enable the multi-region writes in Azure Cosmos DB. Within the ```ConnectionPolicy```, set ```UseMultipleWriteLocations``` to **true** and pass the name of the region where the application is deployed to ```ApplicationRegion```. This will populate the ```PreferredLocations``` property based on the geo-proximity from location passed in. If a new region is later added to the account, the application does not have to be updated or redeployed, it will automatically detect the closer region and will auto-home on to it should a regional event occur.

1) **.NET SDK v2**: To enable multi-region writes in your application, set UseMultipleWriteLocations to true. Also, set SetCurrentLocation to the region in which the application is being deployed and where Azure Cosmos DB is replicated:

```
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        **UseMultipleWriteLocations = true**
    };
policy.SetCurrentLocation("West US 2");
```
2) **.NET SDK v3**: To enable multi-region writes in your application, set ApplicationRegion to the region in which the application is being deployed and where Cosmos DB is replicated:

```
CosmosClient cosmosClient = new CosmosClient("<connection-string-from-portal>", new CosmosClientOptions() { ApplicationRegion = Regions.WestUS2, });
```

3) **Java SDK V4** (Async): To enable multi-region writes in your application, call .multipleWriteRegionsEnabled(true) and .preferredRegions(preferredRegions) in the client builder, where preferredRegions is a List containing one element - that is the region in which the application is being deployed and where Cosmos DB is replicated:

```
ArrayList<String> preferredRegions = new ArrayList<String>();
preferredRegions.add(region);

CosmosAsyncClient client =
        new CosmosClientBuilder()
                .endpoint(HOST)
                .key(MASTER_KEY)
                .multipleWriteRegionsEnabled(true)
                .preferredRegions(preferredRegions)
                .buildAsyncClient();
```                

4) **Java SDK V4** (Sync): 

```
ArrayList<String> preferredRegions = new ArrayList<String>();
preferredRegions.add(region);

CosmosClient client =
        new CosmosClientBuilder()
                .endpoint(HOST)
                .key(MASTER_KEY)
                .multipleWriteRegionsEnabled(true)
                .preferredRegions(preferredRegions)
                .buildClient();
```      
