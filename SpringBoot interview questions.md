
# **How to Exclude Classes from Auto-Configuration in Spring Boot**

## **1. Using `@SpringBootApplication` or `@EnableAutoConfiguration`**

### **Exclude Specific Auto-Configuration Classes**

java

// Method 1: Using @SpringBootApplication
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,
    HibernateJpaAutoConfiguration.class,
    SecurityAutoConfiguration.class
})
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

// Method 2: Using @EnableAutoConfiguration
@Configuration
@EnableAutoConfiguration(exclude = {
    DataSourceAutoConfiguration.class
})
@ComponentScan
public class MyConfiguration {
}

### **Exclude by Class Name**

java

// Useful when the class is not in classpath at compile time
@SpringBootApplication(excludeName = {
    "org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration",
    "org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration"
})
public class MyApplication {
}

## **2. Using `application.properties` or `application.yml`**

### **Properties File**

properties

# Exclude specific auto-configuration classes
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration

# Exclude multiple - comma separated
spring.autoconfigure.exclude=com.example.CustomAutoConfiguration,org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration

### **YAML File**

yaml

spring:
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
      - org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration
      - com.example.MyCustomAutoConfiguration

### **Profile-Specific Exclusions**

yaml

# application-test.yml
spring:
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration
      - org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration

# application-prod.yml
spring:
  autoconfigure:
    exclude:
      - org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration

## **3. Excluding Components from Component Scan**

### **Using Component Scan Filters**

java

@SpringBootApplication
@ComponentScan(
    basePackages = "com.myapp",
    excludeFilters = {
        @ComponentScan.Filter(
            type = FilterType.ASSIGNABLE_TYPE,
            classes = {
                ExcludedService.class,
                ProblematicComponent.class
            }
        ),
        @ComponentScan.Filter(
            type = FilterType.REGEX,
            pattern = "com\\.myapp\\.excluded\\..*"
        ),
        @ComponentScan.Filter(
            type = FilterType.ANNOTATION,
            classes = {
                Deprecated.class,
                ExcludeFromApp.class
            }
        )
    }
)
