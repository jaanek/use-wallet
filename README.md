# 👛 useWallet()

[<img src="https://img.shields.io/npm/v/use-wallet" alt="" />](https://www.npmjs.com/package/use-wallet) [<img src="https://img.shields.io/bundlephobia/minzip/use-wallet" alt="" />](https://bundlephobia.com/result?p=use-wallet)

useWallet() allows dapp users to connect to the provider of their choice in a way that is as straightforward as possible. It provides a common data structure for any connected account, no matter what provider has been chosen by the user. It aims to provide some features that are often reimplemented by dapp developers: connecting to a wallet, keeping track of transactions.

## Features

- All-in-one solution to connect to Ethereum providers.
- Completely library agnostic (use Web3.js, ethers.js, …).
- Provides the current balance.
- Keeps track of the recent transactions.

### Opinionated?

Oh yes:

- React only.
- Web environment.
- Exports as ES Modules.
- Ethereum only (for now).
- Supports one network at a time.
- Embeds as many providers as possible.
- Every prop and parameter is optional.

### What useWallet() is not

- An Ethereum wallet implementation.
- A low-level, fully configurable connecting system for Ethereum providers (see [web3-react](https://github.com/NoahZinsmeister/web3-react) if you are after that).
- An general library to interact with Ethereum (ethers.js, Web3.js etc.).

## Usage

Add it to your project:

```console
yarn add use-wallet
```

Use it in your React app:

```jsx
// App.js

import React from 'react'
import { useWallet, UseWalletProvider } from 'use-wallet'

function App() {
  const wallet = useWallet()
  const blockNumber = wallet.getBlockNumber()

  return (
    <>
      <h1>Wallet</h1>
      {wallet.connected ? (
        <div>
          <div>Account: {wallet.account}</div>
          <div>Balance: {wallet.balance}</div>
          <button onClick={() => wallet.deactivate()}>disconnect</button>
        </div>
      ) : (
        <div>
          Connect:
          <button onClick={() => wallet.activate()}>MetaMask</button>
          <button onClick={() => wallet.activate('frame')}>Frame</button>
          <button onClick={() => wallet.activate('portis')}>Portis</button>
        </div>
      )}
    </>
  )
}

// Wrap everything in <UseWalletProvider />
export default () => (
  <UseWalletProvider
    chainId={1}
    connectors={{
      // This is how connectors get configured
      portis: { dAppId: 'my-dapp-id-123-xyz' },
    }}
  >
    <App />
  </UseWalletProvider>
)
```

## API

### &lt;UseWalletProvider />

This is the provider component. It should be placed above any component using `useWallet()`. Apart from `children`, it accepts two other props:

#### chainId

The [Chain ID](https://chainid.network/) supported by the connection. Defaults to 1.

#### connectors

The configuration of the different connectors. If a connector that requires a configuration gets used without, an error will be thrown.

- `injected`: no configuration needed.
- `frame`: no configuration needed.
- `fortmatic`: `{ apiKey }`
- `portis`: `{ dAppId }`
- `authereum`: no configuration needed.
- `squarelink`: `{ clientId, options }`
- `torus`: `{ initOptions, constructorOptions }`
- `walletlink`: `{ url, appName, appLogoUrl }`

See the [web3-react documentation](https://github.com/NoahZinsmeister/web3-react/tree/v6/docs) for more details.

### useWallet()

This is the hook to be used throughout the app.

It takes an optional object as a single param, containing the following:

- `pollBalanceInterval`: the interval used to poll the wallet balance. Defaults to 2000.
- `pollBlockNumberInterval`: the interval used to poll the block number. Defaults to 5000.

It returns an object representing the connected account (“wallet”), containing:

- `account`: the address of the account, or `null` when disconnected.
- `activate(connectorId)`: call this function with a connector ID to “connect” to a provider (see above for the connectors provided by default).
- `activating`: which provider is currently waiting to be activated. `null` otherwise.
- `balance`: the balance of the account, in wei.
- `getBlockNumber()`: this function returns the current block number. This is a function because the block number updates often, which could triggers as many extra renders. Making an explicit call to get the block number allows `useWallet()` to avoid these extra renders when the block number is not needed.
- `connected`: whether the account is connected or not (same as testing `account !== null`).
- `connectors`: the full list of connectors.
- `deactivate()`: call this function to “disconnect” from the current provider.
- `ethereum`: the connected [Ethereum provider](https://eips.ethereum.org/EIPS/eip-1193).
- `isContract`: whether or not the account is a contract.
- `networkName`: a human-readable name corresponding to the Chain ID.

## Special thanks

useWallet() is a built on
[web3-react](https://github.com/NoahZinsmeister/web3-react) and its connectors,
which are doing all the hard work internally.
