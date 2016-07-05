---
layout: post
title: REST Design - Choosing the Right HTTP Method
comments: True
excerpt_separator: <!--more-->
---

One of the challenges when designing a REST API is choosing the right HTTP method (*GET*, *PUT*, *POST* etc.) that corresponds with the operation being performed. Some people incorrectly assume that they can freely choose any method as long as the client and the server agree on it. This is wrong because a request passes through many intermediaries and middleware applications which perform optimizations based on the HTTP method type. These optimizations depend on two key characteristics of HTTP methods: [idempotency and safety]((http://codeahoy.com/2016/06/30/idempotent-and-safe-http-methods-why-do-they-matter/)), which are defined in the [HTTP specification](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

**Safe HTTP Methods**: Safe methods aren't expected to cause any side effects. These operations are read-only. E.g. querying a database.

**Idempotent HTTP Methods**: Idempotent methods guarantee that repeating a request has the same effect as making the request once.

Idempotency and safety are properties of HTTP methods that server applications must correctly implement. This means if you are implementing an operation and choose an idempotent HTTP method to invoke the operation, you must ensure that the implementation returns the same result if invoked once or multiple times for the same input.

<!--more-->

### GET: Idempotent & Safe

GET requests are used for retrieving information. These requests must be idempotent and **safe**: any operation invoked using GET must not alter the state of any resource.

```javascript
GET /books
```

To get a specific book,

```javascript
GET /books/<title>
```

### POST: Non-Idempotent

POST requests are not idempotent. They are used for creating new resources or updating existing ones. For example, suppose we have a resource called `Student` with the following attributes: `name`, `college`, `major` and `gpa`. To enroll a new student, we can use POST to create a new resource:

```javascript
POST /students/ // Create a new student
{
  "name": "Michael Scarn",
  "college": "Stanford",
  "major": "computer science"
}
```

**POST requests are allowed to perform partial updates**. For example, to update the GPA of a student, we'll make the POST request on the specific record (given by the `student_id`) and only supply the attribute we want to update:

```javascript
POST /students/<student_id>
{
  "gpa": "3.9"
}
```

In the above example, if the `student_id` doesn't exist, the application should return `HTTP 404: Not Found` error.

### PUT: Idempotent

PUT requests are idempotent. This means that identical requests can be repeated multiple times, and the state of the resource on the server doesn't change any further after the first request. PUT can be used to create a new resource or update an existing one. For updates, **PUT requests must contain all the attributes of the resource, unlike POST requests which can have partial attributes**. Here's a PUT request to update a student's GPA:

```javascript
PUT /students/<student_id>
{
  "name": "Michael Scarn",
  "college": "Stanford",
  "major": "computer science",
  "gpa": "3.9"
}
```

In the above example, if the `student_id` in the PUT request doesn't exist, the application may create a new record and assign it the `student_id`.

If you are wondering why PUT requests must have all the attributes and not just the one we want to update, consider the following hypothetical situation in which we allow partial updates in the PUT request. Suppose that a student is switching majors. The client issues two PUT requests: the first one to update student's GPA for the existing major and a second request to update the major and reset the GPA. The PUT requests are made in the right order:

```javascript
PUT /students/<student_id> //Partial update: Violates idempotency contract
{
  "gpa": "3.85"
}
```

```javascript
PUT /students/<student_id> //Partial update: Violates idempotency contract
{
  "major": "engineering",
  "gpa": "0.0"
}
```

Assume that the request for the second requests arrives before the first one which is lost due to a network error. Because the client thinks that PUT requests are idempotent, it may retry the first request a second time, that will incorrectly update the student's GPA to 3.85 for the new major, leaving the resource in an *inconsistent state*. It could be very hard to trace and breaks the idempotency contract: multiple invocation result in different states.

```javascript
PUT /students/<student_id>
{
  "name": "Michael Scarn",
  "college": "Stanford",
  "major": "engineering",
  "gpa": "3.85" // GPA updated for the wrong major!
}
```
**Partial updates are important especially if the resource contains a lot of attributes. It is wasteful to first fetch the resource using GET and then send all the attributes when the goal is to update just a few**. In reality, a lot of developers allow partial updates with PUT, which is a violation of the idempotency contract. For partial updates, use POST, or as we'll later see, PATCH.

#### POST v PUT for Updates: It Boils Down to Idempotency

There is a common [misconception](http://www.tbray.org/ongoing/When/200x/2009/03/20/Rest-Casuistry) that it isn't RESTful to use the HTTP POST method to update existing resources. People who hold this view suggest that one should always use the PUT method for updates and POST for creating new resources. This isn't quite right. The REST standard doesn't stop us from using POST requests for updates. In fact, it doesn't even talk about it because **idempotency and safety guarantees are properties of the HTTP protocol, not of the REST standard**. [Roy Fielding](http://roy.gbiv.com/),  [writes](http://roy.gbiv.com/untangled/2009/it-is-okay-to-use-post):

> Some people think that REST suggests not to use POST for updates.  Search my dissertation and you won’t find any mention of CRUD or POST. The only mention of PUT is in regard to HTTP’s lack of write-back caching.  The main reason for my lack of specificity is because the methods defined by HTTP are part of the Web’s architecture definition, not the REST architectural style. [...] For example, it isn’t RESTful to use GET to perform unsafe operations because that would violate the definition of the GET method in HTTP, which would in turn mislead intermediaries and spiders.

Therefore, **the choice between PUT and POST boils down to one thing: idempotency guarantee of these methods**. Because PUT is idempotent, clients or intermediaries can repeat a PUT request if the the response for the first request doesn't arrive on time, even though the request may have been processed by the server. In order to stay idempotent, PUT requests must replace the entire resource and hence must send all the attributes. For partial updates, POST or PATCH (non-idempotent methods) must be used.

### DELETE: Idempotent

DELETE requests are idempotent and are used for deleting a resource. One common confusion people have with DELETE calls is what type of HTTP status code to return on repeat calls. Some people assume that because DELETE is idempotent, it must always return the same HTTP status e.g. `HTTP 200`. This assumption is wrong. Although there is no harm in returning the same status code, if your use case requires it, **returning a different status code like the `HTTP 404: Not Found` doesn't violate the idempotency contract**. Idempotency doesn't concern itself with what is returned to the client. It refers to the state of some resource on the server. So it is perfectly valid to return `HTTP 200: OK` on the first delete call, and `HTTP 404: Not Found` on subsequent ones, since in both cases, the resource is deleted and its state isn't changed on the server side. You might also use `HTTP 204: No Content` if the response body is empty.


### PATCH: Non-Idempotent

I like to think of PATCH as the non-idempotent cousin of the PUT request. Because it is non-idempotent, it could be used partial updates:

```javascript
PATCH /students/<student_id> //Partial update: OK
{
  "gpa": "3.85"
}
```

It is up to you whether you want to adopt PATCH for partial updates or use POST.

Here's a table summarizing the results:

| HTTP Method | Idempotent?      | Operation?   
| ----------- | ---------------  | --------- |
| GET         | yes              | Retrieval or query.      |
| POST        | NO               | Create or update resources. Partial updates are allowed.        |
| PUT         | yes              | Create or update resources. Partial updates are not allowed.      |
| PATCH       | NO               | For partial updates.       |
