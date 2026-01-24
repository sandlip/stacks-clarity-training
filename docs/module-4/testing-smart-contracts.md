## Testing Smart Contracts
Smart contracts are critical infrastructures which must be tested as such. This section introduces the testing frameworks used in the Stacks ecosystem, how to write expressive test cases in TypeScript, and how to simulate blockchain behaviour using Clarinet’s local environment.

### Unit Testing Frameworks
Clarity contracts are tested using the **Clarinet SDK** in combination with **Vitest**, a modern TypeScript testing framework. This setup allows developers to simulate transactions, inspect contract state, and validate logic — all without deploying to testnet.

**Key components:**
- `ClarinetSimnet`: Simulates a local blockchain with accounts, blocks, and contract state.
- `Tx`: Constructs contract calls and read-only queries.
- `types`: Provides helpers for asserting Clarity values (e.g., `types.uint`, `types.ok`, `types.err`).
- `Vitest`: Provides `describe`, `it`, and `expect` for structuring and asserting tests.

**Example:**
```ts
  describe("counter", () => {
    let simnet: ClarinetSimnet;

    beforeAll(async () => {
      simnet = await ClarinetSimnet.init();
    });

    it("increments the counter", async () => {
      const chain = simnet.chain("devnet");
      const deployer = chain.accounts.get("deployer");

      const block = chain.mineBlock([
        Tx.contractCall("counter", "increment", [], deployer.address),
      ]);

      expect(block.receipts[0].result).toEqual(types.ok(types.uint(1)));
    });
  });
```

### Writing Test Cases
A good test case simulates a real-world interaction with your contract and verifies the expected outcome. Every public function should be tested for:

- **Valid inputs**: Ensure expected behaviour under normal conditions.
- **Invalid inputs**: Trigger and assert error responses.
- **Access control**: Confirm that only authorised callers succeed.
- **State transitions**: Validate changes to variables, maps, and assets.

**Example: Testing access control**
```ts
  it("fails when caller is not owner", async () => {
    const chain = simnet.chain("devnet");
    const user = chain.accounts.get("wallet_1");

    const block = chain.mineBlock([
      Tx.contractCall("counter", "reset", [], user.address),
    ]);

    expect(block.receipts[0].result).toEqual(types.err(types.ascii("Unauthorized")));
  });
```

**Best practices:**
- Use descriptive test names (`"should fail when caller is not owner"`)
- Keep tests isolated — no shared state between cases
- Assert both success and failure paths
- Cover edge cases (e.g., zero balances, max supply, duplicate entries)

Reference: [Unit Testing Guide](https://docs.stacks.co/build/clarinet-js-sdk/unit-testing)

### Mocking and Simulations
Clarinet allows full simulation of blockchain behaviour without deploying to the testnet. You can:

- **Mock accounts**: Use predefined identities like `deployer`, `wallet_1`, `wallet_2`
- **Mine blocks**: Simulate transaction ordering and time progression
- **Call read-only functions**: Query contract state without modifying it
- **Simulate inter-contract calls**: Deploy multiple contracts and test interactions

**Advanced simulation techniques:**
- `chain.mineEmptyBlockUntil(height)`: Simulate time passing
- `chain.callReadOnlyFn(...)`: Query contract state
- `chain.mineBlock([...])`: Submit multiple transactions in a single block

**Example: Simulating time-dependent logic**
```ts
  await chain.mineEmptyBlockUntil(100); // simulate reaching block height 100
```
Reference: [Clarinet SDK API](https://docs.stacks.co/reference/clarinet-js-sdk/sdk-reference)