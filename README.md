This repository contains my practice and notes from section 5 of the Udemy course [Master Spring Boot 3 & Spring Framework 6 with Java](https://www.udemy.com/course/spring-boot-and-spring-framework-tutorial-for-beginners), created by Ranga Karanam, the founder of in28minutes.

# Section 5: Getting Started with Spring Boot

Understanding the World Before Spring Boot - 10000 Feet Overview:
* Setting up projects before Spring Boot was NOT easy!
* We needed to configure a lot of things before we have a production-ready application
* Before Spring Boot, the following needed to be managed manually:
    * Dependency Management (pom.xml)
        * Need to manage frameworks and versions in POM file
            * A POM file for creating a REST API would include all these frameworks along with versions:
                * Spring framework, Spring MVC framework, JSON binding framework, ..
    * Define Web App Configuration (web.xml)
        * Example: Configure DispatcherServlet for Spring MVC
    * Spring Configuration - Manage Spring Beans (context.xml)
        * Define your Spring Configuration
            * Component Scan
            * View Resolver
            * …
    * Implement Non-Functional Requirements (NFRs)
        * Logging
        * Error Handling
        * Monitoring
    * All of this had to be repeated for every new project
    * Typically takes a few days to setup for each project (and countless hours to maintain)


Setting up New Spring Boot Project with Spring Initializr:
* To create a Spring Boot project for a REST API:
    * Go to start.spring.io to access the Spring Initializr
        * Group = package
        * Artifact = Class name
    * Click on the “ADD DEPENDENCIES” button and search for “Spring Web” and add it
    * Click the “GENERATE” button to download the zip file to your downloads folder
    * Unzip the folder to your Desktop
    * Create a GitHub repo of the same name as your folder
        * Optional: choose a .gitignore template for your code
    * Clone the repo into your “eclipse-workspace” folder
    * Go to the unzipped folder and open it. Make sure to press “command + Shift + .” to show hidden files and folders
    * Copy all the contents inside the unzipped folder (DON’T COPY THE TOP LEVEL FOLDER)
    * Paste the contents into the cloned repo in your “eclipse-workspace” folder
    * Open the .gitignore file and make sure it has what you need
* A class with a main method should already exist in your project. Open it and click the play button in Eclipse to run the Spring Boot application.
    * You should see info saying Tomcat started on port 8080 and the application started
    * You can click the red terminate button to the top-right of the Eclipse Console window to stop the server

Build a Hello World API with Spring Boot:
* The generated class with the main method has a “@SpringBootApplication” annotation and contains code to start the Spring Application
* Create a REST API with the following:
    * Endpoint: http://localhost:8080/courses
    * Returns fields: id, name, author
* The Java code has the following:
    * The class uses the @RestController annotation so spring knows it’s a REST controller
    * The method uses the @RequestMapping("/courses") annotation so Spring knows the endpoint path to this functionality is "/courses"
@RestController
public class CourseController {	
	@RequestMapping("/courses")
	public List<Course> retrieveAllCourses() {
		return Arrays.asList(
				new Course(1, "Learn AWS", "in28minutes"),
				new Course(2, "Learn DevOps", "in28minutes")
				);
	}
}

* Now, when a user visits the URL “http://localhost:8080/courses”, the new courses are created and sent back to the user as an Array List. The Course class looks like this:
public class Course {
	private long id;
	private String name;
	private String author;
	public Course(long id, String name, String author) {
		super();
		this.id = id;
		this.name = name;
		this.author = author;
	}
	public long getId() {
		return id;
	}
	public String getName() {
		return name;
	}
	public String getAuthor() {
		return author;
	}
	@Override
	public String toString() {
		return "Course [id=" + id + ", name=" + name + ", author=" + author + "]";
	}
}

* The data returned to the user looks like this:
[{"id":1,"name":"Learn AWS","author":"in28minutes"},{"id":2,"name":"Learn DevOps","author":"in28minutes"}]

* The above was done without configuring any Beans or XML

Understanding the Goal of Spring Boot:
* Help you build PRODUCTION-READY apps QUICKLY
    * Build QUICKLY
        * Spring Initializr
        * Spring Boot Starter Projects
            * Quickly find dependencies for your projects
        * Spring Boot Auto Configuration
            * Automatically provides configuration based on the dependencies in the class path
        * Spring Boot DevTools
            * Make application changes without having to manually restart the server
    * Be PRODUCTION-READY
        * Logging
        * Different Configuration for Different Environments
            * local, QA, staging, production, ..
            * Profiles, ConfigurationProperties
        * Monitoring (Spring Boot Actuator)
            * Memory, Metrics, ..

Understanding Spring Boot Magic - Spring Boot Starter Projects:
* Exploring Spring Boot Starter Projects:
    * I need a lot of frameworks to build application features:
        * Build a REST API: I need Spring, Spring MVC, Tomcat, JSON conversion…
        * Write Unit Tests: I need Spring Test, Unit, Mockito, …
    * How can I group them and make it easy to build applications?
        * Starter Projects provide this. They are convenient dependency descriptors for different features
            * Starter projects are found in your POM file, in the “dependencies” section; here are two examples:
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>

* In the example above:
    * “spring-boot-starter-web” allows you to build REST API and Web Applications
    * “spring-boot-starter-test” helps you to write Unit tests
* You can “command + click” on the dependency in the POM file to open the POM file that belongs to that starter project
* Each starter project has its own POM file with dependencies needed to get you going quickly
* Spring Boot provides variety of starter projects:
    * Web Application & REST API - Spring Boot Starter Web (spring-webmvc, spring-web, spring-boot-starter-tomcat, spring-boot-starter-json)
    * Unit Tests - Spring Boot Starter Test
    * Talk to database using JPA - Spring Boot Starter Data JPA
    * Talk to database using JDBC - Spring Boot Starter JDBC
    * Secure your web application or REST API - Spring Boot Starter Security

Understanding Spring Boot Magic - Auto Configuration:
* I need a lot of configuration to build Spring app:
    * Component Scan, DispatchServlet, …
    * Simplify this by using Auto Configuration
    * Auto Configuration is decided base on:
        * Which frameworks are in the Class Path?
        * What is the existing configuration (Annotations, etc)?
    * Example: Spring Boot Starter Web
        * Dispatcher Servlet (DispatcherServletAutoConfiguration)
        * Embedded Servlet Container - Tomcat is the default (EmbeddedWebServerFactoryCustomizerAutoConfiguration)
        * Default Error Pages (ErrorMvcAutoConfiguration)
        * Bean<->JSON (JacksonHttpMessageConvertersConfiguration)
    * Auto Configuration logic is defined in the “spring-boot-autoconfigure-#.#.#-M3.jar”. In a project this is nested under “Maven Dependencies”. There are many packages under  “spring-boot-autoconfigure-#.#.#-M3.jar”. All the logic for developing web applications and REST APIs can be found under the package “org.springframework.boot.autoconfigure.web”.
* To see more details about which configurations are being used:
    * Open the “application.properties” file under “src/main/resources” and add the following code:
        * logging.level.org.springframework=debug
            * This means, for the org.springframework package, log at debug level
    * Run your application; you will see a lot more output in the Console
    * Under “CONDITIONS EVALUATION REPORT” there’s a section called “Positive matches” that shows things that were auto configured, and there’s a section called “Negative matches” that shows things that did not get auto configured
        * The “Positive matches” are all things in the “spring-boot-autoconfigure-#.#.#-M3.jar”
    * Press “command + shift + T” (Open Type) to search for class names
        * You can search for specific classes in the list of “Positive matches” to see more details
        * Example for “DispatcherServletAutoConfiguration”:
            * Above the class declaration, there’s these annotations showing when it’s enabled:
                * @ConditionalOnWebApplication(type = Type.SERVLET)
                    * Enabled for web application or REST API
                * @ConditionalOnClass(DispatcherServlet.class)
                    * Enabled when something called “DispatcherServlet” is in the class path
        * There are auto configured error pages as well. Example: the “ErrorMvcAutoConfiguration” class returns an error when someone tries to visit an endpoint that does not exist

Build Faster with Spring Boot DevTools:
* Spring Boot DevTools allows the server to restart automatically with every code change
* Add this dependency to the POM file for DevTools:
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
</dependency>

* After the dependency above is added there’s no need to restart the server after modifying code, and that’s it, nothing else is needed, just the dependency
* REMEMBER, the exception to this is modifications to the POM file; you will need to restart the server manually for these

Get Production Ready with Spring Boot - 1 - Profiles:
* Managing App. Configuration using Profiles:
    * Each environment (Dev, QA, Stage, Prod, …) needs its own configuration
        * Different databases
        * Different web services
    * Profiles allow for environment specific configuration
    * When no environment specific .properties files are being used it will default to the “application.properties” file
    * To configure development and production environment properties:
        * Create two files called:
            * “application-dev.properties”
            * “application-prod.properties”
    * In order to use the properties in “application-prod.properties”, you need to add this to the “application.properties” file:
        * spring.profiles.active=prod
    * This works because properties in “application-prod.properties” get higher priority
    * Example files:
        * “application.properties”
            * logging.level.org.springframework=debug
            * spring.profiles.active=prod
        * “application-dev.properties”
            * logging.level.org.springframework=trace
        * “application-prod.properties”
            * logging.level.org.springframework=info
    * Because “application.properties” has “spring.profiles.active=prod”, it will log at level info, because that’s what’s in the “application-prod.properties” file
    * Log Levels (in order) (each level logs itself and the level below it, except for off):
        * trace
        * debug
        * info
        * warning
        * error
        * off

Get Production Ready with Spring Boot - 2 - ConfigurationProperties:
* For complex configurations such as URLs, Keys, Usernames, etc, use configuration properties
* To create configuration properties:
    * Create a new java class and name it ClassNameConfiguration; example: CurrencyServiceConfiguration
    * Add the @ConfigurationProperties(“”) annotation above the class name
    * Example:
        * If we want this configuration:
            * currency-service-url=
            * currency-service-username=
            * currency-service-key=
        * The class would look like this:
@ConfigurationProperties(prefix = "currency-service")
@Component
public class CurrencyServiceConfiguration {
	private String url;
	private String username;
	private String key;
	public String getUrl() {
		return url;
	}
	public void setUrl(String url) {
		this.url = url;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}	
	public String getKey() {
		return key;
	}
	public void setKey(String key) {
		this.key = key;
	}	
}

* We added the prefix in the “@ConfigurationProperties(“”)” annotation, it also needs the @Component annotation
* We still add the properties to the “application.properties” file:
    * currency-service.url=http://default.in28minutes.com
    * currency-service.username=defaultusername
    * currency-service.key=defaultkey
* The CurrencyServiceConfiguration class knows to map to the “application.properties” file because of the “@ConfigurationProperties(prefix = "currency-service")” annotation
* To test this, you can create a controller like this:
@RestController
public class CurrencyConfigurationController {
	@Autowired
	private CurrencyServiceConfiguration configuration;
	@RequestMapping("/currency-configuration")
	public CurrencyServiceConfiguration retrieveAllCourses() {
		return configuration;
	}
}

* Then run the server and go to localhost:8080/currency-service
    * The output is:
{
    "url": "http://default.in28minutes.com",
    "username": "defaultusername",
    "key": "defaultkey"
}

* To use different properties in dev, simple add the same properties with the dev values to the “application-dev.properties” file; like this:
    * currency-service.url=http://dev.in28minutes.com
    * currency-service.username=devusername
    * currency-service.key=devkey
* As long as “application.properties” has “spring.profiles.active=dev”, the dev properties will be used

Get Production Ready with Spring Boot - 3 - Embedded Servers:
* Simplify Deployment with Spring Boot Embedded Servers
* Old Way:
    * Install Java
    * Install Web/Application Server
        * Tomcat/WebSphere/WebLogic, etc
    * Deploy the application WAR (Web ARchive)
        * This is the old WAR approach
        * Complex to setup!
* New Way: Embedded Server - Simpler (The server (Tomcat) is already part of the JAR file, making it an embedded server)
    * Install Java
    * Run JAR file
* Make JAR not WAR
* Embedded Server examples:
    * spring-boot-starter-tomcat (default)
    * spring-boot-starter-jetty
    * spring-boot-starter-undertow
* The “spring-boot-starter-web” dependency automatically brings in the “spring-boot-starter-tomcat” dependency, which is part of our JAR file. You can see which dependencies are automatically brought in by “command + click” on the “spring-boot-starter-web” dependency in the POM file.
* To do this:
    * In Eclipse’s Project Explorer, right click your project name -> Run As -> Maven build… 
        * In the “Goals:” text box type “clean install”, click Apply, click Run
* In the Console, there’s a log that says “Building jar: /Users/cadenbarton/eclipse-workspace/starting-spring-boot/target/learn-spring-boot-0.0.1-SNAPSHOT.jar”
    * In Terminal:
        * You can navigate to the “/Users/cadenbarton/eclipse-workspace/starting-spring-boot/target” folder
        * Make sure the .jar file is there (“learn-spring-boot-0.0.1-SNAPSHOT.jar”)
        * Run the JAR file with the command:
            * Java -jar learn-spring-boot-0.0.1-SNAPSHOT.jar
* If the jar runs successfully, you can see it in action by going to your localhost, examples:
    * http://localhost:8080/currency-configuration
    * http://localhost:8080/courses
* To terminate server, go back to terminal and press “control + c”

Get Production Ready with Spring Boot - 4 - Actuator:
* Spring Boot Actuator is for Monitoring your Applications
* Provides a number of endpoints:
    * Beans - Complete list of Spring beans in your app
    * Health - Application health info (is your app up and running?)
    * Metrics - Application metrics
    * Mappings - Details around Request Mappings
* To use Spring Actuator, add the following to your POM file:
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

* Run your app and go to “http://localhost:8080/actuator”, it will produce the actuator endpoints:
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "health-path": {
            "href": "http://localhost:8080/actuator/health/{*path}",
            "templated": true
        }
    }
}

* By default, only the health endpoint is active
* You can enable more endpoints by going to “application.properties” and adding:
    * management.endpoints.web.exposure.include=*
* You can also add specific endpoints; example:
    * management.endpoints.web.exposure.include=health,metrics
* Now “http://localhost:8080/actuator” lists many more endpoints
    * Examples of important ones:
        * http://localhost:8080/actuator/beans
            * Shows all loaded Spring beans, including all auto configured beans
        * http://localhost:8080/actuator/configprops
            * Along with everything we’ve added to “application.properties”, it shows all the things you can configure in the “application.properties” file
            * We added the “CurrencyServiceConfiguration” class, this endpoint logs the following info for that:
"currencyServiceConfiguration": {
    "prefix": "currency-service",
    "properties": {
        "url": "******",
        "key": "******",
        "username": "******"
    },
    "inputs": {
        "url": {
            "value": "******",
            "origin": "class path resource [application-dev.properties] - 3:22"
        },
        "key": {
            "value": "******",
            "origin": "class path resource [application-dev.properties] - 5:22"
        },
        "username": {
            "value": "******",
            "origin": "class path resource [application-dev.properties] - 4:27"
        }
    }
}

        * http://localhost:8080/actuator/env
            * Shows details about the environment (dev, QA, stage, prod, etc)
        * http://localhost:8080/actuator/metrics
            * The names listed here can be appended to the end of the URL with a forward slash to get specific metrics; example:
                * http://localhost:8080/actuator/metrics/http.server.requests

Understanding Spring Boot vs Spring vs Spring MVC:
* Spring Framework: All about dependency injection
    * @Component, @Autowired, Component Scan, etc..
    * Just dependency injection is NOT sufficient (You need other frameworks to build apps)
        * Spring Modules and Spring Projects: Extend Spring Eco System
            * Provide good integration with other frameworks (Hibernate/JPA, JUnit & Mockito for Unit Testing)
* Spring MVC (Spring Module): Simplify building web apps and REST API
    * You used to have to use Struts and it was very complex
    * Spring MVC provides annotations such as @Controller, @RestController, @RequestMapping(“/courses”)
* Spring Boot (Spring Project): Build PRODUCTION-READY apps QUICKLY
    * Starter Projects - Make it easy to build variety of applications
    * Auto Configuration - Eliminate configuration to setup Spring, Spring MVC, and other frameworks!
    * Enable non functional requirements (NFRs):
        * Actuator: Enables advanced monitoring of applications
        * Embedded Server: No need for separate application servers
        * Logging and Error Handling
        * Profiles and ConfigurationProperties
