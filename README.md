# ERC-1363 Payable Token

[![NPM Package](https://img.shields.io/npm/v/erc-payable-token.svg?style=flat-square)](https://www.npmjs.org/package/erc-payable-token)
[![CI](https://github.com/vittominacori/erc1363-payable-token/actions/workflows/ci.yml/badge.svg)](https://github.com/vittominacori/erc1363-payable-token/actions/workflows/ci.yml)
[![Coverage Status](https://coveralls.io/repos/github/vittominacori/erc1363-payable-token/badge.svg?branch=master)](https://coveralls.io/github/vittominacori/erc1363-payable-token?branch=master)
[![MIT licensed](https://img.shields.io/github/license/vittominacori/erc1363-payable-token.svg)](https://github.com/vittominacori/erc1363-payable-token/blob/master/LICENSE)

ERC-1363 allows to implement an ERC-20 smart token. 

It means that we can add a callback after transferring or approving tokens to be executed.

This is an implementation of the [EIP-1363 Payable Token](https://eips.ethereum.org/EIPS/eip-1363) that defines a token interface for EIP-20 tokens that supports executing recipient contract code after `transfer` or `transferFrom`, or spender contract code after `approve` in a single transaction.

## Abstract
There is no way to execute any code on a receiver or spender contract after an EIP-20 `transfer`, `transferFrom` or `approve` so, to make an action, it is required to send another transaction.

This introduces complexity on UI development and friction on adoption because users must wait for the first transaction to be executed and then send the second one. They must also pay GAS twice.

This proposal aims to make tokens capable of performing actions more easily and working without the use of any other listener.
It allows to make a callback on a receiver or spender contract, after a transfer or an approval, in a single transaction.

There are many proposed uses of Ethereum smart contracts that can accept EIP-20 callbacks.

Examples could be

* to create a token payable crowdsale
* selling services for tokens
* paying invoices
* making subscriptions

For these reasons it was originally named **"Payable Token"**.

Anyway you can use it for specific utilities or for any other purposes who require the execution of a callback after a transfer or approval received.



## Install

```bash
npm install erc-payable-token
```

## Usage

```solidity
pragma solidity ^0.8.0;

import "erc-payable-token/contracts/token/ERC1363/ERC1363.sol";

contract MyToken is ERC1363 {

    constructor(string memory name, string memory symbol) ERC20(name, symbol) {
        // your stuff
    }

  // your stuff
}
```

## Code

This repo contains:

### IERC1363

[IERC1363.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/token/ERC1363/IERC1363.sol)

Interface for a Payable Token contract as defined in [EIP-1363 Payable Token](https://eips.ethereum.org/EIPS/eip-1363).

```solidity
interface IERC1363 is IERC20, IERC165 {
    function transferAndCall(address to, uint256 amount) external returns (bool);
    function transferAndCall(address to, uint256 amount, bytes calldata data) external returns (bool);
    function transferFromAndCall(address from, address to, uint256 amount) external returns (bool);
    function transferFromAndCall(address from, address to, uint256 amount, bytes calldata data) external returns (bool);
    function approveAndCall(address spender, uint256 amount) external returns (bool);
    function approveAndCall(address spender, uint256 amount, bytes calldata data) external returns (bool);
}
```

### ERC1363

[ERC1363.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/token/ERC1363/ERC1363.sol)

Implementation of an IERC1363 interface.

### IERC1363Receiver

[IERC1363Receiver.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/token/ERC1363/IERC1363Receiver.sol)

Interface for any contract that wants to support `transferAndCall` or `transferFromAndCall` from ERC1363 token contracts.

```solidity
interface IERC1363Receiver {
    function onTransferReceived(address spender, address sender, uint256 amount, bytes calldata data) external returns (bytes4);
}
```

### IERC1363Spender

[IERC1363Spender.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/token/ERC1363/IERC1363Spender.sol)

Interface for any contract that wants to support `approveAndCall` from ERC1363 token contracts.

```solidity
interface IERC1363Spender {
    function onApprovalReceived(address sender, uint256 amount, bytes calldata data) external returns (bytes4);
}
```

### ERC1363Payable

[ERC1363Payable.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/payment/ERC1363Payable.sol)

Implementation proposal of a contract that wants to accept ERC1363 payments. It intercepts what is the ERC1363 token desired for payments and throws if another is sent.

It emits a `TokensReceived` event to notify the transfer received by the contract.

It also implements a `transferReceived` function that can be overridden to make your stuffs within your contract after a `onTransferReceived`.

It emits a `TokensApproved` event to notify the approval received by the contract.

It also implements a `approvalReceived` function that can be overridden to make your stuffs within your contract after a `onApprovalReceived`.

### ERC1363PayableCrowdsale

[ERC1363PayableCrowdsale.sol](https://github.com/vittominacori/erc1363-payable-token/blob/master/contracts/examples/ERC1363PayableCrowdsale.sol)

As example: an Implementation of a classic token Crowdsale, but paid with ERC1363 tokens instead of ETH.


## Code Analysis

* [Control Flow](https://raw.githubusercontent.com/vittominacori/erc1363-payable-token/master/analysis/control-flow/ERC1363.png)
* [Inheritance Tree](https://raw.githubusercontent.com/vittominacori/erc1363-payable-token/master/analysis/inheritance-tree/ERC1363.png)
* [UML](https://raw.githubusercontent.com/vittominacori/erc1363-payable-token/master/analysis/uml/ERC1363.svg)

## Development

### Install dependencies

```bash
npm install
```

### Linter

Use Solhint

```bash
npm run lint:sol
```

Use ESLint

```bash
npm run lint:js
```

Use Eslint and fix

```bash
npm run lint:fix
```

### Usage (using Truffle)

Open the Truffle console

```bash
npm run truffle:console
```

#### Compile

```bash
npm run truffle:compile
```

#### Test

```bash
npm run truffle:test
```

### Usage (using Hardhat)

Open the Hardhat console

```bash
npm run hardhat:console
```

#### Compile

```bash
npm run hardhat:compile
```

#### Test

```bash
npm run hardhat:test
```

#### Code Coverage

```bash
npm run hardhat:coverage
```

## License

Code released under the [MIT License](https://github.com/vittominacori/erc1363-payable-token/blob/master/LICENSE).
