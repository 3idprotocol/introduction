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
- [Account Template](#account-template)
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

## Account Template

The account model extends the basic functionality of the core DID with more powerful capabilities. It provides a structured and flexible way to represent a user and their information in Ceramic. This template is constructs a set of [add link - tile documents]() that represent different aspects of a user's account. Together they enable a user centric data and service routing system.

![3ID Account Template](https://uploads-ssl.webflow.com/5ebcbef3ac4954196dcdc7b5/5ebffb1489197a29b2e38f38_account-tiles.png)

### Account

The `account` document is the foundational component for extending the core DID with additional capabilities and information. The account document only contains pointers to additional documents, such as the keychain, profile, account links, and other documents found below. Therefore, it acts as the root for routing to the user's linked identity information.

##### Schema

```json
{
  "$schema": "http://json-schema.org/draft-06/schema#",
  "$ref": "#/definitions/Account",
  "definitions": {
    "Account": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "account-links": {
          "type": "string",
          "pattern": "^ceramic:\/\/.+"
        },
        "profile": {
          "type": "string",
          "pattern": "^ceramic:\/\/.+"
        },
        "sources": {
          "type": "string",
          "pattern": "^ceramic:\/\/.+"
        },
        "services": {
          "type": "string",
          "pattern": "^ceramic:\/\/.+"
        },
        "keychain": {
          "type": "string",
          "pattern": "^ceramic:\/\/.+"
        }
      },
      "title": "Account"
    }
  }
}
```

##### Example

```json
{
  "account-links": "ceramic://bafyljsdf1",
  "profile": "ceramic://bafysdfoijwe1",
  "sources": "ceramic://bafysdfoijwe2",
  "services": "ceramic://bafysdfoijwe3",
  "keychain": "ceramic://bafysdfoijwe4"
}
```

### Keychain

The `keychain` document stores data needed to authenticate a DID and/or selectively disclose its encrypted information. It is used to add new authentication methods to the DID, such as blockchain/crypto accounts, allowing for the DID to be controlled by any cryptohraphic account or wallet key. 

The keychain also adds resilience to the DID by delegating key management to the user's many different wallets. The user will never lose control of their DID as long as they maintain control of only one of the authentication methods. The only way a user will lose control of their DID is in the catastrophic scenario where they lose all of their wallet keys at the same time.

Here's what's included in the keychain:
- Auth Public Keys: Public encryption keys (using the `x25519` curve) that allow the user to authenticate their DID with any cryptographic key pair. 
- Auth Data: The seed used to derive the DID keys, encrypted to each of the *Auth Public Keys*. 
- Legacy Data: Data encrypted to the public encryption key of the DID, needed for backwards compatibility of legacy 3IDs.
- Privacy Policy Read Keys: Symmetric keys used to encrypt data about Privacy Policies (more on these in the [Sources](#sources) section below). These keys are encrypted to the public encryption key of the DID. 

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "$ref": "#/definitions/Keychain",
    "definitions": {
        "KeychainTile": {
            "type": "object",
            "additionalProperties": false,
            "properties": {
                "privacy-policy-read-keys": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "auth-public-keys": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "auth-data": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "legacy-data": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                }
            },
            "title": "Keychain"
        }
    }
}
```

##### Example

```jsonc
{
    "privacy-policy-read-keys": [<JWE>],
    "auth-public-keys": [<multicodec-x25519-public-key>],
    "auth-data": [<JWE>],
    "legacy-data": [<JWE>]
}
```

### Account Links

The `account links` document stores a list of pointers to individual [account-links documents](./doctypes/account-link.md) for the user's DID. Account link documents allow DIDs to assert ownership over a blockchain account in a public, verifiable way. This allows others to discover and resolve which blockchain accounts are linked to a DID. For example, this is useful if an app wants to lookup the DID of a user's ETH address to display its profile, claims, or social graph.

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "type": "array",
    "items": {
        "type": "string",
        "pattern": "^ceramic:\/\/.+"
    },
    "definitions": {}
}
```

##### Example

```json
["ceramic://bafyljsdf1", "ceramic://bafyljsdf2"]
```

### Profile

The `profile` document stores

contains public information about the account

### Connections

The `connections` document contains a list of additional documents that represent various aspects of a user's social graph, such as following (public) or contacts (private).

#### Following

The following tile contains a list of DIDs that the user is following.

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "type": "array",
    "items": {
        "type": "string"
    },
    "definitions": {}
}
```

##### Example

```jsonc
["did:3:bafy1...", "did:3:bafy2..."]
```

#### Contacts

To be specified.

### Claims

The `claims` document contains a list of [verifiable claims](https://www.w3.org/TR/vc-data-model/) issued by other users or third-party services about the DID. For example, this could include social verifications for twitter, discord, and discourse, reputation scores, or anti-sybil/identity verifications. 

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "type": "array",
    "items": {
        "type": "string"
    },
    "definitions": {}
}
```

##### Example

```jsonc
[<JWT>, <JWT>]
```

### Sources

The `sources` document contains a list of data sources connected to the user. Data sources are described as a Each entry is encrypted using a unique *Privacy Policy Read Key* (see [keychain](#keychain)). This allows specific data sources to be kept private, but selectively disclosed upon request. Each entry, when decrypted, contains pointers to two additional documents, one for the collection policy and one for the privacy policy.

- Collection Policy: A document created by an application developer that describes the data model of their application. This document contains information about the schemas and configurations of the databases. There is only one single collection policy per application, and references to this document are stored by all users of the application.
- Privacy policy: A document that is generated as the user interacts with an application, which contains the specific ids/addresses of particular database instances that are controlled by the user for this application.

This abstraction provides two benefits. It allows third-party application developers to explore the possible data made available from existing applications by browsing the Ceramic network for public collection policies. However, it protects the privacy of users by obfuscating their relationship to an application and safeguards access to their data through encryption. For example, in the graphic [above](#account-template), there is a ThreadsDB Schema tile which describes the schema used by a Textile ThreadsDB instance.

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "type": "array",
    "items": {
        "type": "string"
    }
}
```
##### Example

```jsonc
["<JWE>", "<JWE>"]
```

##### Decrypted entry example

```json
{
  "collection-policy": "ceramic://bafy123",
  "privacy-policy": "ceramic://bafy234"
}
```

#### Collection Policy

As mentioned, the collection policy is a document created by an application developer to describe the data format for their application. Currently the collection policy supports describing [Textile](https://github.com/textileio/js-threads) and [OrbitDB](https://github.com/orbitdb/orbit-db) databases, but this can easily be extended to other database systems as well.

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "$ref": "#/definitions/CollectionPolicy",
    "definitions": {
        "TextileCollection": {
            "title": "TextileCollection",
            "type": "object",
            "properties": {
                "schema": {
                    "type": "string",
                    "title": "schema",
                    "pattern": "^ceramic:\/\/.+"
                }
            },
            "required": [
                "schema"
            ]
        },
        "Textile": {
            "title": "Textile",
            "type": "object",
            "properties": {
                "type": {
                    "type": "string",
                    "enum": [
                        "Textile"
                    ],
                    "title": "type"
                },
                "collections": {
                    "type": "object",
                    "additionalProperties": {
                        "$ref": "#/definitions/TextileCollection"
                    },
                    "title": "collections"
                }
            },
            "required": [
                "collections",
                "type"
            ]
        },
        "OrbitDB": {
            "title": "OrbitDB",
            "type": "object",
            "properties": {
                "type": {
                    "type": "string",
                    "enum": [
                        "OrbitDB"
                    ],
                    "title": "type"
                },
                "storeType": {
                    "type": "string",
                    "title": "storeType"
                },
                "accessController": {
                    "type": "object",
                    "properties": {
                        "type": {
                            "type": "string",
                            "title": "type"
                        },
                        "skipManifest": {
                            "type": "boolean",
                            "title": "skipManifest"
                        }
                    },
                    "required": [
                        "type"
                    ],
                    "title": "accessController"
                }
            },
            "required": [
                "accessController",
                "storeType",
                "type"
            ]
        },
        "CollectionPolicy": {
            "title": "CollectionPolicy",
            "type": "object",
            "additionalProperties": {
                "anyOf": [
                    {
                        "$ref": "#/definitions/Textile"
                    },
                    {
                        "$ref": "#/definitions/OrbitDB"
                    }
                ]
            }
        }
    }
}
```

##### Example

```json
{
    "my-textile-thread-definition": {
        "type": "Textile",
        "collections": {
            "set1": {
                "schema": "ceramic://bafyaasdf"
            }
        }
    },
    "my-orbitdb-definition": {
        "type": "OrbitDB",
        "storeType": "keyvalue",
        "accessController": {
            "type": "legacy-ipfs-3box",
            "skipManifest": true
        }
    }
}
```

#### Privacy Policy

The privacy policy document contains information that describes a particular data source in such a way as to make it consumable by other services upon consent. All of the data is encrypted using the specific [privacy policy read key](#keychain) of this policy. 

- Collection-policy property: A pointer to the collection policy document that this privacy policy applies to.
- Service-policies property: An array of service policy documents (not specified in this readme) that describe the services and their location where the data in this privacy policy exists. This includes services such as those that back up or pin the data. 
- Applications property: An array of applications that have been granted permission by the user.
- References property: An encrypted array of entries which, when decrypted, contains information about the database used to store the user's information.

##### Schema

```json
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "$ref": "#/definitions/PrivacyPolicy",
    "definitions": {
        "PrivacyPolicy": {
            "type": "object",
            "additionalProperties": false,
            "properties": {
                "collection-policy": {
                    "type": "string"
                },
                "service-policies": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "applications": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "references": {
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                }
            },
            "required": [
                "collection-policy",
                "references",
            ],
            "title": "PrivacyPolicy"
        }
    }
}
```

##### Example

```jsonc
{
    "collection-policy": "<JWE>",
    "service-policies": ["<JWE>"],
    "applications": ["<JWE>"],
    "references": ["<JWE>", "<JWE>"]
}
```

##### Decrypted references examples

```json
{
  "id": "my-orbitdb-definition",
  "orbit-db-address": "/orbitdb/abcd..."
}
```
```json
{
  "id": "my-textile-thread-definition",
  "thread-id": "<textile thread ID>"
}
```


### Services

The `services` document stores links to the service policies of the preferred services that the user can configure. For example, this might include third-party services such as data backup, notifications, email, messaging, and more.

### And More...

You can further customize a 3ID to support additional information by adding new Ceramic tiles and linking them from the account tile.

## Get Started

3ID is currently being implemented on the Ceramic Network. To contribute to research or development, 

Once the Ceramic Network is live, it will be easy to integrate 3ID into your project. You will only need to integrate with a Ceramic API (either through a local JavaScript node or remote http node), pass in the `account-template` module, and support a 3ID provider (such as 3ID Connect to allow users to use existing wallets for authentication).

## License

MIT
