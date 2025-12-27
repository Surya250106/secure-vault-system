# Secure Vault Authorization System

## Overview

This repository contains a secure, deterministic vault system that enforces **explicit, single-use withdrawal authorizations** through a separated on-chain authorization manager.

The system reflects real-world decentralized architectures where **asset custody** and **permission validation** are intentionally split across multiple contracts to reduce risk, improve auditability, and prevent authorization replay attacks.

The project is fully reproducible using Docker and deploys automatically to a local blockchain environment.

---

## System Architecture

The system is composed of two smart contracts:

### SecureVault.sol
- Holds native blockchain currency (ETH)
- Accepts deposits from any address
- Executes withdrawals only after authorization approval
- Updates internal state before transferring value
- Emits events for deposits and withdrawals

### AuthorizationManager.sol
- Validates off-chain generated withdrawal permissions
- Tracks consumed authorizations
- Enforces one-time authorization usage
- Prevents replay and duplicate execution
- Emits authorization consumption events

The vault never performs cryptographic verification directly and relies exclusively on the authorization manager.

---

## Authorization Design

Each withdrawal authorization is generated off-chain and is cryptographically bound to:

- Vault contract address
- Blockchain network identifier (chainId)
- Withdrawal recipient
- Withdrawal amount
- Unique authorization identifier (nonce)
- Signature data

Each authorization may be used **exactly once**.  
Reused or invalid authorizations cause the transaction to revert.

---

## Security Guarantees

The system enforces the following invariants:

- Vault balance can never become negative
- Withdrawals require explicit authorization
- Authorizations cannot be replayed
- State transitions occur exactly once
- Unauthorized callers cannot influence privileged logic
- Cross-contract calls cannot duplicate effects

All value transfers follow the checks-effects-interactions pattern.

---

## Events

The following events are emitted for observability:

- `DepositReceived(address sender, uint256 amount)`
- `AuthorizationConsumed(bytes32 authorizationId)`
- `WithdrawalExecuted(address recipient, uint256 amount)`

Failed withdrawals revert deterministically.

---

## Repository Structure

```
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
```

---

## Local Deployment (Docker)

### Prerequisites
- Docker
- Docker Compose

### Run the System

```bash
docker-compose up --build
```

This command will:

1. Start a local EVM-compatible blockchain
2. Compile the smart contracts
3. Deploy `AuthorizationManager`
4. Deploy `SecureVault` with the authorization manager address
5. Output deployed contract addresses to logs

The RPC endpoint is exposed for local interaction.

---

## Deployment Output

After deployment, logs will display:

- Network identifier
- AuthorizationManager contract address
- SecureVault contract address

This information is required to generate valid off-chain authorizations.

---

## Testing

Automated tests are located in:

```
tests/system.spec.js
```

Tests demonstrate:

- Successful authorized withdrawals
- Reverted unauthorized withdrawals
- Replay protection enforcement
- Correct balance accounting
- Proper event emission

---

## Assumptions & Limitations

- Contracts are non-upgradeable
- Only native blockchain currency is supported
- Authorization generation occurs off-chain
- No frontend is included

---

## License

MIT License
