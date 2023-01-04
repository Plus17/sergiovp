---
author: Me
title: Useful Patterns to communicate with external services
date: 2023-01-03
description: Some patterns to handle external requests
math: true
categories: []
---
useful-patterns-to-communicate-with-external-services
Communicating with external services like a third-party API REST is hard, not only because the contract is designed by someone else. Also, now you need to consider the Fallacies of Distributed Systems.

Calls to external services can fail for multiple reasons unrelated to business concerns. You can receive a timeout error, the network can fail, bandwith related errors, etc.

However, some patterns exist to build more reliable systems when dealing with network calls.

### Retries
It's basically about automatically retrying failed requests.

But we need to be careful with this pattern if the action isn't idempotent.

### Circuit breaker
It's commonly found services or processes depend at some point on an external call, and when this external service fails causes a cascade of failures.

The [circuit breaker](https://microservices.io/patterns/reliability/circuit-breaker.html) pattern monitors the failures, if the call fails over a certain number of times in a time frame, opens the circuit preventing new requests that you know will fail.

### Background jobs
If we have expensive work that doesn't require completing all steps to send a response to the client this could be very useful.

Exists a lot of libraries provide us with power background jobs with capabilities like automatic retries, exponential back-off, etc.

But even with these libraries we need to concern about issues like having idempotent jobs, not serializing complex structures like a complete object and using an id instead or performance issues, etc.

Off course exists other patterns depending on the problem to handle o multiple ways to manage the same issue. But this is a start point to know some of them.

Lastly, here are some helpful resources:
- [Patterns of Service-Oriented Architecture](https://multithreaded.stitchfix.com/blog/2017/05/09/patterns-of-service-oriented-architecture/)
- [Architecting for Reliability Part 2](https://medium.com/becloudy/architecting-for-reliability-part-2-resiliency-and-availability-design-patterns-for-the-cloud-cf7aaaed0df2)
- [Architecture Patterns - Azure learn](https://learn.microsoft.com/en-us/azure/architecture/patterns/)
- [Architecture Patterns - Redhat articles](https://www.redhat.com/architect/topics/architecture-patterns)
