## Non-Fungible Tokens (SIP-009)
Non-fungible tokens (NFTs) represent unique digital assets — each with its own identity, metadata, and ownership. Unlike fungible tokens, NFTs are not interchangeable. This section introduces the SIP-009 standard for NFTs on Stacks, explains how to design token IDs and metadata, and walks through implementing and testing a compliant NFT contract in Clarity.

### What Are NFTs?
NFTs are digital assets that are **provably unique** and **individually owned**. Each token has:
- A distinct **token ID**
- A single **owner**
- Optional **metadata** (e.g. image, name, description)

Common use cases include:
- Digital art and collectables
- Identity and credentials
- Game items and achievements
- Real-world asset representation

### SIP-009 Interface
SIP-009 defines the standard trait for NFTs on Stacks. Any contract implementing this trait can be recognised by wallets, marketplaces, and other contracts.

**Required functions:**
- `(mint (token-id uint) (owner principal)) : (response bool uint)`
- `(transfer (token-id uint) (sender principal) (recipient principal)) : (response bool uint)`
- `(get-owner (token-id uint)) : (response principal uint)`
- `(get-token-uri (token-id uint)) : (response (string-utf8 256) uint)`

These functions ensure basic NFT functionality: creation, ownership, transfer, and metadata access.

Reference: [SIP-009 NFT Standard – GitHub](https://github.com/stacksgov/sips/blob/main/sips/sip-009/sip-009-nft-standard.md)

### Token ID Design and Metadata Storage
Each NFT must have a **unique token ID**. This can be:

- A sequential number (`u1`, `u2`, `u3`)
- A hash of content or metadata
- A UUID-style random number

Metadata is typically stored off-chain (e.g. IPFS, HTTPS) and referenced via a URI.

**Example:**
```Clojure
  (define-map token-uris ((id uint)) ((uri (string-utf8 256))))
```

This map links each token ID to its metadata URI.

### Implementing a SIP-009 NFT Contract
Use `use-trait` to import the SIP-009 interface from a published contract.

**Example:**
```Clojure
  (use-trait sip009-nft .sip009-trait.nft)

  (define-map token-owners ((id uint)) ((owner principal)))
  (define-map token-uris ((id uint)) ((uri (string-utf8 256))))

  (define-public (mint (token-id uint) (owner principal))
    (begin
      (asserts! (is-none (map-get? token-owners token-id)) (err u100)) ;; Already minted
      (map-set token-owners token-id owner)
      (map-set token-uris token-id "https://example.com/metadata.json")
      (ok true)))

  (define-public (transfer (token-id uint) (sender principal) (recipient principal))
    (begin
      (let ((current-owner (map-get? token-owners token-id)))
        (asserts! (is-some current-owner) (err u101)) ;; Token doesn't exist
        (asserts! (is-eq (unwrap! current-owner (err u102)) sender) (err u103)) ;; Unauthorized
        (map-set token-owners token-id recipient)
        (ok true))))

  (define-read-only (get-owner (token-id uint))
    (match (map-get? token-owners token-id)
      owner (ok owner)
      (err u104)))

  (define-read-only (get-token-uri (token-id uint))
    (match (map-get? token-uris token-id)
      uri (ok uri)
      (err u105)))
```

### Minting and Transferring NFTs
Use Clarinet to simulate minting and transfers in tests.

**Test case:**
```ts
  it("mints and transfers an NFT", async () => {
    const chain = simnet.chain("devnet");
    const deployer = chain.accounts.get("deployer");
    const user = chain.accounts.get("wallet_1");

    const mintBlock = chain.mineBlock([
      Tx.contractCall("nft", "mint", [types.uint(1), deployer.address], deployer.address),
    ]);
    expect(mintBlock.receipts[0].result).toEqual(types.ok(types.bool(true)));

    const transferBlock = chain.mineBlock([
      Tx.contractCall("nft", "transfer", [types.uint(1), deployer.address, user.address], deployer.address),
    ]);
    expect(transferBlock.receipts[0].result).toEqual(types.ok(types.bool(true)));
  });
```

Reference: [Clarinet Unit Testing Guide](https://docs.stacks.co/build/clarinet-js-sdk/unit-testing)