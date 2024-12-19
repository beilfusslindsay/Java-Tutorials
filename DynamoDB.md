# [DynamoDB](https://aws.amazon.com/dynamodb/?nc2=h_ql_prod_db_ddb)
Amazon DynamoDB is a serverless, NoSQL, fully managed database so you don't need to provision any servers, or patch, manage, install, maintain, or operate any software. This document steps briefly through how to:

* Implement a class that represents any item from a provided DynamoDB table.
* Implement a class that represents any item from a provided DynamoDB table, where the names of some of its fields differ from the corresponding names of attributes of the DynamoDB table.
* Implement functionality that retrieves a unique item by hash key from a provided DynamoDB table.
* Implement functionality that retrieves a unique item by hash and range key from a provided DynamoDB table.
* Implement functionality that creates an item in a provided DynamoDB table.
* Implement functionality that modifies an existing item in a provided DynamoDB table.  
  
## Implement a class that represents any item from a provided DynamoDB table.
How do we map DynamoDB items to Java? Creating a [POJO](https://www.baeldung.com/java-pojo-class) helps us map items from our table to the Java class, which allows us to use Java to create, delete, and update items in our table. An instance of that POJO will represent a single item in the table and attributes of the item are fields of the POJO. When we are writing POJOs to be used with DynamoDB, we want to always use the wrapper classes. Reason being, DynamoDB allows us to have items without every attribute specified, if we use wrapper classes this is represented as null for that field. If we use primitive type and the attribute isn’t specified for that item, the field will have the default value. e.g. when we use the wrapper Boolean, we know the only time it was unspecified is if the value is null.

```java
@DynamoDBTable (tableName = “Songs“)
public class Song {
}
```
The @DynamoDBTable annotation annotates the class declaration that appears directly below (or to the right of) the annotation. When using an annotation, it's important to make sure that the next line of code is the value that you want to annotate. The DynamoDB annotation has a parameter, tableName. The value for tableName should be whatever the name of your table is in DynamoDB. This is how Java knows which table to access and the annotation is therefore required. As you can see here, our tableName is Songs and our class name is Song. It's a pretty typical naming convention to have the name of your database table be plural (like songs) and the class be singular (like song) since the table holds multiple songs, while the class represents an individual instance of the song

```java
@DynamoDBHashKey (attributeName “artist”)
public String getArtist(){
  return artist;
}
```
The @DynamoDBHashKey annotation annotates the variable used as the partition key, so it should appear just above the getter for the partition key. Although in DynamoDB we refer to it as a partition key, the Java annotation uses hash key instead. The value for attributeName should be whatever the name of the attribute is in DynamoDB. This parameter is optional if the attribute name exactly matches the name of your Java field (including capitalization and any punctuation/special characters). Since all DynamoDB tables have a partition key, this annotation is required.

```java
@DynamoDBRangeKey(attributeName = "song_title") 
public String getSongTitle() {
  return songTitle;
}
```
The @DynamoDBRangeKey annotation annotates the variable used as the sort key, so it should appear just above the getter for the sort key. Although in DynamoDB we refer to it as a sort key, the Java annotation uses range key instead. Both terms are used by DynamoDB interchangeably to refer to the same attribute. 

```java
@DynamoDBAttribute(attributeName = "genre")
public String getGenre() {
    return genre;
}
```
To explicitly map other attributes in the table that aren't a key, we use @DynamoDBAttribute with the parameter attributeName. This attribute appears right before the getter for the appropriate Java field, just like we saw with the other annotations. This annotation is optional and is only required when the attribute names don't exactly match the Java names. It is considered best practice to always include the annotations and attributeName fields even when they are not required. 

## Implement a class that represents any item from a provided DynamoDB table, where the names of some of its fields differ from the corresponding names of attributes of the DynamoDB table.
```java
public class Song {
private Integer year;

@DynamoDBAttribute(attributeName = “year_published”)
public Integer getYear(){
return year;
}
```

## Implement functionality that retrieves a unique item by hash key from a provided DynamoDB table.
Loading items with a partition key: 
```public <T> T load([Class]{.underline}<T> clazz, [Object]{.underline} hashKey)```
The first parameter is of type Class, The second parameter is the primary key for the class, which in this case is a partition key. If the table uses a partition + sort key as the primary key, you must specify both keys.

Shoes
| shoe_id | cubby_location | color | style   | occasion |
|---------|----------------|-------|---------|----------|
| SN01    | 1              | grey  | sneaker | athletic |
| BO01    | 2              | grey  | boot    | work     |
| SN02    | 3              | black | sneaker | casual   |


To load the first shoe in the Shoes table:

```java
   DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
   Shoe shoe01 = mapper.load(Shoe.class, "SN01");
```

> Note: The first line of code creates a new instance of the DynamoDBMapper class and passes in an instance of an AmazonDynamoDBClient, which is defined in the DynamoDBClientProvider class. As we discussed in the last section, the DynamoDBClientProvider class creates the connection between our program and the DynamoDB where our table is. By passing in the client, we're setting up a connection for mapper, an instance of DynamoDBMapper, to access the correct DynamoDB.

> Note: The second line of code creates a new instance of the Shoe class, shoe01, and then calls the load() method using the Shoe class and the partition key SN01. As you can see in the Shoes table, SN01 is the partition key for the first shoe. Our load() method searches through the Shoes table to find the item with a matching partition key, which is the first shoe in the table, and then maps that item to shoe01, an instance of our Shoe class. We have successfully loaded the first shoe!

## Implement functionality that retrieves a unique item by hash and range key from a provided DynamoDB table.
Loading items with a partition and sort key:

```java
DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
Song song01 = mapper.load(Song.class, "Black Eyed Peas", "I Gotta Feeling");
```
First, we create a new instance of DynamoDBMapper. Then, we create a new instance of the Song class, song01, and specify the Song class as the POJO we're mapping to. The only difference is that this time we are specifying both the partition and the sort key. As seen in our table above, the partition key for our first song is "Black Eyed Peas" and the sort key is "I Gotta Feeling." Once that item is found using the key pairing, it will be loaded into the Song instance, song01. 
>Note: It's important to remember that the load() method is used to get a singular item from a table using a unique key, so if a table has a composite primary key, you must use the unique key pairing of partition and sort key. 


## Implement functionality that creates an item in a provided DynamoDB table.  → save()
```java
DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
Song song = new Song();
song.setArtist("Linkin Park");
song.setSongTitle("In the End");
song.setGenre("rock");
song.setYear(2000);
song.setFavorited(false);
mapper.save(song);
```

We're setting up the mapper again in the first line, so let's move on to the next line. This line creates song, a new instance of the Song class. The next few lines utilize the setter methods to set the values we want on our new object. Unlike the load() method where you explicitly pass in the key values, the save() method looks for the key values that are set in our Song instance. Since the annotations in our Song class say that artist and songTitle are the key values, the save() method knows that "Linkin Park" and "In the End" are the key values for the instance by looking in the object itself using the getter methods. The last line of code uses the DynamoDBMapper method, save(), to save the new song we created to our Songs table as a new item. 

## Implement functionality that modifies an existing item in a provided DynamoDB table.  → load() + save()
It's possible to update an item using the same DynamoDBMapper save() method we used to create it. The behavior of the save() method depends entirely on whether the primary key values for the item already exist in the table. For a table that uses only a partition key as the primary key, the save() method checks if an item already exists in the table that has the same partition key value as the item being saved. If it does, it will update that item instead of creating a new one. For our Songs table, which uses a composite primary key of partition + sort key, the save() method checks if an item already exists in the table that has the same partition and sort key as the item being saved. If it does, it will update that item instead of creating a new one. 

To update the Linkin Park song "In the End" in our table, we could create a new song instance that's the same as the item that already exists except the favorited value is true. The code for updating the item in this way looks like the following:
```java
DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
Song song = new Song();
song.setArtist("Linkin Park");
song.setSongTitle("In the End");
song.setGenre("rock");
song.setYear(2000);
song.setFavorited(true);
mapper.save(song);
```

But when we're updating we don't want to have to provide all the same values again, even if they aren't actually changing. That's why instead of using the save() method alone, like we did in the previous example, we generally want to use the load() and save() method together to safely update an item. We'll first load() the item to get all the existing attributes, and update only the ones we want in code. We can rewrite the previous code to get an item using the load() method, modify only necessary fields, and then use the save() method to safely update the item, knowing the other fields are unchanged. We could also add error handling to throw an exception if the load() method returns a null value, meaning that the song we attempted to load does not exist in the table.

```java
DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
Song song = mapper.load(Song.class, "Linkin Park", "In the End");
if (Object.isNull(song)) {
    throw new Exception("Couldn't find a song with those key values);
} else {
    song.setFavorite(true);
    mapper.save(song);
}
```

#
## DynamoDB Tutorial
AWS offers a digital curriculum that teaches you how applications interact with Amazon DynamoDB through its API and related AWS services. Through AWS Skill Builder, you can take all of the material above a step further and learn how to create and interact with DynamoDB tables and indexes and how to optimize databases for monitoring, performance, and security. You can also learn how to architect applications using DynamoDB design principles.  See [AWS Skill Builder](https://explore.skillbuilder.aws/learn/course/external/view/elearning/1525/developing-with-amazon-dynamodb)






