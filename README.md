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
- Are you building a website?
- Are you building a SPA?
- Are you building a mobile app?
- If not, why are you doing it?

### How does HTTP work?

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
        - Content-Length: Size of content
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
    - Separation of client and server
    - Servers are stateless
    - Cacheable requests
    - Uniform interface

**Problems of REST**

REST concepts and contextualization can be extremely difficult to embody and can be a stumbling block for some developers who struggle to apply them pragmatically in real-world applications.

- Too difficult to be qualified as "REST"
- Dogma of REST vs Pragmatism
- Structured architectural style
- The need to be productive

**Why Design your API first?**

- Extremely difficult to fix an API after publishing
- Too easy to add ad-hoc endpoints
- Help understand the requirements
- Well-designed APIs have longer maturity and lifespan

**Parts of APIs**

|    VERB      |     URI (Query String)    |
|--------------|---------------------------|
|    GET       |   https://restdesign.dev  |
|              |        Headers            |
|              |    Request Body           |

**What are URIs?**

- URIs are just paths to resources
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

- Not required, it is used for non-resource properties.

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

#### Design Entities

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

**Design structure**

- Members Names
    - Should not expose Server Details
        - I prefer camelCasing versus Pascal
        - If objectionable 
        - At least be consistent so the API user is not confused.
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

**Design Association**

In REST API design, Designing Associations (often referred to as Modeling Relationships) is about representing the relationships between different resources (data entities) in your API's URL structure and responses.

Design Association is how you represent links or references between resources—like how a customer has many orders, or a project belongs to a company.

In raw terms, it is the idea of utilizing URI paths to get objecst that are related to one another. 

| Association Type | Description                                           | Example Endpoints                                 |
|------------------|-------------------------------------------------------|---------------------------------------------------|
| One-to-One       | A user has one profile                                | `GET /users/123/profile`                          |
| One-to-Many      | A customer has many orders                            | `GET /customers/123/orders`  <br> `POST /customers/123/orders` |
| Many-to-Many     | Students enrolled in many courses and vice versa      | `GET /students/456/courses` <br> `GET /courses/789/students`   |


**Paging**

- Lists should support paging
- Query strings are commonly used for paging
    - `/api/tickets?page=2&pageSize=24`
    - 💡 TIP: PageSize of 25 as long as they are in rows of 5is going to look clean, 24 is enumerated of 12 and it would work well for different size screens
    - Use a wrapper to imply paging

- Server side logic

    ```
    -- To fetch data for the page
    SELECT * FROM tickets
    LIMIT 10 OFFSET 20;  -- For page 3 with pageSize 10

    -- To get the total count of items
    SELECT COUNT(*) FROM items;

    ```

- Empty Results: If no results are found for a page, return an empty results array.

- Invalid Page: If a page number is too large (i.e., out of range), return an empty page or a 404 error, depending on your business logic.

- Default Paging: If no page or pageSize is specified, you can default to page=1 and pageSize=10 (or another default).

**Design Error Handling**

- Dont Return just the status code
- Return an object with error information
    - `400 Bad Request`
    - `{ Error: "Failed to supply id}`
- For common errors you might not need to return any additional information.
    - ` 404 Not Found`

Example: 

- Request ℹ️

        ```
        POST {{site}}/api/customers
        Accept: application/json
        Content-Type: application/json

        {
            "companyName": "", 
            "contact": ""
        }

        ```


- Response 📦

        ```
        {
            "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
            "title": "One or more validation errors occurred.",
            "status": 400,
            "errors": {
                "CompanyName": [
                "'Company Name' must not be empty.",
                "The length of 'Company Name' must be at least 5 characters. You entered 0 characters."
                ]
            }
        }

        ```


- Request ℹ️

        ```
        POST {{site}}/api/customers
        Accept: application/json
        Content-Type: application/json

        {
            "companyName": "Box", 
            "contact": ""
        }

        ```


- Response 📦

        ```
        {
            "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
            "title": "One or more validation errors occurred.",
            "status": 400,
            "errors": {
                "CompanyName": [
               "The length of 'Company Name' must be at least 5 characters. You entered 3 characters."
                ]
            }
        }

        ```

**Design Caching**

HTTP caching is a way to make websites and APIs faster by saving copies of responses so they don’t have to be sent from the server every time. When you request something like a webpage or API data, the browser or a middle server (like a CDN) can store the response for a while. If you ask for the same thing again soon, it can give you the saved version instead of bothering the server again—saving time and reducing traffic. Caching uses headers like Cache-Control, ETag, and Last-Modified to know when to reuse or refresh the saved data.


            - Request:
                - GET
                - Version: last_xyz
                - Hello World!
🖥️ ===============================================>🗄️

               

            - Response:
                - 304 Not Modified
🖥️ <===============================================🗄️

---

            - Request:
                - PUT
                - If-Match=last_xyz
                - Hello World!
🖥️ ===============================================>🗄️

            - Response:
                - 401 Precondition Failed
🖥️ <===============================================🗄️


**Entity Tags (ETags)**

One of the most common ways people handle this is with Entity Tags. 

Entity Tags (ETags) are a way for servers to tell if the content you're asking for has changed since the last time you checked. When a server sends a response, it includes an ETag—a unique ID (like a fingerprint) for that version of the content. Next time you request the same resource, your browser can send that ETag back. If the content hasn’t changed, the server just replies with a 304 Not Modified status—saving bandwidth and loading faster. If the content has changed, the server sends the new version with a new ETag.

- Strong and Waek Caching Support
- Returned in the Response
    ```
    HTTP/1.1 200 OK
    Content-Type: text/xml;
    Date: Sun, 23 May 2013
    ETag: W/"4893023942098"
    Content-Length:639

    ```
- GET 
    - Request with If-None-Match 
        - `If-None-Match: "4893023942098"`
    - Use 304 to indicate that it's cahced
        - `HTTP/1.1 304 Not Modified`

- PUT or DELETE
    - `If-Match: "4893023942098"`
    - `HTTP/1.1 412 Precondition Failed`



