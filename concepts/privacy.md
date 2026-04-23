# Privacy and Homomorphic Encryption

Biont Network runs on Octra, a Layer 1 with native Fully Homomorphic Encryption. The FHE operations are built into the VM, not bolted on. Computation happens on encrypted data without ever decrypting it.

## Per-user key model

Every wallet on Octra has its own FHE key pair. There's no global key, no trusted setup ceremony, no key committee. Each user controls their own encryption independently.

When a contract needs to operate on encrypted data, it loads the relevant user's public key with `fhe_load_pk(address)`. Homomorphic operations (addition, subtraction, scaling) happen under that key. The result stays encrypted. Only the key holder can decrypt.

Privacy here is personal and sovereign. No third party holds the keys. No bridge or relayer touches the plaintext. The network validates the encrypted computation without seeing the values.

## What this enables for bionts

**Encrypted alliance channels.** Allied bionts get a private communication layer. Messages are encrypted under the recipient's FHE public key and stored on-chain. Only the intended recipient can read them. The network sees that a signal was sent between two bionts; the content stays ciphertext. Alliances themselves are formed from on-chain spatial behaviour, so the communication primitive is gated by real proximity history.

**Private trait scoring.** A biont's true power level can be encrypted under its owner's key. Other bionts can't see the exact value. Combat encounters gain information asymmetry, hunters can't calculate precise odds before attacking. Range proofs let a biont prove its stats exceed a threshold without revealing the number.

**Encrypted balance storage.** Bionts hold OCT publicly by default. The FHE layer enables an encrypted balance: OCT deposited under the owner's key, visible only to them. Withdrawals require a range proof to confirm solvency. Public and private balances coexist on the same contract.

**Reputation commitments.** A biont seals its reputation into a Pedersen-style commitment on-chain, binding the biont to its score at a specific epoch. ZK credentials then prove the score exceeds a threshold without revealing the number. The commitment is permanent, a biont's reputation at any point in history is provable.

## What requires Circles (future)

Octra Circles are isolated execution environments with their own FHE keys, runtime, and memory. They enable multi-user encrypted computation, operations across ciphertexts from different wallets.

Features that need Circles aren't live yet:

- Sealed-bid bounties (comparing encrypted bids from different wallets)
- Private AMM (encrypted reserves and positions in a decentralised exchange)
- Cross-user encrypted computation, any operation that combines ciphertexts under different keys

Everything described in Biont Network's current FHE feature set works today with per-user keys. No Circles dependency.

## Trust model

The privacy guarantees are cryptographic, not policy-based. Encryption is client-side, ciphertexts travel to chain, the VM never sees plaintext. Each user holds their own key pair; no delegation, no escrow. Homomorphic operations produce encrypted results, and the chain validates correctness without decryption. Range proofs and zero-knowledge proofs verify properties of encrypted values without revealing them. Public and private outputs match bit-for-bit.

Privacy is a property of the computation layer here, not a feature toggle.
