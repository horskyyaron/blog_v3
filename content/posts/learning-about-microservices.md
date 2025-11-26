+++
title = 'Learning About Microservices'
date = 2025-11-19T10:03:53+02:00
draft = false
+++

This is a ongoing summary of the book *"Building Microservices"* by Sam Neuman.

# Chapter 1 - What are microservices

From the book:

> Microservices are small, autonomous services that work together. 

Breaking it down we get:

* Small - in terms of code base and team.
* Autonomous - separate entities, deployed as a independent service.

Communication between services are through network calls.

Thoughts are made about what is exposed vs hidden.

## Key Benefits

### Technology Heterogeneity

Since each service is independent, we can choose the right tools for the job,
for example:

-  database
-  language

### Resilience 

One service going down won't make the whole system crash.
NOTE: the system should be ready for this cases and handle them correctly.

### Scaling

With smaller services, in contrast to monolith, we can just scale the part of the system that 
requires more resources.

### Ease of deployment

We can deploy just the service that changes were made in, not the whole system.

### Optimize for replaceability

The cost of replacing one service is not that expensive since it is small in size 
and complexity.
And since requirements are being changed all the time, and new technology is being
created all the time, the system must be ready to embrace changes!
Microservices are optimize for that uncertainty

# Chapter 2 - The Evolutionary Architect

The term "Architect" has problems in the world of software.
It was borrowed from an existing profession when the architect has accountability
for example: if the building collapsed, the architect is the responsible persona.

But with software, things change over time, all the time, so you can't have a "correct"
timeless solution, since the future is unknown.

When we create documentation, and diagrams on diagrams describing the perfect system, that is a lie.

We should think of ourselves as 'City planner' as we care about zones in the system.

Requirements and resources are rapidly changing (money, technology)

Once our product reaches the client, we will reveal how the user actually uses the product,
and what we thought will be the main part of the system, might not be, thus we need
to differently allocate our resources.

We should plan to allow for changes.

Avoid over specifying things.

> the system should be habitable for developers to

We should worry less about what happen inside the zones and more between the zones.
How do they talk to each other.

## Principled approach

In Microservices we have many decisions we can make: database, language, this framework or the other.

What should we do then? Framing

We should set principles and practices. 

Principles - set of rules we made in order to achieve some larger goal.
Practices - how we ensure our principles are being carried out. A set of detailed practice
guidance for preforming a task.

Defining the required standard - what is a good citizen?

- monitoring (system health, push vs pull approaches)
- interfaces. (HTTP, verbs or nouns? pagination resources? Versioning of endpoint?)
- safety. 

# Chapter 3 - How to model services

How to think about the boundaries of our microservices.

## What makes a good service?

1. loosely coupled
2. high in cohesion

### loose coupling

A Change in one service does not require a change in another.

A loosely coupled service knows as little as it needs about services it collaborates with.

Thus, we'll probably want to limit the number of different calls from one service to another.
This will reduce the change for tight coupling.

### High cohesion

Related behavior will sit together while different behavior sit elsewhere.

So we need to find boundaries in our problem domain that will help ensure that 
services of related behavior sits together.

## The bounded context

Any domain consists of multiple bounded contexts.
Each has its own explicit interface, deciding what to share with other contexts.

In `Domain-Driven-Design` book, Evans uses the metaphor of a cell.

> cell can exist because their membraned define what is in and out and determine what can pass

Example of bounded context, Warehouse department and Finance department.

### Shared and hidden models

A warehouse department has knowledge that only interest himself, e.g. number of
forklifts it has.
But it also has knowledge that interest other services such as the finance department
say list of the inventory.

Thus, the *stock item* becomes a shared model.

But the stock item in the warehouse context can have a representation different that one in the finance team.
So there is an *internal representation* and *external representation* .

A simple example:

Say a shared model is user:

```javascript
{
  "id": "u123",
  "name": "Yaron"
}
```

Authentication service internal representation:


```javascript
{
  "userId": "u123",
  "fullName": "Yaron Horsky",
  "passwordHash": "6fsd09dfj0932fjsd9f...",
  "lastLoginAt": "2025-11-20T10:00:00Z",
  "failedLoginAttempts": 3
}
```

Notification service representation:

```javascript
{
  "id": "u123",
  "displayName": "Yaron",
  "email": "yaron@example.com",
  "emailOptIn": true,
  "preferredLanguage": "en"
}
```

Sometimes models can mean very different things depending on the context being used.
For example *Order*:

Payment Service context:

```javascript
{
  "orderId": "o123",
  "totalAmount": 124.90,
  "currency": "USD",
  "paymentStatus": "PENDING"
}
```

Order Service context:

```javascript
{
  "orderId": "o123",
  "customerId": "c1",
  "items": [
    { "sku": "x", "quantity": 2 }
  ],
  "status": "PLACED"
}
```

Shipping Service context

```javascript
{
  "orderId": "o123",
  "address": {
    "street": "...",
    "city": "...",
    "country": "IL"
  },
  "weight": 5.4,
  "shippingStatus": "READY_TO_SHIP"
}

```

### Premature decomposition

Premature decomposition can be costly.
It is much easier to start with one code base and then split it than thinking ahead 
and split first.

## Business capabilities

We should think about context capabilities and not data that is shared.

E.g an *Ordering Service*, these are some capabilities that interests us:

* creating an order
* validate item availability
* apply business rules (discount, limits)
* change order state
* etc..

It exposes *behaviors* like:

```HTTP
POST /orders/validate
POST /orders/confirm
POST /orders/cancel
```

This is a bounded context. The order data is not the main issue, but the capabilities of the
service are.

VS the anemic version, a CRUD version of the same *Ordering Service*:

```HTTP
GET /orders/:id
POST /orders
PUT /orders/:id
DELETE /orders/:id
```

This is basically db with a wrapper.

Why this is bad?

- other services needs to implement the business rules
- high coupling: the one that writes the data controls behavior
- the core is the data, not the capabilities

## Communication in terms of business concepts

Changes are often about changes the business wants to make to how the system
behaves. We are changing capabilities exposed to our customers.

If the system is decomposed by bounded context of our domain, changes are local 
and isolated.

Thus its important to think of the communication in terms of business concepts.

It can be useful to think of forms being sent between these microservices, much
as forms are sent around an organization.

An example of form passing - Loan Application

Loan application Service -> credit check service -> risk service -> approval service -> payout service 

# Chapter 4 - Integration

Getting integration right is key to make microservices hold their benefits, otherwise
a disaster is coming.

## What is the best integration technology?

* avoid breaking changes. e.g. some ms add a new field to a piece of data it sends, 
existing consumers shouldn't be impacted.
* Technology-Agnostic API - since changes are coming, this is a fact, it is important
that the communication between microservices won't need major changes. 
* Make services simple for consumers - we would like to allow the consumers to choose
whatever tech they want, such that the communication with this service won't affect that
choice or restrict it.
* Hide implementation detail - consumers shouldn't be bound to the service internal implementation.
This is tightly coupled. Any technology that makes us expose these details should be avoided.


## The shared database

This is bad!
* Allowing external parties view internal implementation details.
* Changes to the schema breaks consumers.
* Consumers are bound to specific technology choice (relational db for example) (tightly coupled)
* where is the logic? logic is spread across multiple consumers. (no cohesion)

Database integration makes it easy to share data, but doesn't share behavior.

## Sync vs Async

Async can be useful for long running tasks.
Firing requests and assuming they worked allows us to update the UI quickly
give the user a better experience, and later updating when the tasks end and we get the response.

There are 2 different types of collaboration:
- request/response
- event-based 

Event based is saying that "something appended" and the parties that care about it
will do stuff accordingly.

Another benefit of event-based collaboration is that it is highly decoupled.

## Orchestration vs choreography

Complex system requires processes that spans over multiple parts of the system, 
more than the boundary of one service.

**Orchestration**: relying on one central point which guide and drive the process.
Like the conductor in an orchestra.

**Choreography**: we tell each part of its job, and let it work out the details.
Like a ballet dancer reacting to others around them.

Example scenario:

When a new user is created, we
1. send an email
2. we post a thank you note

Each of these is handled through a different service: email service and post service

Downside of Orchestration is that it becomes too much of a center.

With Choreography approach, the customer service emit an event in an async manner
saying that a new customer has been created
The downside here is that the explicit process is implicit in our solution, 
meaning we need to somehow track to tell if the process has indeed finished successfully.
A good solution here is to build a monitoring system that explicitly matches the business process.

## Remote Procedure calls (RPC)

I need to read more about it.

## REST

REpresentational State Transfer (REST) is in a architectural style inspired by the Web.

Resource - a thing that the service know about (like a Customer) 
the server create different representation of this resource on a request.
How this resource is exposed is decouple from how it look internally.

E.g. a client ask for a JSON representation while the resource internally is being 
kept in a relational db.

### REST and HTTP

HTTP protocol goes well with REST style, e.g. http verbs.
HTTP also bring a lot of tooling with it. Some allows for better security.

For server-to-server communication if extremely low latency or small size is important,
we will use TCP instead of http.

Overall REST over HTTP is the default choice for service-to-service communication today. 

## Implementing Async event-based collaboration

### Technology choices

Two main parts: 
1. a way to emit events
2. a way for consumers to find out these events happened.

One solution - a massage broker. Like RabbitMQ.
Keep your middleware (where the messages pass) dumb, keep the logic at the ends.

### Complexities

You need to think about failures and how to treat them, for example,
say the response comes back, does it return to the same node it came out of?
What if that node is down?
It Is important to have monitoring and the use of correlation IDs is recommended 
so we can track a sequence of requests across services.

## Services as state machines

We want to avoid dumb services that are CRUD wrappers.
Having the life cycle of key domain concept explicit modeled is pretty powerful.

## Access by reference

We need to know that the microservices for Customer hold all of the entity lifecycle.
This also needs to be the source of truth for the customer.

It is possible that after we ask for a Customer, while it is in our hands, the 
entity has changed.
If we avoid requesting data more than we need, our system will be much more efficient.

Example: we ask the email service to send an email when an order has been shipped.

**bad**: we can send the email service the order details with the customer information.
if the email is pulling these from a queue, while the requests waits there, things change.
**good**: a better alternative is to send URI for the *Customer* and *Order* resources and the 
email service will look them up when it is time.

## Versioning

* postpone until you must
* semantic versioning MAJOR.MINOR.PATCH

MAJOR change - breaking changes has been made.
MINOR change - new functionality has been added, should support backwards compatibility
PATCH change - bug fix for existing features.

* coexist different endpoints
gradually moving into a new service.
Expand and contract pattern.
We expand with a new service, once everyone is using the new one, 
we contract and remove the old service

One way is adding version number to the URI. e.g. `/v1/customer/` or `/v2/customer`

* using multiple concurrent service versions

## User Interface (UI)

The user can interact with our application through different kinds of devices:
mobile phone, web, tablet, computer.

Each device has constraint, e.g. you can't right click on a phone for example.
Or some activities are battery draining.

Thus, there is a need to handle those differences between each type of interface

### backends for frontends

One way to handle chatty interface is to have one endpoint in a gateway that 
will fetch all of the data from the different services

another model is to have a server for each user interface

Mobile app - mobile backend
website - website backend
helpdesk - helpdesk backend

if there is an api authentication and auth layer, we can put this layer 
for all backends before it reaches them

## Integrating 3rd party software

Author advice, if it is unique for the business that we are building, build it, otherwise
there is no reason for you to develop, just buy the current existing tool for the job.
Examples: CRM, CMS

### Customization

Sometimes, these services requires heavy customization
Find the core concept that are important to your domain, it will front the CRM
expose simple REST API and we can integrate using this service
this is a called a facade

