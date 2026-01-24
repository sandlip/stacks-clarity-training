## Frontend Basics

### Connecting dApps to Smart Contracts
In Web2, your frontend communicates with servers via HTTP requests. In Web3, your frontend communicates with the blockchain via specialised libraries and APIs. On Stacks, this connection is established through **Stacks.js** — a collection of JavaScript libraries that handle everything from wallet authentication to transaction broadcasting.

The fundamental shift is this: instead of sending data to a server that processes it privately, you construct transactions that are broadcast to a public blockchain, validated by nodes, and executed transparently on-chain. Every state change is permanent and auditable.

### The Architecture of a Stacks dApp

A typical Stacks dApp consists of three layers:

1. **Frontend (Client)**: Built with standard web technologies (React, Vue, Svelte, vanilla JavaScript). This is where users interact with your application through familiar UI components.
2. **Stacks.js Libraries**: The bridge between your frontend and the blockchain. These libraries handle:
    - Wallet connections and authentication
    - Transaction construction and signing
    - Reading blockchain state
    - Broadcasting transactions to the network
3. **Blockchain Layer**: Your deployed Clarity smart contracts, the Stacks blockchain nodes, and the Stacks Blockchain API. This is where state is stored and logic is executed.

**Data Flow:**

```
User Action → Frontend UI → Stacks.js → Wallet (signs) → Stacks Node → Blockchain
                  ↑                                                        ↓
                  └─────────── API Response ← Stacks API ←─────────────────┘

```

### Reading Blockchain State

Before a user interacts with your contract, they need to see the current state. This is done through **read-only function calls** that query the blockchain without creating transactions.

**Example: Reading a counter value**

```jsx
import { callReadOnlyFunction, cvToValue } from '@stacks/transactions';
import { StacksMainnet } from '@stacks/network';

async function getCounterValue() {
  const network = new StacksMainnet();

  const result = await callReadOnlyFunction({
    network,
    contractAddress: 'SP2...',
    contractName: 'counter',
    functionName: 'get-counter',
    functionArgs: [],
    senderAddress: 'SP2...' // any valid address
  });

  const value = cvToValue(result); // converts Clarity value to JS value
  console.log('Counter value:', value);
}

```

Read-only calls are synchronous from the user's perspective — they return immediately because they don't modify state or require consensus.

Reference: [Stacks.js Read-Only Calls](https://stacks.js.org/modules/_stacks_transactions#call-read-only-functions)

### Writing to Smart Contracts

To modify blockchain state, you must construct a transaction, have it signed by the user's wallet, and broadcast it to the network. Unlike read-only calls, write operations:

- Cost transaction fees (paid in STX)
- Require user approval via wallet
- Take time to confirm (usually 10-30 seconds on Stacks)
- Are irreversible once confirmed

**Transaction Lifecycle:**

1. **Construction**: Your frontend builds a transaction object with the contract address, function name, and arguments.
2. **Signing**: The transaction is sent to the user's wallet for cryptographic signing.
3. **Broadcasting**: The signed transaction is broadcast to Stacks nodes.
4. **Mining**: Miners include the transaction in the next block.
5. **Confirmation**: The transaction is confirmed and state changes are applied.

This asynchronous flow requires careful UI design — users need feedback at each stage (pending, success, failure).

### The Role of APIs

While you can query blockchain state directly from nodes, the **Stacks Blockchain API** provides a more developer-friendly interface. It indexes on-chain data and exposes RESTful endpoints for:

- Account balances (STX, fungible tokens, NFTs)
- Transaction history
- Contract details and source code
- Block information
- Event logs

**Example: Fetching account balance**

```jsx
async function getAccountBalance(address) {
  const response = await fetch(
    `https://api.mainnet.hiro.so/extended/v1/address/${address}/balances`
  );
  const data = await response.json();
  console.log('STX Balance:', data.stx.balance);
}

```

The API is particularly useful for displaying transaction history, monitoring events, and building dashboards without managing your own blockchain indexer.

Reference: [Stacks Blockchain API](https://docs.hiro.so/stacks/api)

### Wallet Integration

Wallet integration is the cornerstone of Web3 authentication. Instead of creating accounts with email and password, users connect their existing Stacks wallets. This provides instant authentication with cryptographic proof of identity.

### How Wallet Authentication Works

Unlike traditional login systems that create sessions on a server, wallet authentication is **client-side and cryptographic**. Here's the flow:

1. User clicks "Connect Wallet" in your dApp
2. Your dApp requests authentication via Stacks Connect
3. User's wallet (e.g., Hiro Wallet, Leather) opens
4. User approves the connection
5. Wallet returns the user's Stacks address
6. Your dApp stores this address locally (e.g., in state or localStorage)

**Key properties:**

- No passwords, no email verification, no account recovery flows
- The user's private key never leaves their wallet
- Authentication is instant and doesn't require backend infrastructure
- Users can connect multiple accounts from the same wallet

### Installing Stacks Connect

Stacks Connect is the primary library for wallet integration. It provides a unified interface that works with all Stacks-compatible wallets.

```bash
npm install @stacks/connect

```

Or with Yarn:

```bash
yarn add @stacks/connect

```

### Basic Wallet Connection

Here's a minimal implementation for connecting a wallet:

```jsx
import { connect } from '@stacks/connect';

async function connectWallet() {
  try {
    const response = await connect();
    console.log('Connected address:', response.addresses.stx[0].address);

    // Store the address for future use
    localStorage.setItem('stacksAddress', response.addresses.stx[0].address);
  } catch (error) {
    console.error('Connection failed:', error);
  }
}

```

The `connect()` function opens the user's wallet and prompts them to authorise your dApp. Upon approval, it returns an object containing:

- **STX addresses**: The user's Stacks address for the current network
- **BTC addresses**: Associated Bitcoin addresses (useful for sBTC workflows)

**Important:** As of Stacks.js 8.x, only the current network's address is returned for security reasons. If your dApp needs to support both mainnet and testnet, ensure you're configured for the correct network.

Reference: [Stacks Connect Documentation](https://connect.stacks.js.org/)

### Checking Connection State

Your dApp needs to know whether a user is connected and display the appropriate UI:

```jsx
import { connect, disconnect, isConnected } from '@stacks/connect';

function WalletButton() {
  const [connected, setConnected] = useState(isConnected());
  const [address, setAddress] = useState(null);

  useEffect(() => {
    if (connected) {
      const storedAddress = localStorage.getItem('stacksAddress');
      setAddress(storedAddress);
    }
  }, [connected]);

  const handleConnect = async () => {
    const response = await connect();
    setAddress(response.addresses.stx[0].address);
    setConnected(true);
  };

  const handleDisconnect = () => {
    disconnect();
    setConnected(false);
    setAddress(null);
  };

  return (
    <div>
      {connected ? (
        <div>
          <p>Connected: {address.substring(0, 8)}...{address.substring(address.length - 4)}</p>
          <button onClick={handleDisconnect}>Disconnect</button>
        </div>
      ) : (
        <button onClick={handleConnect}>Connect Wallet</button>
      )}
    </div>
  );
}

```

### Wallet Configuration

For production dApps, you may need to configure wallet connection with specific options:

```jsx
await connect({
  walletConnectProjectId: 'YOUR_PROJECT_ID' // Get this from Reown dashboard
});

```

If you want to force the wallet selection UI (useful when multiple wallets are installed):

```jsx
import { request } from '@stacks/connect';

await request({ forceWalletSelect: true }, 'getAddresses');

```

### Handling Multiple Wallets

The Stacks ecosystem supports multiple wallets:

- **Hiro Wallet**: Browser extension wallet (most widely used)
- **Leather Wallet**: Previously known as Xverse web wallet
- **Xverse**: Mobile wallet with browser extension
- **Boom Wallet**: Community-built wallet

Stacks Connect abstracts wallet detection and selection. Users can choose their preferred wallet, and your dApp works seamlessly with any of them.

### Session Persistence

Wallet connections don't persist across page refreshes automatically. You need to manage this in your application state:

```jsx
// On app initialisation
useEffect(() => {
  const savedAddress = localStorage.getItem('stacksAddress');
  if (savedAddress && isConnected()) {
    setUserAddress(savedAddress);
  }
}, []);

// On connection
const handleConnect = async () => {
  const response = await connect();
  const address = response.addresses.stx[0].address;
  localStorage.setItem('stacksAddress', address);
  setUserAddress(address);
};

// On disconnection
const handleDisconnect = () => {
  disconnect();
  localStorage.removeItem('stacksAddress');
  setUserAddress(null);
};

```

### Security Considerations

When integrating wallets, keep these security principles in mind:

1. **Never request private keys**: Wallets handle all signing. Your dApp should never ask for or store private keys.
2. **Verify addresses client-side**: Always validate that the address format is correct before using it in transactions.
3. **Handle disconnections gracefully**: Users might disconnect or switch accounts. Your UI should handle these state changes smoothly.
4. **Use post-conditions**: Always include post-conditions in transactions to protect users from malicious or buggy contracts (covered in detail in the next section).
5. **HTTPS only**: Always serve your dApp over HTTPS in production. Wallets will refuse to connect to insecure origins.

Wallet integration is the gateway to your dApp. A smooth, secure connection flow sets the tone for the entire user experience.