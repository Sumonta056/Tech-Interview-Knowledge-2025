# Bean!

#### Spring Boot flows with Beans!

```
Application Starts
        |
Spring Container (ApplicationContext)
        |
Scans for @Service, @Component, etc.
        |
Creates Bean Objects
        |
Stores them in a container
        |
Injects them where needed
```

```java
@Service
class PaymentService {}

@Service
class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

```
1️⃣ Spring Boot starts
        |
2️⃣ Component Scan runs
        |
3️⃣ Finds @Service classes
        |
4️⃣ Creates PaymentService Bean
        |
5️⃣ Creates OrderService Bean
        |
6️⃣ Injects PaymentService into OrderService
        |
7️⃣ Application Ready
```

```
Spring Container
    |
    |---- PaymentService (Bean)
    |
    |---- OrderService (Bean)
               |
               |---- needs PaymentService
               |
               |---- Spring injects it automatically
```

```
Spring Boot Starts
        |
Scans package no.dn.lantern
        |
Finds @Service
        |
Creates YouTubeEmbedProcessor Bean
        |
Stores it in a container
        |
Injects it into wherever ArticleBodyProcessor is used
```







