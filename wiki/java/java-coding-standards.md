# Java Coding Standards

> Sources: gwang, 2026-06-01
> Raw: [java-coding-standards](../../raw/java/java-coding-standards.md)
> Updated: 2026-06-01

## Overview

Project coding standards for Java: constructor injection for DI, JUnit 5 + AssertJ for testing, @WebMvcTest for controller-layer tests, TDD as methodology.

## Dependency Injection

Use **constructor injection** exclusively. Avoid field injection (`@Autowired` on fields) and setter injection.

- Constructor injection makes dependencies explicit and final
- Enables testing without Spring context (plain `new` in tests)
- Required fields visible at construction time

```java
// Correct
@Service
public class OrderService {
    private final OrderRepository repo;

    public OrderService(OrderRepository repo) {
        this.repo = repo;
    }
}

// Avoid
@Service
public class OrderService {
    @Autowired
    private OrderRepository repo;
}
```

## Testing Stack

### JUnit 5

Use JUnit 5 (`org.junit.jupiter`). Do not use JUnit 4 (`org.junit`).

### AssertJ

Use AssertJ for all assertions. Do not use Hamcrest matchers or plain JUnit `assertEquals`.

```java
// Correct
assertThat(result).isEqualTo("expected");
assertThat(list).hasSize(3).contains("item");

// Avoid
assertEquals("expected", result);
assertThat(result, is("expected"));
```

### @WebMvcTest for Controller Tests

Use `@WebMvcTest` for controller-layer tests. Loads only MVC infrastructure — faster than `@SpringBootTest`.

```java
@WebMvcTest(OrderController.class)
class OrderControllerTest {
    @Autowired MockMvc mvc;
    @MockBean OrderService service;

    @Test
    void getOrder_returns200() throws Exception {
        mvc.perform(get("/orders/1"))
           .andExpect(status().isOk());
    }
}
```

## Methodology

Follow **TDD**:
1. Write failing test
2. Write minimum code to pass
3. Refactor

Red → Green → Refactor. Test first, always.
