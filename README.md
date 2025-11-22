# Arbitrage-Bot

Welcome to the `Arbitrage-Bot` repository — a comprehensive toolkit designed to demystify blockchain arbitrage strategies for developers. This project provides thoroughly documented code and detailed instructions to help you understand arbitrage concepts and develop your own effective strategies.

## 🙋‍♂️ Cᴏɴᴛᴀᴄᴛ ᴍᴇ Oɴ ʜᴇʀᴇ: 👋 ##

Telegram: https://t.me/opensea712

<div style={{display : flex ; justify-content : space-evenly}}> 
    <a href="https://t.me/opensea712" target="_blank"><img alt="Telegram"
        src="https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white"/></a>
    <a href="https://discordapp.com/users/343286332446998530" target="_blank"><img alt="Discord"
        src="https://img.shields.io/badge/Discord-7289DA?style=for-the-badge&logo=discord&logoColor=white"/></a>
</div>


## Table of Contents

- [What is Arbitrage?](#what-is-arbitrage)
- [Why Blockchain Arbitrage?](#why-blockchain-arbitrage)
- [Verification and Security](#verification-and-security)
- [Getting Started](#getting-started)
  - [Clone the Repository](#clone-the-repository)
  - [Install Dependencies](#install-dependencies)
- [Configuration Setup](#configuration-setup)
- [Development Environment](#development-environment)
- [Deployment Process](#deployment-process)
  - [Launch Hardhat Node](#launch-hardhat-node)
  - [Deploy Trading Contract](#deploy-trading-contract)
  - [Start the Trading Bot](#start-the-trading-bot)
  - [Trigger Arbitrage Simulation](#trigger-arbitrage-simulation)
- [Technical Details](#technical-details)
  - [Strategy Implementation](#strategy-implementation)
- [Bot Architecture and Flow](#bot-architecture-and-flow)
- [Mainnet Monitoring](#mainnet-monitoring)
- [Multi-Chain Adaptation](#multi-chain-adaptation)
- [Testing and Continuous Operation](#testing-and-continuous-operation)
- [Community](#community)
  - [Contributions](#contributions)
  - [Project Updates](#project-updates)
- [Support the Project](#support-the-project)
  - [Core Values](#core-values)
- [License](#license)

## What is Arbitrage?

Arbitrage is a sophisticated trading strategy that capitalizes on price discrepancies of identical assets across different markets. By simultaneously purchasing at lower prices and selling at higher prices, traders can secure risk-free profits while promoting market efficiency.

## Why Blockchain Arbitrage?

Blockchain arbitrage offers multiple advantages for developers:

- **Financial Opportunity**: Leverage price inefficiencies across decentralized exchanges
- **Skill Enhancement**: Develop advanced blockchain development expertise
- **Market Efficiency**: Contribute to price equilibrium across trading platforms
- **Strategy Integration**: Incorporate into broader algorithmic trading systems
- **Liquidity Enhancement**: Occasional improvement of market liquidity in emerging tokens

## Verification and Security

Every code modification undergoes rigorous verification and digital signing. This ensures the absolute integrity and authenticity of our codebase. We strongly advise against using any unverified modifications, as they may contain malicious elements.

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/earthskyorg/Dex-Arbitrage-Bot.git
```

### Install Dependencies

Ensure you have `node.js` and `npm` installed before running:

```bash
npm install
```

## Configuration Setup

Create a `.env` file with the following parameters:

```env
ARB_FOR=""             # Token0 address
ARB_AGAINST=""         # Token1 address
PRICE_DIFFERENCE=""    # Target price difference between DEXs
UNITS=""               # Price reporting units
GAS_LIMIT=""           # Maximum gas limit
GAS_PRICE=""           # Gwei price
```

Use addresses from trusted block explorers and tailor numerical values to the strategy you intend to test. Maintain strict privacy of your `.env` file and never share its contents.

## Development Environment

1. Configure your deployment environment by forking your target network for the Hardhat node. We recommend using a personal RPC URL for optimal reliability. Create Web3 API keys at [Infura](https://www.infura.io/).

2. Add the following to your `.env` file:

```env
API_KEY=""        # Your Web3 API key
PRIVATE_KEY=""    # Hardhat wallet private key
```

## Deployment Process

### Launch Hardhat Node

```bash
npx hardhat node
```

### Deploy Trading Contract

```bash
npx hardhat run --network localhost scripts/1_deploy.js
```

Copy the deployed contract address and update the `ARBITRAGE_ADDRESS` field in [config.json](./config.json).

### Start the Trading Bot

```bash
node bot.js
```

### Trigger Arbitrage Simulation

Since the forked blockchain state remains static, we must simulate price movement to trigger arbitrage:

```bash
npx hardhat run --network localhost scripts/2_manipulate.js
```

**Note**: After running this script, you may need to restart your Hardhat node and redeploy contracts for subsequent tests.

This simulation only affects your local blockchain, not the actual network. For tokens other than the provided example (LINK/WETH), you'll need to:
- Identify and use a relevant whale address from block explorers
- Adjust token manipulation amounts
- Modify profitability parameters in the `executeTrade` function

## Technical Details

The [Server](./helpers/server.js) script establishes and maintains a local server instance for the application.

### Strategy Implementation

The current implementation demonstrates a basic strategy using the **manipulate.js** script to alter Uniswap prices. After price manipulation, the system:
1. Fetches reserves from Uniswap and Sushiswap
2. Determines the lower LINK amount
3. Divides this amount by half for trade execution

This division approach is provided as an example and may not be optimal for all strategies.

## Bot Architecture and Flow

The runtime logic in `bot.js` follows a deterministic flow that keeps the bot highly observable:

- `main()`: Subscribes to Uniswap and Sushiswap swap events and orchestrates the remaining functions.
- `checkPrice()`: Fetches and logs price data from both venues, returning an actionable `priceDifference`.
- `determineDirection()`: Builds the router path and decides the buy/sell order when the price delta exceeds your threshold.
- `determineProfitability()`: Performs gas-aware profitability checks and short-circuits if the trade would not be net-positive.
- `executeTrade()`: Calls the deployed contract to perform the flash-loan-based trade and records a report before resuming monitoring.

Helper utilities inside `helpers/helpers.js` support token metadata lookups, reserve calculations, and quote estimations that feed the flow above.

## Mainnet Monitoring

To observe production liquidity without deploying locally:

1. In `.env`, replace the Hardhat testing private key with the private key of the wallet that should sign mainnet transactions.
2. In `config.json`, set `isLocal` to `false`. Toggle `isDeployed` to `false` if you only want to monitor opportunities without invoking a contract, or `true` if a contract address is configured and should execute trades.
3. Run `node bot.js` to listen for live swap events. The bot will stream opportunities yet will only execute trades when both `isDeployed` is `true` and profitability criteria are met.

## Multi-Chain Adaptation

When targeting another EVM-compatible chain:

1. Update token addresses in `.env` using the appropriate explorers (for example: [etherscan.io](https://etherscan.io/), [arbiscan.io](https://arbiscan.io/), [polygonscan.com](https://polygonscan.com/), [avascan.info](https://avascan.info/) and their respective testnets such as Sepolia, Mumbai, or Fuji).
2. Revisit strategy thresholds (`PRICE_DIFFERENCE`, `UNITS`, `GAS_LIMIT`, `GAS_PRICE`) to reflect the liquidity profile and fee market of the new chain.
3. Edit `config.json` to provide the router and factory addresses that correspond to the exchanges you intend to arbitrage. Cross-reference documentation from the venue (for example [Uniswap V2 forks](https://defillama.com/forks/Uniswap%20V2)).
4. Adjust `helpers/initialization.js` with a chain-specific WebSocket RPC URL, and update `hardhat.config.js` so the forked network URL matches the chain RPC you are targeting.
5. Review `contracts/Arbitrage.sol` and, if necessary, swap the flash-loan provider for one that is deployed on the target chain (Balancer currently supports Ethereum, Arbitrum, Optimism, Polygon, Gnosis, and Avalanche).
6. Re-test helper scripts and deployment steps, paying special attention to Uniswap math helpers such as `getReserves`, `getAmountsOut`, and `getAmountsIn` to ensure assumptions remain valid.

## Testing and Continuous Operation

- Unit and integration scenarios can be executed locally with:

```bash
npx hardhat test
```

- Start the live bot with:

```bash
node bot.js
```

Once the bot is running, allow the market to trigger swap events—manual token manipulation will not be detected until a real event fires. The `determineProfitability` function is wrapped in try/catch logic so that math or RPC issues log gracefully without interrupting the websocket listeners.

## Community

### Contributions

We welcome contributions to enhance this project. If you identify bugs, have feature suggestions, or wish to improve functionality, please open an issue or submit a pull request.

### Project Updates

We continuously monitor the evolving DeFi ecosystem and will update this project to align with emerging developments and industry best practices.

## Support the Project

### Core Values

Our project operates on principles of open source and privacy. We maintain no social media presence, conduct no marketing activities, and receive no compensation for our GitHub contributions. We have no affiliations with other projects.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
