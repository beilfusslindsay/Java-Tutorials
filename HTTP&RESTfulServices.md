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

> Note: There can be many different services on one server that each have their own port assigned. i.e. a single server can be configured to host multiple services, each service would be waiting for requests on a unique port. Ports are like a specific apartment within a building, they allow users to connect to a specific service on a single computer.

* cURL - command line tool for sending and receiving data using a wide variety of protocols, including HTTP. curl command structure? curl <flags> <URL>
-G: use the GET HTTP method for the request
-d: specifies the data to pass in the body of the request, and will use the POST HTTP method for the request
-i: include headers in the output
-I: only show headers in the output 
-X PUT: -X changes the HTTP method based on the provided value, commonly used with -d

* Idempotence - describes an operation that in response to the same input will always result in the system having the same end state. GET, PUT, and DELETE methods are used in RESTful services are idempotent. If we make multiple identical GET or PUT requests, then we can expect the result to always be the same. On the other hand, the way that the POST methods are usually implemented in RESTful services is not idempotent. if you make multiple, identical POST requests, you will not always get the same result. It’s important to realize that the HTTP methods are not inherently idempotent or non-idempotent. Idempotence is not built into the protocol, but when implementing a RESTful API it’s important to consider which method to use and the behavior that works best for your customer.
