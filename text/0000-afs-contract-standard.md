<Topic Name>
==============

---
Request For Comments: 0000
---
Author(s): Eric Jiang
---
Category: Standard
---
Date: July, 2018
---

## 0. Abstract

**ARA Filesystem Contract Standard** defines a common abstract contract inherited by AFS smart contracts. This standard allows AFSs to be treated atomically in an object-oriented way on the blockchain by deploying a smart contract instance for each AFS.

## 1. Status

This RFC is under _active development and consideration_.

## 2. Introduction

**ARA Filesystem Contract Standard** enables AFSs to have a defined, structured, and self-contained presence on the blockchain. This standard provides basic AFS blockchain interactions such as price-setting, random-access-contract storage, and purchasing.

## 3. Background & Motivation

A standard abstract contract allows any AFS to be used everywhere on ARA, provides greater clarity into how AFSs are represented on the blockchain, and enhances ownership tracking. Furthermore, because we expect ARA tokens to be used for payments, unique contracts for each AFS facilitate more discrete [approvals](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#approve)[1], and thus, greater security.

## 4. AFS Contract Standard

> This section should detail any terminology used through the rest of
> this section or document that may be foreign, conflated, or confused by the
> the reader.

> This section is intended to explain the design features, protocols,
> structures, or systems described by this document. This section should
> be verbose, intuitive, contain necessary citations, provide code blocks, and
> contain enough information for implementation or integration.

This section outlines the functions and global variables included in the AFS abstract contract.

### 4.1 Terminology

The rest of the sections that follow make use of abbreviated forms of the
following terminology outlined in this section.

* _**DID**_ - Decentralized Identifier, the ARA identity of this AFS [2]

### 4.2 AFS Abstract Contract

``` js
contract AFS {
  address public owner;

  event Commit(string _identity);
  event Unlisted(string _identity);
  event PriceSet(string _identity);
  event RewardSet(string _identity);
  event Purchased(string _identity);

  // Storage (random-access-contract)
  mapping(uint8 => Buffers) buffer_mappings;
  struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256 largest_key;
    uint256[] keys;
    bool invalid;
  }

  // Basic methods
  function did() view returns (string did);
  function listed() view returns (bool valid);
  function price() view returns (uint256 price);
  function reward() view returns (uint256 reward);

  // Storage methods (random-access-contract)
  function write(uint8 file, uint256 offset, bytes buffer, bool last_write) returns (bool success);
  function read(uint8 file, uint256 offset) view returns (bytes buffer);
  function unlist() returns (bool success);

  // Pricing methods
  function setPrice(uint256 price) returns (bool success);
  function setReward(uint256 reward) returns (bool success);

  // Purchase methods
  function purchase(string identity) returns (bool success);
}
```

### 4.3 Global Variables

#### owner

The owner of this AFS. This is the only address that can modify the storage of this AFS contract.

``` js
address public owner
```

#### buffer_mappings

The AFS SLEEP files `metadata.tree` and `metadata.signature`.

``` js
mapping(uint8 => Buffers) buffer_mappings
```

### 4.4 Structs

#### Buffers

Represents either the `tree` or `signature` file

``` js
struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256[] keys;
    bool invalid;
  }
```

##### buffers

Maps buffer offsets to entries in the `tree` or `signature` file

``` js
mapping (uint256 => bytes) buffers
```

##### keys

An array of all the offset keys in the `buffers` mapping for the `tree` or `signature` file

``` js
uint256[] keys
```

##### invalid

Flag representing the validity of this SLEEP file

``` js
bool invalid;
```

### 4.5 Methods

#### did

Returns the ARA DID of this AFS generated when the AFS was created.

``` js
function did() public view returns (string did)
```

#### listed

Returns whether this piece of content can be purchased.

``` js
function listed() public view returns (bool valid)
```

#### price

Returns the total price (in ARA tokens) of this AFS, including rewards.

``` js
function price() public view returns (uint256 price)
```

#### reward

Returns the reward allocation for this AFS. Cannot exceed `price`

``` js
function reward() public view returns (uint256 reward)
```

#### write

Writes `buffer` at `offset` in `file`. If `last_write` is true, emits the `Commit` event. If `file` has been marked `invalid`, this function reverts. Only `owner` may call this function.

``` js
function write(uint8 file, uint256 offset, bytes buffer, bool last_write) returns (bool success)
```

#### read

Returns the `buffer` located at `offset` in `file` if it exists. Otherwise, returns an empty buffer.

``` js
function read(uint8 file, uint256 offset) view returns (bytes buffer)
```

#### unlist

Invalidates the SLEEP files for this AFS by setting both `Buffer` structs to `invalid`. `listed` should always return `false` after this function is called. Only `owner` may call this function.

``` js
function unlist() returns (bool success)
```

#### setPrice

Sets the price (in ARA tokens) of this AFS, including rewards. Only the `owner` may call this function. This function should `throw` if `reward` has been set and `price` does not exceed it.

``` js
function setPrice(uint256 price) returns (bool success)
```

#### setReward

Sets the reward allocation (in ARA tokens) for this AFS. Only the `owner` may call this function. This function should `throw` if `price` has been set and `reward` does not exceed it.

``` js
function setReward(uint256 reward) returns (bool success)
```

#### purchase

Transfers `price` ARA tokens from `purchaser`'s wallet and adds this `did` to `purchaser`'s library in the _Library_ contract. Requires `purchaser` to first `approve` this AFS contract address in the _ARA Token_ contract.

``` js
function purchase(string purchaser) returns (bool success)
```

### 4.6 Events

#### Commit

MUST trigger when the final buffer has been written in a commit

``` js
event Commit(string _did, uint8 _file, uint256 _offset, bytes _buffer)
```

#### Unlisted

MUST trigger when an AFS is unlisted

``` js
event Invalidated(string _did)
```

#### PriceSet

MUST trigger when an AFS price is set

``` js
event PriceSet(string _did, uint256 _price)
```

#### RewardSet

MUST trigger when an AFS reward is set

``` js
event RewardSet(string _did, uint256 _reward)
```

#### Purchased

MUST trigger when an AFS is purchased

``` js
event Purchased(string _purchaser, string _did)
```

## 5. Real World Example

> This section should present a real world example showing expected
> outcomes described by the content of this document.

> If an RFC describes a data structure, a human readable visual representation
> should be shown, such as a JSON structure.

> If an RFC describes a system or protocol, visuals showing interactions
> between entities described therein should be present along with
> ancillary data structures.

> If an RFC describes a functional API for a programming language, it
> should detail the signatures, usage, and combinations with other
> functions described in this document.

> This section is intended to be intuitive and present a real world case
> for the content described in this document.

## 6. Drawbacks

> This section should describe known drawbacks for implementers
> wishing to implement or integrate features, protocols, structures, or systems
> described by this document. A reader should be able to read this section
> and understand why, if there is a reason, they should *not* implement
> or integrate features, protocols, structures, or systems described by
> this document.

## 7. Alternatives

> This section should describe alternatives to the features, protocols,
> structures, or systems detailed in this document. If a RFC describes
> a key exchange protocol, then it should also detail the alternatives
> such as the Diffie-Hellman key exchange algorithm.

## 8. Adoption Strategy

> This section should describe necessary details for the implementation,
> integration, or adoption of the content described by this section.

## 9. Unresolved Questions

> This section should provide and attempt to resolve any lingering questions
> regarding the contents of this document.

## 10 Security Considerations

> This section should describe all security considerations related to
> the implementation or integration of the features, protocols, structures,
> or systems described by this document.

## 11. References

> Reference citation should be indexed and detailed in this section.
