AFS Contract Standard
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

A standard abstract contract allows any AFS to be used everywhere on ARA, provides greater clarity into how AFSs are represented on the blockchain, and enhances ownership tracking. Furthermore, because we expect ARA tokens to be used for payments, unique contracts for each AFS facilitate more discrete [approvals](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#approve), and thus, greater security.

## 4. AFS Contract Standard

This section outlines the functions and global variables included in the AFS abstract contract.

### 4.1 Terminology

The rest of the sections that follow make use of abbreviated forms of the
following terminology outlined in this section.

* _**DID**_ - Decentralized Identifier, the ARA identity of this AFS [1]

### 4.2 AFS Abstract Contract

``` solidity
contract AFS {
  address public owner;

  event Commit(string _did, uint8 _file, uint256 _offset, bytes _buffer);
  event Unlisted(string _did);
  event PriceSet(string _did, uint256 _price);
  event RewardSet(string _did, uint256 _reward);
  event Purchased(string _purchaser, string _did);

  // Storage (random-access-contract)
  mapping(uint8 => Buffers) metadata;
  struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256[] offsets;
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

``` solidity
address public owner
```

#### metadata

The AFS SLEEP files `metadata.tree` and `metadata.signatures`. `0` maps to the `tree` file and `1` maps the `signatures` file.

``` solidity
mapping(uint8 => Buffers) metadata
```

### 4.4 Structs

#### Buffers

Represents either the `tree` or `signatures` file

``` solidity
struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256[] offsets;
    bool invalid;
  }
```

##### buffers

Maps buffer offsets to entries in the `tree` or `signatures` file

``` solidity
mapping (uint256 => bytes) buffers
```

##### offsets

An array of all the offsets in the `buffers` mapping for the `tree` or `signatures` file

``` solidity
uint256[] offsets
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
event Unlisted(string _did)
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

TBD (include example of deployed AFS contract)

## 6. Drawbacks

> This section should describe known drawbacks for implementers
> wishing to implement or integrate features, protocols, structures, or systems
> described by this document. A reader should be able to read this section
> and understand why, if there is a reason, they should *not* implement
> or integrate features, protocols, structures, or systems described by
> this document.

TBD

## 7. Alternatives

* Create functional smart contracts with mappings for each AFS (i.e., separate contracts for `Storage`, `Price`, `Purchase`, `Rewards`)

## 8. Adoption Strategy

This AFS Contract Standard should be built into the AFS `commit` function, wherein the first ever `commit` for an AFS should deploy a smart contract for that AFS. All relevant AFS and DCDN functions, such as `destroy`, `price`, `rewards`, and `purchase` should seek to interface with this contract.

## 9. Unresolved Questions

TBD

## 10 Security Considerations

TBD

## 11. References

* [1] - *https://w3c-ccg.github.io/did-spec/*
