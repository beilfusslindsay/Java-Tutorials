# [DynamoDB - Delete](https://aws.amazon.com/dynamodb/?nc2=h_ql_prod_db_ddb)
<br>


## DeleteItem - [API Reference](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html)
<br>

Deleting an object from a DynamoDB table is straightforward. Whether the entry being deleted has a partition key or a partition + sort key, you will write the delete method like “mapper.delete(object)”. However, you need to make sure the entry has been stored or loaded correctly into an object. That could look like:
```java
Object obj = new Object();
obj.setPartitionKeyVariable(“partition key”);
mapper.delete(obj);
```
  
By instantiating an object that’s mapped to the DB table, and setting it’s variable (that’s mapped to the DB partition key) to a value that exists on the DB table, you are then able to use that object as an argument in the delete() function and remove it from the table. Since delete() is idempotent, there will be no message or error sent whether the entry existed or not. If it was there, it will be quietly removed, but the user will still not receive a message. If it didn’t exist, the user can try and delete this phantom entry a million times, but will still not receive a message. This is because delete() is idempotent.

<br>
<br>
 
## Conditions for Deleting
<br>

Conditional deleting allows you to set parameters for what gets deleted, and what does not. This could be helpful if you have a table of clothing items, with an attribute of sold status, and only want to delete entries that sold. You can then condition the delete function so it only deletes entries which have their status set to “sold”, or something similar. This is made possible by the DynamoDBDeleteExpression class. See the following setup:

```java
DynamoDBMapper mapper = new DynamoDBMapper(DynamoDBClientProvider.getDynamoDBClient());
Clothing boots = new Clothing();
boots.setId("12345");

try {
    DynamoDBDeleteExpression deleteExpression = new DynamoDBDeleteExpression();
    Map<String, ExpectedAttributeValue> expected = new HashMap<>();
    expected.put("status", new ExpectedAttributeValue(new AttributeValue("received")));
    deleteExpression.setExpected(expected);
    mapper.delete(boots, deleteExpression);
} catch (ConditionalCheckFailedException e) {
    System.out.println(e);
}
```

<br>

> Note: See [Using expressions in DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.html)
<br>

The code sample above sets up a DynamoDBMapper, instantiates a Java instance of the DynamoDB table, and sets it’s partition (or partition + sort) key. It also sets up a try/catch block, which catches the [ConditionalCheckFailedExcpetion](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.Errors.html) if the item being deleted fails to meet the expected condition.

 
In more detail, we first start by instantiating an instance of DynamoDBDeleteExpression in the try. This class enables adding options to a delete operation, making it more flexible and dynamic. We then instantiate a new [HashMap](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/HashMap.html), with key String and value ExpectedAttributeValue. ExpectedAttributeValue represents a condition to be compared with an attribute value, and can be used with DeleteItem, UpdateItem, and PutItem operations. The ExpectedAttributeValue accepts an AttributeValue, which is the value you expect your attribute to match to pass. We then add a key/value pair to the HashMap, containing the key (attribute name) and value (attribute value which must be met for this to be true) and then ```setExpected()``` of DynamoDBDeleteExpression to that HashMap. We’re then able to add the DynamoDBDeleteExpression as a 2nd parameter to the delete() function, setting a condition for delete.

<br>
<br>

## Iterators and Delete
<br>

[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html) is an Interface which belongs to the [Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html) framework and is used to retrieve items one by one. They’re useful because we can use them to access or remove elements in a collection, and because they have cursors to track the current place in the collection.

```
Boolean hasNext() – returns true if iterator has at least one more element to iterate through
object next() – returns next element as long as hasNext() returns true; throws NoSuchElementException if hasNext() is false
void remove() – removes current element in the collection
```
<br>

> Note: ```remove()``` throws IllegaStateException if remove() is called without a value accessed (can happen if you call remove() twice in a row or call remove() at the start of the iterator)

<br>
<br>

Calling ```next()``` will move the iterator to the right of the collection by one, and return the element it passed over. For instance:

|a.b.c.d.e

Calling ```next()``` on the above will shift the cursor to the right and return “a”

a|b.c.d.e

<br>
<br>

## ListIterator 
<br>

[ListIterator](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html) gives the ability to move forward and backward, and provides more functionality than a standard Iterator, but can only be used on Lists (hence, ListIterator). It has the original ```hasNext()```, ```next()```, and ```remove()```, and four new methods:

<br>

```
object previous() – same as next(), but going backwards, returns NoSuchElementException if hasPrevious() is false
boolean hasPrevious() – same as hasNext(), but going backwards
void add() – adds object immediately before the cursor (if next() was used), or after the cursor (if preivous() was used)
void set() – will replace element returned by previous() or next() with specified element (this means it can either replace the element before or after the cursor, depending on last method called). 
```

<br>

>Note: Throws IllegalStateException if ```set()``` is called before ```previous()``` or ```next()``` has been called (since there’s no returned value to act upon). If ```add()``` or ```remove()``` is called, ```next()``` or ```previous()``` must be called again before ```set()``` can be called. Simply put, ```next()``` and ```previous()``` retrieve a value, and this value is “used up” by either add, remove, or set and a new value must be retrieved before these methods can be called again.

<br>

Iterators allow us to add/remove/set values in a collection, which we can’t normally do in a for loop:
```java
//instantiating an ArrayList “list” that has a full set of elements
for (ListIterator i = list.listIterator(); i.hasNext();) {
    System.out.println(i.next() + " ");
}
```

<br>

In the above, we are mimicking a for loop (int I = 0 becomes ListIterator I = list.listIterator() and i++ becomes i.hasNext())
 
Using a while loop:
```java
ListIterator i = list.listIterator();
while (i.hasNext()){
    System.out.print(i.next() + " ");
}
```

<br>

>Note: The while loop is a more common use for iterators because ```hasNext()``` is a [Boolean](https://docs.oracle.com/javase/8/docs/api/java/lang/Boolean.html), making it a perfect fit
 

