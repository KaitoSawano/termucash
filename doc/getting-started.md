## Getting started

This tutorial will help you to go through the basics to use Termucash Core after you completed the [installation instructions](/INSTALL.md). You now have `termucashd` or `termucash-qt` executables available to run a node, and `termucash-cli`/`termucash-tx` tools to help you transact TERM.

> **Note:** For simplicity, this guide assumes that executables can be found under the `PATH` environment variable.
If needed, you can specify their location by typing `PATH=$PATH:/path/to/executables`, or prepend the full path to the command like:
> ```console
> aldianokto:~$ /path/to/termucash-cli [arguments ...]
> ```

### Table of contents

1. [Starting a termucash node](#starting-a-termucash-node)
2. [Introduction to the JSON-RPC API](#introduction-to-the-json-rpc-api)
    * [Creating a wallet](#creating-a-wallet)
    * [Verifying your balance](#verifying-your-balance)
    * [Sending transactions](#sending-transactions)
    * [Inspecting blocks and transactions](#inspecting-blocks-and-transactions)
3. [Node configuration](#node-configuration)
    * [Mainnet, testnet and regtest](#mainnet-testnet-and-regtest)
    * [Data directory](#data-directory)
    * [RPC credentials](#rpc-credentials)
    * [Ports](#ports)
    * [Memory](#memory)
    * [Troubleshooting](#troubleshooting)

## Starting a Termucash node

To start your node, you can run a headless server using `termucashd`:
```console
aldianokto:~$ termucashd -daemon
```

Or you can use the Graphical User Interface (GUI), `termucash-qt`:
```console
aldianokto:~$ termucash-qt
```

Detailed logging is recorded in `debug.log`, located in the [data directory](#data-directory).
*Use `-help` to see all available options for each executable.*

Your node is now running and starts with a *synchronization process* that downloads the entire blockchain from other nodes. This operation will take many hours to complete, but you are now part of the Termucash network!

> **Note:** The rest of this guide assumes the use of a headless node. The RPC server is not exposed with `termucash-qt` until you activate the `-server` option as a startup argument, but inside the GUI application, you can use all the commands explored below (without `termucash-cli`) by going to `Help -> Debug window` and inside the popup window selecting the tab `Console`.

## Introduction to the JSON-RPC API

Termucash Core exposes a JSON-RPC interface that allows you to request information about the network, blockchain and individual transactions, send transactions to the networks and manage your wallet.

The Termucash Core installation provides the `termucash-cli` tool to interact with the JSON-RPC from the command line, and the interface is exposed over HTTP on port `8598`, so that other tools and libraries can interact with it.

To have an overview of the available commands, use the `help` command:

```console
#List all commands
aldianokto:~$ termucash-cli help

#Get help for a specific command
aldianokto:~$ termucash-cli help COMMAND
```

Some commands are different, but it's possible to use the [bitcoin RPC API documentation](https://developer.bitcoin.org/reference/rpc/).

### Creating a wallet

To receive TERM, you need an address that is securely derived from a private key through a series of automatic, cryptographic operations. The *address* can be shared with anyone to receive TERM, but the *private key* is sensitive information that allows anyone that knows it to spend the TERM on the associated address.

By default, the Termucash Core software will automatically create an address for you and securely store the private key in the wallet file.

You can list wallet addresses using `getaddressesbyaccount`:

```console
aldianokto:~$ termucash-cli getaddressesbyaccount ""
[
  "TCheDTR8xAFrNTTbxWZtdgDFk9bkAyLhaj"
]
```

Using `getnewaddress` will generate a new wallet address:
```console
aldianokto:~$ termucash-cli getnewaddress
TRZQJSKz9hfBfxpq92a8wrHLdJp64sY7gr
```

Private keys are stored in the `wallet.dat` file. You can use `backupwallet` to save a copy:

```console
aldianokto:~$ termucash-cli backupwallet /path/of/wallet/backup
```

**Tip:** Termucash addresses start with the letter `T`.

You now have two wallet addresses to share with other people to receive TERM! Consider avoiding [address reuse](https://en.bitcoin.it/wiki/Address_reuse) for anonymity and security reasons.

### Verifying your balance

The total balance of all addresses held in your wallet can be found with the `getbalance` command.

```console
#Syntax
aldianokto:~$ termucash-cli getbalance "*" minconf
```

`minconf` stands for minimum confirmations.
For example, to see current balance with transaction having at least 5 confirmations:

```console
aldianokto:~$ termucash-cli getbalance "*" 5
51841.03168688
```

### Sending transactions

Termucash implements the [Unspent Transaction Output (UTXO)](https://en.wikipedia.org/wiki/Unspent_transaction_output) model to track which amounts of coin belong to an address. Owning TERM means that you know the private key(s) to addresses that are associated with unspent outputs. To spend them, you have to compose a new transaction that spends the value from currently unspent outputs to new outputs.

##### sendtoaddress

It's possible to use a single command to create, sign and send a transaction :
```console
#Syntax
aldianokto:~$ termucash-cli sendtoaddress address amount

#Example
aldianokto:~$ termucash-cli sendtoaddress TFDmKY8hCUqUYkgTkALWnu7ryLde1hefRM 5
```

So much spending power !

Alternatively, four commands are needed to manually create a transaction: `listunspent`, `createrawtransaction`, `signrawtransaction` and `sendrawtransaction`.

##### listunspent

This displays a list of UTXOs associated to addresses kept in the wallet.

```console
#Syntax
aldianokto:~$ termucash-cli listunspent minconf maxconf '["address", ...]'

#Example
aldianokto:~$ termucash-cli listunspent 1 9999999 '["TFDmKY8hCUqUYkgTkALWnu7ryLde1hefRM"]'
[
  {
    "txid": 296a44526fa7feeee5daa55829929decec4210435882db5b5dbda8d3feefa3fb",
    "vout": 0,
    "address": "TFDmKY8hCUqUYkgTkALWnu7ryLde1hefRM",
    "account": "",
    "scriptPubKey": "22e914cdb14e11f5447f0fb077e4c5662063df118992df48es",
    "amount": 50.00000000,
    "confirmations": 590,
    "spendable": true,
    "solvable": true
  }
]
```

The `minconf` and `maxconf` parameters filter the minimum and maximum number of [confirmations](https://www.pcmag.com/encyclopedia/term/bitcoin-confirmation) of the UTXO returned.

> **Note:** The example address starts with `n` instead of `T`, because it uses [testnet](#mainnet-testnet-and-regtest).

##### createrawtransaction

You can now build a new transaction using the available UTXOs from above.

```console
#Syntax
aldianokto:~$ utxos_to_use='
  [
    {
      "txid": "id",
      "vout": n
    },
    ...
  ]'
aldianokto:~$ termucash-cli createrawtransaction "$utxos_to_use" '{"address":amount, ...}'

#Example
aldianokto:~$ utxos_to_use='
[
  {
    "txid": "b869566603452e6446d0002db02cf998ab693dd5b22661556269536def8aa2444",
    "vout": 0
  }
]'
aldianokto:~$ termucash-cli createrawtransaction "$utxos_to_use" '{"TFDmKY8hCUqUYkgTkALWnu7ryLde1hefRM":69, "TRZQJSKz9hfBfxpq92a8wrHLdJp64sY7gr": 30.999}'
0100001101332dda9bf03656261611249f588d93b68a86cde2db12dc46642ed506664d69878800000000ffffffff0200a5459b010007401976a91418a894e422de6564c464gb07573babd579243161188ac60b8c4b8055531301976a91488977da37560e353552e14e36992991a4c27266c88ac00000100
```

You can combine multiple UTXO and send it to multiple recipients by extending the `utxos_to_use` and recipient JSON structures.

> **Tip:** The transaction returned is encoded in hexadecimal encoding. You can use `termucash-cli decoderawtransaction` or `termucash-tx -json` to convert the content to JSON format.

##### signrawtransaction

Before sending a transaction, it must be signed by the private key that the address was derived from. Termucash Core will automatically use the correct private key when spending UTXO known to the wallet.

```console
#Syntax
aldianokto:~$ termucash-cli signrawtransaction encoded_transaction

#Example
aldianokto:~$ termucash-cli signrawtransaction "01000000013324faf8f03695261611669f5bdd93b68a86cf02db12dc46642ed50666ed69b80000000000ffffffff0200a5459b010000001976a91418a89ee36293f15c4db4c01173babd579243161188ac60b8c4b8000000001976a914c6977da37560e1432c2e14e16952981a4c272cac88ac00000000"
{
  "hex": "01000000013324faf8f03695261611669f5bdd93b68a86cf02db12dc46642ed50666ed69b8000000006a47304402200e1bf722d4335179de170f7c762755b463b3f7b8f026f30950f701bc834f0e6e022036295fdd5e607ca41c4e0e62e59d0911b607bfabedde2424665ffae13564d0e001210388f8f226d12eccd3ba93c1454ec4498b065cea96e29b918fbdb517872ebbf581ffffffff0200a5459b010000001976a91418a89ee36293f15c4db4c01173babd579243161188ac60b8c4b8000000001976a914c6977da37560e1432c2e14e16952981a4c272cac88ac00000000",
  "complete": true
}
```

##### sendrawtransaction

Finally, broadcast the transaction to the network so that it can be included in a block by miners:

```console
#Syntax
aldianokto:~$ termucash-cli sendrawtransaction signed_transaction

#Example
aldianokto:~$ termucash-cli sendrawtransaction 01000000013324faf8f03695261611669f5bdd93b68a86cf02db12dc46642ed50666ed69b8000000006a47304402200e1bf722d4335179de170f7c762755b463b3f7b8f026f30950f701bc834f0e6e022036295fdd5e607ca41c4e0e62e59d0911b607bfabedde2424665ffae13564d0e001210388f8f226d12eccd3ba93c1454ec4498b065cea96e29b918fbdb517872ebbf581ffffffff0200a5459b010000001976a91418a89ee36293f15c4db4c01173babd579243161188ac60b8c4b8000000001976a914c6977da37560e1432c2e14e16952981a4c272cac88ac00000000
b4fae2a43cb35f8016a547e9658e061f1da4a043efafecc42f739d46d95dee21
```

### Inspecting blocks and transactions

Blocks and transactions are identified by unique *hashes*.
Let's find the *[coinbase transaction](https://www.javatpoint.com/coinbase-transaction)* of block 69.

> **Note:** To be able to query transactions not related to your own wallet, like in this example, you will need to enable the `-txindex` option. This options requires the Termucash Core software to re-index the entire blockchain, and can take up to several hours.

First, request the information about block 69:

```console
#Find block hash from his height
aldianokto:~$ termucash-cli getblockhash 69
4682873e6ce38ab24ab513fc70a365a5f94c6af35959da3827d9e37a39fbc087

#Get block data
aldianokto:~$ termucash-cli getblock 3d2def20cd0d3aca148741ef469bda11647a3040d7669c82745d03c728706a8b
{
  "hash": "4682873e6ce38ab24ab513fc70a365a5f94c6af35959da3827d9e37a39fbc087",
  "confirmations": 1087,
  "strippedsize": 170,                                                                                 "size": 170,                                                                                         "weight": 680,                                                                                       "height": 69,                                                                                        "version": 2359300,                                                                                  "versionHex": "00240004",
  "merkleroot": "0cac86f8c30fbaaa3ac60409eb2b16600bfe6525068b04637e9798eefe7ca3d0",
  "tx": [
    "0cac86f8c30fbaaa3ac60409eb2b16600bfe6525068b04637e9798eefe7ca3d0"                                 ],                                                                                                   "time": 17812xx779,                                                                                  "mediantime": 17812xx315,                                                                            "nonce": 42026,                                                                                      "bits": "1e0ffff0",
  "difficulty": 0.000xxxxxx,
  "chainwork": "0000000000000000000000000000000000000000000000000000000004600460",
  "previousblockhash": "e5d55c1ab933ad621c2c25b1c429e409886777cf637cdbf6807f66b355f92c97",             "nextblockhash": "4d7deecb5f2d2149eaaefff9d0a12483b4c6702a2e6020bceb47d5b2e7b1ecc1"                }
```

The `tx` field contains a list of all transactions included in this block. Only one transaction exist in block 69, the coinbase transaction.

We can see the entire transaction by querying for its identifier:

```console
#Syntax
aldianokto:~$ termucash-cli getrawtransaction txid verbose

#Example
this is just a composition or example

aldianokto:~$ termucash-cli getrawtransaction 4682873e6ce38ab24ab513fc70a365a5f94c6af35959da3827d9e37a39fbc087 1
{
  "hex": "00000xxxxx",
  "txid": "0cac86f8c30fbaaa3ac60409eb2b16600bfe6525068b04637e9798eefe7ca3d0",
  "hash": "4682873e6ce38ab24ab513fc70a365a5f94c6af35959da3827d9e37a39fbc087",
  "size": 89,
  "vsize": 89,
  "version": 1,
  "locktime": 0,
  "vin": [
    {
      "coinbase": "01450105",
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 50.00000000,
      "n": 0,
      "scriptPubKey": {
        "asm": "cdc14c81f5997f0cb088e4c5662063df198752cd OP_CHECKSIG",
        "hex": "xxxxx",
        "reqSigs": 1,
        "type": "pubkey",
        "addresses": [
          "TUj962N9p9aNssnUCW9cVVhy3YmPWpZx7H"
        ]
      }
    }
  ],
  "blockhash": "4682873e6ce38ab24ab513fc70a365a5f94c6af35959da3827d9e37a39fbc087",
  "confirmations": 1087,
  "time": 1781231779,
  "blocktime": 1781227315
}
```

The `vout` structure will give you information about where the transaction output.

## Node configuration

There are many parameters that can be configured to tune your node to your liking. There are two ways to change the configuration.

Using `termucashd -help` will display all available configuration parameters that can be added as arguments:

**Command example :**
```console
aldianokto:~$ termucashd -daemon -paytxfee=0.00001 - sendfreetransactions=1 -maxconnections=150
```

Configuration can be persisted by creating a `termucash.conf` file. Create it in the directory defined with the `datadir` setting, `$HOME/.termucash` by default, or specify the file location with `-conf`.

**termucash.conf example :**
```
daemon=1
server=1
listen=1
paytxfee=0.00001
sendfreetransactions=1
maxconnections=150
```
You can see a more concrete example [here](/contrib/debian/examples/termucash.conf).

### Mainnet, testnet and regtest

When trying out new things, for example to test your application that interacts with the Termucash chain, it is recommended to not use the main Termucash network. Multiple networks are built-in for this purpose.

**Mainnet** : The main network where real transaction operate.  
**Testnet** : The test network, with peers.  
**Regtest** : The regression test network, to test with only local peers and create blocks on-demand.

When not specifying any network, *Mainnet* is the network used by default. To enable *testnet*, use the `termucashd -testnet`.

To enable *regtest*, use the `-regtest` option.

> **Tip:** Remember to specify the network when you want to use `termucash-cli`.

### Data directory

The data directory is the location where Termucash Core files are stored, including the wallet, log files and blocks. You can modify the location with the `-datadir` setting.

**Default location :**

Platform | Data directory path
---------|--------------------
Linux    | `$HOME/.termucash`
macOS    | `$HOME/Library/Application Support/Termucash`
Windows  | `%APPDATA%\Termucash`

You may need to specify `-datadir` also when using `termucash-cli`.

See the [full documentation on file system](files.md) for more information.

### RPC credentials

Authentication is required to interact with the RPC interface. When no credentials are provided, Termucash uses a [random cookie](https://bitcoin.org/en/release/v0.12.0#rpc-random-cookie-rpc-authentication) that gets generated when the software is launched. It's possible to define your own credentials using `rpcuser` and `rpcpassword` parameters.

### Ports

A node can expose 2 different ports: one port for the **Peer to Peer Network** (P2P) to communicate with other nodes, and a second port for access to the RPC API. By default, the ports are configured as follows:

| Function | mainnet | testnet | regtest |
| :------- | ------: | ------: | ------: |
| P2P      |   8599 |   12253 |   8433 |
| RPC      |   8598 |   12252 |   8432 |

To configure them use the `-port` and `-rpcport` parameters.

### Memory

Running Termucash Core can require a lot of memory, so in some situations it may be necessary to optimize its usage. You can find more information about reducing the memory footprint in the [related guide](reduce-memory.md).

### Troubleshooting

By default, Termucash Core keeps detailed logs in the `debug.log` file, located in the `datadir`. Alternatively, the `-printtoconsole` parameter displays the log interactively to the terminal instead.

To get more verbose log output, you can enable debug mode by using the `-debug=<topic>` parameter to increase logic for a specific topic, or use `-debug=all` to see detailed logs on all topics.
