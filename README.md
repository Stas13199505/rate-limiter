# Rate Limiter Library

## Overview
The **Rate Limiter Library** implements a rate-limiting pattern to manage API request limits for different clients. By providing a set of configurable and extendable rules, the library allows developers to control access to API resources effectively, ensuring compliance with rate limits and preventing server abuse.

### Features
- Configurable rules for rate-limiting (e.g., X requests per timespan, a delay between requests).
- Extendable design to add new rule types or combinations.
- In-memory storage for rule tracking, avoiding external database dependencies.
- Unit-tested for robustness and reliability.

---

## Key Concepts

### Rate Limiting
Rate limiting restricts the number of requests that a client can make within a specific timeframe. Each client is identified by an access token used for all requests.

When a request is received:
1. The library determines if the request complies with the rate-limiting rules.
2. If within limits, the request is processed.
3. If the limit is exceeded, the request is rejected.

### Examples of Rules
1. **X requests per timespan**: Allow up to N requests within a specified time window.
2. **Delay between requests**: Enforce a minimum delay between consecutive requests.
3. **Region-specific rules**: Apply different rules based on client location (e.g., stricter rules for US-based tokens).
4. **Combinations**: Combine multiple rules for a single resource.

---

## Library Design
The library is designed with flexibility and configurability as primary goals.

### Core Components
1. **Rule Interface**
   - Defines the contract for all rate-limiting rules.
   - Example: `bool IsRequestAllowed(ClientContext context)`

2. **Predefined Rules**
   - **FixedWindowRule**: Limits X requests within a fixed timespan.
   - **SlidingWindowRule**: Limits requests dynamically based on a sliding window.
   - **CooldownRule**: Ensures a delay between consecutive requests.

3. **RateLimiter**
   - Orchestrates rule evaluation for API resources.
   - Supports applying multiple rules to a single resource.

4. **ClientContext**
   - Represents the client details (e.g., access token, region).
   - Passed to rules for evaluation.

5. **In-Memory Storage**
   - Tracks request metadata to evaluate rules.

---

## Usage

### Setting Up
1. Add the library to your project.
2. Create an instance of `RateLimiter`.
3. Configure rules for each API resource.

### Example
```csharp
// Create a RateLimiter instance
var rateLimiter = new RateLimiter();

// Define rules
var ruleA = new FixedWindowRule(maxRequests: 10, timeSpan: TimeSpan.FromMinutes(1));
var ruleB = new CooldownRule(delay: TimeSpan.FromSeconds(5));

// Associate rules with a resource
rateLimiter.AddRules("/api/resource", new[] { ruleA, ruleB });

// Check a request
var clientContext = new ClientContext("clientToken", "US");
if (rateLimiter.IsRequestAllowed("/api/resource", clientContext)) {
    Console.WriteLine("Request allowed.");
} else {
    Console.WriteLine("Request denied.");
}
```

---

## Test Project
The solution includes a test project to validate the library's functionality.

### Test Coverage
1. Rule evaluation (e.g., FixedWindowRule, CooldownRule).
2. Integration tests for `RateLimiter` with multiple rules.
3. Edge cases (e.g., exceeding limits, concurrent requests).

### Running Tests
1. Open the solution in your IDE.
2. Build the project to restore dependencies.
3. Run the test project using your preferred test runner.

---
