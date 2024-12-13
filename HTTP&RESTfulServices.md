# HTTP and RESTful Services
The three main elements of a RESTful API are:
* **Client**: The software or application that requests a resource from the server
* **Server**: The software or application that controls the resource and responds to client requests
* **Resource**: The data or content that the server controls and makes available to the client

## Learning Objectives
* Given a use case fulfilled by a RESTful service, predict whether its endpoint should be invoked via a GET, POST, PUT or DELETE request.
* Given a description of a specific interaction with a RESTful endpoint, predict the HTTP response code class.
* Given a RESTful API’s documentation, submit a GET request to retrieve a resource.
* Given a RESTful API’s documentation, submit a PUT request to update a resource.
* Given a RESTful API’s documentation, submit a POST request to create a resource.

##
### Given a use case fulfilled by a RESTful service, predict whether its endpoint should be invoked via a GET, POST, PUT or DELETE request.
**GET**: the method retrieves a set of data from the RESTful service or retrieves a specific resource by identifier. e.g. GitHub API GET/users would return a list of users. See [REST API to get public and private information about authenticated users](https://docs.github.com/en/rest/users/users?apiVersion=2022-11-28)

GET/users [cURL](https://curl.se/) request example:
```
curl -L \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer <YOUR-TOKEN>" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/users
  ```
**POST**: the method creates a new element in the RESTful service.

**PUT**: the method updates an element in the RESTful service.

**DELETE**: the method removes an element in the RESTful service.

##
### Given a description of a specific interaction with a RESTful endpoint, predict the HTTP response code class.
HTTP response [status codes](https://httpwg.org/specs/rfc9110.html#overview.of.status.codes) indicate whether a specific HTTP request has been successfully completed. Responses are grouped in five classesand are defined by RFC 9110:

1. Informational responses (100 – 199)
1. Successful responses (200 – 299) 
1. Redirection messages (300 – 399)
1. Client error responses (400 – 499)
1. Server error responses (500 – 599)


##
### Given a RESTful API’s documentation, submit a GET request to retrieve a resource.
Use -I flag to see the headers  `curl -G -I https://developer.amazon.com`, but with flag -i you can see both the headers and the content

```
curl -G https://api.stripe.com/v1/customers \
  -u "sk_test_4eC39HqLyjWDarjtT1zdp7dc:" \
  -d limit=3
```
##
### Given a RESTful API’s documentation, submit a PUT request to update a resource.
### Given a RESTful API’s documentation, submit a POST request to create a resource.
The key difference between PUT and POST methods is that a PUT is restricted to create or update operations, while a POST operation may perform any type of processing. Unlike a POST, PUT operations may only operate on the resource identified by the URL provided.
```
curl -X PUT "https://nzbzh3a24d.execute-api.us-east-1.amazonaws.com/socialmediaexample/status?userId=1&statusId=1&status=Hello"
```
```
curl -X POST "https://nzbzh3a24d.execute-api.us-east-1.amazonaws.com/socialmediaexample/status?userId=1&status=Hello"
```
> Note: You can also do `curl -d` and it works the same as command `curl -X POST`, this is because POST is more common so -d is default POST `curl -d <data><url>`. If you don’t use -d, you can specify data in the URL / /?userId=1&number=3. If you use -d {userid:1;number:3}

##
### To send an HTTP request, the host name must be resolved to an IP address.
FQDN stands for fully qualified domain name, and it's the complete domain name for a specific computer or host on the internet. When making an HTTP request using a FQDN, an application must perform a DNS lookup to resolve the IP address? A DNS Lookup is like a contact list for the Internet and maps a FQDN to an IP address. When a user makes an HTTP request in a browser, the a DNS lookup `nslookup` is performed to resolve the FQDN to the corresponding IP address. If you want to lookup https://developer.amazon.com, in the terminal, enter `nslookup developer.amazon.com`.

```
C:\Users\patronfiltered>nslookup developer.amazon.com
Server:  publicdc.Public.krl.org
Address:  10.20.216.100
Name:    developer.amazon.com
Address:  44.215.133.6
```

> DNS lookup result explanation: the 1st line tells us the server the command was run from (DNS?), the 2nd line shows the IP address of the computer used to run the command, the 3rd line tells us the domain name of the website entered, the last line shows the IP address associated with that domain name.

> Note: do not include https:// because it’s not part of the domain

##
### Explain how ports enable a host to handle incoming requests for multiple services.
What is port? A port provides more details to an IP address in order to identify a specific process or a type of network service running on the computer at that IP address. Ports enable a host to handle incoming requests for multiple services running on it, such as website and email. If a port number is not shown in URL, does it mean it’s not being used? NO. The port number is not always shown but it’s always being used. Some port numbers are so standard that many applications will assume a port number unless you specifically override it to connect to a different port. e.g. the port used by web servers is port 80 (http) and port 443(https).

##
## Definitions
* RESTful API -  API (application programming interface) helps you understand how to interact with a server correctly.  A RESTful API (aka RESTful service, Representational State Transfer) is a document interface that allows two systems to request and send data over HTTP, i.e. a design pattern used by developers to create understandable APIs. Many web-based systems have RESTful APIs available for us, such as Twitter, Git and AWS. Even connecting to a website using a web browser is a RESTful transaction. All RESTful APIs are built on top of HTTP.
* HTTP - It stands for HyperText Transfer Protocol. a protocol is a shared and agreed upon common format for transmitting data between devices over the internet. HTTP is the underlying protocol used by the World Wide Web. i.e. a request-response protocol between a client and server. HTTPS is a secure version of HTTP but for our purposes works the same as HTTP.To transfer data between the server and the client we use HTTP messages. There are two types of HTTP messages: requests, which are sent by the client to retrieve information or trigger an action on the server, and responses, which are the server’s answer. One of the benefits of HTTP is that it provides a standard format for transferring these messages.

What’s the relationship between server and services? There can be many different services on one server that each have their own port assigned. i.e. a single server can be configured to host multiple services, each service would be waiting for requests on a unique port. Ports are like a specific apartment within a building, they allow users to connect to a specific service on a single computer.
What’s the most important part of any HTTP request? making sure it gets to the right server b/c if the server never receives your request then you’ll never receive a response. HTTP, FQDN, and a port are all used to get us to the correct server.
How to make HTTP request for specific information? URL (Universal Resource Locator)
URL breakdown: 1) “https://” is what defines the HTTP protocol that’s used for the request, 2)“docs.aws.amazon.com” is FQDN that will be used to look up the IP address,  3) “443” is the port for web server and would not be shown in your browser, 4) “/codecommit/” is the endpoint which is the particular resource on the web server (still fuzzy about what exactly is endpoint?), 5) “?id=docs_geteway” is the parameters that together direct you to a specific page or API method on the site.

What is IP address? IP stands for Internet Protocol. IP address is a numerical designation that’s assigned to each device connected to a computer network that uses the Internet Protocol for communication. The World Wide Web uses the IP and HTTP works on top of it. Without IP addresses, HTTP doesn’t know where to send a request. Basically IP address functions as the computer’s home address.
What is IPv4? It’s an IP address format Internet Protocol version 4. it’s four numbers each divided by a dot (.). each of the four numbers can range in value from 0 to 255. an example IP address is :192.168.1.1

How to organize domains? .com part is called the top level domain, this is the major grouping and is supposed to specify what type of domain it is. e.g. “com” specifies a company or business vs. “edu” specifies a school or educational site. “amazon” is the main domain and “developer” is the sub-domain. This is just a way or organizing domains so people know the developer site is part of Amazon.
In addition to a response code, what else will a successful response include? it will include the type of data being returned as specified in the HTTP response header. This response type helps the client to know how to handle the data being returned by the server. e.g. if the response type is text/html then the client would know that it’s formatted using HTML tags. If the response type is application/json then the client would knwo it’s JSON formatted text.

what is curl? it stands for client URL. curl is a command line tool for sending and receiving data using a wide variety of protocols, including HTTP.
what’s the curl command structure? curl <flags> <URL>
when use URL in the curl command, ‘http://’ is optional. i.e. ‘curl -G http://yualex.aka.corp.amazon.com:8000‘ is the same as ’curl -G yualex.aka.corp.amazon.com:8000’. as far as if you can use ‘https://’ it depends on the server configuration.  But when you do DNS loopup, you have to lose ‘http://’ as it’s not part of the domain name i.e. ‘nslookup yualez.aka.corp.amazon.xom:8000’ is the only correct way.
What are common curl flags? 
  1) -G: use the GET HTTP method for the request
  2) -d: specifies the data to pass in the body of the request, and will use the POST HTTP method for the request
     Q: do you only use -d for POST and PUT?
  3) -i: include headers in the output
  4) -I: only show headers in the output 
  5) -X PUT: -X changes the HTTP method based on the provided value, commonly used with -d
     Q: what does it mean by -X changes the HTTP method???????????
What is idempotence? Idempotence describes an operation that in response to the same input will always result in the system having the same end state.GET, PUT, and DELETE methods are used in RESTful services is idempotent. If we make multiple identical GET or PUT requests, then we can expect the result to always be the same. On the other hand, the way that the POST methods are usually implemented in RESTful services is not idempotent. if you make multiple, identical POST requests, you will not always get the same result. It’s important to realize that the HTTP methods are not inherently idempotent or non-idempotent. Idempotence is not sth built into the protocol, but when implementing a RESTful API it’s important to consider which method to use and the behavior that works best for your customer.
What does it mean to consume an API? To consume an API is to make sure of it in the program you are writing. The ease of consuming an API largely relies on the APIs documentation. API documentation is important because it contains information for the developer about how to effectively use and integrate the API.
What is URL parameter? A question mark is appended to the endpoint and the parameters are combined using an ampersand, &, as shown: ?userId=1&statusId=1.    "https://nzbzh3a24d.execute-api.us-east-1.amazonaws.com/socialmediaexample/status?&statusId=1&userId=1" 
If you get an error “missing authentication token” when doing a PUT command, it could mean you don’t have the right end point. if you want to update info for an item with id as partition key, the end point is the id name.
dash C is same as dash A, dash B
what’s the difference using grep and less? grep search for some string, provide all occurrences of string, less is viewing the file. less: shift + F
check your own logs in your local container: rde exec
curl -X tells the next is method, -G is default, -d default is POST
json is the default format for API
standard process is adding separate json block vs. put in as part of url parameter, that’s why we need -d flat for post and put. curl -d means POST b/c -d default is POST,  curl -X PUT (-d)  you have to put json data right aftrer -d
curl -d '{"username":"llna","answer":"a"}' https://yualex.aka.corp.amazon.com:8001 means creating
(i.e. a POST request) user and answer to the server. this is a successful POST request
make a PUT request to the server at port 8002. this server expects your usersname as a path parameter in the end point and the answer to your question in the body of the request, as a JSON string
curl -X PUT -d '{"answer":"confusing"}' http://yualex.aka.corp.amazon.com:8002/llna
POST request :
curl -X POST -d '{"question":"avocadotest4","multipleChoiceAnswers":["aa","bb"],"correctAnswerOption":"aa"}' http://localhost.a2z.com:1187/questions

Review Questions:
what’s the difference between an end point and a resource?
When making an HTTP request using a fully qualified domain name (FQDN), what does an application need to do first to resolve the IP address? A DNS loopup must be performed. a DNS lookup is like a contact list for the internet and maps a FQDN to an IP address. command: nslookup domainname
How a single server would be configured to host two different services, such as a website and an email server? each service would be waiting for request on a unique port. ports are like a specific suite or apartment within a building, they allow users to connect to a specific service on a single computer.
what’s a valid HTTP URL? http+domain name + endpoint + port number. e.g. https://kindle.amazon.com/rewards/:80
what does the “-u” flag represent? it provides a user and password
You're trying to get the data and headers from https://example.com/user for a user whose userId is 123. Write the appropriate curl command: 
you can put ?isDebug=1 in Amazon.com page as URL parameters, and it will show you all the services POC and file bugs to different teams on the detail page
GET and DELETE requests ignore data block 
how to use get request? 1) curl -G -i https://example.com/user?userId=123 : ? stops the path, before that is the endpoint 2) URL: https://example.com/user/123 
one IP address can have multiple domains  i.e. you can have two different domain names pointing to the same IP address


Lesson 2 remote debugging and logging
Securely connect to the command line on a remote host.
Use SSH (secure Shell). SSH is a communications protocol that allows you securely connect to another host over a network. once connected using SSH, you have shell running on that host that works just like opening up the terminal on your laptop.

Using SSH to connect to a remote host: if you had a host running at my-host.amazon.com, you could connect to that host by running >ssh my-host.amazon.com
//
ssh my-host.amazon.com *you use authentication and can have a terminal . after entering ssh, you’re connected to a remote host.

Why use SSH? 1)  e.g. Git → VersionSet → Alpha (port 80). Alpha is amazon.dev.com, then you can look into logging info in the remote host. your code  2) storage space, more powerful machine. basically have a second environment.

Connect to the command line in a local container.
rde stack exec <app-name> bash
<app-name> is the specific container
e.g. rde stack exec LlnaATACurriculumMusicPlaylistService bash
use “exit” command to exit local container after running rde stack exec <app-name> bash

Q: so ATAPlaylistService is a container???
Locate which log files in a directory structure include a provided text fragment.
grep -win “John Williams” ./*
grep -li Error *
grep -n error . (-n: number line)
grep -r .
Locate log entries that include a provided text fragment.
ATA explanation: finding where in the log file the log entries of interest are. it’s helpful to find a particular entry of interest, then view the log entries before and after to reconstruct what happened. -?
grep -win “John Williams” names.txt ?

Trace log entries to diagnose a reported problem.
trace the bread crumb to see what’s going on
B - before
A - after
C - context
Attach an IDE debugger to a service running in a local container.
one of the many tools we’ll use to debug

Use the AWS console to read a service’s Cloudwatch logs.
covered in the music playlist project. look in the pipeline run events, it will take you to Cloudwatch. get comfortable with it, but won’t be covered in the test.
Determine which log group contains a given log event type.
a. session ID to differentiate logging in a concurrent environment?
b. instead of having many logs in one file, e.g. separate application log, errors log. a way of organizing logs
c. log groups in CloudWatch? what is log group??
Add logging statements that facilitate future diagnosis.
a.the contextual information may be vital when trying to diagnose a fault e.g. what we’re doing and what caused the exception .
b. applying a standard format to your logged messages

Determine the log level to use when adding a logging statement.
INFO: general info. use cases: version, request received. 

WARN: WARN level message should be used to indicate the application faced a potential problem, however the user experience has not been affected in any way.
we use WARN level to log messages about unexpected event, but the application is able to continue. use case: the first connection failed, but the second connection passed. in the if statement?

ERROR: ERROR messages should be used to indicate that the application faced a significant problem and that, as a result, the user experience was affected in some way. 
we use this level to log messages when an error has occurred in the application. You can log an error and throw an exception. 

FATAL: the user experience was not just affected, it has entirely ceased! (i.e. the service is not running)

DEBUG: this log level is used to indicate that the logged message is to be used for debugging purposes
we should use this log level to log detailed messages to troubleshoot a problem. log.XML for debugging 

* INFO provides more general info and DEBUG provides specific targeted info. Debug has more info.  debug is turned off at first, once an error/warn happened, turn on the debug to look for targeted info.

Other things learned but not covered in LOs:
GREP (global regular expression print)  allow us to search multiple files for references to errors we see. 
FINDING TEXT IN A CERTAIN FILE
grep the data : search the in data file
grep -n the data : to turn the line number on use -n
grep -n ‘the’ data: find all occurrences containing the, same with or without single quotes ‘ ’
grep -n ‘ the ’ data: only matches ‘the’, not other words containing the like boothe

if you want to search if the file contains “Jane Williams” in names.txt file
grep “Jane Williams” names.txt //when there’re no results found, it will jump to the next line
grep “John Williams” names.txt //output: John Williams John Williamson

if you want exact match, use -w (meaning whole words)
grep -w “John Williams” names.txt //output: John Williams

grep -w “John W” ./*.txt  (search for all txt files in the current directory)
can you do “grep -w ”John“ ./”?

if you want grep not to be case sensitive, using -i (dash i) option
grep -wi “John Williams” names.txt //output: john williams John Williams (return both lower case version and normal version)
add line option
grep -win “John Williams” names.txt
if you want see a certain number of lines before and after the match 
see the 4 lines before the match, use -B 4
grep -win -B 4 “John Williams” names.txt //output shows 4 lines before John Williams
see the certain number of lines, say 4, after the match, use -A 4
grep -win -A 4 “John Williams” names.txt //output shows 4 lines after John Williams
see the lines before and after the match, use the upper case C
grep -win -C 2 “John Williams” names.txt //output shows 2 lines before and after the match John Williams

SEARCH FOR TEXT IN MULTIPLE FILES AT ONCE, use a asterisk * as a wildcard after the directory ./
grep -win “John Williams” ./* //output shows all matches in multiple files in the directory 
grep can’t search in directory/subdirectory in the folder. if you see “./Personnel: Is a directory” it can be fixed by  specifying search in text files by adding .txt after the wild card
grep -win “John Williams” ./*.txt //output shows all matches in txt files, didn’t try to search in the subdirectory 

Search for sth if you can’t remember where it’s located, i.e. search every single file and subdirectory in your project, use the recursive search -r (dash r ) option
grep -winr “John Williams” ./
output 

To clean up the output, remove forward slash /
grep -winr “John Williams”.

Note: be careful with recursive search as it could be be time-consuming if there’re too many subdirectories 

If you only want to see what files contain the match, use -l option
grep -wirl “John Williams” . //output shows only the file name containing the match in the directory 


if you want to see how many matches in each file, use -c option 
grep -wirc “John Williams” . //output also displays the number of matches in each file


LESS commands: let us read/view and search through individual files, within a file
show the content of the file from the start until the terminal allows, hit the down arrow key if you want to see more content , one line at a time. use space key to go thru page by next (hit space it will show the next page), B (capital B) to go to the previous page. if you want to go to the end of the file, shift +G,. If go to the top of the file, shift +g. i.e. capital G to go to the end, small g to the top.
less big.txt
If you want to search certain string in the file, use forward slahs /
/book  //highlight the book keyword you’re searching for 
press n (next)  for the next occurrence of the key word book

when searching down up using ?
?book
press n to find the next occurrence of the key word from down to up 

to quit LESS, use “ :q” , to view all less commands, use “man less”
Logging is the process of recording application actions and state to a secondary interface. secondary interface could be a file, web-service, email etc...
A structured approach to logging: good logging framework provide a simple classification system for log message b/c some state changes or actions performed by an application are more important than others. the typical classification system associates a log level with each item sent to the log. These log levels are WARN, ERROR, FATAL,  One for general information purposes INFOm and one for the purpose of debugging DEBUG. below are guidelines for each level:
INFO: to use this log level effectively, try to think about what general information would be useful for diagnosing an application error when the primary interface is not accessible. e.g. version information relating to the software, usage information (who is using the software), and what external services is application using (Databases, web-services)
WARN: This is the first level which indicates some form of application failure. no user experience impacted
ERROR: some user experience impacted
FATAL: server down
DEBUG
we want to separate logging with user interface. logging is the process of recording application actions 
./* is the same as *, both shows all the files within the directory 
logging info apache defines 5 logging levels (INOF, WARN, ERROR, FATAL, DEBUG). you can define your own levels but it’s best practice to stick with these
cd ../ go to the earlier directory 
don’t log in PII (personal identifiable information)
have to run “rde wflow run” every time you make any update in the source file, this way the update will reflect in the local container - aka ATAMusicServicePlayer?
A developer is debugging the cause of the error message “Error: Unable to reach login server” seen when running a program.  The error has been logged to the log1.txt file in the logs directory.  What command would the developer run to view the line that the error message occurs in and surrounding lines?  less logs/log1.txt’ then ‘/Error: Unable to reach login server
when to use Grep and Less?  grep search for some string, provide all occurrences of string; while less is viewing the file.
in general there’s no need to test that you rlogging statements are working correctly.
* is the wildcard character, which means executing the grep command and search in any filename. -r flag means we’re running a recursive grep which will search whatever directories are provided. . means staring in the current directory, recursively search in all directories.
you can use either single quote or double quotes when running grep commands (as long as you open and close with the same type). it can be helpful to choose one or the other in case you’re searching for a single quote or double quote, it will just make the search string simpler.
There’re two ways when searching for text in multiple files: 1) use the directory name directly: grep -win “text” log* (assuming log is the directory name) 2) grep -win “text” ./ (assuming your currently directory is log)
if you don’t use -r , grep won’t search text in the subdirectory, it will only search text in the files of the directory.
benefits of a local container: 1) containers let us easily deploy and run multiple services on one computer, rather than deploying them all to separate computers, 2) allow deploying exact same code to many servers. read: https://www.docker.com/resources/what-container
grep “error” * is the same as grep “error” ./* , excluding ./ infer that you are searching in the current directory 
what terminal command can be used to connect to a remote host? ssh. SSH (secure shell) is a communications protocol that allows you securely connect to remote host over a network. oNce connected using SSH, you have a shell running on that host that works just like opening up the terminal on your laptop. note RDE can be used to connect to your local container.
What terminal command can be used to search for a text snippet among multiple files at once? grep. grep allows us to use a wildcard character (asterisk) that will search for all files in the current directory. grep “needle” * will search for all files in the current directory for “needle”.
Which log level should we use to log general info to assist future troubleshooting? log.___("Received API Request [{}]", apiRequest);INFO
Which log level should we use to log message about unexpected events, but the application is able to continue? log.___("Expected stored user profile, but could not find any for user {}."
     + " Using default configuration", userId);WARN. We encountered something in our code that caused it to behave differently than how we expected, so it is useful to write this at a WARN level so it can be quickly viewed, but we don't know if we need to take action on it. It might indicate a bug, or it might indicate a user behaving in a way we didn't expect. However, multiple of the same WARN logs in a short period could indicate a problem with the application.
Which log level should we use to log messages when an error has occurred in the application? log.___("Received exception while saving orderId {} to the database", orderId, ex);ERROR.Use ERROR logs when its important to notify that something has happened in your application. When debugging a problem in an application, one of the first things to look at is any ERROR lines that have been logged recently. When writing an ERROR log, it's important to include any relevant information that led up to the error to assist in debugging.
Which log level should we use to log detailed messages to troubleshoot a problem?DEBUG. log.___("Sending message to endpoint: [{}], message: [{}], (message Length: [{}])",
     endpoint, message, message.getMessageLength());It's possible to log too much unrelated or loosely related information, making it difficult to debug when an error occurs in your application. DEBUG logs should be reserved for adding targeted information to troubleshoot a specific problem. Some services are even configured to not log DEBUG lines at all, unless explicitly overridden. Otherwise it's possible to "drown out" more critical logs like WARN or ERROR
less filename, (less test.txt) use / data to find data within one file
Lesson 3 Intro to DynamoDB 
Describe the benefits of using a database.
      1. protection: 1) each change/transaction/commit is stored in a transaction log, which is a history of the actions executed by the database system. If the database runs into trouble, it can often recover all data by reprocessing the transaction log from a last known good data. a database ensures that few, if any, transactions will be lost. 2)when error occurs, databases won’t perform partial writes. i.e. the database will not allow part of the item to be written, even in the case of an unexpected error. 3) A backup is an additional protection against data loss that most databases provide. A database backup stores a copy of the data so that it can be recovered later in case something goes wrong.
scalability: 1) one of the other benefits of databases is that they can be used by many users at the same time. the ability to handle multiple simultaneous requests is called concurrency, which describes multiple computations happening at the same time. 2) concurrency in databases helps manage users modifying data by restricting other users from making changes until the edits are committed
Identify a unique item in a provided DynamoDB table by its hash key.
a partition key is a primary key with a single value.
We nearly always want our key values to be immutable, meaning that the values never change no matter what other attributes change on the item.
primary keys can be used to distinguish between multiple items that are exactly the same, such as owning two identical shirts. 
Identify a unique item in a provided DynamoDB table by its hash and range keys.
a sort key can be used to order values that have the same partition key value. 
partition+sort key is also referred to as composite primary key. in order to identify a unique item in a partition+sort key table, you must specify both the partition and the sort key.
with a composite primary key, the partition key does not have to be unique.
What’s the advantage of using a composite primary key? 1) the partition key allows you to easily retrieve commonly needed groups of related items. 2) using a composite primary key also provides a data storing benefit. With a composite primary key, items with the same partition key are stored together on the same computer, sorted by their sort value, which can make related item faster to retrieve. 
Describe the benefits of distributed over non-distributed data stores.
What is distributed data-stores? distributed databases are databases that store data across multiple machines/
Benefits: 1) distributed data-store allow a much larger set of data to be stored in a single database. as data grow, additional machines can be added to store it all. 2) distributed databases also allow supporting more concurrent requests to access data, because each machine can handle data requests. 3) Distributed databases can also run across multiple geographic location to keep servicing requests even when an issue occurs in one location. In a distributed data-store model, if one location goes down or is experiencing errors, then the other locations can usually provide access to the same data with minimal performance loss.
Determine which Java types should be represented by a DynamoDB Number.
include both primitive and wrapper classes: Long/long, Integer/int, Double/double, Float/float, BigDecimal and BigInteger
Determine which Java types should be represented by a DynamoDB String.
String. A String is a sequence of characters, able to represent text or alphanumeric data (containing numbers and letters), such as IDs that consist of mixed numbers and letters.
Determine which Java types should be represented by a DynamoDB Boolean.
Boolean. The boolean type is useful for attributes only as true (corresponding to 1) or false (corresponding to 0).
Determine which Java types should be represented by a DynamoDB NumberSet.
Set<T>. For the DynamoDB NS, the Java Type is depicted as a generic type representing any of the primitive wrapper classes. the most commonly used numeric types in ATA will be Integer and Double.
Determine which Java types should be represented by a DynamoDB StringSet
Set<String>

Other
scalar type contains a single value
Set type represent multiple values. Sets are used when an attribute can have multiple unique values, just like a Java Set. But unlike a Java Set which can hold any kind of object, a DynamoDB Set can only be a Set of Strings or a Set of Numbers.
can you store JSON object in DynamoDB? yes it’s String
set of type options may vary for different data bases
best practice: partition key should have no meanings to consumers. if Partition key is unique, you don’t need sort key. use partition+sort when you can’t find unique partition key???? when designing table, make sure there’re no collisions (duplicates). once creating a table, you can’t change partition (+sort) key.
Java map to DynamoDB type
DynamoDB is HashMap (a huge one). hasing the primary key
what could we use a database for? email. where does it store? could be file system but could lose data. Email client. what tables should we have? Messages with attributes To, From, Content. Date; Mailbox table. 1..* (one to many relationship); Contact table could be populated from Messages table To/From attributes. Each entity has many instances of the table?
Is it always best practice to use the existing attributes? depends on the use case
for composite key, partition and sort key are hierachy
what’s the advantage of using DynamoDB over a traditional relational database service? Factors that are often taken into consideration includes cost, ease of use, maintenance, familiarity, DynamoDB is a managed service meaning AWS takes care of a lot of the setup, kind of like AWS Lambda lets us run our code without needing to create a server.
terminology: in the context of DynamoDB, you may see entity, item, and record all refer to a single item or entry, inside a DynamoDB table.
keys in DynamoDB are immutable meaning they cannot be changed once saved. attempting to store an item with a changed key will end up creating a new item with that modified key.
an item is a group of related data held within the same structure in Dynamo, representing an instance of an entity.

