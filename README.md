# Chainlink CCIP Cross-Chain Messaging and Token Transfer

A Solidity project for learning and experimenting with **Chainlink CCIP (Cross-Chain Interoperability Protocol)** using Remix.

This repository covers:

- CCIP Local Simulation
- Cross-chain message passing
- Cross-chain USDC token transfer
- Cross-chain contract execution
- Vault deposit via CCIP

⚠️ This is a learning project based on tutorials and experimentation.  
Example contracts contain hardcoded addresses and unaudited code.  
Not intended for production use.

---

# Architecture

This project includes 3 layers of CCIP examples:

## 1. Local Simulation

Uses Chainlink Local Simulator to test CCIP behavior locally.

- Simulated router
- Simulated message routing
- Local development environment

Contracts:

- `CCIPLocalSimulator.sol`

---

## 2. Basic Cross-Chain Messaging

Simple text message transfer between chains.

Flow:

```text
MessageSender
   ↓
CCIP Router
   ↓
MessageReceiver
```

Contracts:

- `chainlink-local/MessageSender.sol`
- `chainlink-local/MessageReceiver.sol`

Features:

- Send string payload ("Hey there!")
- Pay CCIP fees in LINK
- Receive and decode message
- Store last received message
- Emit send/receive events

---

## 3. Cross-Chain Token Transfer + Vault Execution

Advanced example combining:

- Cross-chain USDC transfer
- Cross-chain contract call
- Remote vault deposit

Flow:

```text
Ethereum Sepolia

Sender.sol
   ↓

CCIP Router
   ↓

Base Sepolia

Receiver.sol
   ↓

Vault.sol.deposit()
```

---

# Contracts

## CCIPLocalSimulator.sol

Imports and uses:

- Chainlink CCIP Local Simulator

Purpose:

- Test CCIP locally
- Simulate source/destination chains

---

## CCIPTokenSender.sol

Simple CCIP token transfer example.

Features:

- Sends USDC cross-chain
- Pays fees in LINK
- Uses `ccipSend()`
- Emits `USDCTransferred`
- Owner can withdraw tokens

Hardcoded:

- Sepolia Router
- LINK Token
- USDC Token
- Base Sepolia destination selector

---

## MessageSender.sol

Basic CCIP message sender.

Features:

- Sends "Hey there!"
- Builds EVM2AnyMessage
- Calculates CCIP fees
- Pays fees using LINK

---

## MessageReceiver.sol

Basic CCIP receiver.

Features:

- Receives string message
- Decodes payload
- Stores last message
- Emits `MessageReceived`

---

## Sender.sol

Advanced cross-chain sender.

Features:

- Sends USDC cross-chain
- Encodes Vault deposit call:

```solidity
deposit(address account, uint256 amount)
```

- Uses:

```solidity
abi.encodeWithSelector(
IVault.deposit.selector,
msg.sender,
_amount
)
```

- Transfers token + message together

This demonstrates:

- Programmable Token Transfer
- Cross-chain contract execution

---

## Receiver.sol

Advanced CCIP receiver.

Features:

- Receives tokens + calldata
- Allowlists source sender
- Decodes:

```solidity
(address target, bytes functionCallData)
```

Executes:

```solidity
target.call(functionCallData)
```

Triggers:

```solidity
Vault.deposit(...)
```

Security:

- Source chain allowlist
- Sender allowlist
- Owner controls sender authorization

---

## Vault.sol

Simple vault on Base Sepolia.

Features:

- Tracks balances

```solidity
mapping(address => uint256) balances
```

- Deposit USDC
- Withdraw USDC

Used as target contract for cross-chain deposit execution.

---

## IVault.sol

Interface used by Sender.

Functions:

```solidity
deposit(address,uint256)
withdraw(uint256)
```

---

# Network Setup

Source Chain:

- Ethereum Sepolia

Destination Chain:

- Base Sepolia

Assets:

- LINK (CCIP Fees)
- USDC (Cross-chain token transfer)

---

# Project Structure

```bash
contracts/
│
├── CCIPLocalSimulator.sol
├── CCIPTokenSender.sol
│
├── chainlink-local/
│   ├── MessageSender.sol
│   └── MessageReceiver.sol
│
├── interfaces/
│   └── IVault.sol
│
├── Sender.sol
├── Receiver.sol
└── Vault.sol
```

---

# Concepts Covered

This project explores:

- CCIP Messaging

- EVM2AnyMessage

- Any2EVMMessage

- Chain Selectors

- CCIP Fee Estimation

- Programmable Token Transfer

- Cross-Chain Contract Calls

- Allowlisting Source Chains and Senders

---

# Technologies

- Solidity 0.8.26

- Chainlink CCIP

- Chainlink Local

- OpenZeppelin

- Remix IDE

---

# References

Chainlink CCIP Docs

https://docs.chain.link/ccip

Chainlink CCIP Directory

https://docs.chain.link/ccip/directory

Chainlink Local

https://docs.chain.link/chainlink-local

Remix

https://remix.ethereum.org

---

# License

MIT
