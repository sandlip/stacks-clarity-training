## Secure Asset Management
Tokens are valuable assets — and like any asset, they must be protected. Poor access control or unchecked minting logic can lead to exploits, inflation, or unauthorised transfers. This section covers the key patterns and practices for securing token contracts, including role-based access, supply caps, and safe burning or freezing mechanisms.

### Access Control: `tx-sender`, `asserts!`, Role-Based Patterns
Access control ensures that only authorised principals can perform sensitive actions such as minting, burning, or freezing tokens.

#### 1. `tx-sender`
The `tx-sender` keyword returns the principal that initiated the transaction. It’s the foundation of access control in Clarity.

```Clojure
  (asserts! (is-eq tx-sender contract-owner) (err u100))
```

This ensures that only the contract owner can proceed.

#### 2. `asserts!`
The `asserts!` macro halts execution if a condition fails. It’s used to enforce preconditions and return custom error codes.

```Clojure
  (asserts! (>= balance amount) (err u101)) ;; Insufficient balance
```

#### 3. Role-Based Access
For more flexible control, define roles using `define-map` or `define-data-var`.
```Clojure
  (define-map roles ((user principal)) ((role (string-ascii 16))))

  (define-read-only (is-minter (user principal))
    (match (map-get? roles user)
      role (is-eq (get role role) "minter")
      false))
```

Use this to gate minting or admin functions:
```Clojure
  (asserts! (is-minter tx-sender) (err u102))
```

Reference: [Clarity Functions](https://docs.stacks.co/reference/clarity/functions#asserts)

### Preventing Unauthorised Minting or Transfers
**Minting logic must**:
- Check that the token ID hasn’t been minted before
- Restrict minting to authorised roles
- Prevent overwriting ownership or metadata

**Example:**
```Clojure
  (define-public (mint (token-id uint) (recipient principal))
    (begin
      (asserts! (is-minter tx-sender) (err u102))
      (asserts! (is-none (map-get? token-owners token-id)) (err u103))
      (map-set token-owners token-id recipient)
      (ok true)))
```

**Transfer logic must**:
- Confirm token exists
- Confirm sender is the current owner
- Prevent transfers to `none` or contract itself

**Example:**
```Clojure
  (asserts! (is-eq sender (unwrap! (map-get? token-owners token-id) (err u104))) (err u105))
```

### Token Supply Caps, Burning, and Freezing

#### 1. Supply Caps
Use `define-data-var` to enforce a maximum supply.
```Clojure
  (define-data-var max-supply uint u10000)
  (define-data-var current-supply uint u0)

  (asserts! (< (var-get current-supply) (var-get max-supply)) (err u106))
```

Increment `current-supply` on each mint.

#### 2. Burning Tokens
Burning removes tokens from circulation. For fungible tokens, subtract from both the balance and the total supply.

```Clojure
  (define-public (burn (amount uint))
    (begin
      (let ((balance (default-to u0 (map-get? balances tx-sender))))
        (asserts! (>= balance amount) (err u107))
        (map-set balances tx-sender (- balance amount))
        (var-set total-supply (- (var-get total-supply) amount))
        (ok true))))
```

For NFTs, remove ownership mapping:
```Clojure
  (map-delete token-owners token-id)
```

#### 3. Freezing Transfers
Use a flag to disable transfers globally or per token.
```Clojure
  (define-data-var transfers-enabled bool true)

  (asserts! (var-get transfers-enabled) (err u108))
```

You can also freeze individual tokens:
```Clojure
  (define-map frozen ((id uint)) ((flag bool)))

  (asserts! (not (get flag (default-to {flag: false} (map-get? frozen token-id)))) (err u109))
```

### Best Practices for Asset Safety and Compliance
- **Use explicit error codes** for traceability
- **Avoid hardcoding principals** — use `contract-owner` or role maps
- **Test all failure paths** (e.g. unauthorised mint, double transfer)
- **Simulate edge cases** (e.g. max supply, zero balance, frozen token)
- **Document roles and permissions** in contract comments
- **Validate all inputs**, especially token IDs, amounts, and recipient addresses
- **Restrict sensitive functions** with role checks and `tx-sender` assertions
- **Avoid state ambiguity** — update maps and vars in predictable order