+++
Categories = ["lab"]
Tags = ["spring","config-server","cloudfoundry"]
date = "2016-04-11T23:37:14-04:00"
title = "lab 3"
weight = 2
+++

Cloud Native App development using Spring Cloud Config Server on Pivotal Cloud Foundry
-

### Goals

Create a spring boot application using Spring Cloud Config Server and deploy it on the Cloud Foundry Platform.

<!--more-->

Spring Cloud Services and Spring Boot Application on Cloud Foundry
-
Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state). Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand up services and applications that implement those patterns. They will work well in any distributed environment, including the developer’s own laptop, bare metal data centers, and managed platforms such as Cloud Foundry.

The big picture : Use Spring Cloud Services design patterns to build cloud Native applications

<img src="/images/spring-1.png" alt="Cloud Native Spring Application Architecture" style="width: 600px;"/>



Prerequisites
--

1. Java SDK 1.7+

2. Git from [github.com](https://mac.github.com/)

3. Cloud Foundry CLI for [Mac](https://github.com/cloudfoundry/cli/releases) or [Windows](http://docs.cloudfoundry.org/devguide/installcf/install-go-cli.html#windows)

4. Curl from [curl](http://curl.haxx.se/)

5. Pivotal Web Services Account.  Create a free trial account here [Pivotal Web Services](http://run.pivotal.io/)

6. Maven for build (https://maven.apache.org/install.html)


Lab
--
In this workshop we are going to follow these steps to build our first Cloud Native Spring Boot app on Cloud foundry using the Spring Cloud Config Server.


Learn how to
- Set up a Git repository to hold configuration data
- Configure Spring Cloud Config server (config-server) on Pivotal Cloud Foundry with a Git backend
- Set up a client (greeting-config) to pull configuration from the config-server
- Use @ConfigurationProperties to capture configuration changes (greeting-config)
- Use @RefreshScope to capture configuration changes (greeting-config)
- Use Cloud Bus to notify applications (greeting-config) to refresh configuration at scale
- Config a Spring Cloud Service Registry

Desired the architecture of this Cloud Native Spring boot app is:
![Config Server with Cloud Native Spring App](/images/spring-2.png)



### Step 1

Clone the git repo which has a simple boilerplate Spring boot app built using Spring Initializer.

The Spring Labs repo contains multiple apps, we are going to focus on greeting-config app in this exercise.

````
git clone https://github.com/rjain-pivotal/pcf-workshop-spring-labs.git
````

More info on Spring Initializer (http://start.spring.io/)
![Spring Initializer](/images/Spring-Initializer.png)


### Step 2
Login into Pivotal Cloud Foundry

The students have userId's (student1-student25) and the passwords will be distributed in the workshop.
Each student is assigned their own Organization (student1-org)

````
cf login -a https://api.pcf2.cloud.fe.pivotal.io --skip-ssl-validation
  Email: student1
  Password: ••••••••
````

Login to the App Console at https://apps.pcf2.cloud.fe.pivotal.io

![PCF App Console](/images/pcf-console.png)


### Step 3
The greeting-config app uses the Spring Cloud Services Config Server to read config data.
Clone the git repo which has the config properties which are read by the greeting-config


````
git clone https://github.com/rjain-pivotal/workshop-app-config.git
cd workshop-app-config/
````

Set the Remote URL to your own/new repo you would like to create or you can fork the workshop-app-config repo

````
git remote set-url origin https://rjain-pivotal@github.com/rjain-pivotal/student1-workshop-app-config.git
git push origin master
````

In this repo you have the following config files:
![Git Config Server Files](/images/git-1.png)

The config server serves the configuration request using the following path formats, where the application name is set in the application.yml for the client application, profile and label are set as environment variables.

````
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.Properties
````
For more details on the Config Server config files refer to the documentation (http://docs.pivotal.io/spring-cloud-services/config-server/server.html)

You could also have multiple branches in your Git repo, and in the Config Service instance, you can configure which branch to read the config information.

master
------
https://github.com/myorg/configurations
|- myapp.yml
|- myapp-development.yml
|- myapp-production.yml

tag v1.0.0
----------
https://github.com/myorg/configurations
|- myapp.yml
|- myapp-development.yml
|- myapp-production.yml


### Step 5
Configure the Spring Cloud Config Service Instance from the marketplace

1. In the PCF App Console, create a instance of the Config Server service from the marketplace.

![Marketplace Services](/images/pcf-console-1.png)

2. Select the default plan.
3. Name the service instance as 'studentXX-config-service'

![Config Server](/images/pcf-config-service-1.png)

4. This will create the studentXX-config-service service instance. Next configure this service by clicking manage.

![Config Server](/images/pcf-config-service-2.png)

The Git repository URL is the URL of your cloned git repo in Step 3.

````
https://rjain-pivotal@github.com/rjain-pivotal/student1-workshop-app-config.git
````

We are using defaults for the rest, hence leave them blank.
For detailed documentation on the other configuration items, refer to the product documentation.
http://docs.pivotal.io/spring-cloud-services/config-server/creating-an-instance.html


### Step 6

Code walk through of the Config Properties used by (greeting-config)

Let's walk through the code in the greeting-config app in the source repo (Step #1) using your favorite editor (Atom/Sublime/Eclipse/IntelliJ/STS)

1. greeting-service

In GreetingProperties.java, @ConfigurationProperties annotation allows for reading of configuration values. Configuration keys are a combination of the prefix and the field names. In this case, there is one field (displayFortune). Therefore greeting.displayFortune is used to turn the display of fortunes on/off. Remaining code is typical getter/setters for the fields.

````
@ConfigurationProperties(prefix="greeting")
public class GreetingProperties {

	private boolean displayFortune;

	public boolean isDisplayFortune() {
		return displayFortune;
	}

	public void setDisplayFortune(boolean displayFortune) {
		this.displayFortune = displayFortune;
	}
}
````

greetingProperties.isDisplayFortune() is used to turn the display of fortunes on/off. There are times when you want to turn features on/off on demand.

````
@EnableConfigurationProperties(GreetingProperties.class)
public class GreetingController {

	Logger logger = LoggerFactory
			.getLogger(GreetingController.class);


	@Autowired
	GreetingProperties greetingProperties;

	@Autowired
	FortuneService fortuneService;

	@RequestMapping("/")
	String getGreeting(Model model){

		logger.debug("Adding greeting");
		model.addAttribute("msg", "Greetings!!!");

		if(greetingProperties.isDisplayFortune()){
			logger.debug("Adding fortune");
			model.addAttribute("fortune", fortuneService.getFortune());
		}

		//resolves to the greeting.vm velocity template
		return "greeting";
	}

}
````

2. quote-service

QuoteService uses the @RefreshScope annotation. Beans with the @RefreshScope annotation will be recreated when refreshing configuration. The @Value annotation allows for injecting the value of the quoteServiceURL configuration parameter.

````
@Service
@RefreshScope
public class QuoteService {
	Logger logger = LoggerFactory
			.getLogger(QuoteController.class);

	@Value("${quoteServiceURL}")
	private String quoteServiceURL;

	public String getQuoteServiceURI() {
		return quoteServiceURL;
	}

	public Quote getQuote(){
		logger.info("quoteServiceURL: {}", quoteServiceURL);
		RestTemplate restTemplate = new RestTemplate();
		Quote quote = restTemplate.getForObject(
				quoteServiceURL, Quote.class);
		return quote;
	}
}
````

3. In the app-config repo in the Github, review the greeting-config.yml file, which has the displayFortune turned on and the quoteService point to an existing URL.

````
security:
  basic:
    enabled: false

management:
  security:
    enabled: false

logging:
  level:
    io:
      pivotal: DEBUG

greeting:
  displayFortune: true # <----Change to true

quoteServiceURL: http://quote-service-dev.cfapps.io/quote
````


### Step 5
Push the app and set the cf set-env

1. Change the manifest.yml file in the greeting-config/ to reflect the name of the app and the config-service

````
---
applications:
- name: student1-greeting-config
  memory: 512M
  buildpack: https://github.com/cloudfoundry/java-buildpack
  instances: 1
  host: student1-greeting-config
  path: target/greeting-config-0.0.1-SNAPSHOT.jar
  services:
    - student1-config-service
  env:
    SPRING_PROFILES_ACTIVE: dev

````

2. Build the app using maven

````
mvn clean package
````

3. Push the app using cf cli

````
cf push
````

4. Set the ENV property

````
cf set-env student1-greeting-config CF_TARGET https://api.pcf2.cloud.fe.pivotal.io
````

5. Restage the app to reflect the new env variables

````
cf restage student1-greeting-config
````

6. Open in the browser the App

````
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/random-quote
````

### Step 6
Change the property and curl RefreshScope

1. In the app-config repo, edit the greeting-config.yml file.

````
greeting:
  displayFortune: false # <----Change to true

quoteServiceURL: http://quote-service-qa.cfapps.io/quote
````

2. Force refresh the beans

````
curl -X POST http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/refresh
````

This will output the properties which changed
````
["quoteServiceURL","greeting.displayFortune"]
````

3. Open in the browser the App

You will see the Greetings doesn't have any fortune and the random-quote is from qa service
````
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/random-quote
```


### Step 7
Change the profile and push

Next, update the manifest.yml to point to the SPRING_PROFILES_ACTIVE to qa

````
---
applications:
- name: student1-greeting-config
  memory: 512M
  buildpack: https://github.com/cloudfoundry/java-buildpack
  instances: 1
  host: student1-greeting-config
  path: target/greeting-config-0.0.1-SNAPSHOT.jar
  services:
    - student1-config-service
  env:
    SPRING_PROFILES_ACTIVE: qa

````

Now the properties will be served by app-config/greeting-config-qa.yml

You can verify by opening the two URLs
````
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/random-quote
```


### Step 8
Refreshing Application Configuration at Scale with Cloud Bus
When running several instances of your application, this poses several problems:

- Refreshing each individual instance is time consuming and too much overhead
- When running on Cloud Foundry you don’t have control over which instances you hit when sending the POST request due to load balancing provided by the router

Spring Cloud Bus addresses the issues listed above by providing a single endpoint to refresh all application instances via a pub/sub notification.

1) Create a RabbitMQ service instance, bind it to greeting-config

````
$ cf cs p-rabbitmq standard cloud-bus
$ cf bs greeting-config cloud-bus
````

2. Add the dependency to the pom.xml

````
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
````

3. Build the app and push 3 app instances

````
$mvn clean package
$cf push -i 3
````

4. Change the app-config/greeting-config.yml and refresh all the app instances using Cloud Bus

````
curl -X POST http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/bus/refresh
````

5. Verify by opening the two URLs
````
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/
http://student1-greeting-config.pcf2.cloud.fe.pivotal.io/random-quote
```

### Step 9

Check the Actuator Endpoints
http://route-to-app/beans

Dumps all of the beans in the Spring context.

http://route-to-app/autoconfig

Dumps all of the auto-configuration performed as part of application bootstrapping.

http://route-to-app/configprops

Displays a collated list of all @ConfigurationProperties.

http://route-to-app/env

Dumps the application’s shell environment as well as all Java system properties.

http://route-to-app/mappings

Dumps all URI request mappings and the controller methods to which they are mapped.

http://route-to-app/dump

Performs a thread dump.

http://route-to-app/trace

