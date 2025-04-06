
# REST API Best Practices


## The History of Distributed APIs

Distributed computing has evolved significantly over the decades, shaping how systems communicate and scale. It began in the 1970s with foundational concepts like Remote Procedure Calls (RPC), messaging systems, and queues, which enabled basic inter-process communication. By the 1980s and 1990s, the rise of object-oriented programming introduced APIs such as COM, DCOM, CORBA, and Java RMI, allowing applications to interact more seamlessly across distributed environments. The 2000s marked a shift toward web-based APIs, with XML HTTP, REST, and SOAP becoming the backbone of service-to-service communication over the internet. In the 2010s, modern technologies like GraphQL and gRPC emerged, offering more efficient, flexible, and performant ways to query and transmit data. This progression reflects a continuous drive toward more scalable, interoperable, and developer-friendly approaches to building distributed systems.

**Dawn of distributed computing**

Communicating between two computers is not a new problem it dates back to almost 50 years ago. 

- 1970 
    - RPC
    - Messaging
    - Queues

**Object-oriented APIs**
- 1980
- 1990
    - COM
    - DCOM
    - CORBA
    - JAVA RMI

**Web-based APIs**
- 2000
    - XML HTTP
    - REST
    - SOAP
- 2010
    - GraphQL
    - gRPC
- 2020

**Do you really need a web API?**
    - Are you bilding a website?
    - Are you building a SPA?
    - Are you building a mobile app?
    -If not, wht are you doing it?



### How does HTTP work?**


- **Client** ➡️ makes a request to the ➡️ **Server**

- **Server** ➡️ respond to the client with info ➡️ **Client**

            - Request:
                - verb    - What Action to take?
                - headers  - Info About the request
                - content - The request data
🖥️ ===============================================>🗄️
               
                - POST
                - Content Length: 12
                - Hello World!

---
            - Response:
                - status Code - Did it work or not
                - headers     - Info About the response
                - content     - The response data
🖥️ <===============================================🗄️
                
                - Status 201 
                - Content Type: Text
                - Hello World!


**Request Deconstructed**

- verb
    - Action to perform on the server
        - GET: Request Resource
        - POST: Create Resource
        - PUT: Update Resource
        - PATCH: Update Partial Resource
        - DELETE: Delete Resource
- headers
    - Metadata about the request
        - Content-Type: The format of the content
        - Contet-Length: Size of content
        - Authorization: Who's making the call
        - Accept: What type(s) can accept
        - Cookies: Client data in the request
        - Custom headers or Additional info can be passed
- content
    - Data to send to server
        - HTML, CSS, JavaScript, XML, JSON
        - Content is not valid with some verbs, like Get
        - Information to help fulfill request
        - Binary and blobs common (e.g. jpeg)

**Response Deconstructed**

- status code 
    - Operation Status
        - 100-199: Informational 
        - 200-299: Success
        - 300-399: Redirection
        - 400-499: Client Error
        - 500-599: Server Errors
- headers
    - Metadata about the response
        - Content-Type: The format of content
        - Content-Length: Size of content
        - Expires: When to consider stale
        - Cookies: Passenger data in the request
        - More Headers or Custom Headers ...

- content
    - Content 
        - HTML, CSS, JavaScript, XML, JSON
        - Binary and blobs common (e.g. jpeg)
        - APIs often have their own types

### What is REST?

**REpresentational State Transfer**
- Concepts include: 
    - Seperation of client and server
    - Servers are stateless
    - Cacheable requests
    - Uniform interface.


**Problems of REST**
REST concepts and contextualization can be extremelly difficult to embody and can be a stumbling block for some developers who struggle to apply them pragmatically in real-world applications.

- Too difficult to be qulified as "REST"
- Dogma of REST vs Pragmatism
- Structure architectural style
- The need to be productive

**Why Design your API first?**

- Extremelly difficult to fix an API after publishing
- Too easy to add ad-hoc endpoints
- Help understand the requirements
- Well-designed APIs have longer maturaty and lifespan.


**Parts of APIs**

|    VERB      |     URI (Query String)    |
|--------------|---------------------------|
|    GET       |   https://restdesign.dev  |
|              |        Headers            |
|              |    Request Body           |


**What are URIs?**

- URIS are just paths to resources
    - restdesign.dev/people
- Query strings for non-data elements
    - format, sorting, searching, etc.

**What are resources**

- Resources are real-world entities that the API exposes and manages
    - Examples: People, invoices, payments, products, etc.

- Each resource has:
    - A unique URI

    - A representation, usually in JSON or XML

- Resources are the core context for all interactions in REST APIs—they represent "what" is being acted on.

**Identifier in URIs**

- Use unique identifier 
- Does not have to be 'primary keys' but it needs to identify the resource and **result in one resource only!**

``` 
/customers
/customers/1
/customers/microsoft
/customers/msft
...
```

**Query strings**

- Not required, it is used for non-resource properties

```
/customers?includeProjects=true
/tickets?page=1
/products?category=shoes
/posts?page=2&limit=10
/orders?startDate=2024-01-01&endDate=2024-12-31
```


## How to design verbs?

| Resource            | GET (Read)                     | POST (Create)                     | PUT (Update)                    | DELETE (Delete)                |
|---------------------|--------------------------------|-----------------------------------|---------------------------------|--------------------------------|
| `/customers`        | Get list of all customers      | Create a new customer             | Update a batch of customers     | Error (No ID specified)        |
| `/customers/123`    | Get customer with ID 123       | Error (ID specified, POST not supported) | Update customer with ID 123     | Delete customer with ID 123    |


#### Idempotent

It means that making the same request multiple times has the same effect as making it once. For example, a GET request is considered idempotent only if it doesn't modify any data. If the request causes side effects, such as altering the data, it would no longer be idempotent.

**GET**: ✅ Idempotent – Fetching data doesn’t change it.

**PUT**: ✅ Idempotent – Updating a resource with the same data repeatedly doesn’t change the outcome.

**DELETE**: ✅ Idempotent – Deleting the same resource multiple times results in the same state (resource is gone).

**POST**: ❌ Not idempotent – Creating the same resource multiple times can result in duplicates.


#### Design Results

Design the entities based on how you want them to relate to each other. Don’t over-engineer it—just think about the API more broadly and how it will be used.

```
{
    "id": "9"
    "companyName": "McSilva & Sons",
    "contact": "Ian Lynch",
    "phoneNumber": "466-369-5083 x777",
    "email": "il@mcsilva.com",
    "addressLine1": "715 Benny Center",
    "addressLine2": null,
    "addressLine3": null,
    "city": "Fernandoburgh",
    "stateProvince": "MN",
    "postalCode": "77240-4410",
    "country": "USA",
    "projects": []
}

```

**Design results**
- Members Names
    - Should not exporse Server Details
        - I preferer camelCasing versus pascal
        - If objectionable 
        - At least be consistent so the API user is not confuse.
- Design Collections
    - Give the user more than just the information result. 
    - Adding totalCount, prevPage, nextPage, and so forth, can give the API user a better experience and context to what they are consuming.   

 ```
 {
  "totalCount": 100,
  "prevPage": "/api/tickets?page=1&pageSize=10",
  "nextPage": "/api/tickets?page=3&pageSize=10",
  "currentPage": 2,
  "pageSize": 10,
  "results": [
    {
      "id": 47,
      "employee": {
        "id": 7,
        "name": "Sarah Adams",
        "billingRate": 225
      },
      "employeeId": 7,
      "hours": 5,
      "billingRate": 225,
      "date": "2024-04-14T16:23:18.6925374+00:00",
      "project": {
        "id": 10,
        "projectName": "kaci.name",
        "customer": null,
        "startDate": "2024-11-22T07:59:41.8720483+00:00",
        "endDate": null,
        "customerId": 3
      },
      "projectId": 10,
      "workPerformed": "Try to navigate the RSS circuit, maybe it will navigate the virtual circuit!"
    },
    ....
  ]
}

```
## Advance REST Design


