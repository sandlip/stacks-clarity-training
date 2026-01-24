
## Web3 Security

### Common Wallet Risks

Web3 gives you full control — and full responsibility. Here are common risks to watch out for:
- **Phishing attacks**: Fake websites or apps that trick you into revealing your seed phrase.
- **Social engineering**: Scammers impersonating support staff or friends to gain access.
- **Clipboard hijacking**: Malware replaces copied wallet addresses with attacker-controlled ones.
- **Blind signing**: Approving transactions without understanding what they do.
- **Malicious browser extensions**: Can intercept wallet interactions and steal credentials.

### The Security of Private Keys and Mnemonic Phrases
Your private key is the only way to access your wallet. If someone gets it, they can drain your assets. If you lose it, your assets are gone forever. Mnemonic phrases are a human-readable backup of your private key.

#### Best Practices for Wallet Security
- Write down your seed phrase and store it in multiple secure, offline locations.
- Never share your private key or seed phrase with anyone.
- Use hardware wallets for large or long-term holdings.
- Be cautious of phishing websites and fake wallet apps.
- Always verify URLs and avoid signing unknown transactions.
- Avoid storing keys in cloud services, messaging apps or password managers.

Wallets use hierarchical deterministic (HD) structures (BIP-32) to derive multiple addresses from a single seed, allowing for privacy and key rotation.


### The Security Risks of Digital Signatures
When you interact with a dApp, you often need to sign a message or transaction. This proves that you authorised the action. These digital signatures are generated using your private key and verified using your public key. However, signing unknown or malicious messages can expose you to:
- **Replay attacks**: Reusing a valid signature in a different context.
- **Approval exploits**: Signing a transaction that grants unlimited access to your tokens.
- **Malicious payloads**: Signing hex-encoded data without knowing its effect.
- **Blind signing**: Approving a transaction without understanding what it does.

To stay safe:
- Use wallets that show clear transaction previews.
- Avoid signing raw data unless you understand it.
- Revoke token approvals when necessary.

### Common Fraud Tactics
Web3 fraud often involves:

- **Rug pulls**: Developers abandon a project after collecting funds.
- **Pump-and-dump schemes**: Artificially inflating token prices before mass sell-offs.
- **Fake support channels**: Scammers impersonate wallet or exchange staff.
- **Airdrop scams**: Fake tokens prompt users to connect wallets and sign malicious transactions.

### Preventive Measures and Handling Theft

To protect yourself:

- Use multisig wallets for critical assets.
- Bookmark official dApp URLs.
- Monitor wallet activity using blockchain explorers.
- Double-check contract addresses.
- If compromised:
    - Transfer assets to a new wallet immediately.
    - Revoke token approvals.
    - Notify the community and relevant platforms.