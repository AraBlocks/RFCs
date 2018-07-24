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

**ARA Filesystem Contract Standard** defines a common interface inherited by AFS smart contracts. This standard allows AFSs to be treated atomically in an object-oriented way on the blockchain by deploying a smart contract instance for each AFS.

## 1. Status

This RFC is under _active development and consideration_.

## 2. Introduction

> An introduction into the features, protocols, structures, or systems described
> throughout this document.

## 3. Background & Motivation

> An explanation of the background and motivation for content described
> throughout the document. This section should detail use cases
> and expected outcomes.

## 4. AFS Contract Standard

> This section should detail any terminology used through the rest of
> this section or document that may be foreign, conflated, or confused by the
> the reader.

> This section is intended to explain the design features, protocols,
> structures, or systems described by this document. This section should
> be verbose, intuitive, contain necessary citations, provide code blocks, and
> contain enough information for implementation or integration.

```
pragma solidity 0.4.24;

contract AFS {
  address public owner;

  // Basic AFS info
  string did;
  bool valid;
  uint256 price;
  uint256 reward;

  // Storage 
  mapping(uint8 => Buffers) buffer_mappings;
  struct Buffers {
    mapping (uint256 => bytes) buffers;
    uint256 largest_key;
    uint256[] keys;
    bool invalid;
  }

  // Basic methods
  function getDID() public view returns (string did);
  function getIsValid() public view returns (bool valid);

  // Storage methods
  function write(uint8 file, uint256 offset, bytes buffer, bool last_write) public;
  function read(uint8 file, uint256 offset) public view returns (bytes buffer);
  function delete() public;

  // Pricing methods
  function setPrice(uint256 price) public;
  function getPrice() public view returns (uint256 price);
  function setReward(uint256 reward) public;
  function getReward() public view returns (uint256 reward);

  // Purchase methods
  function purchase(string identity) public;
}
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
