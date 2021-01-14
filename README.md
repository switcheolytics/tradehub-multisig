# Switcheo Tradehub (Cosmos) Multisig Wallet

When collaborating with others on a decentralized network it can be difficult to delegate funds without a trusted and secure way to share responsibility of asset managment. In the absence of this it is often necessary to find a trusted "escrow" account that is dedicated to allocating funds when and where it is needed.

Blockchain technology has allowed for greater innovation in this space to ensure a quorum of users have a say over asset managment. This comes in the form of mutlisig wallets that cryptographically require M-of-N signatures to interact with a wallet.

## SDF Funding or DAO's Require Multisig Wallets

This has become an issue recently with the SDF funding and being able to share an address that is shared amongst multiple participants instead of relying on an escrow. Ideally these wallets would be shared by 3, 5, or 7 wallet holders and require a quorum of signatures to release funds based on deliverables or to cover expenses.

Cosmos has a multisig option available natively and docs can be found here: https://godoc.org/github.com/cosmos/cosmos-sdk/crypto/keys/multisig

The steps to create a cosmos multisig wallet are explained and can be found at the following link: https://github.com/cosmos/gaia/blob/81d52750f20eeea61f7c226fe45e013e124e932e/docs/resources/gaiad.md#multisig-transactions

## Tradehub Multisig Process

The process and explaination given from Cosmos is a good start but it isn't entirely clear how to do this without requiring each of the private keys involved. This doc is intended to make this process clear.

Example: Create a wallet that is a 2-of-3 multisig wallet, which means if 2 of the 3 signatures are present, the blockchain will accept the transaction and process it on their behalf. It is possible to configure this as a 3-of-3 multisig wallet as well, but that is up to the user.

1. Generate a Tradehub wallet. If you already have one you can skip to step 2.

```
switcheocli keys add alice

Enter keyring passphrase:
Re-enter keyring passphrase:

- name: alice
  type: local
  address: tswth1j7rcm00xr8jzcn3thx5rfdxhazfu0a5r6hk9w2
  pubkey: tswthpub1addwnpepqdrc6v0zfns436r2mdscnr6dvsw4gk24aqwhuvptuenl0qdsswl95r2rzdw
  mnemonic: ""
  threshold: 0
  pubkeys: []


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

jump believe tooth length baby fame arrive vote buyer okay cream repair pave fresh couple hole old chunk minimum cigar change filter need entry
```

2. Find your Public Key and share/collect it with your multisig parties.

From the example above the public key is as follows:

```
pubkey: tswthpub1addwnpepqdrc6v0zfns436r2mdscnr6dvsw4gk24aqwhuvptuenl0qdsswl95r2rzdw
```

3. Import each Public Key from your other mutlisig participants.

In this example we will have 3 parties involved in the multisig wallet, so we will import 2 public keys that have been shared from our colleagues.

```
switcheocli keys add bob --pubkey tswthpub1addwnpepqw7ges8a4y0y23kjg6fsl4u6mddq8ewdcmhghjldt9ucj0f686d2xaxn22h
switcheocli keys add charlie --pubkey tswthpub1addwnpepqvk48s2gjpchwyht8qz5s0p9vfv4c8gf2ej9d2jjgrell3hhe2ryzqnxrrn
```

4. Create Multisig Wallet from Public Keys

To restate the situation and explain the parameters here, we are creating a multisig wallet with 3 potential signers and requiring (threshold) that at least 2 sign the transaction before it can be valid on the Tradehub blockchain.

```
switcheocli keys add abcmultisig --multisig alice,bob,charlie --multisig-threshold 2
switcheocli keys show --address abcmultisig
tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l
```

**NOTE** - It is not clear to me that this is actually working as intended b/c the __threshold__ is reporting 0.

```
switcheocli keys show abcmultisig --multisig-threshold 2

Enter keyring passphrase:
- name: abcmultisig
  type: multi
  address: tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l
  pubkey: tswthpub1ytql0csgqgfzd666axrjzqed20q53yr3wufwkwq9fq7z2cjetswsj4ny2649ys8nllr00j5xgyfzd666axrjzq6835c7yn8ptr5x4kmp3x856eqa23v4t6qa0cczhen877qmpqa7tgfzd666axrjzqau3nq0m2g7g4rdy35nplte4k66q0jum3hw30976kte3y7n50565v5wuvdt
  mnemonic: ""
  threshold: 0
  pubkeys: []
```

Multisig JSON

```
switcheocli keys show abcmultisig -o json

Enter keyring passphrase:
{"name":"abcmultisig","type":"multi","address":"tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l","pubkey":"tswthpub1ytql0csgqgfzd666axrjzqed20q53yr3wufwkwq9fq7z2cjetswsj4ny2649ys8nllr00j5xgyfzd666axrjzq6835c7yn8ptr5x4kmp3x856eqa23v4t6qa0cczhen877qmpqa7tgfzd666axrjzqau3nq0m2g7g4rdy35nplte4k66q0jum3hw30976kte3y7n50565v5wuvdt"}
```

5. Fund the Account

On the testnet you can submit the wallet address generated above to the Telegram bot and it will send you funds within a couple of minutes.

On the mainnet you will need real funds. I highly suggest you walk through this process a couple of times to ensure this is functioning and to your liking before doing this on the mainnet.

6. Check Account Balance

```
switcheocli query account <Account Address>
switcheocli query account tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l
```

7. Generate Transaction

The resulting `unsignedTx.json` file will need to be shared with the other multisig participants to be signed in the next step.

```
switcheocli tx send \
  --fees 100000000swth \
  --from <Keychain Account Name> \
  --generate-only <From (Multisig) Account Address> \
  <To Account Address> <Amount><token> > unsignedTx.json


switcheocli tx send \
  --fees 100000000swth \
  --from abcmultisig \
  --generate-only tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l \
  tswth1j7rcm00xr8jzcn3thx5rfdxhazfu0a5r6hk9w2 250000000swth > unsignedTx.json
```

8. Sign Transaction with Each Key

After distributing the `unsignedTx.json` file with the other multisig participants, each will individually need to sign this transaction with his or her private key.

```
switcheocli tx sign \
  unsignedTx.json \
  --multisig <Multisig Wallet Address> \
  --from <Keyring Account Name> \
  --output-document <filename>

switcheocli tx sign \
  unsignedTx.json \
  --multisig tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l \
  --from alice \
  --output-document aliceSignature.json

switcheocli tx sign \
  unsignedTx.json \
  --multisig tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l \
  --from bob \
  --output-document bobSignature.json

switcheocli tx sign \
  unsignedTx.json \
  --multisig tswth1h5mgyje2aqfkrphkg6a3lu2xd8q785mptuvt8l \
  --from charlie \
  --output-document charlieSignature.json
```

9. Compose Multisig Transaction

At this point the transaction has been signed by everyone (meeting the minimum of 2) all of which needs to be combined to make the final signed transaction that consists of all signatures as part of the multisig collective.

```
switcheocli tx multisign \
  unsignedTx.json \
  abcmultisig \
  aliceSignature.json bobSignature.json charlieSignature.json > signedTx.json

Enter keyring passphrase:
root@054da71c3eb7:/app# switcheocli tx broadcast signedTx.json
height: 0
txhash: 4A2693EFAF6A6BC4796A902C424F72A353222B291A10537875A30203719F9E96
codespace: ""
code: 0
data: ""
rawlog: '[]'
logs: []
info: ""
gaswanted: 0
gasused: 0
tx: null
timestamp: ""
```

10. Broadcast Multisig Transaction

The `signedTx.json` file has the information necessary to be sent to the blockchain successfully, all that is left is broadcasting it to the network and getting it included into a block.

```
switcheocli tx broadcast signedTx.json
```

This will generate `txhash` that you can then query the progress of via the CLI or the UI of Tradehub. At this point the transaction has been approved and funds have been moved according to the parameters set. All of this done trustlessly and fairly.