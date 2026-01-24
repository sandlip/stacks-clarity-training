## Advanced Language Concepts

### Control Flow in Clarity
Clarity uses expression-based control flow. There are no statements — every construct returns a value. The language avoids loops and recursion to ensure **decidability**, meaning all execution paths are known at compile time.

### `if` Expression
The `if` expression evaluates a condition and returns one of two branches:
```Clojure
  (if (> amount u100)
    (ok "Large transfer")
    (ok "Small transfer"))
```
- The condition must evaluate to a boolean.
- Both branches must return the same type.

### `match` Expression
Used to handle optional values like `(some ...)` or `none`. It allows pattern matching on the result of expressions that may or may not return a value.
```Clojure
  (define-private (add-10 (x (optional int)))
  (match x
    value (+ 10 value)
    10))
  (add-10 (some 5)) ;; Returns 15
  (add-10 none) ;; Returns 10
```
- The first clause matches a value.
- The `_` clause is the fallback if no match is found.

### Loops and Recursion
Clarity **does not support loops or recursion**. This is a deliberate design choice to prevent unbounded execution and make contracts statically analyzable. Instead, developers use:

- **Maps and lists** for iteration-like behavior.
- **Read-only functions** to simulate queries over collections.

### Structs and Enums
Clarity does not have native `struct` or `enum` types like Solidity, but you can model them using **tuples** and **constants**.

### Tuples (Struct-like)
Tuples are fixed key-value pairs. They are used to represent structured data.
```Clojure
  (define-map users {owner: principal} {name:(string-ascii 100), age: uint})
```

Accessing tuple fields:
```Clojure
  (let ((user (get-map users tx-sender)))
  (match user
    u (ok (get name u))
    _ (err "User not found")))
```

### Enums (via Constants)
Enums can be modelled using constants:
```Clojure
  (define-constant STATUS_PENDING u0)
  (define-constant STATUS_APPROVED u1)
  (define-constant STATUS_REJECTED u2)
```

Use these constants in logic:
```Clojure
  (if (is-eq status STATUS_APPROVED)
    (ok "Approved")
    (err "Not approved"))
```

### Error Handling and Assertions
Clarity enforces explicit error handling. Every public function must return a response type: `(ok ...)` for success or `(err ...)` for failure.

### Example: Error Handling
```Clojure
  (define-public (withdraw (amount uint))
  (if (> amount (var-get balance))
      (err "Insufficient funds")
      (begin
        (var-set balance (- (var-get balance) amount))
        (ok amount))))
```
- Errors are returned as tagged responses.
- Clients must handle both success and failure cases.

### Assertions
Use `asserts!` to enforce conditions. If the condition fails, the transaction is aborted.
```Clojure
  (asserts! (is-eq tx-sender owner) "Unauthorized caller")
```
- Assertions are useful for access control and invariant enforcement.
- They simplify logic by failing fast when conditions aren’t met.
