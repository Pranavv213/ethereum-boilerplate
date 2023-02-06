# `Cross-Chat`

> Fully ready ReactJS Dapp for Cross-Chain Messaging using Router Cross-Talk

🚀DEMO: https://cross-chat-47b25.web.app/

This project is built with [Router CrossTalk](https://dev.routerprotocol.com/crosstalk-library/overview/introduction)

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/) 

![cross-chat](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/image.gif?alt=media&token=51706ade-52ea-4a41-9a73-c5885a039c91)

# ⭐️ `Star us`

If this repository helps you build cross-chain dapps faster and easier - please star this project, every star makes us very happy!

# 🤝 `Need help?`

If you need help or have other some questions - don't hesitate to write in our discord channel and we will check asap. [Discord link](https://discord.gg/xvx2pFu9). The best thing about this is the super active community ready to help at any time! We help each other.

# 🚀 `Quick Start`

📄 Clone or fork `cross-chat`:

```sh
git clone https://github.com/protocol-designer/cross-chat.git
```

💿 Install all dependencies:

```sh
cd cross-chat-main
npm install
```

🚴‍♂️ Run your App:

```sh
npm start
```
# 🧭 `Table of contents`
- [🚀 Quick Start](#-quick-start)
- [🧭 Table of contents](#-table-of-contents)
- [🏗 Frontend](#-React JS, Moralis)
  - [`Provider`]
  - [`Basic imports & setup`]
  - [`Authentication`]
  - [`handleCreate`]
  - [`addMessage`]
  - [`getContact`]
  - [`getMessages`]
- [🏗 Backend](#-Solidity, Router Cross-Talk Library)
  - [`<NFTBalances />`]
  - [`<ERC20Balances />`]
  - [`<ERC20Transfers />`]
  - [`<NFTTransfers />`]
  - [`<Transactions />`]
# 🏗 Frontend

### `Provider`

This project uses the Moralis library for interacting with the Ethereum blockchain. Moralis is a provider for the Web3.js library, which allows for communication with Ethereum nodes.

In the Index.js file, the React app has been wrapped with the MoralisProvider component provided by the Moralis library. This sets up the Moralis instance and makes it available to the rest of the app through the React context API.

For more information on Moralis, visit their official website at https://moralis.io/.

As an alternative, one could use the Ether.js or Web3.js libraries for similar functionality. The choice of Moralis in this project was made due to its ease of use and comprehensive documentation.

```sh
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <MoralisProvider 
  >
    <App />
  </MoralisProvider>
);
```

### `Basic imports & basic setup`

In order to interact with the blockchain using Moralis, you need to import the useMoralis and useWeb3Contract hooks from the react-moralis library

```sh
import { useMoralis, useWeb3Contract } from 'react-moralis';
```
Next, you can use the useMoralis hook to get information about the the user's account and chain ID.

```sh
const {
  isWeb3Enabled,
  enableWeb3,
  account,
  chainId: chainIdHex,
} = useMoralis();
```
The useWeb3Contract hook is used to run functions on smart contracts deployed on the blockchain.
```sh
const { runContractFunction } = useWeb3Contract();
```

### 'Authentication'

To ensure that the user has a Web3 enabled wallet connected, you can check the isWeb3Enabled value returned by the useMoralis hook. If it is false, you can prompt the user to connect their wallet by rendering a button that calls the enableWeb3 function.

```sh
if (!isWeb3Enabled) {
  return (
    <div>
      <h2>CrossChat</h2>
      <button
        onClick={async () => await enableWeb3()}
      >
        Connect Wallet
      </button>
    </div>
  );
}
```
