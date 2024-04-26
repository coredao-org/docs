---
description: Creating Your First dApp on Core Testnet
---

# Building a dApp on Core

Decentralized applications (dApps) use a blockchain or on-chain smart contracts to store and reference data, rather than relying on traditional centralized databases. A common, simple dApp structure generally consists of a React.js or Vue.js front-end using Web3.js or Ethers.js to interact with smart contracts deployed to an EVM-compatible blockchain.

In this tutorial, we'll develop a simple dApp using React.js and Ethers.js that stores data in a smart contract on the Core blockchain and displays it to users. The dApp's full code is available on GitHub in the[ dApp-tutorial](https://github.com/coredao-org/dapp-tutorial/tree/master) repository.

Note: this tutorial assumes that the reader has front-end development experience and is familiar with JavaScript, Node.js, and React.js.

## Run dApp Locally

1\. Clone the [dApp-tutorial](https://github.com/coredao-org/dapp-tutorial/tree/master) repository:

```
git clone https://github.com/coredao-org/dapp-tutorial.git
```

2\. Navigate into the project directory:

```
cd dapp-tutorial
```

3\. Install all the dependencies (node modules):

```
npm install
```

4\. Serve with hot reload at [http://localhost:5173](http://localhost:5173/):

```
npm run dev
```

## Key Implementations

The application's key blockchain logic is implemented in [App.tsx](https://github.com/coredao-org/dapp-tutorial/blob/master/src/components/App.tsx):

1. [App.tsx (Wallet)](https://github.com/coredao-org/dapp-tutorial/blob/master/src/components/App.tsx#L16): used to connect to MetaMask.
2. [App.tsx (Store)](https://github.com/coredao-org/dapp-tutorial/blob/master/src/components/App.tsx#L54): used to write data to a smart contract.
3. [App.tsx (Retrieve)](https://github.com/coredao-org/dapp-tutorial/blob/master/src/components/App.tsx#L83): used to read data from a smart contract.

We recommend using Remix and the MetaMask web wallet for the remainder of the tutorial. To connect MetaMask to Core Testnet, refer to our guide on[ using MetaMask on Core Testnet](https://docs.coredao.org/developer/develop-on-core/using-core-testnet/connect-to-core-testnet).

## Smart Contract Deployment

To interact with our smart contract, we'll first need to deploy the smart contract to Core Testnet. Please follow the steps in our [Remix deployment](https://docs.coredao.org/developer/develop-on-core/building-on-core/using-remix) and [HardHat deployment](https://docs.coredao.org/developer/develop-on-core/building-on-core/using-hardhat) tutorials to deploy the following `1_Storage.sol` contract to Core Testnet:

```javascript
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;

/**
 * @title Storage
 * @dev Store & retrieve value in a variable
 */
contract Storage {

    uint256 number;

    /**
     * @dev Store value in variable
     * @param num value to store
     */
    function store(uint256 num) public {
        number = num;
    }

    /**
     * @dev Return value 
     * @return value of 'number'
     */
    function retrieve() public view returns (uint256){
        return number;
    }
}
```

After the smart contract is successfully deployed, copy the address into[ Line 9 of App.tsx](https://github.com/coredao-org/dapp-tutorial/blob/master/src/components/App.tsx#L9). Additionally, we'll need the ABI metadata to interact with the contract from our dApp.

For simplicity, we can use the smart contract metadata from Remix located at `/default workspace/contracts/artifacts/Storage_metadata.json` on the Remix File Explorer. Create a new file called `Storage.json`, copy in the metadata, and save it to the `/src/contracts folder`.

## Test Locally Using MetaMask

1\. Make sure that your MetaMask wallet is correctly installed and switched to Core Testnet as described in our [Core Testnet user guide](https://docs.coredao.org/developer/develop-on-core/using-core-testnet/connect-to-core-testnet). You'll also need to connect your MetaMask wallet to the local site.

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

2\. Enter a number in the input field and click the **store** button to save it to the contract. A write action on the smart contract invokes the MetaMask wallet. Click the **Confirm** button to sign the transaction and wait for confirmation on the blockchain.

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

3\. After the transaction is confirmed on the blockchain, click the **retrieve** button to read the value from the smart contract. You will notice the value has been updated.

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

Congratulations, you've just interacted with your newly-deployed contract using your dApp's front end! You can build on the codebase by deploying and interacting with different contracts, and by adding new UI components to the website for your users.
