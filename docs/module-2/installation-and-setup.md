## Installations and Setup

### Programming Environment and Tools

To start building on Stacks, you’ll need to set up your local development environment. Here’s what you’ll need:

- **Node.js**: Required for using Stacks.js  and running Clarinet.
- **Clarinet**: Install via npm or download from the Hiro GitHub repo.
- **Hiro**: Install the browser extension to interact with your dApp.
- **Text Editor**: VS Code is recommended for its Clarity syntax support.
- **Git**: For version control and managing open-source projects.

### Clarinet Installation
**With Homebrew**
```bash
  brew install clarinet
```

Alternatively, if you have `asdf` installed and not Homebrew, run the following commands
```bash
  asdf plugin add clarinet
  asdf install clarinet latest
```

To confirm successful installation run
```bash
  clarinet --version
```

Once installed, you can initialise a new Clarity project using Clarinet and begin writing contracts.

**Create your project**
```bash
  clarinet new [project-name]
```

**Generate your contract**
```bash
  cd [project-name]
  clarinet contract new [project-name]
```
[See official docs for more details](https://docs.stacks.co/reference/clarinet/quickstart)

### User Account
To deploy contracts and interact with dApps, you’ll need a Stacks wallet. The Hiro Wallet is the most widely used and supports both testnet and mainnet.

Steps to set up:
1. Install the Hiro Wallet browser extension.
2. Create a new wallet and save your seed phrase securely.
3. Switch to testnet for development.
4. Fund your wallet with testnet STX using the [testnet faucet](https://learnweb3.io/faucets/stacks/).

Your wallet will be used to sign transactions, deploy contracts, and manage assets.