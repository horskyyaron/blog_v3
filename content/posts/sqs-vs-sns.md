+++
title = 'Sqs vs Sns'
date = 2025-11-22T17:27:01+02:00
lastmod = 2025-11-26T10:03:53+02:00
draft = false
tags = ["microservices", "system-design"]
+++

As the journey of learning about microservices continues, I learned about the topic
of async communication for services collaboration.

We'll explore some terms like: job, queue, worker, publisher, subscriber, horizontal scaling, vertical scaling,
sqs, sns.


Why is that important? Enough words, an Example!

## Pizza example

### Case 1: Joe is hungry

Joe is hungry. Joe wants Pizza, 

Joe opens his *X-Food-Delivery* application and orders the best pizza a 10 km radius can buy!

Joe's order is waiting for its turn, since many other people ordered pizzas as well.

At this point, one of the thousands of *X-Food-Delivery* application deliverymen (or **workers**) sees the
order and takes on himself to complete the **job** of delivering Joe his pizza.

Questions:

* Does Joe care which of the deliverymen (**workers**) handles his order? Assuming similar customer service from 
each of them, the answer is - no.
* Does Joe needs to wait with his face glue to the screen, **unable to move**, until a "Someone is taking care of your order" message
arrive? The answer is - no.
* Does any other role besides deliverymen **CARES** about the fact that Joe made an order?
For now lets say that it doesn't matter. So the answer for now is - no,

In this simple case we have: 

* A TASK (or job) - a food order.
* A QUEUE for all of the different customer orders; if Joe orders 30 seconds before Jake,
it will be handled first (assuming perfect world and simple one queue).
* A WORKER which takes that job, and does WORK;  
    * WORKER = deliveryman. 
    * WORK = driving to the restaurant, taking the food, drive to Joe's cave and delivering him
    his precious 10$ pizza - all of which **takes time**.

**This is a Simple Queue Service (SQS)**

Service A (Joe) pushes a job (food order) into the queue. \
Service B (deliveryman, singular!) takes care of that job. 

We can think of deliverymen, plural, as "**scaling** the deliveryman". \
And in terms of microservices, this is **horizontal scaling** - increasing the capacity\
of taking more orders by adding more deliverymen.\
A **Vertical scaling** would be using a flying robot - **a faster better deliveryman** - which can get 
places very fast, thus reducing the time of each order handling (which parallels to a machine with better resources like GPU). 

### Case 2: Jake the Pizza shop tech guy sees Joe's order 

In order for a pizza to be born into this world, number of things needs to happen:

1. tricking with the pizza dough on your finger to create the pizza base.
2. adding toppings
3. bring garlic sauce (a non negotiable step)
4. package the pizza

Since the Pizza shop is efficient, they have different roles for making different processes
in the pizza creation.

They have *Tanjiro*, which is responsible for dough and making the pizza base.
They have *Inosuke*, which is responsible for toppings.
They have *Zeintsu*, which is responsible for the bringing sauces.
They have *Bill*, which is responsible for cleaning the tables in the restaurant. 
And they have *Muzan*, which is responsible for Packaging. 

When *Jake* gets the order, he needs to tell **EVERYONE** about the order, he can't just 
tell only *Tanjiro*, or we will have pizza with only the dough and no toppings. 
Moreover, if *Muzan* didn't know about the order the pizza won't be packaged and sadly won't be delivered to Joe.

This is a **simple notification system (SNS)**.

The dedicated pizza shop workers all (expect Bill, we'll get to it in a second) care about the new orders so they **listen** 
to the counter guy to know if a new order arrived.
Also, we can assume that Bill doesn't care that a new order arrived, but he just looks for dirty table to clean.

In terms of microservices we have a **publisher**, which is the counter guy.\
The **subscribers** - Tanjiro, Inosuke, Zeintsu and Muzan who care about new orders and ALL needs to 
be notified in order for a pizza to be born into this world.




