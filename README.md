![3ID Logo](https://uploads-ssl.webflow.com/5ebcbef3ac4954196dcdc7b5/5ebf4cbf6f47032c789b2074_3ID%20Github.png)

# [WIP] 3ID: A protocol for cross-platform identity

> 3ID is a cross-platform identity protocol built on the Ceramic network

[3ID](http://3idprotocol.org) is a flexible, lightweight framework for creating and managing W3C-compliant [decenentralized identifiers](https://www.w3.org/TR/did-core/) (DIDs) that work across all platforms and authentication systems, providing unification and interoperability to users across the worldwide web. 

3ID provides:
- Permissionless, unified identity that works across the worldwide web
- Integration with existing Web3 account frameworks and other authentication systems
- Storage for any kind of identity metadata, such as profiles, claims, social graphs, etc
- User-centric routing to external resources, such as blockchain data, off-chain data stores, APIs, etc
- Unified access control to off-chain resources, such as data stores, services, APIs, etc

### Contents

- [Architecture](#architecture)
- [Decentralized Identifier (DID)](#decentralized-identifier-did)
- [Account Model](#account-model)
  - [Account](#account)
  - [Keychain](#keychain)
  - [Account Links](#account-links)
  - [Profile](#profile)
  - [Connections](#connections)
  - [Claims](#claims)
  - [Sources](#sources)
  - [Services](#services)
  - [And more](#and-more)
- [Get Started](#get-started)
- [License](#license)

## Architecture

3IDs are built using documents on the [Ceramic Network](http://ceramic.network). Ceramic is a network that allows for the permissionless publishing of metadata and content in a chain-agnostic way. Metadata is the perfect analogy for decentralized identity, since identifiers and their associated data, are forms of metadata about various accounts or resources that many exist in many different locations.

This document-based design provides infinite flexibility and extensibility, while Ceramic ensures identities, and their associated metadata, are always publicly available and resolvable. Also, Ceramic is agnostic to underlying blockchains or other services and acts as a connector of networks, or meta-network. 

## Decentralized Identifier (DID)

At a minimun, 3IDs consist of a DID Document and a seed used to sign and enctypt messages and data. DIDs are controlled by a seed.

- DID: 
- Signing & Encryption Keys: Communication Interoperable signing and encryption and access control. A way to commonly and universally attribute messages or data to a DID across resources.
- Owners and Delegates (Optional): Great for allowing users to link multiple DIDs, and also for non-user identity models such as businesses, DAOs, ... Admin/employee functionality

Account (Optional): Optional

## Account Model

The account model extends the basic functionality of the core DID with more powerful capabilities and additional information. The account model is constructed using [add link - Ceramic tiles]().

![3ID Account Template](https://uploads-ssl.webflow.com/5ebcbef3ac4954196dcdc7b5/5ebffb1489197a29b2e38f38_account-tiles.png)

### Account

The `account` document is the foundational component for extending the core DID with additional capabilities and information. The account tile only stores links to additional documents, and acts as the root for routing to this linked identity information.

### Keychain

The `keychain` document stores additional authentication and access control information for the DID. It is used to add new authentication methods to the DID, such as blockchain/crypto accounts, allowing for the DID to be controlled by any cryptohraphic account or wallet key. 

The keychain also adds resilience to the DID by delegating key management to the user's many different wallets, since the user will never lose control of their DID as long as they maintain control of one of the authentication methods. The only way a user will lose control of their DID is in the catastrophic scenario where they lose all of their wallet keys at the same time.

### Account Links

The `account links` document stores links to [add link - account link]() documents. Account link documents allow DIDs to publicly assert ownership over a blockchain account in a verifiable way. This allows others to publicly discover and resolve which blockchain accounts are linked to a DID. For example, this is useful if an Ethereum app wants to lookup the DID of an ETH address to display its profile, claims, or social graph.

### Profile

The `profile` document stores

### Connections

The `connections` document stores

### Claims

The `claims` document stores

### Sources

The `sources` document stores Things trusted by the user such as backup services, notifications services, email services, etc.

### Services

The `services` document stores

### And More...

You can further customize a 3ID to support additional information by adding new Ceramic tiles and linking them from the account tile.

## Get Started

3ID is currently being implemented on the Ceramic Network. To contribute to research or development, 

Once the Ceramic Network is live, it will be easy to integrate 3ID into your project. You will only need to integrate with a Ceramic API (either through a local JavaScript node or remote http node), pass in the `account-template` module, and support a 3ID provider (such as 3ID Connect to allow users to use existing wallets for authentication).

## License

MIT
