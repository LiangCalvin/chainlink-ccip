# Chainlink Automation Examples

## Overview
This repository demonstrates three types of Chainlink Automation patterns:

- **Time-Based Trigger**: Executes logic at fixed time intervals  
- **Custom Logic Trigger**: Executes logic based on on-chain conditions  
- **Log Trigger (Event-Based)**: Executes logic when a specific event is emitted  

These examples illustrate how to automate smart contract execution without manual intervention.

---

## Contracts

### 1. TimeBased

A simple contract used for time-based automation.

**Functionality:**
- Maintains a `counter`
- Provides a `count()` function to increment the counter

**Usage:**
- Chainlink Automation can be configured to call `count()` at fixed intervals

---

### 2. CustomLogic

A contract implementing condition-based automation using `AutomationCompatibleInterface`.

**Key Features:**
- Executes logic only when a time interval has passed
- Uses `checkUpkeep` to determine if execution is needed
- Uses `performUpkeep` to perform the action

**State Variables:**
- `counter`: Tracks number of executions  
- `i_updateInterval`: Time interval between executions  
- `lastTimeStamp`: Last execution timestamp  

**Workflow:**
1. `checkUpkeep()` checks if `(block.timestamp - lastTimeStamp) > i_updateInterval`
2. If true → returns `upkeepNeeded = true`
3. Chainlink Automation calls `performUpkeep()`
4. Counter increments and timestamp updates

---

### 3. EventEmitter (Log Producer)

A helper contract that emits logs for log-trigger automation.

**Event:**
- `WantsToCount(address indexed msgSender)`

**Function:**
- `emitCountLog()` emits the event

---

### 4. LogTrigger

A contract implementing log-based automation using `ILogAutomation`.

**Key Features:**
- Reacts to emitted logs (events)
- Decodes event data and performs logic

**State Variables:**
- `counted`: Number of times upkeep executed

**Workflow:**
1. `EventEmitter` emits `WantsToCount`
2. Chainlink Automation detects the log
3. `checkLog()` is triggered:
   - Always returns `true`
   - Extracts `msgSender` from log topics
4. `performUpkeep()`:
   - Increments `counted`
   - Emits `CountedBy` event

---

## Automation Types Summary

| Type            | Trigger Condition                  | Example Contract |
|-----------------|----------------------------------|------------------|
| Time-Based      | Fixed interval                    | TimeBased        |
| Custom Logic    | On-chain condition (timestamp)    | CustomLogic      |
| Log Trigger     | Smart contract event (logs)       | LogTrigger       |

---

## Deployment Steps

1. Deploy contracts:
   - `TimeBased`
   - `CustomLogic` (with interval parameter)
   - `EventEmitter`
   - `LogTrigger`

2. Register upkeep on Chainlink Automation:
   - Select appropriate trigger type:
     - Time-based → call `count()`
     - Custom logic → use `checkUpkeep` / `performUpkeep`
     - Log trigger → connect `EventEmitter` logs to `LogTrigger`

---

## Example Use Cases

- Scheduled tasks (cron-like jobs)
- Automated rebalancing or state updates
- Event-driven workflows
- Off-chain automation triggers

---

## Tech Stack

- Solidity ^0.8.26
- Chainlink Automation (Keepers)

---

## Notes

- Ensure sufficient LINK balance for upkeep execution
- Gas limits must be configured correctly during upkeep registration
- Log trigger requires correct event signature and topic filtering

---

## License

MIT
