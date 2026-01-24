## Smart Contracts

### What Are Smart Contracts?

A smart contract is a program deployed on a blockchain that executes logic automatically when triggered by transactions. On Stacks, smart contracts are written in **Clarity**, a language designed for **security, transparency, and predictability**.

Clarity is **decidable**, meaning all possible execution paths are known at compile time. This eliminates the risk of reentrancy or unbounded recursion, which are common attack vectors in other smart contract platforms. Clarity contracts are **interpreted**, not compiled — they are deployed as human-readable source code, making them auditable directly on-chain.

**Core components of a Clarity smart contract:**
- **Data variables**: Persistent state stored on-chain.
- **Constants**: Immutable values defined at compile time.
- **Public functions**: Callable by users or other contracts.
- **Private functions**: Internal logic only.
- **Read-only functions**: For querying the state without modifying it.

Clarity supports strong typing, explicit error handling, and post-conditions — rules that must be true after a transaction executes. These features make it ideal for building secure financial applications, governance systems, and asset management protocols.

Reference: [Clarity Overview – Stacks Docs](https://docs.stacks.co/clarity/overview)

### Lifecycle of a Smart Contract

The development lifecycle of a smart contract on Stacks follows a structured path from design to interaction:

#### 1. Design
- Define the contract’s purpose, logic, and data structures.
- Use Clarity primitives like tuples, maps, constants, and principals to model your domain.
- Plan for post-conditions and access control (e.g., using `tx-sender` to restrict function calls).

#### 2. Development
Use **Clarinet**, the official CLI tool from Hiro, to scaffold and manage your project.

**Install Clarinet:**
```bash
  brew install clarinet
```

**Create a new project:**
```bash
  clarinet new my-dapp
```

This generates:
- `contracts/` folder for Clarity source files
- `tests/` folder for unit tests
- `Clarinet.toml` manifest for configuration
- `settings/` for devnet, testnet, and mainnet deployment plans

**Create a new contract:**
```bash
  clarinet contract new counter
```

This scaffolds a new Clarity file at `contracts/counter.clar`.

Reference: [Clarinet Project Development – Hiro Docs](https://docs.stacks.co/reference/clarinet/project-development)

#### 3. Testing
Clarinet supports unit testing via TypeScript and the Clarinet SDK. You can simulate blockchain state, mine blocks, and validate contract behaviour.

**Example test:**
```tsx
  import { describe, it, expect } from 'vitest';
  import { Cl } from '@stacks/transactions';

  const accounts = simnet.getAccounts();
  const wallet1 = accounts.get('wallet_1')!;

  describe('stx-defi', () => {
    it('allows users to deposit STX', () => {
      // Define the amount to deposit
      const amount = 1000;

      // Call the deposit function
      const deposit = simnet.callPublicFn('defi', 'deposit', [Cl.uint(amount)], wallet1);

      // Assert the deposit was successful
      expect(deposit.result).toBeOk(Cl.bool(true));

      // Verify the contract's total deposits
      const totalDeposits = simnet.getDataVar('defi', 'total-deposits');
      expect(totalDeposits).toBeUint(amount);

      // Check the user's balance
      const balance = simnet.callReadOnlyFn('defi', 'get-balance-by-sender', [], wallet1);
      expect(balance.result).toBeOk(
        Cl.some(
          Cl.tuple({
            amount: Cl.uint(amount),
          })
        )
      );
    });
  });
```

**Run the Test**
```bash
  npx run test
```

Reference: [Clarinet SDK Introduction](https://docs.stacks.co/build/clarinet-js-sdk/unit-testing)

#### 4. Deployment
Clarinet provides deployment tooling that helps you move from local development to production networks. Whether you're testing on devnet, staging on testnet, or launching on mainnet, Clarinet streamlines the process

**Example using Clarinet:**
Deployment plans are YAML files that describe how contracts are published or called. Generate a plan for any network:
```bash
  clarinet deployments generate --testnet
```

Deployment behaviour is configured in two places:
- **Project configuration (`Clarinet.toml`)** – Clarity versions, dependencies, epoch requirements
- **Network configuration (`settings/<network>.toml`)** – Account details, balances, endpoints

#### Deploying to different networks

**Devnet**
Devnet deploys contracts automatically when it starts:
```bash
  clarinet devnet start
```

To deploy manually against a running devnet:
```bash
  clarinet deployments apply --devnet
```

**Testnet**
> Prerequisites:
    - Request testnet STX from the faucet
    - Configure your account in `settings/Testnet.toml`
    - Validate contracts with `clarinet check`

Generate a deployment plan with cost estimation:
```bash
  clarinet deployments generate --testnet --medium-cost
```

Deploy to testnet:
```bash
  clarinet deployments apply --testnet
```

Reference: [Clarinet Contract Deployment](https://docs.stacks.co/reference/clarinet/contract-deployment)

#### 5. Interaction
Use the clarinet console to launch an interactive session with your contracts deployed to a local simulated blockchain:
```bash
  clarinet console
```
Example contract calls:
```
  (contract-call? 'SM3VDXK3WZZSA84XXFKAFAF15NNZX32CTSG82JFQ4.sbtc-token get-decimals)
  ;; (ok u8)

  (contract-call? 'SP2C2YFP12AJZB4MABJBAJ55XECVS7E4PMMZ89YZR.arkadiko-token get-name)
  ;; (ok "Arkadiko Token")
```

Reference: [Clarinet Contract Interaction](https://docs.stacks.co/reference/clarinet/contract-interaction)