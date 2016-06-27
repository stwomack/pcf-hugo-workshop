+++
Categories = ["agenda","workshop"]
Tags = ["logistics","speakers"]
date = "2016-03-15T16:23:23+09:00"
title = "Welcome to the Pivotal Cloud Foundry Workshop@Detroit"
type = "Introduction"
weight = 1
+++

##### Date and Time
Date: **May 9 , 2016**

Time: **9:30AM—4:30PM**

Click to get the Agenda, Prerequisites and Setup for the workshop.

<!--more-->


#### Agenda
|  |  |
|------|------|
| **8:00 AM–9:00 AM** | Registration and Breakfast  |
| **9:00 AM–10:00 AM** | Cloud Native Architecture & Pivotal Cloud Foundry |    
| **10:15 AM–12:00 PM** | Hands-On Experience with Pivotal Cloud Foundry  (push, bind, scale, monitor) |
| **12:00 PM–1:00 PM** | Lunch and Networking  |
| **1:00 PM–2:00 PM** |Routing Services and API Management using Apigee Service  |
| **2:15 PM–3:30 PM** | CI/CD on Cloud Foundry using Concourse  |
| **3:30 PM–4:00 PM** | Wrap Up, Q&A, Feedback  |

---

##### Prerequisites
1. Java SDK 1.7+

2. Git CLI from [github.com](https://mac.github.com/)

3. Cloud Foundry CLI for [Mac](https://github.com/cloudfoundry/cli/releases) or [Windows](http://docs.cloudfoundry.org/devguide/installcf/install-go-cli.html#windows)

4. Curl from [curl](http://curl.haxx.se/)

5. Use Workshop PCF Env or Pivotal Web Services Account.  Create a free trial account here [Pivotal Web Services](http://run.pivotal.io/)

6. Maven for build (https://maven.apache.org/install.html)

7. Vagrant

8. Virtual box


##### Setup

1. Install the Prerequisites software (cf cli)

2. Check if you are able to use the cf cli to connect to the PCF Workshop Env. Alternatively, you can create a PWS account and check the firewall/connectivity before the Workshop

          cf login -a https://api.pcf2.cloud.fe.pivotal.io  --skip-ssl-validation

3. Check if you are able to connect to Git repo and download / clone the repo using CLI
4. Login to the App Manager Console at

        https://apps.pcf2.cloud.fe.pivotal.io

5. Note: Tiles preinstalled in the PCF Workshop environment which we will be using the workshop. You don't need any setup for these tiles.

          ER
          Apigee
          Spring Cloud Services
          RabbitMQ
          MySQL
          MongoDB
          Neo4J
          Redis
          GitLab
          JFrog Artifactory
          Cloud Bees Jenkins



##### Speakers

    Rajesh Jain, Platform Architect
    Chris Johnson, Platform Architect
    Rick Ross, Platform Architect

#### Presentation


{{< googleslide "https://docs.google.com/presentation/d/1wNT7il4szv25Tl0KBG4SaetQkwKwyqk_rrQfskDSLgo/embed?start=false&loop=false&delayms=3000" >}}


#### Videos


{{< youtube V75fE_dxuBQ >}}


{{< youtube 7APZD0me1nU >}}
