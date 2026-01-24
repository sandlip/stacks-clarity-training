## Security in Smart Contracts

### Common Vulnerabilities
Clarity’s design avoids entire classes of vulnerabilities by removing features like loops, recursion, and dynamic dispatch. However, developers must remain vigilant about logic errors, unsafe assumptions, and poor access control.

#### 1. Reentrancy
Reentrancy occurs when a contract calls an external contract that then calls back into the original contract before the first call finishes. This can lead to an inconsistent state or double withdrawals.

**Clarity mitigation:**
- Clarity does not support asynchronous calls or fallback functions — all calls are synchronous and explicit by design to ensure transparency and determinism.
- All contract calls are atomic and state updates are isolated.

Reference: [Clarity Overview – Stacks Docs](https://docs.stacks.co/clarity/overview#clarity-does-not-permit-reentrancy)

#### 2. Integer Overflow / Underflow
In languages like Solidity, arithmetic operations can overflow silently. Clarity uses fixed-size integers and throws errors on overflow or underflow.

**Example:**
```Clojure
  (+ u9223372036854775807 u1) ;; throws error
```

**Mitigation:**
- Always validate arithmetic inputs.
- Use `asserts!` to enforce bounds.

#### 3. Unchecked Conditions
Failing to validate caller identity or input values can lead to unauthorised access or broken logic.

**Example:**
```Clojure
  (define-public (update-owner (new-owner principal))
  (begin
    (var-set owner new-owner)
    (ok true))) ;; missing tx-sender check
```
**Mitigation:**
```Clojure
  (asserts! (is-eq tx-sender (var-get owner)) "Unauthorised")
```

#### 4. Unbounded Storage Growth
Maps and lists can grow indefinitely if not managed properly. This can lead to bloating and performance issues.

**Mitigation:**
- Use expiration logic or pruning functions.
- Limit the size of stored collections.

### Best Practices for Secure Coding
Clarity’s safety features are powerful, but they must be used correctly. Here are essential practices for secure contract development:

#### 1. Use Post-Conditions
Post-conditions define what must be true after a transaction. They prevent unintended asset transfers and enforce transaction boundaries.

**Example:**
```Clojure
  (begin
    (asserts! (is-eq tx-sender owner) "Unauthorized")
    (ok true))
```

When you use a Stacks wallet like the Hiro web wallet and initiate a transaction, the wallet will display the post conditions set by the developer and tell the user exactly what is going to happen. If the action taken by the smart contract matches, the transaction goes through fine, otherwise it aborts.

Here's what that looks like:
![Image demo of Post-Conditions](https://docs.stacks.co/~gitbook/image?url=https%3A%2F%2F2842511454-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FH74xqoobupBWwBsVMJhK%252Fuploads%252Fgit-blob-5b26b1c9eb08397ee84db2af8b95bf04ded839bb%252Fimage.png%3Falt%3Dmedia&width=768&dpr=4&quality=100&sign=81584eaf&sv=2)

Reference: [Post-Conditions – Stacks Docs](https://docs.stacks.co/transactions/post-conditions)

#### 2. Validate Inputs and Caller Identity
Always check:

- `tx-sender` for authorisation
- Input types and ranges
- Optional values using `match`

#### 3. Fail Fast with `asserts!`
Use assertions to enforce invariants and abort transactions early.
```Clojure
  (asserts! (>= amount u0) "Negative amount not allowed")
```

#### 4. Minimise State Changes
Avoid unnecessary writes to data variables. Use read-only functions for queries and isolate logic into private functions.

#### 5. Write Comprehensive Tests
Use `clarunit` to simulate edge cases, invalid inputs, and attacker scenarios.

Reference: [Testing Smart Contracts – Stacks Docs](https://docs.stacks.co/build/get-started/testing-smart-contracts)