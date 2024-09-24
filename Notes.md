# Personal notes

These were taken while following the tutorials in docs.substrate, in order.

## Simulate a network

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

./target/release/solochain-template-node build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json

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

###
```
----

1.

# Tutorial omits this for the network's bootstrapping node, it seems to have fallen out of date.
./target/release/solochain-template-node key generate-node-key \
    --base-path /tmp/node01 \
    --chain customSpecRaw.json

./target/release/solochain-template-node key insert \
  --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "chuckle hard laugh process legend knife pizza vivid shop wash chef engine" \
  --password-interactive \
  --key-type aura

./target/release/solochain-template-node key insert \
  --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "chuckle hard laugh process legend knife pizza vivid shop wash chef engine" \
  --password-interactive \
  --key-type gran

./target/release/solochain-template-node \
  --base-path /tmp/node01 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --rpc-port 9945 \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode01 \
  --password-interactive

2.

./target/release/solochain-template-node key generate-node-key \
    --base-path /tmp/node02 \
    --chain customSpecRaw.json

./target/release/solochain-template-node key insert \
  --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "little plate hotel slogan hire ship daughter neutral fat confirm hamster trial" \
  --password-interactive \
  --key-type aura

./target/release/solochain-template-node key insert \
  --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "little plate hotel slogan hire ship daughter neutral fat confirm hamster trial" \
  --password-interactive \
  --key-type gran

./target/release/solochain-template-node \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30334 \
  --rpc-port 9946 \
  --validator \
  --rpc-methods Unsafe \
  --name MyNode02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWALgfhpaAw1qwKm6xDnLTfLgNLMeQKTHcmUfmGeZr4FhP \
  --password-interactive