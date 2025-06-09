# How to Implement Feign Client in Spring Boot Microservices?

- [Feign Client](https://javatechonline.com/feign-client/)
 - [java](https://javatechonline.com/java/)
 - [Microservices](https://javatechonline.com/microservices/)
 - [Spring Boot](https://javatechonline.com/spring-boot/)
 - [Spring Cloud](https://javatechonline.com/spring-cloud/)

 by [devs5003](https://javatechonline.com/author/erdsingh24/) - May 10, 20249

Last Updated on May 1st, 2025

![How to Implement Feign Client in Spring Boot Microservices?](https://javatechonline.com/wp-content/uploads/2021/07/OpenFeignClient-1.jpg)When two web applications communicate with each other for data exchange, they work on Producer-Consumer technique. An application who produces data is known as a Producer/Provider application. Similarly the one who consumes data is known as Consumer application. As a Java developer, we might be very familiar with REST API for Producer application whereas RestTemplate for Consumer application. With Microservices based application also, two Microservices communicate with each other and follow the Producer-Consumer model. Here, in consumer side, we use a concept ‘Feign Client’ as a better option instead of RestTemplate in order to minimize our effort of coding. Therefore, our topic of discussion is ‘How to Implement Feign Client in Spring Boot Microservices?’.

Apart from consuming REST services in an easy way, FeignClient/OpenFeign when combined with Eureka also offers us an easy load balancing. We will discuss about both the features of OpenFeign step by step in this article. Let’s discuss our topic ‘How to Implement Feign Client in Spring Boot Microservices?’ and the related concepts.

Table of Contents

[](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#)

- [What is Feign or Open Feign?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#What_is_Feign_or_Open_Feign)
- [Why we use Feign Client in Microservices?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Why_we_use_Feign_Client_in_Microservices)
- [How to Implement Feign Client in Spring Boot Microservices?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#How_to_Implement_Feign_Client_in_Spring_Boot_Microservices)
- [Details of the Use Case](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Details_of_the_Use_Case)
- [Create Microservice #1(Eureka Server)](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Create_Microservice_1Eureka_Server)
    - [Step #1: Create a Spring Boot Project](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step_1_Create_a_Spring_Boot_Project)
    - [Step #2: Apply Annotation @EnableEurekaServer at the main class](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step_2_Apply_Annotation_EnableEurekaServer_at_the_main_class)
    - [Step #3: Modify application.properties file](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step_3_Modify_applicationproperties_file)
        - [eureka.client.register-with-eureka=false](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#eurekaclientregister-with-eurekafalse)
        - [eureka.client.fetch-registry=false](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#eurekaclientfetch-registryfalse)
- [Create Microservice #2(Producer Service)](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Create_Microservice_2Producer_Service)
    - [Step#1: Create a Spring Boot Project](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step1_Create_a_Spring_Boot_Project)
    - [Step#2: Apply Annotation @EnableEurekaClient at the main class](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step2_Apply_Annotation_EnableEurekaClient_at_the_main_class)
    - [Step#3: Modify application.properties file](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step3_Modify_applicationproperties_file)
    - [Step#4: Create Model class as Book.java](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step4_Create_Model_class_as_Bookjava)
    - [Step#5: Create a RestContoller class as BookRestController.java](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step5_Create_a_RestContoller_class_as_BookRestControllerjava)
- [Create Microservice #3(Consumer Service)](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Create_Microservice_3Consumer_Service)
    - [Step#1: Create a Spring Boot Project](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step1_Create_a_Spring_Boot_Project-2)
    - [Step#2: Apply Annotation @EnableEurekaClient and @EnableFeignClients at the main class](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step2_Apply_Annotation_EnableEurekaClient_and_EnableFeignClients_at_the_main_class)
    - [Step#3: Modify application.properties file](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step3_Modify_applicationproperties_file-2)
    - [Step#4: Create Model class as Book.java](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step4_Create_Model_class_as_Bookjava-2)
    - [Step#5: Create an interface as BookRestConsumer.java](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step5_Create_an_interface_as_BookRestConsumerjava)
    - [Step#6: Create a RestController as StudentRestController.java](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Step6_Create_a_RestController_as_StudentRestControllerjava)
- [How to test FeignClient Enabled Microservice?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#How_to_test_FeignClient_Enabled_Microservice)
- [How to test FeignClient Enabled Microservice as a Load Balancer?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#How_to_test_FeignClient_Enabled_Microservice_as_a_Load_Balancer)
- [FAQs](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#FAQs)
    - [Is Feign Client Limited to HTTP Communication?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Is_Feign_Client_Limited_to_HTTP_Communication)
    - [What is difference between feign client and RestTemplate?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#What_is_difference_between_feign_client_and_RestTemplate)
    - [Can We Customize Feign Client Behavior?](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Can_We_Customize_Feign_Client_Behavior)
- [Conclusion](https://javatechonline.com/how-to-implement-feign-client-in-spring-boot-microservices/#Conclusion)

ADVERTISEMENT

  

## What is Feign or Open Feign?

Feign, often referred to as “OpenFeign,” is a Java-based  [declarative REST Client](https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-feign.html). OpenFeign is often used in microservices and cloud-native applications to simplify communication between different services. It is a part of the Spring Cloud ecosystem and designed to work smoothly with other Spring Cloud components to create microservices-based applications. In simple words, it simplifies the process of writing web service clients.

In order to use Feign, create an interface and apply @FeignClient annotation on it. Moreover, it internally generates a Proxy class at runtime using Dynamic Proxy Pattern. It actually gets Application name (Service Instance) from Eureka and supports Making HTTP call. Generally, we use two annotations for this concept, they are:

**@EnableFeignClients** :  To apply at starter class

**@FeignClient(name=”ApplicationName”)** : To define an interface for a Consumer

## Why we use Feign Client in Microservices?

Feign simplifies the process of making API calls in a microservices architecture. It allows developers to focus on implementing their services instead of worrying about low-level details of HTTP communication. Further, there are multiple reasons to use Feign Client in Microservices:

1) **Simplifies API calls:** Feign allows us to define our API interface using annotations, which makes it easier to create API clients and eliminates a lot of boilerplate code.

2) **Declarative programming:** Feign uses declarative programming, which means that we only need to specify what we want to do, not how to do it. This makes it easier to read and maintain our code.

3) **Load balancing and service discovery:** Feign integrates well with other tools like Netflix Eureka and Consul, which offer service discovery and load balancing abilities. This makes it easier to call services that are running on different instances or nodes.

4) **Inter-service communication:** In a microservices architecture, services need to communicate with each other frequently. Feign makes it easier to call other services by creating a client for each service and using them to make HTTP requests.

## How to Implement Feign Client in Spring Boot Microservices?

Programmatically, FeignClient is implemented by creating an interface and annotating it with @FeignClient as below.

**@FeignClient(name="ApplicationName")**
**public interface <AnyName> {**

   **//abstract method**
   **@__Mapping("/path")**
   **public <ReturnType> <anyMethodName>(<params>);** 
    **.**
    **.**
    **.**
**}**

## Details of the Use Case

We are going to implement a simple use case to make the concept of Feign Client crystal clear. Here, we will use three Microservices: Eureka Server, Producer service, and Consumer Service. Both Producer as well as Consumer services will register themselves with Eureka in order to communicate with each other. Furthermore, let’s assume Book Service as a Producer and Student Service as a Consumer. Needless to say, Book service will publish the data and Student service will consume that data. Here, Feign Client will play an important role to consume data produced by Book service.

Apart from that we will see the magic of the Feign Client as a load balancer when used with Eureka. Moreover, we will implement load balancer in the following sections of this article.

Now, let’s find the answer of ‘How to Implement Feign Client in Spring Boot Microservices?’ by applying step by step process.

## Create Microservice #1(Eureka Server)

In order to discover and communicate Microservices with each other, we need to  create a Eureka Server Service. Creating a Eureka Server is itself similar to creating a Microservice. Moreover, it is just a Spring Boot Project that incorporates Spring Cloud’s Eureka Server dependency. In ‘application. properties’ file we will have some specific properties that will indicate that this application/microservice is a Eureka server. In order to create Eureka Server follow the below steps.

### Step #1: Create a Spring Boot Project

Here, we will use STS(Spring Tool Suite) to create our Spring Boot Project. If you are new to Spring Boot, visit [Internal Link](https://javatechonline.com/saving-data-into-database-using-spring-boot-data-jpa-step-by-step-tutorial/#Step_1_Creating_Starter_Project_using_STS) to create a sample project in spring boot. While creating a project in STS, add starter ‘Eureka Server’ in order to get features of it.

### Step #2: Apply Annotation @EnableEurekaServer at the main class

In order to make your application/microservice acts as Eureka server, you need to apply @EnableEurekaServer at the main class of your application.

**import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class SpringCloudEurekaServerApplication {

   public static void main(String[] args) {
      SpringApplication.run(SpringCloudEurekaServerApplication.class, args);
   }
}**

### Step #3: Modify application.properties file

Add below properties in your application.properties file.

**server.port=8761**
**eureka.client.register-with-eureka=false**
**eureka.client.fetch-registry=false**

#### eureka.client.register-with-eureka=false

Default value of property ‘eureka.client.register-with-eureka’ is true. Please note that this property is mandatory to include in Eureka Server in order to make its value as false. However, this is optional to add in case of other microservices/applications that are not Eureka server. Moreover, every microservice project is connected to Spring Cloud project that provides default value to true. Therefore, We should include ‘eureka.client.register-with-eureka=false’ for one time only in case of Eureka server as Eureka Server itself can’t be registered.

#### eureka.client.fetch-registry=false

This property signifies that Eureka Server is supported to fetch instance details of microservice to make intra-communication between microservices happen. If one microservice wants to communicate with another microservice by using Eureka then inside microservice we should add this property (eureka.client.fetch-registry) and set it to true. However, inside Eureka Server we should include this property with a value as false. However, Eureka server will never try to fetch registry as it is itself having a registry. Hence the value of this property in case of Eureka server will be false. Moreover, every microservice project is connected to Spring Cloud project that provides default value to true.

Default port for Eureka Server is 8761.

## Create Microservice #2(Producer Service)

In our case, Book Service is the provider/producer Service. Moreover, the Book service will publish the REST endpoints. Let’s develop it step by step.

### Step#1: Create a Spring Boot Project

Here, we will use STS(Spring Tool Suite) to create our Spring Boot Project. If you are new to Spring Boot, visit a separate article on ‘[How to create a sample project](https://javatechonline.com/saving-data-into-database-using-spring-boot-data-jpa-step-by-step-tutorial/#Step_1_Creating_Starter_Project_using_STS)‘ in spring boot. While creating a project in STS, add starter ‘Eureka Discovery Client’ , ‘Spring Web’ and ‘Lombok’ in order to get all required features. Furthermore, if you are new to ‘Lombok’, kindly visit ‘[How to configure Lombok](https://javatechonline.com/how-to-reduce-boilerplate-code-in-java-using-lombok-annotations/#How_to_configure_Lombok_in_eclipseSTS)‘ and to know all about it in detail.

### Step#2: Apply Annotation @EnableEurekaClient at the main class

**import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication**
**@EnableEurekaClient**
**public class SpringCloudFeignBookServiceApplication {**

    **public static void main(String[] args) {**
       **SpringApplication.run(SpringCloudFeignBookServiceApplication.class, args);**
    **}**
**}**

### Step#3: Modify application.properties file

Add below properties in your application.properties file.

**server.port=9000**
**spring.application.name=BOOK-SERVICE**
**eureka.client.service-url.default-zone=http://localhost:8761/eureka**           #Register with Eureka

Moreover, ‘eureka.client.service-url.default-zone=http://localhost:8761/eureka’ indicates that this service is getting registered with the Eureka Server.

### Step#4: Create Model class as Book.java

Let’s create model class as Book.java as below. Please note that, here we have used ‘Lombok’ annotations in order to reduce the boilerplate code.

**import lombok.AllArgsConstructor;**
**import lombok.Data;**
**import lombok.NoArgsConstructor;**

**@Data**
**@NoArgsConstructor**
**@AllArgsConstructor** 
**public class Book {**

     **private Integer bookId;**
     **private String bookName;**
     **private Double bookCost;**
**}**

### Step#5: Create a RestContoller class as BookRestController.java

In order to publish Book Service, we will create a BookRestController and define the endpoints as below. Please note that, here we are not using a database as our focus is on Feign Client functionality. Instead, we are using some hardcoded values and the Collections to store values wherever required.

**import java.util.List;**
**import org.springframework.beans.factory.annotation.Autowired;**
**import org.springframework.core.env.Environment;**
**import org.springframework.http.HttpStatus;**
**import org.springframework.http.ResponseEntity;**
**import org.springframework.web.bind.annotation.GetMapping;**
**import org.springframework.web.bind.annotation.PathVariable;**
**import org.springframework.web.bind.annotation.RequestMapping;**
**import org.springframework.web.bind.annotation.RestController;**
**import com.dev.springcloud.feign.model.Book;**

**@RestController**
**@RequestMapping("/book")**
**public class BookRestController {**

      **@Autowired**
      **Environment environment;**

      **@GetMapping("/data")**
      **public String getBookData() {**
         **return "data of BOOK-SERVICE, Running on port: "**
           **+environment.getProperty("local.server.port");**
      **}**

      **@GetMapping("/{id}")**
      **public Book getBookById(@PathVariable Integer id) {**
         **return new Book(id, "Head First Java", 500.75);**
      **}**

      **@GetMapping("/all")**
      **public List<Book> getAll(){**
         **return List.of(**
                **new Book(501, "Head First Java", 439.75),**
                **new Book(502, "Spring in Action", 340.75),**
                **new Book(503, "Hibernate in Action", 355.75)**
         **);**
      **}**

      **@GetMapping("/entity")**
      **public ResponseEntity<String> getEntityData() {**
         **return new ResponseEntity<String>(**
           **"Hello from BookRestController",** 
            **HttpStatus._OK_);**
      **}**
**}**

## Create Microservice #3(Consumer Service)

In our case, Student Service is the consumer Service. Also, the Consumer service will consume the services published by Book service. Our primary focus here is on the implementation of Feign Client. Additionally, we will see how the Feign Client makes our service call easy. Let’s develop it step by step.

### Step#1: Create a Spring Boot Project

Here, we will use STS(Spring Tool Suite) to create our Spring Boot Project. If you are new to Spring Boot, visit [Internal Link](https://javatechonline.com/saving-data-into-database-using-spring-boot-data-jpa-step-by-step-tutorial/#Step_1_Creating_Starter_Project_using_STS) to create a sample project in spring boot. While creating a project in STS, add starter ‘OpenFeign’, ‘Eureka Discovery Client’ , ‘Spring Web’ and ‘Lombok’ in order to get all required features. Moreover, if you are new to ‘Lombok’, kindly visit ‘[How to configure Lombok](https://javatechonline.com/how-to-reduce-boilerplate-code-in-java-lombok-api/#How_to_configure_Lombok_in_eclipseSTS)‘ and to know all about it in detail.

### Step#2: Apply Annotation @EnableEurekaClient and @EnableFeignClients at the main class

In order to get the features of OpenFeign, we will additionally need to apply @EnableFeignClients

**import org.springframework.boot.SpringApplication;**
**import org.springframework.boot.autoconfigure.SpringBootApplication;**
**import org.springframework.cloud.netflix.eureka.EnableEurekaClient;**
**import org.springframework.cloud.openfeign.EnableFeignClients;**

**@SpringBootApplication
@EnableEurekaClient**
**@EnableFeignClients**
**public class SpringCloudFeignStudentServiceApplication {**

     **public static void main(String[] args) {**
        **SpringApplication.run(SpringCloudFeignStudentServiceApplication.class, args);**
     **}**
**}**

### Step#3: Modify application.properties file

Add below properties in your application.properties file.

**server.port=9100**
**spring.application.name=STUDENT-SERVICE**
**eureka.client.service-url.default-zone=http://localhost:8761/eureka**

Needless to say, we need to add ‘eureka.client.service-url.default-zone=http://localhost:8761/eureka’ in order to get Student Service registered with Eureka Server.

### Step#4: Create Model class as Book.java

Let’s replicate the model class as Book.java as it is in the producer service. Please note that, here we have used ‘Lombok’ annotations in order to reduce the boilerplate code.

**import lombok.AllArgsConstructor;**
**import lombok.Data;**
**import lombok.NoArgsConstructor;**

**@Data**
**@NoArgsConstructor**
**@AllArgsConstructor**
**public class Book {**

      **private Integer bookId;**
      **private String bookName;**
      **private Double bookCost;**
**}**

### Step#5: Create an interface as BookRestConsumer.java

This is the most important file when talking about Feign Client. At Interface level, we need to apply @FeignClient annotation and provide the name of producer service/application as below. Further, declare the methods as per the requirement.

**import org.springframework.cloud.openfeign.FeignClient;**
**import org.springframework.http.ResponseEntity;**
**import org.springframework.web.bind.annotation.GetMapping;**
**import org.springframework.web.bind.annotation.PathVariable;**
**import com.dev.springcloud.feign.model.Book;**

**@FeignClient(name="BOOK-SERVICE")**
**public interface BookRestConsumer {**

      **@GetMapping("/book/data")**
      **public String getBookData();**

      **@GetMapping("/book/{id}")**
      **public Book getBookById(****@PathVariable Integer id);**

      **@GetMapping("/book/all")**
      **public List<Book> getAllBooks();**

      **@GetMapping("/book/entity")**
      **public ResponseEntity<String> getEntityData();**
**}**

### Step#6: Create a RestController as StudentRestController.java

At the end, create a RestController as StudentRestController to receive the data from Book service as below. The important point to note here is that we need to auto-wire the BookRestConsumer here in this class and then use it.

**import org.springframework.beans.factory.annotation.Autowired;**
**import org.springframework.http.ResponseEntity;**
**import org.springframework.web.bind.annotation.GetMapping;**
**import org.springframework.web.bind.annotation.RequestMapping;**
**import org.springframework.web.bind.annotation.RestController;**
**import com.dev.springcloud.feign.consumer.BookRestConsumer;**

**@RestController**
**@RequestMapping("/student")**
**public class StudentRestController {**

      **@Autowired**
      **private BookRestConsumer consumer;**

      **@GetMapping("/data")**
      **public String getStudentInfo() {**
         **System.out.println(consumer.getClass().getName());**  //prints as a proxy class
         **return "Accessing from STUDENT-SERVICE ==> " +consumer.getBookData();**
      **}**

      **@GetMapping("/allBooks")**
      **public String getBooksInfo() {**
         **return "Accessing from STUDENT-SERVICE ==> " + consumer.getAllBooks();**
      **}**

      **@GetMapping("/getOneBook/{id}")**
      **public String getOneBookForStd(@PathVariable Integer id) {**
         **return "Accessing from STUDENT-SERVICE ==> " + consumer.getBookById(id);** 
      **}**

      **@GetMapping("/entityData")**
      **public String printEntityData() {**
         **ResponseEntity<String> resp = consumer.getEntityData();**
         **return "Accessing from STUDENT-SERVICE ==> " + resp.getBody() +" , status is:" + resp.getStatusCode();**
      **}**
**}**

That’s All from coding!

## How to test FeignClient Enabled Microservice?

It’s time to test our FeignClient enabled Microservice. Please follow below steps:

1) Start Service/Application containing Eureka Server  
2) Start Producer Service/Application (Book)  
3) Start Consumer Service/Application (Student)  
4) Open a browser window, hit below URLs and observe the results.

http://localhost:9100/student/data  
http://localhost:9100/student/allBooks  
http://localhost:9100/student/getOneBook/501  
http://localhost:9100/student/entityData

## How to test FeignClient Enabled Microservice as a Load Balancer?

Now we are going to test load-balancing functionality of FeignClient enabled Microservice. Please follow below steps:

1) Start Service/Application containing Eureka Server  
2) Start multiple instances of Producer Service/Application (Book). In order to get it, change the server port in application.properties file, save the file and then start the application. Let’s assume that we need three instances of the application. Port 9000 is already configured, now configure two more 9001 and 9002.  
3) Start Consumer Service/Application (Student)  
4) Now, open a browser window, hit  URL ‘http://localhost:9100/student/data‘ which contains server port information. Further, refresh the browser multiple times and observe the port number in the results. It will randomly pick the server from 9000, 9001 and 9002 as shown below.

![Feign Client as Load Balancer](https://javatechonline.com/wp-content/uploads/2021/07/FeignClient-1-1.jpg)

**Also read:** Other **[Implementations & Tutorials on Java Microservices](https://javatechonline.com/microservices-tutorial/)**

## FAQs

### **Is Feign Client Limited to HTTP Communication?**

Yes, Feign Client primarily supports HTTP-based protocols and is built for HTTP communication. To communicate with the target microservices, it uses HTTP methods like GET, POST, PUT, DELETE, etc. If you need to communicate using a different communication protocol than HTTP, alternative solutions or technologies may be better appropriate for your use case.

### What is difference between feign client and RestTemplate?

**RestTemplate:** RestTemplate is a synchronous HTTP client provided by Spring Framework. It requires developers to manually create HTTP requests and handle the responses. RestTemplate offers a wide range of methods for making HTTP requests and provides flexibility in terms of customization. However, it involves more boilerplate code, and can be less intuitive as compared to Feign Client.

**Feign Client:** Feign Client is a declarative web service client developed by Netflix. It allows developers to define interfaces with annotated methods representing the HTTP API of the target microservice. Feign Client handles the elementary details of making HTTP requests, including request marshalling, load balancing. It simplifies the development process and focus on a more concise and declarative coding style.

### Can We Customize Feign Client Behavior?

Yes, we can customize the behavior of the Feign Client with the help of additional configurations.

**Adding Request Interceptors:** We can add request interceptors to modify or log requests before they are sent.

**Implementing Error Handling:** Feign Client allows us to define custom error handling mechanisms, such as handling specific HTTP error codes or exceptions.

**Configuring Retry Logic:** We can configure Feign Client to automatically retry failed requests with configurable retry policies.

**Enabling Compression:** Feign Client supports the request and response compression, which can be enabled to reduce network bandwidth usage.

## Conclusion

After going through all the theoretical & example part of ‘How to Implement Feign Client in Spring Boot Microservices?’, finally, we should be able to implement Feign Client with the Microservices. Similarly, we expect from you to further extend these examples and implement them in your project accordingly. I hope you might be convinced by the article ‘How to Implement Feign Client in Spring Boot Microservices?’. In addition, If there is any update in the future, we will also update the article accordingly. Moreover, Feel free to provide your comments in the comments section below.

### _Related_