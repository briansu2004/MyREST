# MyREST

My REST

## My REST projects

...

## application/merge-patch+json

### Why we need `application/merge-patch+json`? application/json is not good enough?

While application/json is a widely used media type for representing and exchanging data in JSON format, it does have some limitations when it comes to updating resources.

When updating resources using HTTP PUT or PATCH requests, the entire resource representation is usually sent in the request body. With application/json, this means that the entire resource object must be sent, including any unchanged properties. This can be inefficient, especially when dealing with large resources.

To address this issue, application/merge-patch+json was introduced as a media type that allows for partial updates of resources. With merge-patch+json, the server can merge the representation in the request body with the current state of the resource, applying only the changes that are specified in the request. This can help reduce the amount of data that needs to be sent and processed, which can be particularly useful in scenarios where bandwidth or processing power is limited.

Additionally, merge-patch+json provides a standardized way to express partial updates, which can help improve interoperability between different systems and APIs.

In summary, while application/json is a versatile media type for representing data in JSON format, application/merge-patch+json offers additional benefits for partial updates of resources, such as improved efficiency and interoperability.

### Can you give me an example of REST request with application/merge-patch+json format?

Sure, here's an example of a REST request with application/merge-patch+json format:

```bash
PATCH /collectionTreatmentMgmt/v1/collectionTreatmentStep/123 HTTP/1.1
Host: example.com
Content-Type: application/merge-patch+json
Content-Length: 25

{
  "status": "completed"
}
```

In this example, the request is being sent to the /collectionTreatmentMgmt/v1/collectionTreatmentStep/123 endpoint using the HTTP PATCH method. The request body contains a JSON payload with the update to be applied to the CollectionTreatmentStep object with id 123.

The JSON payload uses the merge-patch format, which allows for a partial update of the object. In this case, only the status property of the object is being updated to "completed".

Note that the Content-Type header is set to application/merge-patch+json to indicate that the payload is in the merge-patch format.

## REST Discovery

- Convention /api !

    There is a convention (recommended in REST API Design Handbook for example) that you expose a /api endpoint at the root of your service. This returns an XML or JSON response containing the "child" resources that your service supports e.g. /api/products.

- Swagger

- WADL vs WSDL

    There's a WSDL-like file for REST webservices, it's called WADL (Web Application Description Language)

- HATEOAS

- SoapUI can discover the REST services.

    It works as a proxy, writing down all requests/responses that pass through. Having requests and responses SoapUI recreates descriptions/definitions of the services. Now the definitions can be stored in WADL and WSDL formats (as WSDL and XML-Schema in XML world). It also can be stored in Swagger format.

    Swagger can be stored in SwaggerHub right from SoapUI, which is like GitHub for source code. SwaggerHub is one of many API management systems.

- Traditional way

## REST load balancing

- Cloud
  - Auto scaling with K8S, OpenShift, Docker, Apache etc.
  - AWS, Azure, GCP have their offers

- BFF Gateway (i.e. Kong)

## REST resilience

Resilient REST API

Resiliency is the ability to recover from failures and continue to function.

The word “Resilience” means “the ability to recover quickly from difficulties; toughness”. In the world of Microservices, it is very common for the services to fail but failure is not a problem. Our aim should be how quickly our system can recover from it, avoid cascading failures and the other microservices should not be affected from the failure of a particular micro-service (Apply this mantra in your real life also :p). It is not necessary to add resilience in our microservices but it is a good practice to do so in order to enhance the user expeerience.

- Retry logic

- Circuit breaking

How do you make an API resilient?

Tips for resilient API design

- Use headers to convey upcoming events / Information. ...
- Always return information where possible. ...
- Put meaningful data in API tokens to reduce DDoS Load. ...
- Tell the user when to retry. ...
- Tell the client what to do in the error reply. ...
- Mark every request with a specific request ID. ...
- Set your User-Agent.

Resilience in Spring Boot Microservices using Netflix Hystrix

pom.xml

```
<dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

@EnableCircuitBreaker

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;

@SpringBootApplication
@EnableCircuitBreaker
public class Microservice1Application {
public static void main(String[] args) {
  SpringApplication.run(Microservice1Application.class, args);
 }
}
```

@HystrixCommand

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;

@RestController
public class Micro1Controller {
 
     @HystrixCommand(fallbackMethod = "getMicro2InstanceFallback")
     @GetMapping("/getmicro2")
     public String getMicro2Instance()
     {
          String url = "http://localhost:8081/microservice2/port";
          String response = new RestTemplate().getForObject(url, 
                            String.class);
          return response;
     }
 
     public String getMicro2InstanceFallback()
     {
          return "Microservice2 is down!!";
     }
}
```

application.properties

```dos
hystrix.command.default.circuitBreaker.requestVolumeThreshold=2
hystrix.command.default.metrics.rollingStats.timeInMilliseconds=10000
hystrix.command.default.circuitBreaker.errorThresholdPercentage=50
hystrix.command.default.circuitBreaker.sleepWindowInMilliseconds=30000
```
