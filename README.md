# Post-Quantum Vulnerabilities in DeFi Smart Contracts

This repository explores how **Post-Quantum Cryptography (PQC)** impacts DeFi smart contracts, wallet authorisation, and signature-based execution flows.

The focus is on **what breaks first** when ECDSA becomes vulnerable to quantum attacks.

# TL;DR

Post-quantum attacks **do not create traditional smart contract bugs** like reentrancy.

Instead, they **break signature-based authorization**, allowing attackers to:

* Forge `permit()` approvals
* Bypass multisig wallets
* Drain DeFi vaults
* Take over DAO governance
* Hijack ERC-4337 smart accounts
* Forge bridge validator signatures

# Root Cause

Most DeFi protocols rely on:

* ECDSA signatures (secp256k1)
* `ecrecover()`
* `permit()` approvals
* off-chain signed messages
* meta-transactions

A sufficiently powerful quantum computer running **Shor's algorithm** can:

* derive private key from public key
* forge valid signatures
* bypass authorization logic

Smart contracts remain correct — **cryptographic trust breaks**.

# Most Affected DeFi Functions

## 1. permit() — Highest Risk

Used in:

* ERC20Permit
* Uniswap
* Lending protocols
* Vault approvals

```solidity
function permit(
    address owner,
    address spender,
    uint256 value,
    uint256 deadline,
    uint8 v,
    bytes32 r,
    bytes32 s
)
```

### Attack

forge signature
→ gain allowance
→ transferFrom()
→ drain wallet
```

Impact: **Direct token drain**

## 2. withdraw() with signature authorization

Used in:

* Bridges
* Vaults
* Custodial DeFi

```solidity
function withdraw(uint256 amount, bytes signature)
```

### Attack

```
forge signature
→ call withdraw()
→ send funds to attacker
```

Impact: **Protocol liquidity drain**


## 3. execute() / meta-transaction functions

Used in:

* Multisig wallets
* Smart accounts
* Relayers
* ERC-4337 accounts

```solidity
execute(to, value, data, signature)
```

### Attack

```
forge signature
→ execute arbitrary call
→ transfer funds
```

Impact: **Full contract control**

## 4. Governance Signature Functions

```
castVoteBySig()
delegateBySig()
```

### Attack

```
break whale key
→ forge vote
→ pass malicious proposal
→ drain treasury
```

Impact: **DAO takeover**

## 5. Bridge Validator Verification

```
verifySignatures(validators, sigs)
```

### Attack

```
break validator key
→ forge approval
→ mint tokens
→ drain liquidity
```

Impact: **Bridge collapse**

# What PQC Does NOT Affect

These are logic bugs unrelated to quantum attacks:

* Reentrancy
* Honeypots
* Flash loan attacks
* Arithmetic overflow
* Access control bugs

PQC breaks **identity**, not **execution flow**.

# Realistic PQC DeFi Attack Flow

```
Quantum attacker derives private key
        ↓
Forge permit() signature
        ↓
Gain token allowance
        ↓
Transfer tokens
        ↓
Swap on DEX
        ↓
Remove liquidity
        ↓
Send funds to attacker EOA
```

# Highest Risk Protocol Types

* ERC20Permit tokens
* Lending protocols
* Cross-chain bridges
* Multisig treasury wallets
* ERC-4337 smart accounts
* Vault protocols
* Meta-transaction relayers


# ERC-4337 Impact

ERC-4337 enables programmable signature verification.

This allows:

* Dilithium wallets
* Falcon wallets
* SPHINCS+ wallets
* Hybrid PQ wallets

But if smart accounts still use **ECDSA**, they remain vulnerable.

Account abstraction is **quantum-ready**, not quantum-safe.


# ecrecover() Risk

Most smart contracts rely on:

```solidity
ecrecover(hash, v, r, s)
```

Quantum attacker can:

* derive private key
* forge `(v,r,s)`
* bypass signature verification

Impact: **authorization bypass**

# Mitigation (Future)

Possible post-quantum solutions:

* Dilithium signature verification
* Falcon signatures
* SPHINCS+ signatures
* Hybrid PQ + ECDSA wallets
* ERC-4337 PQ smart accounts
* New EVM PQ precompiles

# Research Goal

This repository explores:

* Post-quantum DeFi attack surface
* PQC wallet architecture
* ERC-4337 quantum-safe design
* ecrecover vulnerability analysis
* PQC signature integration


# One-Line Summary

Post-quantum attacks break signature-based authorisation in DeFi, enabling unauthorised withdrawals, permit-based token drains, multisig bypass, and governance takeover rather than traditional logic vulnerabilities.


# License

MIT
