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

<summary>Question - 3: If you have two Spring Boot microservices communicating via REST and one frequently time out under load, how would you handle it?</summary>

Answer: Check if the load balancer is properly distributing traffic rather than sending all requests to a single server. Analyze the code for inefficiencies, such as O(n2) loops, that take too long to process. You can also implement circuit breaking using Resilience4j and tune your connection timeouts.





</details>

<details>

<summary>Question - 4: How would you improve the performance of an API that takes too long because it loads a large dataset? ⭐</summary>

Answer: Implement pagination. Instead of sending the entire dataset at once, send the data in manageable chunks based on the requested page size and page number.

**Real-world scenario**: You have a `/api/orders` endpoint that loads all orders for a merchant. As the business grows to 500k+ orders, the API starts timing out. Pagination alone helps, but there are 4 techniques that work together.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Layer 1 — Pagination (offset vs cursor)**

**Offset pagination — simple but has a hidden problem:**

```java
// ❌ Works fine at page 1. Crawls at page 5000.
// OFFSET 100000 means DB scans and discards 100k rows before returning 20
@GetMapping("/api/orders")
public Page<OrderDTO> getOrders(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size) {

    Pageable pageable = PageRequest.of(page, size, Sort.by("createdAt").descending());
    return orderRepository.findAll(pageable).map(OrderMapper::toDTO);
}
```

**Cursor pagination — consistent performance at any depth:**

```java
// ✅ No OFFSET — always fast regardless of how deep you are
@GetMapping("/api/orders")
public CursorPageResponse<OrderDTO> getOrders(
        @RequestParam(required = false) String cursor,  // last seen order ID
        @RequestParam(defaultValue = "20") int size,
        @RequestParam Long merchantId) {

    List<Order> orders = orderRepository.findByMerchantWithCursor(
        merchantId, cursor, size + 1  // fetch one extra to check hasMore
    );

    boolean hasMore = orders.size() > size;
    if (hasMore) orders = orders.subList(0, size); // trim the extra

    String nextCursor = hasMore
        ? orders.get(orders.size() - 1).getId().toString()
        : null;

    return CursorPageResponse.<OrderDTO>builder()
        .data(orders.stream().map(OrderMapper::toDTO).toList())
        .nextCursor(nextCursor)
        .hasMore(hasMore)
        .build();
}
```

```java
// OrderRepository.java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Cursor query — WHERE id < :cursor means "everything after the last seen item"
    @Query("""
        SELECT o FROM Order o
        WHERE o.merchantId = :merchantId
          AND (:cursor IS NULL OR o.id < :cursor)
        ORDER BY o.id DESC
        """)
    List<Order> findByMerchantWithCursor(
        @Param("merchantId") Long merchantId,
        @Param("cursor") Long cursor,
        Pageable pageable  // just used for LIMIT here
    );
}
```

```java
// CursorPageResponse.java
@Builder
public record CursorPageResponse<T>(
    List<T> data,
    String nextCursor,  // client passes this back as ?cursor= on next call
    boolean hasMore,
    int count
) {}
```

***

**Layer 2 — Projection (fetch only what the UI needs)**

```java
// ❌ BROKEN — fetches all 30+ columns including blobs, heavy nested objects
public List<Order> getOrdersForDashboard(Long merchantId) {
    return orderRepository.findByMerchantId(merchantId); // full entity
}

// ✅ FIXED — interface-based projection, only 4 fields
// Spring Data generates SELECT id, status, total_amount, created_at
public interface OrderSummary {
    Long getId();
    String getStatus();
    BigDecimal getTotalAmount();
    LocalDateTime getCreatedAt();
}

// OrderRepository.java
List<OrderSummary> findByMerchantId(Long merchantId, Pageable pageable);
```

**Or use a DTO projection directly in JPQL if you need computation:**

```java
// OrderDTO.java
public record OrderDTO(Long id, String status, BigDecimal total, LocalDateTime createdAt) {}

// OrderRepository.java
@Query("""
    SELECT new com.example.dto.OrderDTO(
        o.id, o.status, o.totalAmount, o.createdAt
    )
    FROM Order o
    WHERE o.merchantId = :merchantId
      AND (:cursor IS NULL OR o.id < :cursor)
    ORDER BY o.id DESC
    """)
List<OrderDTO> findSummariesWithCursor(
    @Param("merchantId") Long merchantId,
    @Param("cursor") Long cursor,
    Pageable pageable
);
```

***

**Layer 3 — DB Indexing (make the query itself fast)**

Without an index, every query does a full table scan. This is the silent killer.

```sql
-- See what's happening first
EXPLAIN ANALYZE
SELECT id, status, total_amount, created_at
FROM orders
WHERE merchant_id = 42
  AND id < 789
ORDER BY id DESC
LIMIT 20;

-- If you see "Seq Scan" in the output → missing index
-- You want to see "Index Scan"
```

```sql
-- ✅ Composite index matching your query's WHERE + ORDER BY
-- merchant_id first (equality filter), then id (range filter + sort)
CREATE INDEX idx_orders_merchant_id
    ON orders (merchant_id, id DESC);

-- If you also filter by status frequently:
CREATE INDEX idx_orders_merchant_status
    ON orders (merchant_id, status, id DESC);
```

```java
// Or define it on the JPA entity
@Entity
@Table(
    name = "orders",
    indexes = {
        @Index(name = "idx_orders_merchant_id",
               columnList = "merchant_id, id DESC"),
        @Index(name = "idx_orders_merchant_status",
               columnList = "merchant_id, status, id DESC")
    }
)
public class Order {
    @Id
    private Long id;
    private Long merchantId;
    private String status;
    private BigDecimal totalAmount;
    private LocalDateTime createdAt;
    // ...
}
```

***

**Layer 4 — Caching hot pages**

The first page of results is hit by every user on every dashboard load. Cache it.

```java
// OrderService.java
@Service
@RequiredArgsConstructor
@Slf4j
public class OrderService {

    private final OrderRepository orderRepository;

    // Cache only page 1 (cursor=null) — it's the only one hit frequently
    @Cacheable(
        value = "orders-page",
        key = "#merchantId + ':' + #size",
        condition = "#cursor == null"  // only cache the first page
    )
    public CursorPageResponse<OrderDTO> getOrders(
            Long merchantId, String cursor, int size) {

        log.info("Cache MISS — querying DB for merchant {} page 1", merchantId);
        // ... cursor query logic
    }

    // Evict when a new order is created for this merchant
    @CacheEvict(value = "orders-page", key = "#order.merchantId + ':*'")
    public Order createOrder(Order order) {
        return orderRepository.save(order);
    }
}
```

```yaml
# application.yml — short TTL for live data
resilience4j:
spring:
  cache:
    type: redis

# Per-cache TTL config
resilience4j:
  # (in CacheConfig.java)
```

```java
// CacheConfig.java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisCacheConfiguration ordersConfig = RedisCacheConfiguration
            .defaultCacheConfig()
            .entryTtl(Duration.ofSeconds(30));  // short TTL — orders change often

        return RedisCacheManager.builder(factory)
            .withCacheConfiguration("orders-page", ordersConfig)
            .build();
    }
}
```

***

**Bonus — async count for `totalCount`**

`SELECT COUNT(*)` on 500k rows is expensive and usually not needed on every page. Decouple it:

```java
@GetMapping("/api/orders")
public CursorPageResponse<OrderDTO> getOrders(...,
        @RequestParam(defaultValue = "false") boolean includeTotalCount) {

    List<OrderDTO> data = orderRepository.findSummariesWithCursor(...);

    // Only run COUNT if the UI explicitly asks — e.g. only on first load
    Long totalCount = includeTotalCount
        ? orderRepository.countByMerchantId(merchantId)
        : null;

    return CursorPageResponse.<OrderDTO>builder()
        .data(data)
        .nextCursor(nextCursor)
        .hasMore(hasMore)
        .totalCount(totalCount)  // null when not requested
        .build();
}
```

***

**Decision guide — which technique for which problem**

| Symptom                           | Root cause                      | Fix                          |
| --------------------------------- | ------------------------------- | ---------------------------- |
| Slow at page 1 too                | No index / full table scan      | Add composite index          |
| Fast at page 1, slow at page 500+ | OFFSET scanning discarded rows  | Switch to cursor pagination  |
| Response payload is huge          | Fetching unused columns / blobs | Use DTO/interface projection |
| Page 1 hammered by all users      | Repeated identical DB queries   | Cache first page, short TTL  |
| COUNT query killing performance   | COUNT(\*) on every request      | Make totalCount optional     |

The mental model: **pagination controls&#x20;**_**how much**_**&#x20;you fetch, projection controls&#x20;**_**what**_**&#x20;you fetch, indexing controls&#x20;**_**how fast**_**&#x20;the DB finds it, and caching controls&#x20;**_**how often**_**&#x20;you ask the DB at all.** All four together is what takes a 40s endpoint to under 20ms.



</details>

<details>

<summary>Question - 5: How would you secure sensitive configurations like DB passwords or API keys currently in application.properties?</summary>

Answer: Store the actual sensitive values in a separate Config Server and have the application fetch them during startup. Alternatively, if deploying via Kubernetes, define the sensitive variables inside K8s, place placeholders in the application, and use ConfigMaps to inject the values.

**Real-world scenario**: Your `application.properties` has DB password, JWT secret, and a third-party payment API key hardcoded. Someone accidentally pushes it to a public GitHub repo. Game over. Here's how you prevent that across different deployment contexts.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**The baseline — what your `application.properties` should look like**

```properties
# ✅ application.properties — safe to commit, zero real values
spring.datasource.url=jdbc:postgresql://${DB_HOST:localhost}:${DB_PORT:5432}/${DB_NAME:orderdb}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

jwt.secret=${JWT_SECRET}
payment.api.key=${PAYMENT_API_KEY}

# ${VAR:defaultValue} syntax — fallback used only in local dev
server.port=${APP_PORT:8080}
```

```java
// Bind into a config class with validation — fail fast at startup if missing
@Configuration
@ConfigurationProperties(prefix = "payment")
@Validated
public class PaymentConfig {

    @NotBlank(message = "Payment API key must be configured")
    private String apiKey;

    // getter/setter
}
// If PAYMENT_API_KEY env var is missing → app refuses to start with clear error
// Much better than a NullPointerException at runtime during a live transaction
```

***

**Strategy 1 — Environment Variables**

**Local dev — `.env` file (never commit this):**

```bash
# .env — add to .gitignore immediately
DB_HOST=localhost
DB_PORT=5432
DB_NAME=orderdb
DB_USERNAME=appuser
DB_PASSWORD=localdevpassword123
JWT_SECRET=local-dev-jwt-secret-min-32-chars!!
PAYMENT_API_KEY=pk_test_abc123
```

```bash
# .gitignore — these must be here
.env
.env.local
.env.*.local
application-local.properties
application-secrets.properties
```

**Load `.env` in Spring Boot with a library:**

```xml
<!-- pom.xml -->
<dependency>
    <groupId>me.paulschwarz</groupId>
    <artifactId>spring-dotenv</artifactId>
    <version>4.0.0</version>
</dependency>
```

```yaml
# application.yml — tells Spring to load .env
spring:
  config:
    import: optional:file:.env[.properties]
```

**Docker — inject at container run time:**

```yaml
# docker-compose.yml — values from host env, never hardcoded
services:
  order-service:
    image: order-service:latest
    environment:
      - DB_HOST=postgres
      - DB_USERNAME=${DB_USERNAME}       # reads from your shell env
      - DB_PASSWORD=${DB_PASSWORD}       # never in this file
      - JWT_SECRET=${JWT_SECRET}
    env_file:
      - .env.docker                      # or from an env file outside the repo
```

**GitHub Actions CI/CD — secrets from repo settings:**

```yaml
# .github/workflows/deploy.yml
jobs:
  deploy:
    steps:
      - name: Deploy to server
        env:
          DB_PASSWORD: ${{ secrets.DB_PASSWORD }}      # set in GitHub → Settings → Secrets
          JWT_SECRET: ${{ secrets.JWT_SECRET }}
          PAYMENT_API_KEY: ${{ secrets.PAYMENT_API_KEY }}
        run: |
          ./deploy.sh
```

***

**Strategy 2 — HashiCorp Vault (microservices / enterprise)**

**Dependencies:**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-vault-config</artifactId>
</dependency>
```

**Store secrets in Vault (one-time setup):**

```bash
# Store secrets under a path matching your app name
vault kv put secret/order-service \
    db.password="ProdSuperSecret!" \
    jwt.secret="prod-jwt-secret-min-32-chars!!" \
    payment.api.key="pk_live_realkey"
```

**Spring Boot reads from Vault at startup:**

```yaml
# application.yml — no actual secrets, just Vault coordinates
spring:
  application:
    name: order-service          # Vault path: secret/order-service
  cloud:
    vault:
      host: vault.internal.company.com
      port: 8200
      scheme: https
      authentication: APPROLE    # service authenticates with a role, not a token
      app-role:
        role-id: ${VAULT_ROLE_ID}        # injected via env var — non-secret
        secret-id: ${VAULT_SECRET_ID}    # injected via env var — short-lived

  config:
    import: vault://             # tells Spring to pull config from Vault
```

```java
// Spring automatically maps vault path keys to @Value / @ConfigurationProperties
@Service
public class PaymentService {

    @Value("${payment.api.key}")  // resolved from Vault at startup
    private String apiKey;

    // If Vault is unreachable at startup → app fails to start
    // This is the correct behaviour — never run with missing secrets
}
```

**Vault token with least-privilege policy:**

```hcl
# vault-policy-order-service.hcl
# Order service can only READ its own secrets, nothing else
path "secret/data/order-service/*" {
  capabilities = ["read"]
}

# Cannot read payment-service or user-service secrets
path "secret/data/payment-service/*" {
  capabilities = []    # denied
}
```

***

**Strategy 3 — Kubernetes Secrets + ConfigMaps**

**Create the Secret (base64 encoded, stored in etcd — not in Git):**

```yaml
# k8s/secret.yaml — DO NOT COMMIT THIS FILE
# Apply manually: kubectl apply -f secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: order-service-secrets
  namespace: production
type: Opaque
stringData:                          # stringData auto-encodes to base64
  DB_PASSWORD: "ProdSuperSecret!"
  JWT_SECRET: "prod-jwt-secret-min-32-chars!!"
  PAYMENT_API_KEY: "pk_live_realkey"
```

```yaml
# k8s/configmap.yaml — non-secret config, safe to commit
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
data:
  DB_HOST: "postgres-service"
  DB_PORT: "5432"
  DB_NAME: "orderdb"
  APP_PORT: "8080"
  LOG_LEVEL: "INFO"
```

**Inject both into the Pod:**

```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  template:
    spec:
      containers:
        - name: order-service
          image: order-service:latest
          envFrom:
            # ConfigMap first — non-sensitive config
            - configMapRef:
                name: order-service-config
            # Secrets second — sensitive values
            - secretRef:
                name: order-service-secrets
          # Spring Boot reads ${DB_PASSWORD} — K8s has already injected it
```

**Your `application.properties` stays clean:**

```properties
# application.properties — identical placeholder pattern
# Works locally (env vars) and in K8s (injected by platform) — zero code change
spring.datasource.url=jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
jwt.secret=${JWT_SECRET}
```

***

**Safety net — prevent accidental commits**

```bash
# Install git-secrets to block commits containing secrets
brew install git-secrets
git secrets --install         # hooks into your repo
git secrets --register-aws    # blocks AWS keys
git secrets --add 'password\s*=\s*.+'  # custom pattern

# Or use detect-secrets (Python)
pip install detect-secrets
detect-secrets scan > .secrets.baseline   # baseline what's already there
detect-secrets audit .secrets.baseline   # review flagged items
```

```bash
# Pre-commit hook — runs before every commit
# .git/hooks/pre-commit
#!/bin/bash
if git diff --cached | grep -E "(password|secret|api.key)\s*=\s*[^${\s]"; then
    echo "❌ Possible hardcoded secret detected. Use \${ENV_VAR} placeholders."
    exit 1
fi
```

***

**Choosing the right strategy**

| Context                       | Strategy                          | Why                              |
| ----------------------------- | --------------------------------- | -------------------------------- |
| Local development             | `.env` file + `.gitignore`        | Simple, no infra needed          |
| Single server / VPS           | OS environment variables          | No extra dependency              |
| Docker / docker-compose       | `env_file` outside repo           | Secrets stay off disk in code    |
| GitHub Actions / CI           | Repo secrets (`${{ secrets.X }}`) | Encrypted, access-controlled     |
| Microservices on bare metal   | Spring Cloud Config Server        | Centralised, one place to rotate |
| Microservices — high security | HashiCorp Vault                   | Audit trail, rotation, RBAC      |
| Kubernetes                    | K8s Secrets + ConfigMaps          | Platform-native, no extra infra  |
| AWS deployment                | AWS Secrets Manager               | Native rotation, IAM integration |

The core rule across all strategies: **`application.properties` holds only `${PLACEHOLDER}` references — never actual values.** The platform (OS, Docker, K8s, Vault) is responsible for injecting the real values at runtime. Your codebase becomes a secret-free zone regardless of who can see it.

</details>

<details>

<summary></summary>



</details>



* Question: How do you configure a Spring Security setup where one API is publicly accessible, but another requires authentication?
  * Answer: You can achieve this by overriding the security filter chain. Use `requestMatchers()` alongside `permitAll()` to allow unauthenticated access to the public endpoints, and use `authenticated()` for the remaining endpoints.
* Question: Your application works locally but fails to connect to the production database. What steps would you take to analyze and fix this?
  * Answer: Verify that the correct active profile (like the `prod` profile) is activated. Check the config server to ensure the production database credentials are correct. Other crucial checks include verifying SSL certificates, evaluating the connection pool size, and checking the application logs.



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
