## Introduction to Clarity
![](https://cdn.prod.website-files.com/5ff21113877dd79ed7913b57/65e21b7984d07c8e4fb7cc7c_Hiro-blog-clarity1.jpg)
source - [Hiro Blog](https://www.hiro.so/blog/write-better-smart-contracts-with-the-programming-language-clarity)

Clarity is the smart contract language used in the Stacks blockchain. It was designed specifically for predictability, auditability, and safety — qualities essential for secure smart contract development. Unlike Turing-complete languages such as Solidity, Clarity is **decidable**, meaning developers can know exactly what a contract will do before it runs. This is achieved by eliminating loops and recursion, and by enforcing strict typing and static analysis.

Clarity is inspired by Lisp, so its syntax is parenthesis-heavy and expression-based. Contracts are deployed as source code, not bytecode, which means they are human-readable on-chain.

### Data Types in Clarity
Clarity supports several primitive and complex datatypes, each designed to be explicit and safe. These types are strictly enforced at compile time, preventing many common bugs found in loosely typed languages.

**Core Data Types**
| Type | Description | Example |
| --- | --- | --- |
| `int` | Signed 128-bit integer | `-42`, `0`, `100` |
| `uint` | Unsigned 128-bit integer | `u0`, `u100`, `u999999` |
| `bool` | Boolean value (`true` or `false`) | `true`, `false` |
| `principal` | Represents a user or contract address | `'SP2C2...` |
| `none` | Represents absence of value | `none` |
| `optional` | Wraps a value that may or may not be present | `(some u100)` or `none` |
| `tuple` | Key-value structure with fixed keys | `{name: "John", age: u30}` |
| `list` | Fixed-length array of a single type | `(list u1 u2 u3)` |
| `string-ascii` | ASCII string up to 128 characters | `"hello"` |
| `string-utf8` | UTF-8 string up to 128 characters | `"こんにちは"` |

### Variable Types
Variables in Clarity are declared using two main constructs: `define-data-var` for persistent state and `define-constant` for immutable values.

#### Data Variables
These are stored on-chain and can be read or modified by contract functions.
```Clojure
  (define-data-var total-supply uint u1000000)
```
You can access and modify them using `var-get` and `var-set`:
```Clojure
  (var-set total-supply (+ (var-get total-supply) u100))
```

#### Constants
Constants are immutable and defined at compile time. They are useful for fixed values like token caps or fee rates.
```Clojure
  (define-constant MAX_SUPPLY u1000000)
```
Constants cannot be changed once defined and are accessible throughout the contract.

### Functions
Functions are the core of Clarity contracts. They define the logic that users and other contracts can invoke. Clarity supports three types of functions:

#### Public Functions
These can be called by external users or contracts. They must return a response wrapped in `(ok ...)` or `(err ...)`.
```Clojure
  (define-public (increment-counter)
    (begin
      (var-set counter (+ (var-get counter) u1))
      (ok (var-get counter))))
```

#### Private Functions
These are internal and cannot be called from outside the contract. They are used for modularising logic.
```Clojure
  (define-private (calculate-fee (amount uint))
    (* amount u5))
```

#### Read-Only Functions
These can read contract state but cannot modify it. They are useful for querying data.
```Clojure
  (define-read-only (get-counter)
    (ok (var-get counter)))
```

Functions are stateless unless they modify or read from data variables. Every function must return a value, and Clarity enforces strict type checking to prevent runtime errors.

### Control Flow and Expressions
Clarity uses expression-based syntax. There are no statements — everything is an expression that returns a value.

#### Conditional Logic
```Clojure
  (if (> amount u100)
    (ok "Large transfer")
    (ok "Small transfer"))
```

#### Pattern Matching
Clarity supports `match` for handling optional values:
```Clojure
  (match (get owner token-id)
    owner-address (ok owner-address)
    _ (err "Token not found"))
```

#### Error Handling
Functions return responses using `(ok ...)` for success and `(err ...)` for failure. This enforces explicit error handling.
```Clojure
  (define-public (withdraw (amount uint))
    (if (> amount (var-get balance))
      (err "Insufficient funds")
      (begin
        (var-set balance (- (var-get balance) amount))
        (ok amount))))
```

### Post-Conditions
Clarity allows developers to define **post-conditions** — rules that must be true after a transaction executes. These are enforced by the blockchain and help prevent unintended asset transfers.

Example:
```Clojure
  (begin
    (asserts! (is-eq tx-sender 'SP2C2...) "Sender mismatch")
    (ok true))
```

Post-conditions can be defined at the transaction level to ensure that only specific assets are transferred or that balances remain within expected bounds.