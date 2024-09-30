# Personal notes

These were taken while following the tutorials in docs.substrate, in order.

## Build a blockchain

### Build a local blockchain

From https://docs.substrate.io/tutorials/build-a-blockchain/build-local-blockchain/

```bash
./target/release/solochain-template-node --dev

( cd ../substrate-front-end-template; yarn install && yarn start )
```

### Simulate a network

From https://docs.substrate.io/tutorials/build-a-blockchain/simulate-network/

```bash
./target/release/solochain-template-node purge-chain --base-path /tmp/alice --chain local

./target/release/solochain-template-node \
    --base-path /tmp/alice \
    --chain local \
    --alice \
    --port 30333 \
    --rpc-port 9945 \
    --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
    --validator
    # deprecated
#   --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \

./target/release/solochain-template-node purge-chain --base-path /tmp/bob --chain local

./target/release/solochain-template-node \
    --base-path /tmp/bob \
    --chain local \
    --bob \
    --port 30334 \
    --rpc-port 9946 \
    --node-key 0000000000000000000000000000000000000000000000000000000000000002 \
    --validator \
    --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp
```

### Add trusted nodes

From https://docs.substrate.io/tutorials/build-a-blockchain/add-trusted-nodes/

#### AURA/GRANDPA key generation

```bash
# For Alice
./target/release/solochain-template-node key generate --scheme Sr25519 --password-interactive
# Secret phrase
# payment upon cost remember theory demise father where column such shine kangaroo
# SS58 address (Sr25519)
# 5Cw2K79BH6Bqoepg1ZHkaEWfg1AyBmxf8kue6ztuaqKxMGFv
./target/release/solochain-template-node key inspect --password-interactive --scheme Ed25519 "payment upon cost remember theory demise father where column such shine kangaroo"
# SS58 address (Ed25519)
# 5FctcjjyxKRjM48ktvyNmC3J87QjJheAu6L7nTD9pYyzV5o1

# For Bob
./target/release/solochain-template-node key generate --scheme Sr25519 --password-interactive
# Secret phrase
# dial front limb very slush goddess sea pact spider version speed transfer
# SS58 address (Sr25519)
# 5EDCwiBnULowxZbkZxjDA4v7e7qyRub29i7FSXsHGMQBKpKq
./target/release/solochain-template-node key inspect --password-interactive --scheme Ed25519 "dial front limb very slush goddess sea pact spider version speed transfer"
# SS58 address (Ed25519)
# 5GYj3MQCTYUJKHteiLZgpE9CgUh2CatVEhEkG7B7NhvLcT1C
```

#### Chain specification modification

```bash
# This chapter appears to be out of order
./target/release/solochain-template-node build-spec --disable-default-bootnode --chain local > customSpec.json

# The line containing the entire runtime appears with `-n` greater than 11 
head customSpec.json

tail -n 78 customSpec.json
```

#### Chain specification generation

```bash
./target/release/solochain-template-node build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json
```

#### Add previously generated keys to keystore

A key must be generated for each node with the `key generate-node-key` subcommand.
Otherwise, this error (or another similar one, for Sr25519) will be raised:

```rust
Error::NetworkKeyNotFound("/tmp/node01/chains/local_testnet/network/secret_ed25519")
```

##### Alice

```bash
# Tutorial omits this for the network's bootstrapping node, it seems to have fallen out of date.
./target/release/solochain-template-node key generate-node-key \
    --base-path /tmp/node01 \
    --chain customSpecRaw.json
# 12D3KooWRnsx1JjZk41bBZswVfjbcD2zfkPYqsrYJJBaHWvTo4X5

./target/release/solochain-template-node key insert \
    --base-path /tmp/node01 \
    --chain customSpecRaw.json \
    --scheme Sr25519 \
    --suri "payment upon cost remember theory demise father where column such shine kangaroo" \
    --password-interactive \
    --key-type aura

./target/release/solochain-template-node key insert \
    --base-path /tmp/node01 \
    --chain customSpecRaw.json \
    --scheme Ed25519 \
    --suri "payment upon cost remember theory demise father where column such shine kangaroo" \
    --password-interactive \
    --key-type gran

ls /tmp/node02/chains/local_testnet/keystore
# There shoulb be 2 items shown

./target/release/solochain-template-node \
    --base-path /tmp/node01 \
    --chain ./customSpecRaw.json \
    --port 30333 \
    --rpc-port 9945 \
    --validator \
    --rpc-methods Unsafe \
    --name MyNode01 \
    --password-interactive
```

##### Bob

```bash
./target/release/solochain-template-node key generate-node-key \
    --base-path /tmp/node02 \
    --chain customSpecRaw.json

# 12D3KooWSmRyfPZrWhQMbgMoVXPT2P8pViiUCXwdcK6uJ1eFsQEc

./target/release/solochain-template-node key insert \
  --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "dial front limb very slush goddess sea pact spider version speed transfer" \
  --password-interactive \
  --key-type aura

./target/release/solochain-template-node key insert \
  --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "dial front limb very slush goddess sea pact spider version speed transfer" \
  --password-interactive \
  --key-type gran

ls /tmp/node02/chains/local_testnet/keystore
# There shoulb be 2 items shown 

./target/release/solochain-template-node \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --rpc-port 9946 \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWRnsx1JjZk41bBZswVfjbcD2zfkPYqsrYJJBaHWvTo4X5 \
  --password-interactive
```

### Authorize specific nodes

### Monitor node metrics

See `./prometheus.yml`.

### Upgrade a running network

## Build application logic

### Add a pallet to the runtime

### Specify the origin for a call

### Use macros in a custom pallet

### Add offchain workers

### Publish custom pallets

### Collectibles workshop

## Build a parachain

### Prepare a local relay chain

Saved `raw-local-chainspec-json` to `/tmp`.

From `./polkadot-sdk`, the commands run were

```bash
# polkadot is one lever deeper inside polkadot-sdk
../target/release/polkadot key generate-node-key \
    --base-path /tmp/relay/alice \
    --chain /tmp/raw-local-chainspec.json
# Our generated libp2p node ID for Alice is
# 12D3KooWDvtYmmv5Gek6WrYtgqv3qQvzZVex9ireQ5F4oztZpxmE

../target/release/polkadot \
    --alice \
    --validator \
    --base-path /tmp/relay/alice \
    --chain /tmp/raw-local-chainspec.json \
    --port 30333 \
    --rpc-port 9944
```

In order to avoid an error

```bash
🚨 Your system cannot securely run a validator.
Running validation of malicious PVF code has a higher risk of compromising this machine.
Secure mode is enabled only for Linux
and a full secure mode is enabled only for Linux x86-64.
You can ignore this error with the `--insecure-validator-i-know-what-i-do` command line argument if you understand and accept the risks of running insecurely. With this flag, security features are enabled on a best-effort basis, but not mandatory.
More information: https://wiki.polkadot.network/docs/maintain-guides-secure-validator#secure-validator-mode
```

the command needs to be changed to

```bash
../target/release/polkadot \
    --alice \
    --validator \
    --base-path /tmp/relay/alice \
    --chain /tmp/raw-local-chainspec.json \
    --port 30333 \
    --rpc-port 9944 \
    --insecure-validator-i-know-what-i-do
```

---

To start the second relay chain validator:

```bash
# don't forget the key generation step
../target/release/polkadot key generate-node-key \
    --base-path /tmp/relay/bob \
    --chain /tmp/raw-local-chainspec.json

# Our generated libp2p node ID for Bob is
# 12D3KooWDiViW7g2v2kpCobmAhq6CXsQBy7S7n1eWhsNfxEbHHzo

../target/release/polkadot \
    --bob \
    --validator \
    --base-path /tmp/relay/bob \
    --chain /tmp/raw-local-chainspec.json \
    --port 30334 \
    --rpc-port 9945 \
    --insecure-validator-i-know-what-i-do
```

The network wasn't producing blocks, so `--bootnodes` must be used when initialization Bob's node,
with Alice's information.

```bash
../target/release/polkadot \
    --bob \
    --validator \
    --base-path /tmp/relay/bob \
    --chain /tmp/raw-local-chainspec.json \
    --port 30334 \
    --rpc-port 9945 \
    --insecure-validator-i-know-what-i-do \
    --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWDvtYmmv5Gek6WrYtgqv3qQvzZVex9ireQ5F4oztZpxmE
```

---

After `CTRL^C` signaling the Alice node and mistakenly leaving Bob running, the following error
arose in both nodes, even after restarts:

`2024-09-27 18:16:48 Call to `DetermineUndisputedChain` failed error=DetermineUndisputedChainCanceled(Canceled)`

To reset each node's db, the provided `chain-spec` JSON had to be used:

```bash
../target/release/polkadot purge-chain \
    --base-path /tmp/relay/bob \
    --chain /tmp/raw-local-chainspec.json
../target/release/polkadot purge-chain \
    --base-path /tmp/relay/bob \
    --chain /tmp/raw-local-chainspec.json
```

### Connect a local parachain

Generate a plain-text chain specification using the parachain template's binary.

```bash
./target/release/parachain-template-node build-spec --disable-default-bootnode > plain-parachain-chainspec.json
```

```bash
./target/release/parachain-template-node build-spec --chain plain-parachain-chainspec.json --disable-default-bootnode --raw > raw-parachain-chainspec.json
```

