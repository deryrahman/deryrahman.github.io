+++
title = "Software System Fundamentals"
date = "2021-07-19T15:49:33+07:00"
author = "Dery R Ahaddienata"
authorTwitter = "deryrahman" #do not include @
cover = ""
tags = ["tech", "system-design", "concept", "book-designing-data-intensive-applications"]
keywords = ["software", "system", "concept", "fundamental"]
description = "The book I'm currently reading is [Designing Data Intensive Applications](https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/) by Martin Kleppmann. During the first read, I found concise fundamentals we need to concern most when dealing with a high quality software system."
showFullContent = false
toc = true
+++

The book I'm currently reading is [Designing Data Intensive Applications](https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/) by Martin Kleppmann. During the first read, I found concise fundamentals we need to concern most when dealing with a high quality software system. There're 3 fundamentals as follow:

| Fundamental         | Definition                                                                                         |
|---------------------|----------------------------------------------------------------------------------------------------|
| **Reliability**     | The system should work properly even when the things go wrong (faulty)                             |
| **Scalability**     | The system should be able to handle the growth (data volume, traffic, or complexity)               |
| **Maintainability** | The system should easily adaptable enough during maintenance (bugs, failures, and new features)    |

## Reliability

Faulty can come up from several places:
- **Hardware Faults**: eg. Hard disk has MTTF (mean time to failure) of about 10 to 50 years.
- **Software Faults**: eg. Uncaught bugs in Linux kernel due to leap second on June 20, 2012.
- **Human Errors**: eg. Configuration error by operators leads the most system outage.

## Scalability

Data growth is equal to increasing load. There're 2 things need to describe:
- **Load**: current load existing on the system. Some parameters:
    - request per second
    - response time
    - read write ration on DB
    - number of concurrent user
    - hit rate on cache
- **Performance**: metrics that used to define how good the system handle load. It can be used as SLA / SLO. Some metrics:
    - avg / mean
    - median: p50
    - percentile: p95, p99, p999

## Maintainability

The goal is to minimize pain during maintenance. Three design principles need to follow:
- **Operability**: make it easy for teams to keep the system run smoothly.
- **Simplicity**: make it easy for new engineer to understand the system.
- **Evolvability**: make it easy for engineer to extend the system.

**References**
1. [Designing Data Intensive Applications](https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/) by Martin Kleppmann
