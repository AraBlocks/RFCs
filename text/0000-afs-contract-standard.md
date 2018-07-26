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
contract AFSBase {
  address public owner_;

  ARAToken public token_;
  Library public lib_;

  string  public did_;
  bool    public listed_;
  uint256 public price_;

  mapping(bytes32 => uint256) internal rewards_;
  mapping(bytes32 => bool) internal purchasers_;

  event Commit(string _did, uint8 _file, uint256 _offset, bytes _buffer);
  event Unlisted(string _did);
  event PriceSet(string _did, uint256 _price);
  event RewardDeposited(string _did, uint256 _reward);
  event RewardDistributed(string _did, uint256 _distributed, uint256 _returned);
  event Purchased(string _purchaser, string _did, bool _download);

  modifier onlyBy(address _account)
  {
      require(
          msg.sender == _account,
          "Sender not authorized."
      );
      _;
  }

  // Storage (random-access-contract)
  mapping(uint8 => Buffers) metadata_;
  struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256[] offsets;
    bool invalid;
  }

  function setPrice(uint256 _price) external returns (bool success);

  function depositReward(uint256 _reward) public returns (bool success);
  function distributeReward(address[] _address, uint256[] _amounts) public returns (bool success);

  function purchase(string _purchaser, bool _download) external returns (bool success);

  // Storage methods (random-access-contract)
  function write(uint8 _file, uint256 _offset, bytes _buffer, bool _last_write) external returns (bool success);
  function read(uint8 _file, uint256 _offset) public view returns (bytes buffer);
  function unlist() public returns (bool success);
}
```

### 4.3 Global Variables

#### owner

The owner of this AFS. This is the only address that can modify the storage and pricing of this AFS contract.

``` solidity
address public owner_;
```

#### token

The ARAToken contract.

``` solidity
ARAToken public token_;
```

#### lib

The ARA Library contract.

``` solidity
Library public lib_;
```

#### did

The ARA DID of this AFS generated when the AFS was created.

``` solidity
string public did_;
```

#### listed

Boolean flag indicating whether this AFS can be purchased.

``` solidity
bool public listed_;
```

#### price

The total price (in ARA tokens) of this AFS.

``` solidity
uint256 public price_;
```

#### rewards

Mapping that stores reward amounts deposited by each purchaser.

``` solidity
mapping(bytes32 => uint256) internal rewards_;
```

#### purchasers

Mapping that stores the purchasers of this AFS.

```` solidity 
mapping(bytes32 => bool) internal purchasers_;
````

#### metadata

The AFS SLEEP files `metadata.tree` and `metadata.signatures`. `0` maps to the `tree` file and `1` maps the `signatures` file.

``` solidity
mapping(uint8 => Buffers) metadata_;
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
mapping (uint256 => bytes) buffers;
```

##### offsets

An array of all the offsets in the `buffers` mapping for the `tree` or `signatures` file

``` solidity
uint256[] offsets;
```

##### invalid

Flag representing the validity of this SLEEP file

``` solidity
bool invalid;
```

### 4.5 Methods

#### setPrice

Sets the price (in ARA tokens) of this AFS. Only the `owner_` may call this function.

``` solidity
function setPrice(uint256 _price) external returns (bool success);
```

#### depositReward

Deposits a reward allocation to be associated with the `msg.sender`.

``` solidity
function depositReward(uint256 _reward) public returns (bool success);
```

#### distributeReward

Distributes the previously deposited reward by `msg.sender`. The amounts provided by `_amounts` are sent to the provided `_addresses`, respectively. If there is any reward balance remaining following distribution, it is returned to `msg.sender`.

``` solidity
function distributeReward(address[] _address, uint256[] _amounts) public returns (bool success);
```

#### purchase

Transfers #`price` ARA tokens from `_purchaser`'s wallet and adds this `did` to `_purchaser`'s library in the _Library_ contract. Requires `_purchaser` to first [`approve`](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#approve) this AFS contract address in the _ARA Token_ contract. If `_download` is `true`, any remaining [`allowance`](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#allowance) is deposited as rewards via `depositReward`.

``` solidity
function purchase(string _purchaser, bool _download) external returns (bool success);
```

#### write

Writes `_buffer` at `_offset` in `_file`. If `_last_write` is true, emits the `Commit` event. If `_file` has been marked `invalid`, this function reverts. Only `owner_` may call this function.

``` solidity
function write(uint8 _file, uint256 _offset, bytes _buffer, bool _last_write) external returns (bool success);
```

#### read

Returns the `buffer` located at `_offset` in `_file` if it exists. Otherwise, returns an empty buffer.

``` solidity
function read(uint8 _file, uint256 _offset) public view returns (bytes buffer);
```

#### unlist

Invalidates the SLEEP files for this AFS by setting both `Buffer` structs to `invalid`. `listed()` should always return `false` after this function is called. Only `owner_` may call this function.

``` solidity
function unlist() public returns (bool success);
```

### 4.6 Events

#### Commit

MUST trigger when the final buffer has been written in a commit.

``` solidity
event Commit(string _did, uint8 _file, uint256 _offset, bytes _buffer);
```

#### Unlisted

MUST trigger when an AFS is unlisted.

``` solidity
event Unlisted(string _did);
```

#### PriceSet

MUST trigger when an AFS price is set.

``` solidity
event PriceSet(string _did, uint256 _price);
```

#### RewardDeposited

MUST trigger when rewards are deposited.

``` solidity
event RewardDeposited(string _did, uint256 _reward);
```

### RewardDistributed

MUST trigger when rewards are distributed.

``` solidity
event RewardDistributed(string _did, uint256 _distributed, uint256 _returned);
```

#### Purchased

MUST trigger when an AFS is purchased

``` solidity
event Purchased(string _purchaser, string _did, _download);
```

## 5. Real World Example

The following is a real world example of what a deployed AFS contract might look like with method implementations.

``` solidity
contract AFS is AFSBase {

  constructor(address _lib, address _token, string _did) public {
    owner_  = msg.sender;
    token_  = ARAToken(_token);
    lib_    = Library(_lib);
    did_    = _did;
    listed_ = true;
    price_  = 0;
  }

  function setPrice(uint256 _price) external onlyBy(owner_) returns (bool success) {
    price_ = _price;
    emit PriceSet(did_, price_);
    return true;
  }

  function depositReward(uint256 _reward) public returns (bool success) {
    bytes32 hashedAddress = keccak256(abi.encodePacked(msg.sender));
    if (purchasers_[hashedAddress]) {
      uint256 allowance = token_.allowance(msg.sender, address(this));
      if (allowance >= _reward 
        && token_.transferFrom(msg.sender, address(this), _reward)) {
        rewards_[hashedAddress] += _reward;
        assert(rewards_[hashedAddress] <= token_.balanceOf(address(this)));
        emit RewardDeposited(did_, _reward);
        return true;
      }
    }
    return false;
  }

  function distributeReward(address[] _addresses, uint256[] _amounts) public returns (bool success) {
    bytes32 hashedAddress = keccak256(abi.encodePacked(msg.sender));
    require(rewards_[hashedAddress] > 0 && _addresses.length == _amounts.length);

    uint256 totalRewards;
    for (uint256 i = 0; i < _amounts.length; i++) {
      totalRewards += _amounts[i];
    }
    require(rewards_[hashedAddress] <= token_.balanceOf(address(this)) 
      && rewards_[hashedAddress] >= totalRewards);

    success = true;
    for (uint256 j = 0; j < _addresses.length; j++) {
      success = success && token_.transferFrom(address(this), _addresses[j], _amounts[j]);
      if (success) {
        rewards_[hashedAddress] -= _amounts[j];
      }
    }

    uint256 returned = 0;
    if (rewards_[hashedAddress] > 0 
      && token_.transferFrom(address(this), msg.sender, rewards_[hashedAddress])) {
      returned = rewards_[hashedAddress];
      rewards_[hashedAddress] = 0;
    }

    emit RewardDistributed(did_, totalRewards - returned, returned);

    return success;
  }

  function purchase(string _purchaser, bool _download) external returns (bool success) {
    uint256 allowance = token_.allowance(msg.sender, address(this));
    require (allowance >= price_); // check if purchaser approved purchase

    if (token_.transferFrom(msg.sender, owner_, price_)) {
      bytes32 hashedAddress = keccak256(abi.encodePacked(msg.sender));
      purchasers_[hashedAddress] = true;
      lib_.addLibraryItem(_purchaser, did_);
      emit Purchased(_purchaser, did_, _download);

      if (_download && allowance > price_) {
        depositReward(allowance - price_);
      }

      return true;
    } else {
      return false;
    }
  }

  // Storage methods (random-access-contract)
  function write(uint8 _file, uint256 _offset, bytes _buffer, bool _last_write) external onlyBy(owner_) returns (bool success){
    // make sure AFS hasn't been removed
    require(!metadata_[_file].invalid);

    metadata_[_file].buffers[_offset] = _buffer;
    metadata_[_file].offsets.push(_offset);

    if (_last_write) {
      emit Commit(did_, _file, _offset, _buffer);
    }
    return true;
  }

  function read(uint8 _file, uint256 _offset) public view returns (bytes buffer) {
    if (metadata_[_file].invalid) {
      return ""; // empty bytes
    }
    return metadata_[_file].buffers[_offset];
  }

  function unlist() public onlyBy(owner_) returns (bool success) {
    metadata_[0].invalid = true;
    metadata_[1].invalid = true;
    listed_ = false;
    emit Unlisted(did_);
    return true;
  }
}
```

## 6. Drawbacks

N/A

## 7. Alternatives

* Create functional smart contracts with mappings for each AFS (i.e., separate contracts for `Storage`, `Price`, `Purchase`, `Rewards`)

## 8. Adoption Strategy

This AFS Contract Standard should be built into the AFS `commit` function, wherein the first ever `commit` for an AFS should deploy a smart contract for that AFS. All relevant AFS and DCDN functions, such as `destroy`, `price`, `rewards`, and `purchase` should seek to interface with this contract.

## 9. Unresolved Questions

N/A

## 10 Security Considerations

N/A

## 11. References

* [1] - *https://w3c-ccg.github.io/did-spec/*
