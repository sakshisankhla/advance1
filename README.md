# AVAX-PROOF Module 1

Welcome to **AVAX-PROOF Module 1** by Metacrafters! This module will walk you through the process of creating and deploying a custom ERC20 token and a Vault contract on the Avalanche network using Remix IDE and MetaMask.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Setting Up Your EVM Subnet on Avalanche](#setting-up-your-evm-subnet-on-avalanche)
  - [Deploying Your EVM Subnet](#deploying-your-evm-subnet)
  - [Defining Your Native Currency](#defining-your-native-currency)
  - [Connecting Your Subnet to MetaMask](#connecting-your-subnet-to-metamask)
- [Deploying Smart Contracts](#deploying-smart-contracts)
  - [ERC20 Token Contract](#erc20-token-contract)
    - [Key Functions](#key-functions)
    - [Deployment Steps](#deployment-steps)
  - [Vault Contract](#vault-contract)
    - [Key Functions](#key-functions-1)
    - [Deployment Steps](#deployment-steps-1)
- [MetaMask Configuration](#metamask-configuration)
- [Testing Your Application](#testing-your-application)
- [License](#license)
- [Acknowledgements](#acknowledgements)

## Introduction

In **AVAX-PROOF Module 1**, you'll develop a custom ERC20 token and a Vault contract, deploy them on your Avalanche EVM subnet, and interact with them using Remix IDE and MetaMask. This setup lays the groundwork for building more sophisticated decentralized applications (dApps) and DeFi solutions.

## Prerequisites

Before you get started, make sure you have the following:

- **Operating System:** macOS, Linux, or Windows with Windows Subsystem for Linux (WSL) enabled.
- **Development Environment:** Access to [Remix IDE](https://remix.ethereum.org/) for writing and deploying smart contracts.
- **Wallet:** [MetaMask](https://metamask.io/) installed in your browser to manage accounts and interact with the Avalanche network.

## Setting Up Your EVM Subnet on Avalanche

Follow these instructions to set up your own EVM-compatible subnet on Avalanche:

### Deploying Your EVM Subnet

1. **Use Avalanche CLI:**
   - Utilize the Avalanche CLI to create and deploy a custom EVM subnet.
   - Refer to the [Avalanche Documentation](https://docs.avax.network/) for detailed guidance on deploying subnets.

### Defining Your Native Currency

2. **Create an ERC20 Token:**
   - Develop an ERC20 token that will function as the native currency within your subnet.
   - This token will serve as the in-game currency for your DeFi Kingdom-inspired project.

### Connecting Your Subnet to MetaMask

3. **Integrate with MetaMask:**
   - Connect your newly created EVM subnet to MetaMask to enable seamless transactions and contract interactions.
   - Ensure that MetaMask is correctly configured to communicate with your subnet.

## Deploying Smart Contracts

### ERC20 Token Contract

Start by deploying your custom ERC20 token. Below is the ERC20 contract tailored for this module:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "SAKTOKEN"; 
    string public symbol = "SSM"; 
    uint8 public decimals = 22; 

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        require(balanceOf[msg.sender] >= amount, "Insufficient balance");
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        require(allowance[sender][msg.sender] >= amount, "Allowance exceeded");
        require(balanceOf[sender] >= amount, "Insufficient balance");
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        require(balanceOf[msg.sender] >= amount, "Insufficient balance to burn");
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

#### Key Functions

- **`transfer`**
  ```solidity
  function transfer(address recipient, uint amount) external returns (bool);
  ```
  Transfers a specified number of tokens from the caller's address to the recipient's address. Ensures the sender has sufficient balance.

- **`approve`**
  ```solidity
  function approve(address spender, uint amount) external returns (bool);
  ```
  Allows the spender to withdraw up to a specified number of tokens from the caller's account.

- **`transferFrom`**
  ```solidity
  function transferFrom(address sender, address recipient, uint amount) external returns (bool);
  ```
  Enables a spender to transfer tokens from the sender's account to the recipient, given sufficient allowance.

- **`balanceOf`**
  ```solidity
  function balanceOf(address account) external view returns (uint);
  ```
  Retrieves the token balance of a specific address.

- **`allowance`**
  ```solidity
  function allowance(address owner, address spender) external view returns (uint);
  ```
  Returns the remaining number of tokens that the spender is allowed to withdraw from the owner's account.

- **`mint`**
  ```solidity
  function mint(uint amount) external;
  ```
  Allows the caller to create new tokens, increasing their balance and the total supply.

- **`burn`**
  ```solidity
  function burn(uint amount) external;
  ```
  Enables the caller to destroy a specified number of their tokens, reducing their balance and the total supply.

#### Deployment Steps

1. **Access Remix IDE:**
   - Open [Remix IDE](https://remix.ethereum.org/) in your browser.

2. **Create Contract File:**
   - Create a new file named `ERC20.sol` and paste the ERC20 contract code provided above.

3. **Compile the Contract:**
   - Navigate to the Solidity compiler in Remix.
   - Select compiler version `0.8.17` and compile the contract.

4. **Deploy the Contract:**
   - Switch to the "Deploy & Run Transactions" tab.
   - Ensure MetaMask is connected to your Avalanche EVM subnet.
   - Deploy the ERC20 contract.
   - You can adjust parameters such as `name`, `symbol`, and `decimals` during deployment as needed.

### Vault Contract

The Vault contract handles the deposits and withdrawals of your ERC20 tokens. Below is the Vault contract tailored for this module:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        require(balanceOf[_from] >= _shares, "Insufficient shares to burn");
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        require(token.transferFrom(msg.sender, address(this), _amount), "Token transfer failed");
    }

    function withdraw(uint _shares) external {
        require(balanceOf[msg.sender] >= _shares, "Insufficient shares to withdraw");
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        require(token.transfer(msg.sender, amount), "Token transfer failed");
    }
}
```

#### Key Functions

- **`deposit`**
  ```solidity
  function deposit(uint _amount) external;
  ```
  Allows users to deposit a specified amount of ERC20 tokens into the Vault. It calculates the number of shares to mint based on the current total supply and token balance, ensuring proportional ownership.

- **`withdraw`**
  ```solidity
  function withdraw(uint _shares) external;
  ```
  Enables users to withdraw tokens from the Vault by burning their shares. The amount of tokens returned is proportional to the shares being redeemed relative to the total supply.

- **`_mint`**
  ```solidity
  function _mint(address _to, uint _shares) private;
  ```
  Internal function to create shares for a user upon deposit.

- **`_burn`**
  ```solidity
  function _burn(address _from, uint _shares) private;
  ```
  Internal function to destroy shares when a user withdraws tokens.

#### Deployment Steps

1. **Access Remix IDE:**
   - Open [Remix IDE](https://remix.ethereum.org/) in your browser.

2. **Create Contract File:**
   - Create a new file named `Vault.sol` and paste the Vault contract code provided above.

3. **Compile the Contract:**
   - Navigate to the Solidity compiler in Remix.
   - Select compiler version `0.8.17` and compile the contract.

4. **Deploy the Contract:**
   - Switch to the "Deploy & Run Transactions" tab.
   - Ensure MetaMask is connected to your Avalanche EVM subnet.
   - Deploy the Vault contract by providing the ERC20 token's address (e.g., `SAKTOKEN`) as a constructor parameter.

## MetaMask Configuration

To interact seamlessly with your deployed contracts, configure MetaMask to connect to your custom EVM subnet on Avalanche.

### Network Details

- **Network Name:** myblockchain
- **New RPC URL:** `http://127.0.0.1:9650/ext/bc/myblockchain/rpc`
- **Chain ID:** `25011`
- **Currency Symbol:** `mdh`

### Steps to Add a Custom Network in MetaMask

1. **Open MetaMask:**
   - Click on the MetaMask extension in your browser.

2. **Access Network Settings:**
   - Click on the network dropdown at the top of MetaMask.

3. **Add New Network:**
   - Select **Add Network** from the dropdown menu.

4. **Enter Network Details:**
   - Fill in the fields with the network details provided above.

5. **Save the Network:**
   - Click **Save** to add the new network.

6. **Switch to Your Subnet:**
   - Ensure that MetaMask is now connected to the **myblockchain** network you just added.

## Testing Your Application

After deploying your smart contracts and configuring MetaMask, proceed to test the functionalities to ensure everything operates as expected.

### Interact via Remix IDE

- Use Remix's interface to call functions like `mint`, `burn`, `deposit`, and `withdraw` on your deployed contracts.
- For example, mint new `SAKTOKEN` to your account and verify that the balance updates accordingly.

### Simulate In-Game Activities

- **Minting:** Create new tokens to simulate earning in-game currency.
- **Depositing:** Deposit tokens into the Vault to manage your assets.
- **Withdrawing:** Withdraw tokens from the Vault to simulate spending or cashing out.

### Verify Transactions

- **MetaMask:** Check MetaMask for transaction confirmations and ensure that token balances update correctly.

### Ensure Proper Functionality

- **Minting:** Verify that only authorized accounts (typically the contract owner) can mint new tokens.
- **Burning:** Ensure that burning tokens reduces the total supply and the caller's balance.
- **Deposits & Withdrawals:** Confirm that depositing tokens increases your Vault shares and withdrawing tokens decreases your shares accordingly.

## License

This project is licensed under the [MIT License](LICENSE).

