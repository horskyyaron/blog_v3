+++
title = 'Sqs vs Sns'
date = 2025-11-22T17:27:01+02:00
draft = false
tags = ["config", "docs"]
+++

# Queus vs Pub/Sub

As the journey of learning about microservice continues, there is a concept of 
async collaboration between services.

Why is that important? 
Async communication allows for long running process to not block the client. 
It allows for snappier, better user experience since we will update the client/UI
immediately, and return the calculated answer later.

E.g. sending the client an email.
The user signs up, and as a part of the process we send the user an email. 
There shouldn't be any reason for the user to wait until that email has been sent.
So we immediately redirect him to the homepage while generating a job of "sending an email".

Now comes the interesting part, and the detail here lays in **WHO** should care about this event?

In this simple case, only the email service should care about it, and if there are 
number of instances of the email service, any instance taking care of the job is fair game.
That also means that ONLY the email service should subscribe to these queue.
If there will be more than one service, if one polls for the message, the other service will not know 
about it.

In case multiple services "care" that a user has just signed up, a different approach is needed.
The pub\sub.

In this scenario the Email service and Customer Service both care that a new user has just signed up.
Thus they will both will be consumers of a publisher that will tell them "new user" has just signed up.


