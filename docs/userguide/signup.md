---
sidebar_position: 1
---

# Registering to Yield+

This section shows how to sign up to Yield+.

> **_NOTE:_** DeFi dApps (projects) are referred to as *protocols* in the Yield+ smart contract code and API, so we will use that particular terminology in this section.

## Registering contracts using custom permissions

A smart contract can create custom permissions for its account, in addition to the default `owner` and `active` permissions. This allows the smart contract to have dedicated authorization keys that have less power that the keys assigned to the default permissions. To register your protocol's smart contracts in the Yield+ system, it is likely that you will want to have a custom permission set up on each that allows them to interact with the Yield+ core contract (`eosio.yield`) and nothing else.

The following example command creates a custom `yield` permission (you can call it whatever you want) for a contract account `myprotocol`, and assigns a public key to it (replace `<PUBLIC_KEY>` with a valid public key value).

```bash
cleos set account permission myprotocol yield <PUBLIC_KEY>
```

Next, the example command below configures the permission `yield` to allow the `myprotocol` contract to invoke any (`""`) actions on the `eosio.yield` contract.

```bash
cleos set action permission myprotocol eosio.yield "" yield
```

Once this is done, you will be able to use the public key you specified to sign authorizations on behalf of `myprotocol@yield`, where `myprotocol` is the name of your contract account, and `yield` is the name you gave to the custom permission. In the remainder of the command-line `cleos` examples in this guide, you would then replace the `-p myprotocol` authorization argument with `-p myprotocol@yield`, as you would not be selecting the default `active` permission to sign the action.

Setting up this custom permission will also be of use when registering through the Web3 portal. In that case, store the custom account permission's private key in the wallet software that you are going to use to register through the Web3 portal, and select the custom permission (e.g. `yield`) of your protocol's account when your wallet's prompt to sign the transaction appears.

> **_NOTE_:** The example above assigns a single key to control the custom permission. You can create a more complex authorization structure for your new permission, but how to do so is out of this guide's scope.

## Web3 registration process

If your protocol has only one smart contract, then you can use [the Yield+ Web3 portal](https://tokenyield.io/) to register your protocol (click on **Join Yield+** at the top of the page). In that case, just fill in your application using a web browser. You will be required to use your favourite wallet software to cryptographically sign your registration request, proving your ownership of the protocol's contract account.

Even if your protocol has multiple contracts, you still can do some of the registration work through the Web3 interface. Check out the [Using Web3 registration with multiple contracts](signup#using-web3-registration-with-multiple-contracts) section below.

> **_NOTE:_** Do not attempt to register your protocol using a personal account. If you do that, your application will be rejected. Refer to [Registering contracts using custom permissions](signup#registering-contracts-using-custom-permissions) for help on configuring a new key on your contract that only allows it to interact with the Yield+ core contract.

## Registering your protocol using `cleos`

If your protocol has more than one contract, then you need to interact with the `eosio.yield` on-chain contract directly. Below, we show how to do that using the `cleos` command-line wallet. You will need the authorization keys for all of your protocols' contracts to be already loaded in the wallet file of your local cleos installation.

### Step 1: send a `regprotocol` transaction

> **_NOTE:_** This step is what the [Web3 registration interface](signup#web3-registration-process) does for you.

First, you need to invoke the `regprotocol` action on the `eosio.yield` contract, which takes three arguments:

* `protocol`: the name of the protocol's main smart contract;
* `category`: the name of the type of the protocol;
* `metadata`: a list of key-value pairs which provide all of the necessary registration information for the protocol.

Here's an example (for readability, this example doesn't include all the required metadata keys):

```bash
cleos push action eosio.yield regprotocol '[myprotocol, mycategory, [{"key": "name", "value": "My Protocol"}, {"key": "website", "value": "https://myprotocol.com"}]]' -p myprotocol
```

In the above example, `myprotocol` is the account name of what you consider to be your protocol's main smart contract. It can be any one of your contracts; just keep in mind which one you have chosen to be the main one. The second argument to `regprotocol`, which is `mycategory`, has to be substituted with the name of the category of your protocol. And finally, the third argument is a list of `key` and `value` pairs which provides all of the remaining information about the DeFi project being registered.

To compose your own, valid `regprotocol` transaction, you will need the list of accepted protocol categories and metadata keys.

Here are the currently valid protocol categories:

| Category name     | Description                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------- |
| cdp               | Protocols that mint their own stablecoin using some collateral.                             |
| dexes             | Protocols where you can swap/trade cryptocurrency.                                          |
| lending           | Protocols that allow users to borrow and lend assets.                                       |
| staking           | Protocols that provide rewards or liquidity for staked assets.                              |
| yield             | Protocols that pay rewards for staking or liquidity provision on their platform.            |

And here follows a list of metadata keys. Required keys are in **bold**.

| Key name          | Value type  | Description                                                                   |
| ----------------- | ----------- | ----------------------------------------------------------------------------- |
| **name**          | string      | Name of the protocol (dApp, project).                                         |
| **description**   | text        | A description of the protocol.                                                |
| **website**       | url         | Project website URL.                                                          |
| **logo**          | ipfs        | IPFS hash of your project's logo.                                             |
| **recover**       | string      | [Recover+](https://www.eosrecover.com/) identifier for the project.           |
| token.code        | name        | Name of your project's token contract account.                                |
| token.symcode     | symcode     | Symcode (symbol and decimal precision) of your project's token (`[precision,symbol]`, e.g. `[4,EOS]`) |
| cmc               | url         | URL of your project's [CoinMarketCap](https://coinmarketcap.com) listing.     |
| coingecko         | url         | URL of your project's [CoinGecko](https://coingecko.com) listing.             |
| dappradar         | url         | URL of your project's [DAppRadar](https://dappradar.com) listing.             |
| defillama         | url         | URL of your project's [DefiLlama](https://defillama.com) listing.             |
| twitter           | url         | Your project's [Twitter](https://twitter.com) account.                        |
| discord           | url         | Your project's [Discord](https://discord.com) link.                           |
| telegram          | url         | Your project's [Telegram](https://telegram.com) link.                         |
| github            | url         | Your project's [Github](https://github.com) repository.                       |

The definitive, up-to-date list of valid protocol categories and metadata keys is kept on-chain, in the public tables of the `admin.yield` smart contract, which is maintained by the Yield+ team. You can query the blockchain for the valid categories by following [this link](https://bloks.io/account/admin.yield?loadContract=true&tab=Tables&account=admin.yield&scope=admin.yield&limit=100&table=categories), and for the metadata keys by following [this link](https://bloks.io/account/admin.yield?loadContract=true&tab=Tables&account=admin.yield&scope=admin.yield&limit=100&table=metakeys).

### Step 2: send a `setcontracts` transaction

After `regprotocol` is invoked, the on-chain registation of your protocol will only cointain the main contract of your DeFi dApp.

Now, you need to invoke the `setcontracts` action on the `eosio.yield` contract to include all the other smart contracts of your protocol in your registration request.

This is best done immediately after you perform the first step above, before your registration has a chance to be reviewed by the Yield+ admins. If you take too long to invoke `setcontracts` after your have invoked `regprotocol`, your application might be flagged for processing by the Yield+ admins, at which points calling `setcontracts` will no longer work. If that happens, you will neet to contact the Yield+ admins to fix your registration request.

Here's an example `setcontracts` call:

```bash
cleos push action eosio.yield setcontracts '[myprotocol, [a.myprotocol, b.myprotocol]]' -p myprotocol -p a.myprotocol -p b.myprotocol
```

In the example above, `myprotocol` is the name of the main contract of the protocol being registered (which has been supplied in a previous `regprotocol` call) , and `a.myprotocol` and `b.myprotocol` are the names of all the other contracts of that protocol, which haven't been supplied at the time of the `regprotocol` call).

Note that you have to provide an authorization (i.e. cryptographic signature) for every contract account that you are adding to your registration application. In the example above, there are authorizations (signatures) provided by *all* of the DeFi dApp's contracts, which are mentioned in the `setcontracts` call: the `myprotocol`, `a.myprotocol` and `b.myprotocol` accounts.

### Optional registration actions

In addition to `regprotocol` and `setcontracts`, there are additional registration-related actions that a protocol authority can invoke and that can be helpful for the registration process:

* `setmetadata` can be used to replace the metadata section of your protocol's metadata record;
* `setmetakey` can be used to set or change individual keys of your protocol's metadata record;
* `setcategory` can be used to change the protocol category that you specified during the first registration action;
* `unregister` can be used to remove your protocol from Yield+;
* `setevm` can be used to include your protocol's EVM contract addresses, if any, to its registration request.

Please refer to the [Yield+ Rewards contract reference documentation](../contracts/eosio.yield.md) for information on how to call these actions on the `eosio.yield` contract.

## Using Web3 registration with multiple contracts

If your DeFi dApp has multiple contracts, you can, alternatively, [perform your project's initial registration using the Web3 interface](signup#web3-registration-process), which registers the main contract of your project through `regprotocol` for you, and then use the [`setcontracts` workflow through `cleos`](signup#step-2-send-a-setcontracts-transaction), to complement your initial Web3 registration step. If the protocol has EVM contracts, refer to the [Optional registration actions](signup#optional-registration-actions) section above.

## Next steps

The status of your protocol's acceptance or rejection can be queried directly from the public tables of the `eosio.yield` smart contract. Its [reference documentation](../contracts/eosio.yield.md) can be used by the protocol developers to query the blockchain about whether the project has been accepted or rejected.

If your project has been accepted, you will find it listed under the **DeFi dApps** tab on the [Yield+ Web3 portal](https://tokenyield.io/).

