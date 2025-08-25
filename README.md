
# Verisilence - ZKP Identity Protocol

## Overview

The **ZKP Identity Protocol** is a privacy-preserving identity verification smart contract built on the Clarity language (Stacks blockchain). It enables individuals to prove aspects of their identity (e.g., KYC, age, location, accreditation) via zero-knowledge proofs (ZKPs), without revealing sensitive information.

Verifiers (such as dApps or services) can validate submitted proofs to ensure compliance, while users maintain control over their identity data.

---

## ⚙️ Features

* ✅ **ZKP-based Identity Verification**: Add and validate zero-knowledge proofs tied to specific identity traits.
* 👤 **User-Owned Identities**: Only identity owners or the contract owner can revoke identities.
* 🛡️ **Proof Registry**: Register cryptographic proofs with metadata like expiry and verification type.
* 🧾 **Verifier Registry**: Only authorized verifiers can validate certain verification types.
* ⏳ **Expiry & Revocation Support**: Proofs and identities have time-based validity and can be revoked.
* 🔒 **Merkle Tree Support (via `verify-merkle-proof`)**: Validate inclusion in privacy-preserving structures.

---

## 📚 Data Structures

### Identities

Stores metadata about a registered identity.

```clojure
{
  owner: principal,
  status: uint, ;; ACTIVE, REVOKED, or EXPIRED
  creation-height: uint,
  expiry-height: uint,
  verification-types: (list 10 uint),
  merkle-root: (buff 32),
  attestations: uint,
  revocation-height: (optional uint)
}
```

### Verifiers

Holds information about an authorized verifier.

```clojure
{
  allowed-types: (list 10 uint),
  verifications-performed: uint,
  last-verification: uint,
  is-active: bool
}
```

### VerificationRequests

Logs requests for identity verification.

```clojure
{
  requester: principal,
  identity-hash: (buff 32),
  verification-type: uint,
  request-height: uint,
  status: uint,
  proof: (optional (buff 512)),
  verifier: (optional principal)
}
```

### ProofRegistry

Stores valid proofs for specific identities.

```clojure
{
  identity-hash: (buff 32),
  verification-type: uint,
  creation-height: uint,
  expiry-height: uint,
  is-valid: bool
}
```

---

## 🔐 Constants

### Identity Status Codes

* `STATUS-ACTIVE = u1`
* `STATUS-REVOKED = u2`
* `STATUS-EXPIRED = u3`

### Verification Types

* `TYPE-KYC = u1`
* `TYPE-AGE = u2`
* `TYPE-LOCATION = u3`
* `TYPE-ACCREDITED = u4`

### Error Codes

* `ERR-UNAUTHORIZED = (err u1)`
* `ERR-INVALID-PROOF = (err u2)`
* `ERR-IDENTITY-EXISTS = (err u3)`
* `ERR-NO-IDENTITY = (err u4)`
* `ERR-EXPIRED = (err u5)`
* `ERR-INVALID-VERIFIER = (err u6)`
* `ERR-REVOKED = (err u7)`
* `ERR-VERIFICATION-FAILED = (err u8)`

---

## 🧩 Key Functions

### `add-proof`

Registers a valid ZK proof for a specific identity and verification type.

### `verify-identity`

Allows a verifier to check if a proof is valid and authorized for verification.

### `revoke-identity`

Revokes an identity—either by the identity owner or contract owner.

### `register-verifier`

Only callable by the contract owner; adds or updates a verifier's authorization and verification capabilities.

### `get-identity-info`, `get-proof-info`, `get-verifier-info`, `is-identity-valid`

Read-only views to access state and check validity.

---

## 🛠️ Deployment Requirements

* **Blockchain**: Stacks
* **Language**: Clarity
* **ZK Support**: Proofs assumed to be generated off-chain and verified via hash comparison / Merkle proofs.

---

## 🔒 Security Considerations

* Proofs are stored by their hashes—never revealing raw data.
* Expiry and revocation ensure time-limited trust assumptions.
* Only the identity owner or the contract owner may modify identity status.
* Verifiers are strictly whitelisted by the contract owner.

---

## 💡 Use Cases

* **Regulatory KYC/AML checks**
* **Age verification for restricted content**
* **Location verification for region-locked services**
* **Accreditation verification for investment platforms**

---
