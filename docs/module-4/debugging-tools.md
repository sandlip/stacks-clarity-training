## Debugging Tools
Testing catches bugs before deployment. Debugging helps you understand why they happened. This section introduces the tools and techniques for inspecting failed transactions, analysing logs, and tracing contract behaviour in a local development environment.

### Debugging Transactions
When a transaction fails, you need to know:
- What function was called
- What arguments were passed
- What the contract returned
- What state changes occurred (or didn’t)

Clarinet provides multiple ways to inspect this:

### 1. Transaction Receipts
Every mined block returns a list of receipts. Each receipt contains:
- `result`: the Clarity return value (`(ok ...)`, `(err ...)`)
- `events`: any emitted events (e.g. `print`, `transfer`)
- `cost`: execution cost in micro-STX
- `success`: boolean indicating success/failure

**Example:**
```ts
  const block = chain.mineBlock([
    Tx.contractCall("counter", "increment", [], deployer.address),
  ]);

  console.log(block.receipts[0].result); // e.g., ok(u1) or err("Unauthorized")
```

Use `.expectOk()`, `.expectErr()`, `.expectUint()`, etc., to assert outcomes in tests.

### 2. Console Output
Use `console.log()` in your test files to inspect:
- Transaction arguments
- Intermediate values
- Receipt metadata

This is especially useful for debugging conditional logic or unexpected failures.

### 3. Clarinet Console (REPL)
Run:
```bash
  clarinet console
```

This launches an interactive Clarity REPL that includes all deployed contracts. You can:
- Call functions manually
- Inspect contract state
- Debug logic in isolation

Reference: [Contract Interaction – Clarinet Console](https://docs.stacks.co/build/learn-clarinet/contract-interaction)

### Analysing Logs and Events
Clarity contracts can emit logs using the `print` function. These logs are captured in transaction receipts and can be used to trace execution paths.

### 1. Using `print` in Clarity
```Clojure
  (begin
    (print "Incrementing counter")
    (var-set counter (+ (var-get counter) u1))
    (ok true))
```

- `print` emits a string to the transaction log.
- Logs are visible in test receipts and the Clarinet console.
- Use them to trace execution flow, especially in branching logic.

### 2. Reading Logs in Tests
```Clojure
  const logs = block.receipts[0].events.filter(e => e.type === "print_event");
  console.log(logs[0].value); // e.g., "Incrementing counter"
```

You can also assert log content in tests:
```ts
  expect(logs[0].value).toBe("Incrementing counter");
```

### 3. Event Types
Clarinet captures multiple event types:
- `print_event`: emitted via `print`
- `stx_transfer_event`: STX transfers
- `ft_transfer_event`: fungible token transfers
- `nft_transfer_event`: NFT transfers

Use these to verify asset movements and contract behaviour.

Reference: [Validation and Analysis – Debugging](https://docs.stacks.co/build/learn-clarinet/validation-and-analysis)