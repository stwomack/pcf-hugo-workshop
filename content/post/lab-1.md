+++

Categories = ["lab"]
Tags = ["spring","microservices","cloudfoundry"]
date = "2016-03-15T14:54:11-04:00"
title = "Lab 1"
weight = 2

+++


Pivotal Cloud Foundry with Spring
-

### Goals


To deploy and configure a microservice and UI, leverage the platform for monitoring & management of the microservice, and do a blue green deployment with zero downtime.

<!--more-->

Prerequisites
--

1. Java SDK 1.7+

2. Git from [github.com](https://mac.github.com/)

3. Cloud Foundry CLI for [Mac](https://github.com/cloudfoundry/cli/releases) or [Windows](http://docs.cloudfoundry.org/devguide/installcf/install-go-cli.html#windows)

4. Curl from [curl](http://curl.haxx.se/)

5. Use the Kroger internal instance of Pivotal Cloud Foundry, follow instructions [cloud.kroger.com](http://cloud.kroger.com) and click 'Try Cloud Foundry'
6. OR Pivotal Web Services Account.  Create a free trial account here [Pivotal Web Services](http://run.pivotal.io/)

Pre-work
--
1. Fork and Clone [PCF Workspace](https://github.com/Pivotal-Field-Engineering/pcf-workspace-devops/)
2. Review the [cities](https://github.com/Pivotal-Field-Engineering/pcf-workspace-devops/tree/master) application.

Steps
--
In this workshop we are going to follow these steps to deploy apps on Cloud foundry and manage the lifecycle of the application.

![DevOps on CF](/images/devops-cf.png)


__NOTE__

> The instructions in this document are for Mac/Linux based CLI/Shell. If you are using Windows, you may need to adjust your slashes.


Building apps
---
By this point, you should have cloned (or forked, or downloaded) the [workspace repo](https://github.com/Pivotal-Field-Engineering/pcf-workspace-devops/).  Now you will build the project and deploy it to Cloud Foundry.

For Linux/Mac:
```bash

cd pcf-workspace-devops/cities
./gradlew clean build

```

Windows:
```bash
cd pcf-workspace-devops\cities
gradlew.bat clean build
```
***
# PART 1: Introduction to CF, Push an App.

### Login to the Cloud Platform
Use `cf help` and/or `cf <command> --help` for details on each of the commands below.

1. Review the docs: http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/deploy-app.html
2. Login to the Pivotal Cloud Foundry.
  Using the Kroger HDC instance of Cloud Foundry (everyone will use the same credentials, username and password is kroger):
  ```bash
  $ cf login -a api.cfhdc.kroger.com --skip-ssl-validation
  ```
  If you would prefer to use Pivotal Web Services:
  ```bash
  $ cf login -a api.run.pivotal.io
  ```

4. Verify you are logged in with your own userid (*not admin*) and targeted to your PCF instance:
  ```bash
  $ cf target
  ```
> the switch `--skip-ssl-validation` is used in HDC because of self signed certificates

### Create a Space
This will allow you to have your own environment.
```bash
$ cf create-space <first-initial><lastname>
$ cf target -o KrogerHDC -s <first-initial><lastname>
```
> This can also be done from the web ui at: [console.cfhdc.kroger.com](http://console.cfhdc.kroger.com)

### Push the app


1. Push the cities-hello, put your initials in the app name so we don't get conflicts

    ```bash
    $ cd cities-hello
    $ cf push <first-initial><last-initial>-cities-hello
    // This will give an output which is similar to this
    requested state: started
    instances: 1/1
    usage: 512M x 1 instances
    urls: cities-hello-lactiferous-unanswerableness.cfapps.io
    last uploaded: Mon Jun 15 14:53:10 UTC 2015
    stack: cflinuxfs2
    ```
2. Open the app url
  + When you push the apps, it will give the url route to the app.
  ![Welcome to PCF Workshop](/images/welcome.png)
3. If you haven't already it is a good time to walk through the AppsManager: [console.cfhdc.kroger.com](http://console.cfhdc.kroger.com) 


Recap: Part 1
---
> Cloud Foundry Haiku </br>
  Here is my source code </br>
  Run it on the cloud for me </br>
  I do not care how</br>


Discussion: Part 1
---
+ How do you push an app to the cloud today?
+ How does the cloud platform understand which runtime to use to run the app?

<br>
<hr>

PART 2: Build, Push, Bind, Monitor and Scale an App.
--

The cities-service app requires a database service to store and fetch cities info.


Create a Database Service from Marketplace
---
1. Review the docs on Services:

    [Adding a Service](http://docs.pivotal.io/pivotalcf/devguide/services/adding-a-service.html) <br>
    [Managing Services](http://docs.pivotal.io/pivotalcf/devguide/services/managing-services.html)

2. Create a mysql service instance, name it as `<YOUR INITIALS>-cities-db`

    You can create the service from the `cli` or launch the App Manager http://console.run.pivotal.io and login. <br>
    Navigate to the marketplace and see the available services. Here you will create the service using the CLI.
  ```bash
    $ cf marketplace // check if cleardb mysql service is available
    $ cf create-service cleardb spark <first-initial><last-initial>-cities-db
  ```

3. Launch the DB console via the `Manage` link in the App Manager.  Note the database is empty.


<br>

Push the App
---
1. Do a cf push on cities-service. Notice that the push will fail. In the next step you can learn why.

    ```bash
    $ cf push <first-initial><last-initial>-cities-service -i 1 -m 512M -p build/libs/cities-service-0.0.1-SNAPSHOT.jar
    ```
2. Check the logs to learn more about why the application is not starting

    ```bash
    $ cf logs <first-initial><last-initial>-cities-service --recent
    ```


<br>

Manually Binding the Service Instance
---
1. Review the docs on [Binding a Service Instance](http://docs.pivotal.io/pivotalcf/devguide/services/bind-service.html)
2. Bind the mysql instance `<YOUR INITIALS>-cities-db` to your app cities-service
    You can bind from the App Manager or from the `cli`

    ```bash
    $ cf bind-service <first-initial><last-initial>-cities-service <first-initial><last-initial>-cities-db
    ```

3. Restage your cities-service application to inject the new database.

    ```bash
    $ cf restage <first-initial><last-initial>-cities-service
    ```

    Notice that the application is now running.

4. Check the Env variables to see if the service is bound.
    You can do it from App Manager or from the `cli`

    ```bash
    $ cf env <first-initial><last-initial>-cities-service
    ```

5. Check the MySQL database to see that it now contains data using MySQL Workbench or a similar tool.



__NOTE__

>This app is an Spring Cloud app which uses Spring Cloud Configuration to bind to a database service provided by the cloud platform.
For more information refer to link:Spring-Cloud.adoc[this document] on Spring Cloud configuration.


<br>

Binding Services via the Manifest
---
Next, let's push the cities-service app with a manifest to help automate deployment.

1. Review the documentation: http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/manifest.html
2. Edit the application manifest  `manifest.service` in your `cities-service`

    ```bash
    $ nano manifest.service
    ```

3. Set the name of the app, the amount of memory, the number of instances, and the path to the .jar file.
*Be sure to name your application '<first-initial><last-initial>-cities-service' and use this as the host value.*
4. Add the services binding `<YOUR INITIALS>-cities-db` to your deployment manifest for cities-service .
5. Now, manually unbind the service and re-push your app using the manifest.

    ```bash
    $ cf unbind-service <first-initial><last-initial>-cities-service <first-initial><last-initial>-cities-db
    ```


6. Test your manifest by re-pushing your app with no parameters:

    ```bash
    $ cf push -f manifest.service
    ```

    Notice that using a manifest, you have moved the command line parameters (number of instances, memory, etc) into the manifest.
7. Verify you can access your application via a curl request:

    ```bash
    $ curl -i http://<first-initial><last-initial>-cities-service.cfapps.io
    ```

    We must be able to access your application at https://<first-initial><last-initial>-cities-service.cfapps.io for the next steps to work properly.

__NOTE__

> The default manifest file for an app is `manifest.yml` and it if is present, it is automatically picked without specifying the manifest file option.
In this exercise we have used a different naming convention.

<br>
Health, logging & events via the CLI
---

Learning about how your application is performing is critical to help you diagnose and troubleshoot potential issues. Cloud Foundry gives you options for viewing the logs.

To tail the logs of your application perform this command:
  ```bash
  $ cf logs <first-initial><last-initial>-cities-service
  ```


Notice that nothing is showing because there isn't any activity. Use the following curl command to see the application working:
  ```bash
  $ curl -i http://<first-initial><last-initial>-cities-service.cfapps.io/cities/
  ```

For other ways of viewing logs check out the documentation here: [Streaming Logs](http://docs.pivotal.io/pivotalcf/devguide/deploy-apps/streaming-logs.html#view)

To view recent events, including application crashes, and error codes, you can see them from the App Manager or from the cli.

  ```bash
  $ cf events <first-initial><last-initial>-cities-service
  ```

To view the health of the application you can see from the App Manager or from the cli:
  ```bash
  $ cf app <first-initial><last-initial>-cities-service
  ```

You will get detailed output of the health
  ```bash
  Showing health and status for app cities-service in org  / space development as...
  OK

  requested state: started
  instances: 1/1
  usage: 512M x 1 instances
  urls: cities-service.cfapps.io
  last uploaded: Wed May 27 15:53:32 UTC 2015
  stack: cflinuxfs2

       state     since                    cpu    memory           disk           details
  #0   running   2015-05-27 12:17:55 PM   0.1%   434.5M of 512M   145.4M of 1G
  ```

<br>
Environment variables
---
View the environment variable and explanation of [VCAP Env](http://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#view-env)

  ```bash
  $ cf env <first-initial><last-initial>-cities-service
  ```


You will get the output similar to this on your terminal
  ```bash
  Getting env variables for app rj-cities-service in org Central / space development as rajesh.jain@pivotal.io...
  OK

  System-Provided:
  {
   "VCAP_SERVICES": {
    "cleardb": [
     {
      "credentials": {
       "hostname": "xxxx",
       "jdbcUrl": "xxxx",
       "name": "xxxx",
       "password": "xxxx",
       "port": "3306",
       "uri": "mysql://xxxx?reconnect=true",
       "username": "xxxx"
      },
      "label": "cleardb",
      "name": "rj-cities-db",
      "plan": "spark",
      "tags": [
       "Data Stores",
       "Cloud Databases",
       "Developer Tools",
       "Data Store",
       "mysql",
       "relational"
      ]
     }
    ]
   }
  }

  {
   "VCAP_APPLICATION": {
    "application_name": "rj-cities-service",
    "application_uris": [
     "rj-cities-service.cfapps.io"
    ],
    "application_version": "c3c35527-424f-4dbc-a4ea-115e1250cc5d",
    "limits": {
     "disk": 1024,
     "fds": 16384,
     "mem": 512
    },
    "name": "rj-cities-service",
    "space_id": "56e1d8ef-e87f-4b1c-930b-e7f46c00e483",
    "space_name": "development",
    "uris": [
     "rj-cities-service.cfapps.io"
    ],
    "users": null,
    "version": "c3c35527-424f-4dbc-a4ea-115e1250cc5d"
   }
  }

  User-Provided:
  SPRING_PROFILES_ACTIVE: cloud

  No running env variables have been set

  No staging env variables have been set
  ```


Scaling apps
---
Applications can be scaled via the command line or the console. When we talk about scale, there are two different types of scale: Vertical and Horizontal. Read [Scaling Apps](http://docs.cloudfoundry.org/devguide/deploy-apps/cf-scale.html) doc on more details on scaling applications.

When you vertically scale your application, you are increasing the amount of memory made available to your application. You would vertically scale your application while profiling your app, do performance tuning and to find the best memory settings before you deploy it in production.
Scaling your application horizontally means that you are adding application instances to increase your application throughput and performance under load.

Let's vertically scale the application to 1 GB of RAM.
  ```bash
  $ cf scale <first-initial><last-initial>-cities-service -m 1G
  ```


Now scale your application down to 512 MB.

Next, let's scale up your application to 2 instances
  ```bash
  $ cf scale <first-initial><last-initial>-cities-service -i 2
  ```


To check the status of your applications you can check from the command line to see how many instances your app is running and their current state
  ```bash
  $ cf app <first-initial><last-initial>-cities-service
  ```


Once the second instance as started, scale the app back down to one instance.

<br>
Verify the app from the Console
---
To verify that the application is running, use the following curl commands to retrieve data from the service or use a browser to access the URL:

  ```bash
  $ curl -i http://<first-initial><last-initial>-cities-service.cfapps.io/cities
  ```

  ```bash
  $ curl -i http://<first-initial><last-initial>-cities-service.cfapps.io/cities/162
  ```

  ```bash
  $ curl -i http://<first-initial><last-initial>-cities-service.cfapps.io/cities?size=5
  ```
<br>
Discussion: Part 2
---
In this part of the workshop we created a database service from the marketplace, pushed an app, bound it to the database service, monitored the health of the app and scaled the app.

1. How does the app get the database info today vs. VCAP_SERVICES? <br>
2. How do you horizontally scale your applications?



<br>
<hr>

PART 3: Deploying Upstream App and Bind to backend services
--
The `cities` directory also includes a `cities-ui` application which uses the `cities-client` to consume from the `cities-service`.

The `cities-client` demonstrates using the [Spring Cloud Connector](http://cloud.spring.io/spring-cloud-connectors) project to consume from a microservice.  This is a common pattern for Cloud Native apps.  For more details on building 12 Factor Apps for the Cloud (Cloud Foundry) refer to [12 Factor](http://12factor.net/) website.

The goal of this exercise is to use what you have learned to deploy the `cities-ui` application.

Build the Cities UI and Cities Client App
---

The cities-ui and cities-client can be both built at once by running `./gradlew assemble` in the parent `cities` directory. Run this command now.


Create a User Provided Service Instance.
---
In this section we will create a backend microservice end point for cities-service.

1. Review the documentation on link:http://docs.pivotal.io/pivotalcf/devguide/services/user-provided.html[User Provided Service Instances]
2. Look for the details by running `cf cups --help`.

3. You will need to specify two parameters when you create the service instance: `uri` and `tag` (see: CitiesWebServiceInfoCreator.java in the cities-client project). <br>
  The `uri` should point to your deployed microservice <br>
  The `tag` is a property specified in the CitiesWebServiceInfoCreator.  Tags have a special meaning in CF: <br>

    _Tags_ provide a flexible mechanism to expose a classification, attribute, or base technology of a service, enabling equivalent services to be swapped out without changes to dependent logic in applications, buildpacks, or other services. <br>
     Eg. mysql, relational, redis, key-value, caching, messaging, amqp.  
     <br>
     Tags also allow application configurations to be independent of a service instance name._


4. Refer to the CitiesWebServiceInfoCreator class for the necessary tag value.

  ```bash
  // Use the interactive prompt to create user defined service
  // It will prompt you for the parameters

  $ cf create-user-provided-service <first-initial><last-initial>-cities-ws -p "uri,tag"

  uri>   http://<first-initial><last-initial>-cities-service.cfapps.io/
  tag>   cities

  Creating user provided service....
  ```

<br>
Deploy cities-ui project
---

A `manifest.yml` is included in the cities-ui app.  Edit this manifest with your initials and add the service binding to your cities-service


  ```bash
  ---
  applications:
  - name: <YOUR INITIALS>-cities-ui
    memory: 512M
    instances: 1
    path: build/libs/cities-ui.jar
    services: [ <YOUR INITIALS>-cities-ws ]
    env:
      SPRING_PROFILES_ACTIVE: cloud
  ```

Push the `cities-ui` without specifying the manifest.yml. It will by default pick the manifest.yml file and deploy the app.
  ```bash
  $ cf push
  ```

Note the URL once the application has been successfully pushed.

Verify the backend service is bound to cities-ui
---

```bash
----
$ cf env <first-initial><last-initial>-cities-ui

System-Provided:
{
 "VCAP_SERVICES": {
  "user-provided": [
   {
    "credentials": {
     "tag": "cities",
     "uri": "http://rj-cities-service.cfapps.io/"
    },
    "label": "user-provided",
    "name": "cities-ws",
    "syslog_drain_url": "",
    "tags": []
   }
  ]
 }
}

{
 "VCAP_APPLICATION": {
  "application_name": "rj-cities-ui",
  "application_uris": [
   "rj-cities-ui.cfapps.io"
  ],
  "application_version": "dceb111b-3a68-45ad-83fd-3b8b836ebbe7",
  "limits": {
   "disk": 1024,
   "fds": 16384,
   "mem": 512
  },
  "name": "rj-cities-ui",
  "space_id": "56e1d8ef-e87f-4b1c-930b-e7f46c00e483",
  "space_name": "development",
  "uris": [
   "rj-cities-ui.cfapps.io"
  ],
  "users": null,
  "version": "dceb111b-3a68-45ad-83fd-3b8b836ebbe7"
 }
}

User-Provided:
SPRING_PROFILES_ACTIVE: cloud
```

Access the cities-ui to verify it is connected to your microservice.
---
Open the App Manager (Console) and navigate to your apps. You will see the cities-ui app, with a link to launch the cities-ui application. Alternatively you can open up your browser and navigate to the URL listed from a successful cf push command.

![Cities UI](/images/cities-ui.png)



Discussion: Part 3
---

In this part of the workshop we created a cities-ui app which is loosely bound and independently developed from the backend service. We bound that app to the cities-service microservice.

1. Discussion on loose coupling of your services from your app and 12 Factor App design principles.

<br>
<hr>


PART 4: Deploy Version 2 of the App
--

In this section we are going to do a green-blue deployment using a shell script. The same can be done by executing the commands one at a time.

Delete the unversioned app and the route
---

  ```bash
  cf delete <first-initial><last-initial>-cities-ui
  cf delete-route cfapps.io -n <first-initial><last-initial>-cities-ui
  ```

Push Version 2 and Delete the Old Route using the script
---
We are going to deploy the next version of the `cities-ui` app. The deployment typically is automated using a CD pipeline built with Jenkins or any CD automation tool, but in this workshop we will walk through a simple version number change in the deployment manifest.

1. Edit the `manifest.blue-green` with the following variables
  ```bash
   VERSION: CITIES_APP_1_0
  ```
2. Edit and source the `env` file from the cities-ui folder with the following variables
  ```bash
  export CF_SYSTEM_DOMAIN=     //CF_SYSTEM_DOMAIN will look similar to run.pivotal.io
  export CF_APPS_DOMAIN=       //CF_APPS_DOMAIN will look similar to cfapps.io
  export CF_USER=              //CF_USER is the user account to sign into Pivotal Cloud Foundry, which is usually your email address.
  export CF_ORG=               //CF_ORG is the name of the Organization within Pivotal Cloud Foundry where you want to deploy your applications.
  export CF_SPACE=             //CF_SPACE is the name of the Space within the above Organization where you want your application deployed.
  export CF_APP=<first-initial><last-initial>-cities-ui
  export CF_JAR=build/libs/cities-ui.jar
  export CF_MANIFEST=manifest.blue-green
  export BUILD_NUMBER=1001
```

__Note__

> Be sure to change the CF_APP name to match your application and add the BUILD_NUMBER to the env file. Add the Version number in the manifest.blue-green

3. First deploy the blue v1 of the app.
  ```bash
  // Push the new version of the app, with the version number and route
  $cf push "$CF_APP-$BUILD_NUMBER" -n "$CF_APP-$BUILD_NUMBER" -d $CF_APPS_DOMAIN -p $CF_JAR -f $CF_MANIFEST
  ```

4. Next, increment the BUILD_NUMBER in the env file and source it. Change the VERSION number in the manifest.blue-green
  ```bash
  ....
  export BUILD_NUMBER=2001

  $nano manifest.yml
  ....
  VERSION: CITIES_APP_2_0
  ```

5. Deploy the green v2 and delete the blue v1 of the app.
  ```bash
  // Push the new version of the app, with the version number and route
  $cf push "$CF_APP-$BUILD_NUMBER" -n "$CF_APP-$BUILD_NUMBER" -d $CF_APPS_DOMAIN -p $CF_JAR -f $CF_MANIFEST

  // Map the route to point to the new app
  $cf map-route "$CF_APP-${BUILD_NUMBER}" $CF_APPS_DOMAIN -n $CF_APP

  // Get the deployed version of the app
  $export DEPLOYED_VERSION=`cf apps | grep $CF_APP- | cut -d" " -f1`

  // Un-map an existing routes and delete the app / routes

  $cf unmap-route "$DEPLOYED_VERSION" $CF_APPS_DOMAIN -n $CF_APP
  $cf delete "$DEPLOYED_VERSION" -f
  $cf delete-route $CF_APPS_DOMAIN -n "$DEPLOYED_VERSION" -f

  ```

6. Alternatively, use the bash script `blue-green.sh` in the cities-ui directory, deploy the green v2 and delete the blue v1 of the app. <br>
If you are using the script make sure you increment the BUILD_NUMBER in the env file and change the VERSION number in the manifest.blue-green.


```bash
  $ cat blue-green.sh

  source env
  cf login -a https://api.$CF_SYSTEM_DOMAIN -u $CF_USER -o $CF_ORG -s $CF_SPACE --skip-ssl-validation

  DEPLOYED_VERSION_CMD=$(CF_COLOR=false cf apps | grep $CF_APP- | cut -d" " -f1)
  DEPLOYED_VERSION="$DEPLOYED_VERSION_CMD"
  ROUTE_VERSION=$(echo "${BUILD_NUMBER}" | cut -d"." -f1-3 | tr '.' '-')
  echo "Deployed Version: $DEPLOYED_VERSION"
  echo "Route Version: $ROUTE_VERSION"

  # push a new version and map the route
  cf push "$CF_APP-$BUILD_NUMBER" -n "$CF_APP-$ROUTE_VERSION" -d $CF_APPS_DOMAIN -p $CF_JAR -f $CF_MANIFEST
  cf map-route "$CF_APP-${BUILD_NUMBER}" $CF_APPS_DOMAIN -n $CF_APP

  if [ ! -z "$DEPLOYED_VERSION" -a "$DEPLOYED_VERSION" != " " -a "$DEPLOYED_VERSION" != "$CF_APP-${BUILD_NUMBER}" ]; then
    echo "Performing zero-downtime cutover to $BUILD_NUMBER"
    echo "$DEPLOYED_VERSION" | while read line
    do
      if [ ! -z "$line" -a "$line" != " " -a "$line" != "$CF_APP-${BUILD_NUMBER}" ]; then
        echo "Scaling down, unmapping and removing $line"
        # Unmap the route and delete
        cf unmap-route "$line" $CF_APPS_DOMAIN -n $CF_APP
        cf delete "$line" -f
        cf delete-route $CF_APPS_DOMAIN -n "$line" -f
      else
        echo "Skipping $line"
      fi
    done
  fi
```

Verify the app, zero downtime
---

  ```bash
  $cf apps | grep -i cities-ui
  rj-cities-ui-1001                       started           1/1         512M     1G     rj-cities-ui.cfapps.io, rj-cities-ui-5001.cfapps.io

  ```

  ```bash
  $cf routes | grep -i cities-ui

  development   rj-cities-ui                                           cfapps.io   rj-cities-ui-2001
  development   rj-cities-ui-1001                                      cfapps.io   rj-cities-ui-2001

  ```

  ```bash

  $ curl -i http://<first-initial><last-initial>-cities-ui.cfapps.io/cities/version

  HTTP/1.1 200 OK
  Content-Type: text/plain;charset=ISO-8859-1
  Date: Thu, 21 May 2015 02:22:29 GMT
  Server: Apache-Coyote/1.1
  X-Application-Context: rj-cities-ui-1001:cloud:0
  X-Cf-Requestid: d9fa0481-5cb4-47cd-6335-35adf575a0b6
  Content-Length: 4
  Connection: keep-alive

  CITIES_APP_2_0

  ```
Repeat the Process
---
Change the version (in the manifest) and build numbers (in the env file) and run the script to do blue-green deployment. Check the output using curl.


Process of Blue Green Deployment
---
Review the CF Document for blue green deployment link:http://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html[Using Blue-Green Deployment to Reduce Downtime and Risk]

In summary Blue-green deployment is a release technique that reduces downtime and risk by running two identical production environments called Blue and Green.

![Blue Green Deployment Process](/images/blue-green-process.png)


Newsworthy: Automated Blue Green with cf plugin
---
Cloud Foundry plugin [Autopilot](https://github.com/concourse/autopilot) does blue green deployment, albeit it takes a different approach to other zero-downtime plugins. It doesn't perform any complex route re-mappings instead it leans on the manifest feature of the Cloud Foundry CLI. The method also has the advantage of treating a manifest as the source of truth and will converge the state of the system towards that. This makes the plugin ideal for continuous delivery environments.

  ```bash

  $ mkdir $HOME/go
  $ export GOPATH=$HOME/go
  $ export PATH=$PATH:$GOPATH/bin

  $ go get github.com/concourse/autopilot
  $ cf install-plugin $GOPATH/bin/autopilot
  $ cd cities-services
  // Increment the Build
  $ cf zero-downtime-push cities-services \
      -f manifest.blue-green \
      -p build/libs//cities-service-0.0.1-SNAPSHOT.jar

  ```

Discussion: Part 4
---
In this part of the workshop did deployment using a blue green script without any downtime.
This script / methodology can be used in your CD pipeline to build and deploy Cloud Native Apps with zero downtime.

1. Discussion on how do you do Continous Deployment and Delivery with zero downtime today.


Recap
--
In this workshop we saw how to build, deploy, bind, scale, monitor apps on Cloud foundry and manage the lifecycle of the application

![DevOps on CF](/images/devops-cf.png)


Q/A
--
Feedback
--

Please provide your feedback using this form [Feedback Form](https://docs.google.com/a/pivotal.io/forms/d/1qWlLtTuoULomw9DAW0tuhn7YVWXwVILaMTNKfXkcq0s/viewform?usp=send_form)
