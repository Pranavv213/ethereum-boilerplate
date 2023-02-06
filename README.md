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
- [🏗 Frontend](#React JS, Moralis)
  - [`Provider`](#Provider)
  - [`Basic imports & setup`](#Basic-imports-&-setup)
  - [`Authentication`](#Authentication)
  - [`handleCreate`](#handleCreate)
  - [`addMessage`](#addMessage)
  - [`getContacts`](#getContacts)
  - [`getMessage`](#getMessage)
- [🏗 Backend](#Solidity, Router Cross-Talk Library)
  - [`Initiating the Contract`](#Initiating-the-Contract)
  - [`Creating state variables and the constructor`](#Creating-state-variables-and-the-constructor)
  - [`Creating a channel/ Sending a message to an address on destination chain`](#Creating-a-channel/Sending-a-message-to-an-address-on-destination-chain)
  - [`Handling a crosschain request`](#Handling-a-crosschain-request)
  - [`Deployments`](#Deployments)
  - [`Back to Frontend`](#Back-to-Frontend)
  
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

### `Basic imports & setup`

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

### `Authentication`

To ensure that the user has a Web3 enabled wallet connected, you can check the isWeb3Enabled value returned by the useMoralis hook. If it is false, you can prompt the user to connect their wallet by rendering a button that calls the enableWeb3 function.

![Authentication](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/Authentication.png?alt=media&token=8cbcf306-f736-46e4-a3ac-96ddbb74274a)


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
When the user clicks the "Connect Wallet" button, the enableWeb3 function will be called, which will prompt the user to connect their Web3 enabled wallet (e.g. MetaMask).

Once the user has connected their wallet, the isWeb3Enabled value will change to true, allowing you to proceed with interacting with the Ethereum blockchain through Moralis.

### `handleCreate`

In order to create a channel between two wallets , a NULL message must be sent from one wallet to the other. This can be achieved by calling pingDestination function of the Cross-Talk Library using the runContractFunction function provided by the useWeb3Contract hook.

![handleCreate](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/createHandle.png?alt=media&token=489d3998-23c1-4d47-b315-87c8365166e9)

The following code shows an example of how to use the runContractFunction function to create a channel between two wallets.

```sh
const handleCreate = async () => {
  const options = {
    abi: abi,
    contractAddress: contractAddress,
    functionName: "pingDestination",
    params: {
      chainId: "80001",
      destinationContractAddress: destContractAdd,
      user0: account,
      user1: channelReceiptAddress,
      message: "#NULL#",
    },
  };

  const result = await runContractFunction({ params: options });
  console.log(result, account);
};
```
The abi variable should contain the ABI (Application Binary Interface) of the source contract that is deployed on the Ethereum blockchain. The contractAddress variable should contain the address of the deployed source contract.

The pingDestination function is called with the following parameters:

chainId: The ID of the destination chain.
destinationContractAddress: The address of the destination contract.
user0: The address of the sender's wallet.
user1: The address of the receiver's wallet.
message: A NULL message to create the channel.
Once the runContractFunction function is called, a channel will be created between the two wallets.

### `addMessage`

To send a message through a previously created channel between two wallets, the addMessage function can be used. The function is similar to the handleCreate function that was used to create the channel, but with a text message passed to the pingDestination function instead of a NULL message.

![addMessage](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/addMessage.png?alt=media&token=2a4f3eab-63b3-4276-81e9-e8876d3fcb8c)

```sh
const addMessage = async () => {
  const options = {
    abi: abi,
    contractAddress: contractAddress,
    functionName: "pingDestination",
    params: {
      chainId: "80001",
      destinationContractAddress: destContractAdd,
      user0: account,
      user1: receiptAddress,
      message: msg,
    },
  };
  const result = await runContractFunction({ params: options });
  console.log(result, account);
};
```
The abi and contractAddress variables are the same as in the handleCreate function, and they should contain the ABI and address of the source contract deployed.
The pingDestination function is called with the following parameters:

chainId: The ID of the destination chain.
destinationContractAddress: The address of the destination contract.
user0: The address of the sender's wallet.
user1: The address of the receiver's wallet.
message: The text message to be sent through the channel.
Once the runContractFunction function is called, the text message will be sent through the channel between the two wallets.

### `getContacts`

The getContacts function is used to retrieve the contacts with whom a channel has been created. It calls the getContacts function defined in the deployed smart contract.The getContacts takes in just one parameter ,u0 which is the sender's wallet address.

![getContacts](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/getContacts1.png?alt=media&token=938a73e0-acd9-4d8b-8a37-1175559f9a6b)

```sh
  const getContact = async () => {
    const options3 = {
      abi: abi,
      contractAddress: destContractAdd,
      functionName: "getContacts",
      params: { u0: account },
    };
    let something = await runContractFunction({ params: options3 });
    
  }; 
```

### `getMessage`

The getMessages function retrieves the messages sent through a channel between two wallets. It calls the getMessages function defined in the deployed smart contract [which will be further discussed later in the readme]. It just takes in two parameter , u0 and u1 which are the wallet addresses of sender and receiver respectively.

![getMessage](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/getMessage.png?alt=media&token=bcbac1fb-0dff-44dd-9605-6c0feeab0c76)

```sh
const getMessages = async () => {
    const options3 = {
      chainId: chainId,
      abi: abi,
      contractAddress: destContractAdd,
      functionName: "getMessages",
      params: { u0: account, u1: receiptAddress },
    };
    let something = await runContractFunction({ params: options3 });
    console.log(something);
    setMessage(something);
  };
  ```
  
# 🏗 Backend
  
### `Initiating the Contract`

For initiating the smart contract named "CrossChat", the contract imports three external contracts to be used in the implementation of the "Initiating the Contract" contract.

The "ICrossTalkApplication.sol" and "Utils.sol" contracts are imported from the "evm-gateway-contract/contracts" folder.
The "CrossTalkUtils.sol" contract is imported from the "@routerprotocol/crosstalk-utils/contracts" folder.
The "CrossChat" contract implements the "ICrossTalkApplication" contract by inheriting from it. This means that the "CrossChat" contract must have all the functions and variables defined in the "ICrossTalkApplication" contract.
By importing and implementing these contracts, the "CrossChat" contract will have access to their functionality and will be compatible with other contracts that follow the same standards.

```sh
//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "evm-gateway-contract/contracts/IGateway.sol";
import "evm-gateway-contract/contracts/ICrossTalkApplication.sol";
import "evm-gateway-contract/contracts/Utils.sol";
```
### `Initiating the Contract`

Creating State Variables and the Constructor
In this section of the smart contract code, state variables and events are defined for the contract in the Solidity programming language. The code defines the following:

A public state variable "gatewayContract" of type "address" is created to store the address of the gateway contract. This contract will be responsible for routing messages to the Router Chain.

A public state variable "greeting" of type "string" is created. This variable will store a message that will be set on the destination chain when a "message" is received.

A public state variable "lastEventIdentifier" of type "uint64" is created. This variable will store the nonce for the cross-chain transaction generated by the Gateway contract. This will be used to verify the nonce when the acknowledgment is received from the destination chain.

A public state variable "destGasLimit" of type "uint64" is created. This variable indicates the amount of gas required to execute the function that will handle the cross-chain request on the destination chain. This value can be easily calculated using a gas estimator such as the "hardhat-gas-reporter" plugin.

A public state variable "ackGasLimit" of type "uint64" is created. This variable indicates the amount of gas required to execute the callback function that will handle the acknowledgment received from the destination chain. This value can be easily calculated using a gas estimator such as the "hardhat-gas-reporter" plugin.

A custom error "CustomError" is created, which can be used to throw custom errors.

An event "ExecutionStatus" is created with parameters "uint64 eventIdentifier" and "bool isSuccess". This event will be emitted when the acknowledgment is received and handled by the source chain.

An event "ReceivedSrcChainIdAndType" is created with parameters "uint64 chainType" and "string chainID". This event will be emitted when the acknowledgment is received and handled by the source chain.

A constructor is created with the parameters "address payable gatewayAddress", "uint64 _destGasLimit", and "uint64 _ackGasLimit". The constructor sets the values of the "gatewayContract", "destGasLimit", and "ackGasLimit" state variables using the provided parameters.

```sh
address public gatewayContract;
string public greeting;
uint64 public lastEventIdentifier;
uint64 public destGasLimit;
uint64 public ackGasLimit;

error CustomError(string message);
event ExecutionStatus(uint64 eventIdentifier, bool isSuccess);
event ReceivedSrcChainIdAndType(uint64 chainType, string chainID);

constructor(
	address payable gatewayAddress, 
	uint64 _destGasLimit, 
	uint64 _ackGasLimit
) {
  gatewayContract = gatewayAddress;
	destGasLimit = _destGasLimit;
	ackGasLimit = _ackGasLimit;
}
```

