+++
Categories = ["agenda","workshop"]
Tags = ["logistics","speakers"]
date = "2016-03-15T16:23:23+09:00"
title = "Welcome to the Pivotal Cloud Foundry Workshop@Ford"
type = "Introduction"
weight = 1
+++

##### Date and Time
Date: **Sep 4 2017**

Time: **8:30AM - 12:00PM**

Click to get the Agenda, Prerequisites and Setup for the workshop.

<!--more-->


#### Agenda
|  |  |
|------|------|
| **8:00 AM–10:00 PM** | Config Server Service |    
| **10:10 AM–11:00 PM** | Registry Service |
| **11:10 AM–12:00 PM** | Circuit Breaker Service |
| **12:10 PM–12:30 PM** | Wrap Up, Q&A, Feedback  |

---
#### Speakers
+ Todd Hall - Ford
+ Rajesh Jain - Platform Architect at Pivotal


---

##### Prerequisites
1. Java SDK 1.8+ [Java from Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

          Set the JAVA_HOME variable to the installation dir, in case it is not automatically set

2. Git CLI for [Windows](https://github.com/git-for-windows/git/releases/download/v2.9.0.windows.1/Git-2.9.0-64-bit.exe)
   or Git from [github.com](https://desktop.github.com)

3. Cloud Foundry CLI for [Mac](https://github.com/cloudfoundry/cli/releases) or [Windows](http://docs.cloudfoundry.org/devguide/installcf/install-go-cli.html#windows)

4. Curl for [Windows](http://winampplugins.co.uk/curl/)
   Or for [Mac] (http://pdb.finkproject.org/pdb/package.php/curl)

5. Use Workshop PCF Env or Pivotal Web Services Account.  Create a free trial account here [Pivotal Web Services](http://run.pivotal.io/)


##### Setup

1. Install the Prerequisites software (cf cli)

2. Check if you are able to use the cf cli to connect to the PCF Workshop Env. Alternatively, you can create a PWS account and check the firewall/connectivity before the Workshop

          cf login -a https://api.run.haas-123.pez.pivotal.io  --skip-ssl-validation

3. Check if you are able to connect to Git repo and download / clone the repo using CLI
4. Login to the App Manager Console at

        https://apps.run.haas-123.pez.pivotal.io

5. Note: Tiles preinstalled in the PCF Workshop environment which we will be using the workshop. You don't need any setup for these tiles.

          ER
          RabbitMQ
          MySQL
          Redis



#### Presentation


{{< googleslide "https://docs.google.com/presentation/d/1wNT7il4szv25Tl0KBG4SaetQkwKwyqk_rrQfskDSLgo/embed?start=false&loop=false&delayms=3000" >}}


#### Videos


{{< youtube xdw_9dADM-4 >}}


#### EBooks
Beyond the Twelve-Factor App - https://pivotal.io/beyond-the-twelve-factor-app
Cloud Foundry: The Cloud Native Platform - https://pivotal.io/cloud-foundry-the-cloud-native-platform
Migrating to Cloud Native Application Architectures - https://pivotal.io/platform/migrating-to-cloud-native-application-architectures-ebook
