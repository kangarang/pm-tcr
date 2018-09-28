# Participation-Mined Token-Curated Registry
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fkangarang%2Fpm-tcr.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fkangarang%2Fpm-tcr?ref=badge_shield)


Participation-Mined Token-Curated Registry is a fork of the original [token-curated registry (TCR)](https://medium.com/@ilovebagels/token-curated-registries-1-0-61a232f8dac7) and loosely follows the specifications found in the original [Owner's Manual](https://github.com/skmgoldin/tcr/blob/master/owners_manual.md).

---

## Overview / key differences

PM-TCR has [epochs](<https://en.wikipedia.org/wiki/Epoch_(reference_date)>). The [Registry](./contracts/Registry.sol) contract has exclusive ownership of a [Bank](./contracts/Bank.sol) contract, with reserve tokens released on a regular schedule (once per epoch), effectively inflating the token's liquid supply without increasing the token's total supply. Majority faction voters effectively earn 'inflation rewards' for their curation participation per epoch.

#### Hardcoded storage values:

- `EPOCH_DURATION`: The time between 2 epochs; currently implemented as 30 days, or 2592000 seconds

- `INFLATION_DENOMINATOR`: Used to determine inflation rewards per epoch

- `BIRTH_DATE`: The Unix timestamp of the block the Bank contract was deployed

#### During challenge resolution:

- The epoch number is stored as: `challenge.epochNumber = (block.timestamp - BIRTH_DATE) / EPOCH_DURATION`

- The epoch inflation is stored as: `epoch.inflation = token.balanceOf(Bank) / INFLATION_DENOMINATOR`

- The total number of tokens used for voting by the majority faction voters is stored as: `epoch.tokens += totalWinningTokens`

#### During claimReward:

- The number of tokens a majority faction voter used for voting in a given challenge/epoch is stored as: `epoch.voterTokens[voter] += numTokens`

#### Claim inflation rewards:

In addition to claiming rewards during a challenge, after an epoch ends, a majority faction voter can execute `Bank.claimInflationRewards`, which will transfer to the voter an amount of inflation reward tokens proportional to their token weight during that epoch:

- `epochInflationVoterRewards = epoch.voterTokens[voter] / epoch.tokens * epoch.inflation`

---

## Getting started

Configuration for deployment and contract parameterization is located in the [conf](./conf) directory.

Install node and ethpm dependencies:

    npm install

Compile truffle contracts:

    npm run compile

Run truffle tests on `localhost:7545`:

    npm test

Run truffle tests with RPC logs:

    npm test gas

Run solidity-coverage:

    npm run coverage

---

## Deployment

Scripts are available in [package.json](./package.json).

Note: since [v1.1.0](https://github.com/skmgoldin/tcr/releases/tag/v1.1.0), only the factory contracts are deployed during `truffle migrate`.

This repo requires you have a mnemonic phrase exported as an environment variable called MNEMONIC, e.g. in your `.bash_profile`:

    export MNEMONIC='super entropic mnemonic ...'

You can use [https://iancoleman.io/bip39/](https://iancoleman.io/bip39/) to generate a mnemonic and derive its accounts.

To deploy to a local [Ganache](https://github.com/trufflesuite/ganache-cli) instance, your mnemonic must also be exposed to Ganache:

    ganache-cli -m $MNEMONIC

Deploy factory contracts to a network:

    npm run deploy-[network]

Spawn proxy contracts to a network using a deployed RegistryFactory:

    npm run deploy-proxies:[network]

---

## Packages

The repo consumes several EPM packages. `dll` and `attrstore` are libraries used in PLCRVoting's doubly-linked list abstraction. `tokens` provides an ERC20-comaptible token implementation. `plcr-revival` features batched executions for certain transactions. All EPM packages are installed automatically upon `npm install`.


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fkangarang%2Fpm-tcr.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fkangarang%2Fpm-tcr?ref=badge_large)