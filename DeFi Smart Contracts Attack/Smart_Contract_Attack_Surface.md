# Post-Quantum Impact on DeFi Smart Contracts

This document outlines which DeFi smart contract functions are most affected by Post-Quantum Cryptography (PQC) attacks, and what types of vulnerabilities emerge when ECDSA-based signature schemes become breakable.

# 1. Key Insight

Post-quantum attacks **do NOT introduce traditional smart contract bugs** such as:

* Reentrancy
* Integer overflow
* Honeypots
* Logic flaws
* Flash loan manipulation

Instead, PQC attacks **break cryptographic authentication**, allowing attackers to **forge valid signatures**.

This leads to **authorisation bypass vulnerabilities**.

# 2. Most Affected Smart Contract Functions

## 2.1 permit() — Highest Risk

Used in:

* ERC20Permit
* Uniswap
* Lending protocols
* Vault approvals
* Gasless approvals

Example:

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

### PQC Attack

1. Quantum attacker derives the private key
2. Forge permit signature
3. Gain allowance
4. Call transferFrom()
5. Drain tokens

### Impact

**Direct wallet drain without user interaction**

## 2.2 withdraw() With Signature Authorization

Used in:

* Bridges
* Vaults
* Custodial DeFi
* Off-chain authorised withdrawals

Example:

```solidity
function withdraw(
    uint256 amount,
    bytes calldata signature
)
```

### PQC Attack

* Forge signature
* Call withdraw()
* Transfer funds to the attacker

### Impact

**Protocol liquidity drain**


## 2.3 execute() / meta-transaction functions

Used in:

* Multisig wallets
* Smart accounts
* Relayers
* ERC-4337 accounts

Example:

```solidity
function execute(
    address to,
    uint256 value,
    bytes calldata data,
    bytes calldata signature
)
```

### PQC Attack

* Forge signer's signature
* Execute arbitrary call
* Transfer funds

### Impact

**Full contract control**

## 2.4 Governance Signature Functions

Used in DAO governance:

```solidity
castVoteBySig()
delegateBySig()
```

### PQC Attack

1. Break whale wallet
2. Forge a vote signature
3. Pass the malicious proposal
4. Execute treasury drain

### Impact

**DAO takeover**

## 2.5 Bridge Validator Signature Verification

Used in cross-chain bridges:

```solidity
verifySignatures(validators, sigs)
```

### PQC Attack

* Break validator key
* Forge approval
* Mint wrapped tokens
* Drain liquidity

### Impact

**Bridge collapse / systemic risk**

---

# 3. Attack Types Enabled by PQC

## 3.1 Unauthorised Liquidity Withdrawal

```
forge signature
→ call withdraw()
→ send funds to attacker EOA
```

## 3.2 Permit-Based Token Drain

```
forge permit()
→ gain allowance
→ transferFrom()
→ drain wallet
```

---

## 3.3 Multisig Bypass

```
break signer key
→ fake approval
→ execute transfer
```

## 3.4 Smart Account Takeover (ERC-4337)

```
forge UserOp signature
→ execute arbitrary call
→ drain account
```

---

## 3.5 Governance Takeover

```
break whale key
→ forge a vote
→ pass a malicious proposal
→ drain treasury
```


# 4. Not Affected by PQC

These vulnerabilities are unrelated to quantum attacks:

* Reentrancy
* Honeypots
* Flash loan manipulation
* Arithmetic bugs
* Access control logic errors

PQC affects **identity verification**, not **execution logic**.


# 5. Highest Risk DeFi Protocol Types

1. ERC20Permit tokens
2. Lending protocols
3. Cross-chain bridges
4. Multisig treasury wallets
5. ERC-4337 smart accounts
6. Vault protocols
7. Meta-transaction relayers


# 6. Root Cause

Most DeFi protocols rely on:

* ECDSA signatures
* ecrecover()
* permit()
* off-chain approvals

Quantum computers running **Shor's algorithm** can derive private keys, allowing attackers to generate valid signatures.

Smart contracts remain correct — cryptographic trust breaks.


# 7. One-Line Summary

Post-quantum attacks break signature-based authorisation in DeFi smart contracts, enabling unauthorised withdrawals, permit-based token drains, multisig bypass, and governance takeover rather than traditional logic vulnerabilities like reentrancy.
