# Interview : Spring Boot Question

Spring Boot & Microservices Questions

<details>

<summary>Question - 1: How would you optimize application startup time if it is taking too long due to heavy bean initialization?</summary>

Answer: You can use lazy initialization by adding the `@Lazy` annotation. When enabled, Spring Boot will only create and inject the beans strictly required to start the server, while the remaining beans are created dynamically based on requirement.

**Real-world scenario**: You're building a monolith Spring Boot app where one module handles PDF report generation using a heavy third-party library, and another module sends emails via a complex SMTP client. These services are slow to instantiate — but they're rarely used (only when an admin triggers them). They shouldn't slow down startup for every developer running the app locally, or for the app during a rolling deploy.

**The problem without `@Lazy`**: Spring creates _all_ beans at startup. Your `PDFReportService` and `EmailDispatchService` are instantiated eagerly, even if no request touches them for hours.

**The fix**: Mark those heavy beans `@Lazy`. Spring defers their creation until the first time they're injected/used.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

***

**1. The heavy service — annotated with `@Lazy`**

```java
// PDFReportService.java
@Service
@Lazy  // ← Spring won't touch this until first use
public class PDFReportService {

    public PDFReportService() {
        // Simulates slow init: loading fonts, templates, iText library, etc.
        System.out.println("PDFReportService initializing... (heavy)");
    }

    public byte[] generateReport(Long orderId) {
        // actual PDF generation logic
        return new byte[]{};
    }
}
```

```java
// EmailDispatchService.java
@Service
@Lazy
public class EmailDispatchService {

    public EmailDispatchService() {
        System.out.println("EmailDispatchService initializing... (heavy SMTP setup)");
    }

    public void sendReportEmail(String to, byte[] pdf) {
        // actual email logic
    }
}
```

***

**2. The controller injects them — also needs `@Lazy` on the injection point**

This is the part people miss. You need `@Lazy` on **both** the bean definition AND the injection site. Otherwise Spring still creates it eagerly when injecting:

```java
// ReportController.java
@RestController
@RequestMapping("/api/reports")
public class ReportController {

    private final PDFReportService pdfService;
    private final EmailDispatchService emailService;

    // @Lazy here tells Spring: inject a proxy, not the real bean yet
    public ReportController(
            @Lazy PDFReportService pdfService,
            @Lazy EmailDispatchService emailService) {
        this.pdfService = pdfService;
        this.emailService = emailService;
    }

    @PostMapping("/{orderId}")
    public ResponseEntity<byte[]> generateAndEmail(
            @PathVariable Long orderId,
            @RequestParam String email) {

        // PDFReportService is actually instantiated HERE (first call)
        byte[] pdf = pdfService.generateReport(orderId);
        emailService.sendReportEmail(email, pdf);

        return ResponseEntity.ok()
                .header("Content-Type", "application/pdf")
                .body(pdf);
    }
}
```







</details>

<details>

<summary>Question - 2: How would you debug and fix a REST endpoint returning outdated cached data instead of fresh data?</summary>

Answer: First, verify if the caching mechanism (like `@Cacheable`) is implemented using a specific key; if it lacks a key, it will not update properly. Next, ensure that the corresponding POST or PUT operations are annotated with `@CachePut` so the cache manager automatically updates the cache upon data modification. Additionally, review the Time to Live (TTL) settings.

**Real-world scenario**: You have a `/api/products/{id}` GET endpoint. A product's price gets updated via PUT, but the client keeps seeing the old price. Classic stale cache bug.

There are usually **3 root causes** — missing cache key, missing `@CachePut` on the write side, or wrong TTL. Let me show all three with code.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

***

**Root Cause 1 — Missing `key` on `@Cacheable`**

Without a key, Spring uses a default key based on all method arguments. But if there's no argument (or you don't specify it), every call overwrites the **same single cache slot** — product 1, 2, and 3 all share one entry.

```java
// ❌ BROKEN — no key, everyone shares one cache slot
@Cacheable("products")
public Product getProduct(Long id) {
    return productRepository.findById(id).orElseThrow();
}

// ✅ FIXED — each product ID gets its own slot
@Cacheable(value = "products", key = "#id")
public Product getProduct(Long id) {
    return productRepository.findById(id).orElseThrow();
}
```

**Debugging tip** — add a log to confirm cache is actually being hit:

```java
@Cacheable(value = "products", key = "#id")
public Product getProduct(Long id) {
    log.info(">>> Cache MISS — hitting DB for product {}", id);
    // If you see this log on every call, the cache is never storing
    return productRepository.findById(id).orElseThrow();
}
```

If this log fires on every single request — the cache isn't working at all. Check if `@EnableCaching` is on your main class.

***

**Root Cause 2 — Write operation bypasses the cache (`@CachePut` missing)**

The most common bug. Your PUT updates the DB correctly, but the cache still holds the old object. The next GET reads the cache → stale data.

```java
// ❌ BROKEN — DB updated, cache untouched
@PutMapping("/{id}")
public Product updateProduct(@PathVariable Long id, @RequestBody Product updated) {
    return productService.updateProduct(id, updated);
}

// ProductService.java
public Product updateProduct(Long id, Product updated) {
    Product existing = productRepository.findById(id).orElseThrow();
    existing.setName(updated.getName());
    existing.setPrice(updated.getPrice());
    return productRepository.save(existing); // ← cache still has old version
}
```

```java
// ✅ FIXED — @CachePut writes updated value back to cache with same key
@CachePut(value = "products", key = "#id")
public Product updateProduct(Long id, Product updated) {
    Product existing = productRepository.findById(id).orElseThrow();
    existing.setName(updated.getName());
    existing.setPrice(updated.getPrice());
    return productRepository.save(existing); // ← cache updated automatically
}
```

**Alternatively** — if the update logic is complex and you'd rather just evict and let the next GET rebuild:

```java
// ✅ ALSO VALID — evict on write, rebuild on next GET
@CacheEvict(value = "products", key = "#id")
public Product updateProduct(Long id, Product updated) {
    Product existing = productRepository.findById(id).orElseThrow();
    existing.setPrice(updated.getPrice());
    return productRepository.save(existing);
}

// DELETE should always evict
@CacheEvict(value = "products", key = "#id")
public void deleteProduct(Long id) {
    productRepository.deleteById(id);
}
```

***

**Root Cause 3 — TTL too high**

Even with correct keys and `@CachePut`, if a third-party system or background job updates the DB directly (bypassing your service layer), you'll get stale data until the TTL expires.

```java
// CacheConfig.java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofMinutes(10))       // ❌ too long for frequently changing data
            .serializeValuesWith(
                RedisSerializationContext.SerializationPair
                    .fromSerializer(new GenericJackson2JsonRedisSerializer())
            );

        // ✅ Per-cache TTL — tailor to how often data actually changes
        Map<String, RedisCacheConfiguration> cacheConfigs = new HashMap<>();
        cacheConfigs.put("products", config.entryTtl(Duration.ofMinutes(2)));   // changes often
        cacheConfigs.put("categories", config.entryTtl(Duration.ofHours(6)));   // rarely changes
        cacheConfigs.put("userProfiles", config.entryTtl(Duration.ofMinutes(30)));

        return RedisCacheManager.builder(factory)
            .cacheDefaults(config)
            .withInitialCacheConfigurations(cacheConfigs)
            .build();
    }
}
```

***

**Full corrected `ProductService.java`**

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class ProductService {
    private final ProductRepository productRepository;

    // READ — cache per product ID
    @Cacheable(value = "products", key = "#id")
    public Product getProduct(Long id) {
        log.info("Cache MISS — querying DB for product {}", id);
        return productRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Product not found: " + id));
    }

    // UPDATE — write new value into cache with same key
    @CachePut(value = "products", key = "#id")
    public Product updateProduct(Long id, Product updated) {
        Product existing = productRepository.findById(id).orElseThrow();
        existing.setName(updated.getName());
        existing.setPrice(updated.getPrice());
        existing.setStock(updated.getStock());
        Product saved = productRepository.save(existing);
        log.info("Cache updated for product {}", id);
        return saved;
    }

    // DELETE — remove from cache entirely
    @CacheEvict(value = "products", key = "#id")
    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
        log.info("Cache evicted for product {}", id);
    }

    // NUCLEAR OPTION — clear entire products cache (use carefully)
    @CacheEvict(value = "products", allEntries = true)
    public void clearAllProductCache() {
        log.warn("Full products cache eviction triggered");
    }
}
```

***

**Quick debugging checklist**

| Check                                | Command / Code                                                                            |
| ------------------------------------ | ----------------------------------------------------------------------------------------- |
| Is `@EnableCaching` present?         | On `@SpringBootApplication` class                                                         |
| Is the bean being proxied correctly? | Don't call cached methods from within the same class — self-invocation bypasses the proxy |
| Is Redis actually running?           | `redis-cli ping` → should return `PONG`                                                   |
| What's currently in cache?           | `redis-cli keys "products::*"`                                                            |
| Inspect a specific entry             | `redis-cli get "products::1"`                                                             |
| Flush everything for testing         | `redis-cli flushdb`                                                                       |

**The self-invocation trap** is worth highlighting separately — it's subtle and burns people often:

java

```java
// ❌ BROKEN — calling getProduct() from within the same class
// bypasses the Spring proxy, so @Cacheable is ignored
public Product refreshAndReturn(Long id) {
    return this.getProduct(id); // 'this' = raw object, not the proxy
}

// ✅ FIXED — inject the service into itself, or restructure
@Autowired
private ProductService self; // Spring injects the proxied version

public Product refreshAndReturn(Long id) {
    return self.getProduct(id); // proxy is invoked → cache works
}
```

</details>

<details>

<summary>Question - 3: If you have two Spring Boot microservices communicating via REST and one frequently times out under load, how would you handle it?</summary>

Answer: Check if the load balancer is properly distributing traffic rather than sending all requests to a single server. Analyze the code for inefficiencies, such as O(n2) loops, that take too long to process. You can also implement circuit breaking using Resilience4j and tune your connection timeouts.





</details>



* Question: How do you configure a Spring Security setup where one API is publicly accessible, but another requires authentication?
  * Answer: You can achieve this by overriding the security filter chain. Use `requestMatchers()` alongside `permitAll()` to allow unauthenticated access to the public endpoints, and use `authenticated()` for the remaining endpoints.
* Question: Your application works locally but fails to connect to the production database. What steps would you take to analyze and fix this?
  * Answer: Verify that the correct active profile (like the `prod` profile) is activated. Check the config server to ensure the production database credentials are correct. Other crucial checks include verifying SSL certificates, evaluating the connection pool size, and checking the application logs.
* Question: How would you improve the performance of an API that takes too long because it loads a large dataset?
  * Answer: Implement pagination. Instead of sending the entire dataset at once, send the data in manageable chunks based on the requested page size and page number.
* Question: How would you secure sensitive configurations like DB passwords or API keys currently in application.properties?
  * Answer: Store the actual sensitive values in a separate Config Server and have the application fetch them during startup. Alternatively, if deploying via Kubernetes, define the sensitive variables inside K8s, place placeholders in the application, and use ConfigMaps to inject the values.
* Question: What might cause a scheduled job to run twice in production despite being deployed only once?
  * Answer: This can happen due to an error in the cron job expression/command used for scheduling. It can also occur if multiple instances of the application are running simultaneously or if multiple schedulers are inadvertently enabled.
* Question: How do you fix a REST API where users complain that large file uploads fail silently?
  * Answer: Spring Boot has a default limit for file uploads (typically 1MB). To fix this, manually increase the maximum file size and request size in the `application.properties` file using properties like `spring.servlet.multipart.max-file-size` and `spring.servlet.multipart.max-request-size`.
* Question: After upgrading Spring Boot, a custom bean stopped working because a default bean overrides it. How do you fix this?
  * Answer: You can resolve this by adding the @Primary qualifier to your custom bean to prioritize it, or you can completely disable the default bean's auto-configuration by excluding it.
* Question: What causes an API endpoint to return an empty JSON response even though the database has data?
  * Answer: It could be an issue with DTO mapping, where the fields are not being properly populated with data fetched from the DB. Another cause could be Jackson annotations like `@JsonInclude(JsonInclude.Include.NON_NULL)` excluding fields entirely. Missing getters or general Jackson serialization issues can also result in empty responses.
* Question: How would you handle centralized configuration management for 10 microservices with different DB URLs and feature flags?
  * Answer: Implement a Spring Cloud Config Server to centrally store all required properties. The individual microservices will fetch their configurations from this central server upon startup, allowing you to update configurations in just one place instead of across 10 different servers.
* Question: How would you briefly design a checkout process for an e-commerce platform using microservices?
  * Answer: The user adds items via a Cart Service, proceeds to the Payment Service to execute the transaction, then moves to an Order Service for confirmation, and finally triggers a Notification Service to send order details to the user.
* Question: What happens if two auto-configuration classes define the same bean?
  * Answer: Spring Boot will throw a BeanDefinitionOverrideException, and the application will fail to start. This conflict can be resolved using the `@Primary` annotation.
* Question: What happens if you define two Spring Data JPA repositories for the same entity?
  * Answer: It causes a bean conflict, specifically a BeanDefinitionException. To fix it, you must use `@Primary` and `@Qualifier` annotations to explicitly declare which repository bean to inject.
* Question: What happens if no active profile is set in Spring Boot?
  * Answer: It will default to fetching configurations from the standard `application.properties` file.
* Question: What happens if you annotate a class with both @Controller and @RestController?
  * Answer: It will not create an issue, but it will function simply as a normal `@Controller`. Note that `@RestController` is essentially a combination of `@Controller` and `@ResponseBody`.
