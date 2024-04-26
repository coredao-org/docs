---
description: Connecting to Core Testnet via MetaMask
---

# Connect to Core Testnet

In this guide, we'll connect to Core Testnet and get funded with tCORE from Core's Testnet Faucet. For more information about connecting to Core Mainnet, refer to [Add MetaMask to Core Mainnet](https://medium.com/@core\_dao/add-core-to-metamask-7b1dd90041ce).

## MetaMask Wallet Setup

We recommend using the MetaMask web wallet to connect to Core Testnet. You'll need to install MetaMask and set up your account before you can use it to connect to Core Testnet. You can find MetaMask setup instructions [here](https://geekflare.com/beginners-guide-to-metamask/).

## Adding Core Testnet to MetaMask

Once you've set up your MetaMask wallet, you can use it to connect to Core Testnet by adding Core Testnet's chain details in MetaMask's network settings. To do so, navigate to **Settings->Networks** in MetaMask and click the **Add Network** button. Input the following information:

* Network Name: Core Chain TestNet
* New RPC URL: [https://rpc.test.btcs.network](https://rpc.test.btcs.network/)
* Chain ID: 1115
* Currency Symbol: tCORE
* Block Explorer URL: [https://scan.test.btcs.network](https://scan.test.btcs.network/)

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

If you using the Chrome MetaMask extension, you can use a quick shortcut to add Core Testnet to Metamask. Visit [Testnet Core Scan](https://scan.test.btcs.network), scroll down to the bottom, and select the **Add Core Network** button. This will open a pop-up with all necessary network information loaded - you just need to confirm and approve.

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

## Rebrand Updates

_Satoshi Chain/BTCs_ have been rebranded to _Core Chain/CORE_, respectively. If you added the Testnet before the rebranding, you'll need to update _Network Name_ and _Currency Symbol_ in the MetaMask settings manually; otherwise, you won't be able to use the current version of Core Testnet. Go to **Settings->Network**, and select Core Testnet to open up the network settings. Update _Network Name_ and _Currency Symbol_ to the values stated in the above sections and click the **Save** button. You're good to go!

## Testnet Facuet: tCORE Account Funding

To help users get started and allow developers to quickly prototype their Dapps, Core has released the public[ Core Testnet Faucet](https://scan.test.btcs.network/faucet) to distribute tCORE. Simply navigate to the faucet and fill in your Core Testnet address, complete the CAPTCHA, and click the Get tCORE button. Each address can request one tCORE per day. If you've run out of tCORE and the faucet won't send you more because of the one tCORE daily limit, come back tomorrow and try again!

**tCORE is only used for testing and does not have any value. Do not use it for real financial transactions.**

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

After requesting tCORE from the faucet, you'll see your MetaMask balance increase.

![](<../../.gitbook/assets/image (17).png>)![](<../../.gitbook/assets/image (29).png>)

## Sending tCORE

You can use MetaMask to send tCORE to other addresses, as seen below:

![](<../../.gitbook/assets/image (28).png>)![](<../../.gitbook/assets/image (9).png>)

## Core Scan Blockchain Explorer

You can use the [Testnet Core Scan blockchain explorer](https://scan.test.btcs.network/) to find and review transactions. Copy your account's address from MetaMask and search for it on the explorer. In the picture below, we can see two transactions related to our account:

1. The one in which we fetched tCORE from Faucet;
2. The one in which we sent tCORE to another address;

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
