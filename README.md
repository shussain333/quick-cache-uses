# What is quick-cache
Quick cache is lightweight library developed on java 17. But you can use on Java 8 as well. It can cache the data on specific pod or server. FYI, it is not distributed. Best case to use it, when you have some static data (Should be loaded when application starts) or dynamic limited data (Depending on system capacity). Quick cache library can be used with Spring, Spring-boot and core-java applications. You can specify your on eviction policy in this cache.

# Supported eviction [policy](https://en.wikipedia.org/wiki/Cache_replacement_policies)
By default, LRU (Least recently used) cache strategy is used. List of supported eviction policies are -
1. LRU (default)
2. LFU
3. MRU
4. FIFO

# How to use quick-cache
You can use this cache with below build tools
1. Apache Maven
2. Gradle
3. Gradle (Short)
4. Gradle (Kotlin)
5. sbt
6. ivy
7. grape
8. leiningen
9. buildr

Example using maven -
```
<dependency>
    <groupId>io.github.shussain333</groupId>
    <artifactId>quick-cache</artifactId>
    <version>1.0.0</version>
</dependency>
```

#### You can find latest version and other build tool support on [Maven Central](https://central.sonatype.com/artifact/io.github.shussain333/quick-cache/overview)

# How to use quick-cache
This section divided into 2 path -
1. How to use quick-cache with spring or spring-boot application
2. How to use quick-cache with core-java application

## 1. How to use with spring or spring-boot application
There is little bit config you will have to add in application.yml or application.properties or anywhere else wherever you have hosted your config. Here are 2 ways supported and you can use either 1 at once -

1. Quick cache supports default single cache for entire application
   1. application.yml config
      ```yaml
       com:
         sartaj:
           quick-cache:
             default:
               maxCapacity: 200
               evictionPolicy: FIFO      
      ```
   2. or application.properties config
      ```properties
      com.sartaj.quick-cache.default.maxCapacity=200
      com.sartaj.quick-cache.default.evictionPolicy: FIFO
      ```
   3. Once either one of above step is done, You can inject dependency of quick-cache in any where in your application. And making changes at once place impact change at other place.
      ```java
        import com.sartaj.cache.factory.InMemoryCacheFactory;
        import io.swagger.v3.oas.annotations.responses.ApiResponse;
        import org.springframework.http.ResponseEntity;
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.ResponseBody;
        import org.springframework.web.bind.annotation.RestController;
        
        import java.util.Optional;
        
        /**
        * @author sartajhussain
        */
        @RestController("Check my status")
        @RequestMapping("ping")
        public class PingController {
        
            // You can use either constructor dependency or use @autowired with variable declaration
            // @Autowired
            private final InMemoryCacheFactory inMemoryCacheFactory;
        
            /**
            * Inject quick-cache dependency to the constructor.
            * @param inMemoryCacheFactory quick cache entry point
            */
            public PingController(InMemoryCacheFactory inMemoryCacheFactory) {
                this.inMemoryCacheFactory = inMemoryCacheFactory;
            }
        
            /**
            * Sample API to see how quick cache can be used
            * @return string
            */
            @GetMapping
            @ResponseBody
            @ApiResponse(description = "If service is running fine the response some content")
            public ResponseEntity<String> ping() {
                // Push string data in the cache
                Optional<String> stringOptional = inMemoryCacheFactory.getInMemoryCache("default").put("test", "Hi, I am from admin cache store");
            
                // Retrieve data from cache and return in the API response
                return ResponseEntity.ok(inMemoryCacheFactory.getInMemoryCache("default").get("test", String.class).get());
            }
        }      
        ```
   2. And Quick cache supports context based multi cache for entire application
       1. application.yml config
           ```yaml
           com:
             sartaj:
               quick-cache:
                 multiCache:
                   admin:
                   maxCapacity: 200
                   evictionPolicy: FIFO
                 user:
                   maxCapacity: 200
                   evictionPolicy: LFU
            ```
      2. or application.properties config
         ```properties
         com.sartaj.quick-cache.multiCache.admin.maxCapacity=200
         com.sartaj.quick-cache.multiCache.admin.evictionPolicy: FIFO
      
         com.sartaj.quick-cache.multiCache.user.maxCapacity=200
         com.sartaj.quick-cache.multiCache.user.evictionPolicy: LFU
         ```
      3. Once either one of above step is done, You can inject dependency of quick-cache in any where in your application. And making changes at once place impact change at other place.
         ```java
           import com.sartaj.cache.factory.InMemoryCacheFactory;
           import io.swagger.v3.oas.annotations.responses.ApiResponse;
           import org.springframework.http.ResponseEntity;
           import org.springframework.web.bind.annotation.GetMapping;
           import org.springframework.web.bind.annotation.RequestMapping;
           import org.springframework.web.bind.annotation.ResponseBody;
           import org.springframework.web.bind.annotation.RestController;
        
           import java.util.Optional;
        
           /**
           * @author sartajhussain
           */
           @RestController("Check my status")
           @RequestMapping("ping")
           public class PingController {
        
               // You can use either constructor dependency or use @autowired with variable declaration
               // @Autowired
               private final InMemoryCacheFactory inMemoryCacheFactory;
        
               /**
               * Inject quick-cache dependency to the constructor.
               * @param inMemoryCacheFactory quick cache entry point
               */
               public PingController(InMemoryCacheFactory inMemoryCacheFactory) {
                   this.inMemoryCacheFactory = inMemoryCacheFactory;
               }
        
               /**
               * Sample API to see how quick cache can be used
               * @return string
               */
               @GetMapping
               @ResponseBody
               @ApiResponse(description = "If service is running fine the response some content")
               public ResponseEntity<String> ping() {
                   // Push string data in the cache
                   Optional<String> stringOptional = inMemoryCacheFactory.getInMemoryCache("admin").put("test", "Hi, I am from admin cache store");
            
                   // Retrieve data from cache and return in the API response
                   return ResponseEntity.ok(inMemoryCacheFactory.getInMemoryCache("admin").get("test", String.class).get());
               }
           }      
           ```
      