+++
title = "Infrastructure"

weight = 1

sort_by = "weight"
+++
Chatbot.fm infrastructure consist of the following parts:
  * Turnup environment
  * Control cluster environment
  * Application cluster environment
  
![env overview](/env_overview.png)

## Turnup environment
This is from where automation scripts for infrastructure setup are getting executed from. It is usually a laptop capable of running a docker image. Execution of all scripts and tools required for environments cluster setup is done inside of a docker container.

## Control cluster environment
A set of machines in a private network running mission critical services required for infrastructure of all application clusters, such as:
  * Metrics stack
  * Alerts stack
  * Logging stack
  * Source code repository as a trusted source for changes in the infrastructure
  * CI/CD for driving delivery of the changes in source code
  * Software required to control aspects of the Service region clusters: scaling, load balancing.
  * Software required for cross-cluster data replication: message queues.
  
## Application cluster environment
A set of machines in a private network running application services grouped by region. Every region is represented by individual application cluster environment. Application cluster environment depends on services from the Control cluster environment:
  * Logging
  * Metrics
  * Cross region data replication
