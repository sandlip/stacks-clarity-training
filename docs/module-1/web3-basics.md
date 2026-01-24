## Web3 Basics

### Web3 Overview
Web3 is the decentralised version of the internet; it is a shift from platforms owned by corporations to protocols governed by communities. In Web2, companies like Google, Facebook, and Amazon control user data, identity, and monetisation. Web3 changes this by using blockchain networks, where data is distributed across nodes, and users control their identity and assets through cryptographic keys.

Web3 is built on three core ideas, which serve as its driving force:
- **Decentralisation**: No single point of control. Networks are maintained by distributed nodes.
- **Ownership**: Users own their identity, data, and digital assets.
- **Trustless systems**: Smart contracts replace intermediaries, enabling peer-to-peer interactions.

To understand Web3, you need to grasp four key components:
- **Blockchains**: These are distributed ledgers that record transactions and state changes.
- **Smart contracts**: Programmes deployed on blockchains that execute logic automatically.
- **Wallets**: Tools that manage your cryptographic identity and allow you to sign transactions.
- **Tokens**: Digital assets used for value transfer, governance, and access control.

Web3 applications, also known as decentralised applications (dApps), combine these elements to create trustless systems in which users interact directly with protocols rather than intermediaries.

### Introduction to Blockchain Technology
A blockchain is a data structure that stores information in blocks, which are linked together using cryptographic hashes. Each block contains:

- A **header** with metadata (timestamp, previous block hash, Merkle root)
- A **body** with a list of validated transactions

When a transaction is created, it is broadcast to the network. Nodes verify their validity (e.g., checking signatures and balances), and valid transactions are added to a pool called the mempool. A miner or validator selects transactions from the mempool, executes them, and proposes a new block. Once consensus is reached, the block is added to the chain. This structure ensures that once data is recorded, it cannot be altered without consensus from the network.

Consensus mechanisms vary:
- **Proof of Work (PoW)**: Requires computational effort to solve cryptographic puzzles (used by Bitcoin).
- **Proof of Stake (PoS)**: Validators stake tokens to propose blocks (used by Ethereum).
- **Proof of Transfer (PoX)**: Used by Stacks, where miners transfer Bitcoin to earn the right to write blocks.

Merkle trees are used to efficiently verify transaction inclusion. Each transaction is hashed, and hashes are paired recursively until a single root hash is formed. This root is stored in the block header.

Key properties of blockchains:

- **Immutability**: Data cannot be changed once confirmed.
- **Transparency**: Anyone can verify the history of transactions.
- **Security**: Cryptographic algorithms protect data integrity and authenticity.

Blockchains can be public (like Bitcoin, Ethereum or Stacks), private (used within organisations), or consortium-based (shared among a group of entities).

### Wallet Principles and Security
A wallet is your identity in Web3. It allows you to store and manage your digital assets (like tokens and NFTs), interact with dApps, and sign transactions. It doesn’t store your assets — it stores your private key, which gives you control over assets recorded on the blockchain. But unlike traditional accounts, wallets are non-custodial, meaning you are the sole owner and custodian of your keys.

There are two main types of wallets:
- **Hot wallets**: Software-based wallets connected to the internet. Examples include browser extensions (like Hiro Wallet), mobile apps, or desktop apps. Convenient but more vulnerable to hacks.
- **Cold wallets**: Hardware devices or paper wallets that store your keys offline. More secure but less convenient for frequent use.

#### How Wallets Are Created
1. A random 256-bit number is generated as your **private key seed**, the foundation of your wallet’s cryptographic identity.
2. The number is encoded as a **mnemonic phrase** (usually 12 or 24 words) using BIP-39.
    - The mnemonic is not a direct encoding of the private key.
    - Instead, it represents a seed that can deterministically generate many private keys (via BIP-32/BIP-44).
3. A **private key** is derived from the seed using the wallet’s derivation path.
4. A **public key** is derived from the private key using elliptic curve cryptography.
5. Your **wallet address** is a hashed version of the public key (e.g. RIPEMD-160(SHA-256(pubkey)) for Bitcoin or Keccak-256(pubkey)[last 20 bytes] for Ethereum).

When you create a wallet, a cryptographic key pair is generated:
- **Private key**: A secret string that proves ownership and allows you to sign transactions. This must be kept secure and never shared.
- **Public key**: Derived from the private key. It acts like your account number — others can use it to send you assets.

Security is critical. Transactions are signed locally using your private key, and only the signature is broadcast. If your private key or mnemonic phrase is exposed, your assets can be stolen. Always store your seed phrase offline and never share it. This phrase is the master key to your wallet.

### Understanding dApps

Decentralised applications (dApps) are software programs that run on a blockchain rather than centralised servers. They use smart contracts, which are self-executing code stored on the blockchain to define their logic and consist of:
- A **frontend** built with Web2 tools (e.g., React)
- **Smart contracts** deployed on-chain
- **Wallet integration** for user authentication and transaction signing

Unlike traditional apps, dApps:
- Are open-source and transparent
- Cannot be shut down by a single entity
- Allow users to interact directly via wallets

Examples of dApps include:
- Decentralised exchanges (DEXs)
- NFT marketplaces
- Lending and borrowing platforms
- On-chain games and social networks

Here’s what happens when you use a dApp:
1. You connect your wallet to the dApp.
2. The dApp reads data from the blockchain using RPC or REST APIs.
3. You initiate an action (e.g., minting an NFT).
4. The dApp constructs a transaction and asks your wallet to sign it.
5. The signed transaction is broadcast to the network.
6. The blockchain executes the transaction and updates the state.

dApps are transparent, immutable, and resistant to censorship. They enable use cases like decentralised finance (DeFi), NFT marketplaces, and on-chain governance.