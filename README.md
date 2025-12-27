# Secure Vault Authorization System

## Overview

This repository implements a secure, deterministic vault system that enforces explicit, single-use withdrawal authorizations through a separated on-chain authorization manager.

The system models real-world decentralized architectures where asset custody and permission validation are intentionally split to reduce risk and improve clarity.

## System Architecture

The system consists of two on-chain smart contracts:

### SecureVault
Responsible for:
- Holding native blockchain currency (ETH)
- Executing withdrawals
- Enforcing authorization checks before releasing funds

The vault does not perform cryptographic verification and cannot independently approve withdrawals.

### AuthorizationManager
Responsible for:
- Validating off-chain generated withdrawal permissions
- Tracking authorization usage
- Enforcing one-time execution guarantees

## Design Principles

- Explicit trust boundaries between custody and authorization
- Single-use permissions with deterministic replay protection
- Checks-effects-interactions enforced on all value transfers
- Strict contextual binding of authorizations
- Fail-fast behavior with deterministic reverts

## Authorization Model

Each authorization is bound to:
- Vault address
- Chain ID
- Recipient address
- Withdrawal amount
- Unique nonce
- Signature data

Each authorization may be consumed exactly once.

## Repository Structure

/
├─ contracts/
│  ├─ SecureVault.sol
│  └─ AuthorizationManager.sol
├─ scripts/
│  └─ deploy.js
├─ tests/
│  └─ system.spec.js
├─ docker/
│  ├─ Dockerfile
│  └─ entrypoint.sh
├─ docker-compose.yml
└─ README.md

## Local Deployment

Run the following command:

docker-compose up --build

This will start a local blockchain, deploy contracts, and log deployment details.

## Security Guarantees

- Vault balance never becomes negative
- Withdrawals require valid authorization
- Authorizations cannot be replayed
- State transitions occur exactly once

## License

MIT License
