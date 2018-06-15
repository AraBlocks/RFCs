ARA Module Manager
==============

---
Request For Comments: 0000
---
Author(s): Madeline Cameron
---
Category: Interopability
---
Date: June, 2018
---

## 0. Abstract

_**ARA Node Manager**_ is a process allowing ARA nodes to require other ARA nodes to be booted before booting.

## 1. Status

This RFC is under _active development and consideration_.

## 2. Introduction

**ARA Node Manager** communicates over an IPC protocol. Modules that want to use ANM can communicate over the protocol to voice their requirements. ANM will use localcast as it is easiest to integrate and is well-documented.

## `register`

`register` is an event that an ARA node can push to register its existance so it is known to ANM.

## `unregister`

`unregister` is an event that an ARA node can push to unregister from the local ANM node.

## `require`

`require` is an event that an ARA node can push to describe the nodes it requires. The message should include an array of the nodes' names that are required.

## `success`

`success` is an event that ANM will push back to the requesting node if all nodes desired are started. This message may contain instances as an argument in the exact order they were required in.

## `fail`

`fail` is an event that ANM will push back to the requesting node if any nodes desired do not exist or cannot be started.

## 3. Background & Motivation

* Why are we doing this?

ARA Nodes already contain the ability to return their own instances so this node will enable nodes to share themselves easily

Additionally, this is useful for building nodes that do higher-order functions.

* What use cases does it support?

- Allowing nodes to ensure pre-requisite nodes are booted before they are, and use the same instances

* What is the expected outcome?

- Modules will be able to communicate what other nodes need to be booted before they can

## 5. Real World Example

```javascript
const ARARequire = require('ara-node-require')

const araRequire = new ARARequire('node-name')

const request = araRequire.require([ 'dht', 'dns' ])
request.on('success', ({ dht, dns}) => {
  // Do stuff
})

request.on('fail', (err) => {
  console.error('Something happened: ', err)
})
```

## 6. Drawbacks

It might not be entirely needed. Lower-order nodes like DHT and DNS could always be booted or nodes could boot their own nodes without regards for others.

Additionally we could build a better ARA Network Node CLI which has this functionality built in (which, honestly, is my intention to propose anyway)

## 7. Alternatives

I considered using a proper cross-platform-compatable IPC library but it was poorly documented and the code quality is quite low. I think localcast will allow us to prototype this node quickly and figure out its usefulness.

Additionally, like I said above, we could have nodes manage their own required nodes without regards for others

## 8. Adoption Strategy

It isn't a breaking change. It adds functionality, is completely optional and only requires implementation if they want to. If a node doesn't implement this protocol, it won't be able to required.

## 9. Unresolved Questions

  - Is this needed?
  - Is this forseen as being helpful?
  - Should this be directly proposed as a modification to ANN itself rather than as a stand-alone node?

## 10 Security Considerations

No concerns

## 11. References

[Localcast](https://github.com/mafintosh/localcast)
