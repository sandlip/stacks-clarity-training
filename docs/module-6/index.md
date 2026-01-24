# Frontend Integration with dApps
Building smart contracts is only half the journey. To create a complete decentralised application, you need a frontend that users can interact with — a bridge between human users and the blockchain. This module teaches you how to build decentralised applications (dApps) that connect Clarity contracts to the web — enabling users to interact with the blockchain through familiar, responsive frontends.

In Web2, your frontend connects to REST APIs hosted on centralised servers. In Web3, the "backend" is decentralised — your smart contracts live on-chain, and interactions happen via cryptographic signatures and blockchain transactions. Users authenticate with their wallets instead of usernames and passwords. State is read from the blockchain, not from databases. Transactions are signed locally and broadcast to the network.

This shift requires new patterns, tools, and mental models. You'll need to:
- Integrate wallet connections so users can authenticate without creating accounts
- Read on-chain data using APIs and SDKs
- Construct and broadcast transactions that modify contract state
- Handle asynchronous operations and provide real-time feedback
- Manage post-conditions to ensure transaction safety

By the end of this module, you'll know how to:
- Connect your dApp to Stacks wallets like Hiro Wallet and Leather
- Use Stacks.js to interact with Clarity contracts from React or other frontend frameworks
- Read blockchain state and write transactions from your frontend
- Handle authentication flows, transaction signing, and error states
- Implement post-conditions for secure asset transfers

This module equips you with the tools and knowledge to build production-ready decentralised applications on Stacks.