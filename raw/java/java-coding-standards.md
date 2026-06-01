# Java Coding Standards

> Source: user knowledge (gwang)
> Collected: 2026-06-01
> Published: Unknown

## Dependency Injection

- Use constructor injection (not field injection, not setter injection)

## Testing Stack

- Use JUnit 5 (not JUnit 4)
- Use AssertJ for assertions (not Hamcrest, not plain JUnit assertions)
- Use @WebMvcTest for controller/MVC layer tests (not @SpringBootTest for unit-level controller tests)

## Methodology

- Follow TDD (Test-Driven Development): write test first, then implementation
