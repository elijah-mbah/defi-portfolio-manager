# Portfolio Management Protocol - Smart Contract Documentation

## Overview

The Portfolio Management Protocol is a Clarity smart contract designed for automated portfolio management on the Stacks blockchain. It enables users to create, manage, and rebalance token portfolios with programmable asset allocation strategies. Key features include portfolio creation, percentage-based asset allocation, automated rebalancing, and user-specific portfolio tracking.

## Features

- **Portfolio Creation**: Users create portfolios with up to 10 tokens and custom allocation percentages.
- **Automated Rebalancing**: Rebalance portfolios every 24 hours (logic trigger included; full implementation requires extension).
- **Dynamic Allocation Updates**: Adjust target percentages for assets post-creation.
- **User-Centric Design**: Track portfolios per user with a 20-portfolio limit per address.
- **Protocol Fees**: Configurable fee system (currently set to 0.25% in basis points).
- **Robust Error Handling**: 11 predefined error conditions with clear error codes.

## Technical Specifications

### Data Structures

| **Map**           | **Key**                              | **Value**                                                                     |
| ----------------- | ------------------------------------ | ----------------------------------------------------------------------------- |
| `Portfolios`      | `uint` (portfolio ID)                | Owner, creation time, last rebalance, total value, active status, token count |
| `PortfolioAssets` | Composite: `portfolio-id + token-id` | Target allocation %, current token amount, token contract address             |
| `UserPortfolios`  | `principal`                          | List of portfolio IDs (max 20)                                                |

### Constants

- `MAX-TOKENS-PER-PORTFOLIO`: 10 tokens
- `BASIS-POINTS`: 10,000 (1% = 100 basis points)
- Protocol fee: 25 basis points (0.25%)

## Core Functions

### 1. `create-portfolio`

**Purpose**: Initialize a new portfolio  
**Parameters**:

- `initial-tokens`: List of token addresses (max 10)
- `percentages`: Corresponding allocation percentages (sum must = 10,000 basis points)

**Example Call**:

```clarity
(create-portfolio
  (list 'SP3FBR2AGK5H9QBDH3EEN6DF8EK8JY7RX8QJ5SVTE.token-a 'SP3FBR2AGK5H9QBDH3EEN6DF8EK8JY7RX8QJ5SVTE.token-b)
  (list u5000 u5000)
```

### 2. `rebalance-portfolio`

**Purpose**: Initiate portfolio rebalancing (stub implementation)  
**Checks**:

- Caller must be portfolio owner
- Portfolio must be active
- 24-hour cooldown enforcement

### 3. `update-portfolio-allocation`

**Purpose**: Modify target allocation for a specific asset  
**Parameters**:

- `portfolio-id`: Target portfolio ID
- `token-id`: Index of token in portfolio (0-based)
- `new-percentage`: New allocation in basis points

## Error Codes

| **Code**                         | **Description**                  |
| -------------------------------- | -------------------------------- |
| `ERR-NOT-AUTHORIZED` (u100)      | Unauthorized access attempt      |
| `ERR-INVALID-PORTFOLIO` (u101)   | Nonexistent/inactive portfolio   |
| `ERR-INVALID-PERCENTAGE` (u106)  | Invalid allocation percentage    |
| `ERR-MAX-TOKENS-EXCEEDED` (u107) | Portfolio exceeds 10-token limit |
| Full list in contract code       | 11 total error conditions        |

## Security Model

1. **Ownership Controls**:
   - Protocol owner set at deployment (`protocol-owner`)
   - Portfolio-specific owner permissions
2. **Validation Guards**:
   - Percentage bounds checks (0-10,000 basis points)
   - Token ID index validation
   - Input length matching enforcement
3. **Reentrancy Protection**: Native Clarity safety guarantees

## Usage Example Flow

1. **Deployment**:
   - Initialize contract with `(initialize 'owner-address)`
2. **Create Portfolio**:
   ```clarity
   (create-portfolio
     (list tokenX tokenY)
     (list u3000 u7000))
   ```
3. **Monthly Maintenance**:
   ```clarity
   (update-portfolio-allocation u0 u1 u7500) ;; Update tokenY to 75%
   (rebalance-portfolio u0)
   ```

## Limitations & Extension Points

1. **Current Limitations**:
   - Token value tracking not implemented
   - Actual swap mechanics for rebalancing not included
   - Fee collection mechanism pending
2. **Extension Opportunities**:
   - Integrate with DEX for automated swaps
   - Add price oracle integration
   - Implement fee accrual/distribution

## Version & Compatibility

**Clarity Version**: 2.0+  
**Token Standard**: Assumes SIP-010 fungible tokens  
**Network**: Stacks Mainnet/Testnet compatible
