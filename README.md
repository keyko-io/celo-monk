# celo-monk

The objective of this repository is to use [Monk.io](http://monk.io) to run the attestation service and a validator for the [Celo network](https://celo.org/).

## Table of Contents

- [celo-monk](#celo-monk)
  - [Table of Contents](#table-of-contents)
  - [📝 Requirements](#📝-requirements)
  - [🏃 Quickstart](#🏃-quickstart)
  - [🔣 Celo Node variables](#🔣-celo-node-variables)
  - [🔣 Celo Proxy variables](#🔣-celo-proxy-variables)
  - [🔣 Celo Validator variables](#🔣-celo-validator-variables)
  - [🔣 Attestation Service variables](#🔣-attestation-service-variables)
  - [🏛️ License](#🏛️-license)

---

## 📝 Requirements

The initial requirement to run is that you need to connect with a monk cluster. For that you can follow the next guide about how to install monk and how to start working some basics commands. [Guide](https://docs.monk.io/docs/monk-in-10)

## 🏃 Quickstart

First you have to upload the templates to monk with the next command:

```bash
monk load MANIFEST
```

You may need to override the variables to setup your accounts and desired configuration. To do so, you can create a new yaml file, where you can setup the variables you want to override. For example:

```yaml
namespace: cool-validator

my-attestation-cluster:
  defines: process-group
  inherits: celo/attestation-service-cluster
  variables:
    # For lightest node container
    node-private-key: 6a80b8a2a704181539a4a139c0e295f68ae8c425d6cb0bb358e48814f03241ab
    account-address: 0x5B9aF8E980b58d8CbC5A4AeaC91fd3e5dFb7566b
    # For the attestation-service container
    attestation-signer-address: 0xbe8dfA32Ec7cA83Fd9130f86d910F1c0Cb5C9D74
    celo-validator-address: 0x5B9aF8E980b58d8CbC5A4AeaC91fd3e5dFb7566b
    network: baklava
    ...

my-validator-cluster:
  defines: process-group
  inherits: celo/validator-proxy-stack
  variables:
    node-private-key: 6a80b8a2a704181539a4a139c0e295f68ae8c425d6cb0bb358e48814f03241ab
    validator-signer-account: 0x5B9aF8E980b58d8CbC5A4AeaC91fd3e5dFb7566b
    proxy-private-key: c72cb55f50e4bf02374d1dbb1f8addbcea3d570676a6583deeee17624dd9f479
    proxy-address: 0xbe8dfA32Ec7cA83Fd9130f86d910F1c0Cb5C9D74
    ...
```

Once you have follow the requirements you can start to run the services in the following way:

```bash
monk load my-custom-yaml.yaml
monk run cool-validator/my-validator-cluster
```

## 🔣 Celo Node variables

There are different variables that it is possible to tune when you are running a celo node. If you want to know more about how Celo nodes work you can go to the [documentation](https://docs.celo.org/getting-started/choosing-a-network).

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **network** | Network were the node is syncing. Celo has currently rc1(mainnet) and alfajores and baklava (test networks). | string | "rc1"|
| **host** | Node host. | string | 0.0.0.0 |
| **p2p-port** | P2p connection port. ⚠️ Not change so far. There is dependency with the value of proxy. | int | 30303 |
| **http-port** | Http connection port. | int | 8545 |
| **ws-port** | Web socket connection port. | int | 8545 |
| **apis** | Currently node apis exposed. | string | "eth,net,web3,debug" |
| **http-options** | Node http options. | string | <- `--http --http.api "${apis}" --http.addr ${host} --http.port ${http-port} --http.rpcprefix / --http.corsdomain '*' --http.vhosts '*'` |
| **ws-options** | Node web socket options | string | <- `--ws --ws.api "${apis}" --ws.addr ${host} --ws.port ${ws-port} --ws.rpcprefix / --ws.origins '*'` |
| **extra-options** | Extra options. | string | "" |
| **docker-image** | Geth image from celo. | string | us.gcr.io/celo-org/geth |
| **docker-tag:** | Docker tag. | string | 1.5.0 |
| **internal-volume** | Path to mount the internal volume. | string | /root/.celo |
| **syncmode** | Mode of node syncing. Support full, lightest. | string | full |
| **volume** | Volume path. | string | <- `${moncc-volume-path}/celo` |
| **entrypoint-extra-snippet** | Adding extra configuration to run before the node is up. | string | "" |
| **entrypoint** | Docker entrypoint path. | string | <- `/root/.celo/entry-point.sh` |
| **node-private-key** | Node private key. | string | f6a977f20df191439c3ccbf2e9ac771bb07c13bd5efcccf8d9eb716c64e7dc62 |

## 🔣 Celo Proxy variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **validator-signer-account** | Account address for the validator node. | string | 0x81d06eafb04Bd02d8795d0591Ed1666301c9d675 |
| **proxy-private-key** | Proxy account private key. | string | f6a977f20df191439c3ccbf2e9ac771bb07c13bd5efcccf8d9eb716c64e7dc62 |
| **proxy-address** | Proxy account address. | string | 0x0bc2b254f173d38228fa1f1e1b0554c7f28ca821 |
| **proxy-signer-password** | Proxy signer password. | string | secret |
| **proxy-options** | Proxy node specific option. | string | <- `--nodekey=/root/.celo/pkey --proxy.proxiedvalidatoraddress ${validator-signer-account} --proxy.proxy --proxy.internalendpoint :30503 --unlock=${proxy-address} --password /root/.celo/account/accountSecret --allow-insecure-unlock --proxy.allowprivateip --etherbase=${proxy-address}` |

## 🔣 Celo Validator variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **validator-signer-account** |  Account address for the validator node. | string | 0x81d06eafb04Bd02d8795d0591Ed1666301c9d675 |
| **validator-signer-private-key** | Validator private key. | string | 33f12c1689a9d07d5fc94325fd542201033a65e1b42db25f6be75e9cb578f9bc |
| **validator-signer-password** | Validator address password. | string | secret |
| **validator-options** | Validator node specific options. | string | <- `--mine --unlock ${validator-signer-account} --password /root/.celo/account/accountSecret --allow-insecure-unlock --miner.validator ${validator-signer-account} --tx-fee-recipient ${validator-signer-account}` |
| **proxy-enode-address** | Proxy enode address. | string |  f1d82e23c24d00f71aa584d7850cb9fefcaf764a3265e7c285d754b311d6845c8fdc77a964708e38fae8494889d7134ebc46e8cbae200f7cd5729936d219e711 |
| **proxy-internal-ip** | Proxy internal ip. | string | "" |
| **proxy-hostname** | Proxy hostname. | string | <- get-hostname("celo/proxy", "celo-node") |
| **proxy-external-ip** | Proxy external api. | string | <- peer-ip-address("celo/proxy") |
| **proxied-options** | Option to connect the validator with a proxy. | string | <- `--proxy.proxied --nodiscover --proxy.allowprivateip` |

## 🔣 Attestation Service variables

There is a more deeper documentation about how to run a Celo attestation Service in the following [link](https://docs.celo.org/validator-guide/attestation-service).

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **host** | Host to run the attestation service. | string | 0.0.0.0 |
| **http-port** | Port to run the attestation service. | int | 3000 |
| **internal-volume** | Path to mount the internal volume. | /root/.celo | string |
| **volume** | Volume path. | string | <- `${moncc-volume-path}/celo-attestation-service` |
| **database-name** | Name of the postgres database. | string | attestation-service. |
| **database-url-prefix** | Database url prefix. | string | <- "postgres://postgres:password@" get-hostname("celo/attestation-service-postgresql", "postgres") ":5432/"  concat-all |
| **node-env** | Node nvironment. | string | production |
| **celo-provider** | Celo node provider. | string | wss://alfajores-forno.celo-testnet.org/ws |
| **attestation-signer-address** | Attestations service signer address. | string | 0x8Dab89EC31aDCfF153e73EA3c0FE5A8B1F45475F |
| **celo-validator-address** | Celo validator address. | string | 0x287Cf31a31Fd1E0ef20dFDDB13D78eBb65CFA808 |
| **attestation-signer-keystore-passphrase** | Attestations service keystore passphrase. | string | "" |
| **attestation-signer-keystore-dirpath** | Attestations service keystore directory path. | string | /root/.celo |
| **log-format** | Logging format. | string | stackdriver |
| **sms-providers** | List of sms providers. | string | twilio,nexmo,telekom |
| **sms-providers-randomized** |  If set to 1 and no country-specific providers are configured for the country of the number being requested, randomize the order of the  default providers. Default 0. Note: setting this to 1 is only recommended if you do not have Vonage/Nexmo configured as a provider. | int | 1 |
| **nexmo-key** | The API key to the Nexmo API | string | "" |
| **nexmo-secret** | The API secret to the Nexmo API | string | "" |
| **messagebird-api-key** | The API key to the MessageBird API | string | "" |
| **telekom-api-key** | The API key to the Telekom API | string | "" |
| **telekom-from** | | string | "" |
| **twilio-account-sid** | The Twilio account ID | string | "" |
| **twilio-messaging-service-sid** | The Twilio Message Service ID. Starts with MG | string | "" |
| **twilio-verify-service-sid** | The Twilio Verify Service ID. Starts with VA | string | "" |
| **twilio-auth-token** | The API authentication token | string | "" |

Attestation service process will check for the correctness of the values and configuration provided. The validator and signer address must be a valid validator address and a authorized signer account.

## 🏛️ License

Copyright 2020 Keyko GmbH.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
