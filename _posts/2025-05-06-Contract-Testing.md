---
title: "Contract Testing with Mock Consumers"
date: 2025-05-06
---

Contract validation ensures that API definitions are well-formed and conformant—but it doesn’t guarantee that the service *actually behaves* according to the contract. That’s where contract testing comes in.

## Why Contract Testing?

In a contract-first platform, APIs are often consumed by multiple teams or systems. Even with a valid OpenAPI or AsyncAPI spec, the implementation may drift from expectations over time.

**Contract tests** verify that:

- Providers implement the contract they advertise.
- Consumers can safely rely on contract behaviors.
- Both parties can evolve independently, without regressions.

## Mock Consumers and Provider Verification

A common approach is to use **mock consumers**—test doubles that simulate the behavior of a real consumer by asserting expectations about the provider’s responses.

In this model:

- **Consumers define expectations** (e.g., via Pact, Spring Cloud Contract, or Dredd).
- **Providers test against those expectations** in CI.
- **Mismatches cause pipeline failures**, signaling potential regressions.

This creates a feedback loop between producers and consumers—ensuring changes don’t break downstream systems.

## Example: Pact with Node.js

Let’s look at a simplified Pact setup for a Node.js API:

```js
const { Pact } = require('@pact-foundation/pact');
const provider = new Pact({ 
  consumer: 'FrontendApp',
  provider: 'UserService',
  port: 1234
});

describe('API Pact', () => {
  beforeAll(() => provider.setup());

  it('returns the expected response for GET /users', async () => {
    await provider.addInteraction({
      state: 'users exist',
      uponReceiving: 'a request for all users',
      withRequest: { method: 'GET', path: '/users' },
      willRespondWith: { status: 200, body: [{ id: 1, name: 'Alice' }] }
    });

    const response = await fetch('http://localhost:1234/users');
    const body = await response.json();

    expect(response.status).toBe(200);
    expect(body[0].name).toBe('Alice');
  });

  afterEach(() => provider.verify());
  afterAll(() => provider.finalize());
});
```

The generated contract is then published to a broker and used by the provider in its pipeline to verify conformance.

## Integration with CI/CD

Contract tests can run in the same pipeline as your validation jobs:

- Step 1: Lint and validate the contract (syntax, schema).
- Step 2: Test the implementation against consumer expectations.
- Step 3: Deploy only if both pass.

This workflow ensures semantic correctness—not just syntactic correctness.

## Benefits to Platform Engineering

- **Decoupled Deployments**: Teams can ship safely without tight coordination.
- **Early Failure Detection**: CI catches mismatches before they reach production.
- **Auditability**: Consumer expectations are versioned and traceable.

## Best Practices

- Store contracts in version control alongside the service.
- Version both API and consumer expectations.
- Automate publishing to a contract broker or artifact registry.
- Prefer narrow, scenario-driven interactions over overly broad specs.

---

Contract testing with mock consumers is a cornerstone of reliable API evolution. Combined with CI-based validation and spec linting, it forms a resilient safety net for contract-first platforms—enabling autonomy without sacrificing confidence.
