## Programming Language for Stacks
Stacks uses a unique smart contract language called **Clarity**. Unlike Turing-complete languages like Solidity, JavaScript or Python, Clarity is intentionally non-Turing complete and designed for predictability and security. It is a **decidable** language, meaning you can know exactly what a contract will do before it runs — no hidden logic, no surprises. This makes it easier to audit and reason about smart contracts.

**Key features of Clarity**:
- **Interpreted, not compiled**: Clarity is interpreted directly on the blockchain.
- **No loops or recursion**: This avoids infinite execution and ensures predictable behaviour.
- **Strong typing**: Every variable and function has a clearly defined type (enforces data integrity).
- **Transparent execution**: You can inspect contract logic before interacting with it.
- **Post-conditions support**: Allows developers to define what must be true after a transaction executes.

Clarity is designed to make smart contracts safer and easier to audit, a critical feature in Web3 where bugs can lead to irreversible loss of funds.

## Build Tools for Clarity
To develop smart contracts in Clarity, you’ll use a set of tools provided by the Stacks ecosystem:

- **Clarinet**: A local development toolkit for writing, testing, and deploying Clarity contracts. It includes a REPL (interactive shell), testing framework, and deployment utilities.
- **Hiro Wallet**: A browser extension wallet that lets you interact with Stacks apps and sign transactions.
- **Stacks.js**: A JavaScript library for integrating Stacks functionality into Web apps — useful for frontend development.
- **Explorer**: A blockchain explorer for viewing transactions, contracts, and blocks on the Stacks network.

These tools are designed to give you a smooth developer experience, whether you're building locally or deploying to mainnet.

## APIs and SDKs for Building on Stacks
Stacks provides several APIs and SDKs to help you build full-stack decentralised applications:

- **Stacks.js**: A JavaScript/TypeScript library for interacting with wallets, broadcasting transactions, and calling smart contracts.
[Stacks.js](https://www.hiro.so/stacks-js)
- **Stacks Blockchain API**: A RESTful API that exposes blockchain data, transaction status, and contract state.
[Stacks API Reference](https://docs.hiro.so/en/apis/stacks-blockchain-api)
- **Chainhook**: A programmable event engine that listens to blockchain events and triggers off-chain actions.
[Chainhook Docs](https://docs.hiro.so/en/tools/chainhook)

These tools allow you to build responsive, data-driven dApps that interact seamlessly with the Stacks blockchain.