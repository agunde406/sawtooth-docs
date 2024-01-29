# Sawtooth 1.1 (Bumper)
<!--
  Copyright (c) 2018-2024 Bitwise IO, Inc.
  Licensed under Creative Commons Attribution 4.0 International License
  https://creativecommons.org/licenses/by/4.0/
-->

Sawtooth 1.1 (Bumper) is now available. See the
[documentation](https://sawtooth.splinter.dev/docs/1.1/) to
get started.

This page describes the new and changed features in
Sawtooth 1.1 (since release 1.0).

## Core Sawtooth Components

### New Features

- The consensus API has been completely redesigned. Consensus has been moved to
  a separate process, called a “consensus engine”. See [Sawtooth
  Consensus](#consensus) for more details.
- State pruning is now supported at a configurable block horizon. This helps
  limit the total storage requirements for global state by removing historic
  state after the configured horizon.
- Several example transaction processors have been rewritten in Rust, including
  IntegerKey (intkey), XO, and Smallbank.
- A new `/status` endpoint and `sawtooth status show` command are available to
  query status information for an active validator.
- New metrics have been added for submitted batches and transactions, thread
  pools, transaction processing, and dispatcher queue sizes.
- The internal metrics library has been refactored to be more modular and
  similar to the logging API.
- The gossip protocol used by Sawtooth is now versioned to support
  backwards-compatible upgrades in the future.

### Major Improvements

- The Rust SDK has matured. It now includes libraries for signing, writing
  transaction processors, and writing consensus engines, along with supporting
  information for the auto-generated SDK documentation.
- Several key items have been rewritten in Rust: Validator CLI argument
  parsing, global state database (and supporting views), ChainController, and
  BlockPublisher. This change allows some code to be executed outside of the
  Python global interpreter lock (GIL).
- The ChainController and BlockPublisher have been substantially refactored to
  be more modular and support validating blocks in parallel.

### Minor Improvements

- Transaction processors can now report the maximum number of transactions that
  they can handle at a time. The validator will only request that many
  transactions at a time for processing.
- Logging has been improved to reduce the volume and improve the quality of
  generated log messages.
- Discarded blocks are now explicitly canceled to avoid wasting effort on
  blocks that will never be chosen.
- Deserialization is now cached for messages that have multiple handlers to
  avoid redundant work.
- Peers are requested only from connections that have passed authorization.
- Client message handling has been moved to a separate thread pool to avoid
  interfering with transaction processing.
- The parallel scheduler has been refactored to use a generic `PredecessorTree`
  data structure.

### Bug Fixes

- Fixed a bad internal configuration that allowed the Completer and
  ChainController to get out of sync about which blocks were in the system
- Add missing handlers for client messages
- Correctly decrement "time to live" on gossip messages
- Handled an edge case where a new node will not get the chain until a new
  block is published
- Stopped trying to unschedule transactions that haven't been scheduled yet
- Fixed a number of concurrency issues where data structure were being accessed
  concurrently without protection or with incorrect usage of synchronization
  primitives
- Fixed a bug where a future could be executed in the event loop instead of in
  a thread pool
- Only broadcast to peers that have completed authorization
- Ensured that candidate blocks are only built in the BlockPublisher thread

### Upgrade Considerations

- The consensus setting `sawtooth.consensus.algorithm` setting has been
  deprecated and no longer has an effect. Instead, use the settings
  `sawtooth.consensus.algorithm.name` and `sawtooth.consensus.algorithm.version`
  to set the name and version of the consensus engine.
- All SDKs except Rust and Python have been moved from sawtooth-core to
  [separate repositories](https://github.com/splintercommunity?utf8=%E2%9C%93&q=sawtooth-sdk&type=&language=).
- PoET has been moved to [a new repository](https://github.com/splintercommunity/sawtooth-poet).

## Consensus

Sawtooth 1.1 includes a new consensus interface that enables
features like language independence for consensus algorithms. Consensus
protocols are now implemented as separate processes called “consensus engines",
which enables more consensus options for Sawtooth. Check out the [consensus API
RFC](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0004-consensus-api.md)
for more details.

- The network deployment tools have been updated to launch the consensus
  processes. If you have made your own custom launch scripts, note that the
  consensus engine now runs as its own process, like other services such as the
  REST API and transaction processors. Please see the example Sawtooth
  docker-compose files for reference.
- This release includes the following consensus engines based on the new
  consensus API:
  - [PoET consensus engine](https://github.com/splintercommunity/sawtooth-poet), a
    refactored version of the previous PoET consensus module.
  - Dev mode consensus engine, based on the previous dev mode consensus module.
  - New [PBFT consensus engine](https://github.com/splintercommunity/sawtooth-pbft),
    based on the Practical Byzantine Fault Tolerance (PBFT) consensus algorithm.
  - New [Raft consensus engine](https://github.com/splintercommunity/sawtooth-raft),
    based on the Raft consensus algorithm.

### Sawtooth PoET

The Sawtooth Proof of Elapsed Time (PoET) consensus can be deployed as a pure
Python application using a simulated enclave, called PoET simulator, or with a
C++ module implementing an Intel&reg; Software Guard Extensions (Intel&reg; SGX)
enclave, called PoET-SGX.

- PoET simulator is available in the 1.1 release as a consensus engine. If you
  are using PoET simulator consensus, we recommend upgrading to Sawtooth 1.1.
- PoET-SGX has not been validated on Sawtooth 1.1. Users relying on PoET-SGX
  are recommended to remain on Sawtooth 1.0. We are working on a new
  implementation of poet and its TEE enclave, which is anticipated for a point
  release in the near future.

### Sawtooth PBFT

The Sawtooth 1.1 release includes the [Sawtooth PBFT consensus
engine](https://github.com/splintercommunity/sawtooth-pbft). Sawtooth PBFT is based
on the [original PBFT paper](http://pmg.csail.mit.edu/papers/osdi99.pdf) with
several extensions to make it compatible with Sawtooth and to resolve known
issues with the original protocol. See the RFCs for more details:

- [Initial RFC](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0019-pbft-consensus.md)
- [Extension RFC to mitigate fair ordering and silent leader issues](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0029-pbft-regular-view-change.md)
- [Extension RFC to enable observer validation of consensus](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0030-pbft-consensus-seal.md)

Sawtooth PBFT is still in the prototype phase and is under active development.

## Documentation

In addition to updates for Sawtooth 1.1 features, technical
corrections, and bug fixes throughout, the Sawtooth documentation has the
following changes and improvements.

### Application Developer’s Guide

- Improved procedures for running a single Sawtooth node with Docker, Ubuntu,
  or AWS, plus a new Kubernetes procedure. See [Setting Up a Sawtooth
  Application Development Environment](https://sawtooth.splinter.dev/docs/1.1/app_developers_guide/installing_sawtooth.html).
- New procedures to add multiple nodes to a network for Docker, Ubuntu, and
  Kubernetes. See [Creating a Sawtooth Network](https://sawtooth.splinter.dev/docs/1.1/app_developers_guide/creating_sawtooth_network.html#about-the-sawtooth-network-environment).
- Updated procedure for trying the example tic-tac-toe transaction processor in
  Sawtooth. See [Playing with the XO Transaction Family](https://sawtooth.splinter.dev/docs/1.1/app_developers_guide/intro_xo_transaction_family.html).
- Improved and expanded tutorials for using the Sawtooth JavaScript, Go, and
  Python SDKs, plus a new Rust version. See [Using the Sawtooth SDKs](https://sawtooth.splinter.dev/docs/core/nightly/master/app_developers_guide/using_the_sdks.html).

### API References

- New Rust SDK documentation; see
  [Rust SDK API Reference](https://sawtooth.splinter.dev/docs/1.1/sdks.html#rust).

### System Administrator’s Guide

- Improved procedure for setting up a Sawtooth network with PoET simulator
  consensus, including new steps to change off-chain settings and test the
  system. See [Setting Up a Sawtooth Node](https://sawtooth.splinter.dev/docs/1.1/sysadmin_guide/setting_up_sawtooth_poet-sim.html).
- Updated procedure to configure a proxy server. See [Using a Proxy Server to
  Authorize the REST API](https://sawtooth.splinter.dev/docs/docs/1.1/sysadmin_guide/rest_auth_proxy.html).
- Updated permission information. See [Configuring Validator and Transactor
  Permissions](https://sawtooth.splinter.dev/docs/1.1/sysadmin_guide/configuring_permissions.html).
- New procedure to configure Sawtooth to display Grafana metrics. See [Using
  Grafana to Display Sawtooth Metrics](https://sawtooth.splinter.dev/docs/docs/1.1/sysadmin_guide/grafana_configuration.html).

### Architecture Guide

- General improvements and a new architecture overview.

### Glossary

- New glossary of Sawtooth terminology.

## Smart Contract Engines

### Sawtooth Sabre

Sawtooth 1.1 supports [Sawtooth
Sabre](https://github.com/splintercommunity/sawtooth-sabre), a transaction family
that implements on-chain smart contracts executed in a WebAssembly virtual
machine. Sabre smart contracts are stored on chain and executed using the Sabre
transaction processor.

Sawtooth Sabre includes an SDK for writing Sabre smart contracts in Rust. The
smart contracts can be written in such a way that they can be compiled into
transaction processor and run without Sabre. This also makes it easy to convert
already-written Rust transaction processors to a Sabre smart contract.

**Note:** Sabre is currently at version 0.1 and is under active development.

## DevOps and CI

### Build System Improvements

- The `bin/build_all` script and related build scripts have been replaced with
  `docker-compose`.
- The Docker Compose files now have 'build' sections, so that pre-building
  steps aren't necessary and all required images can be built with a single
  `docker-compose up` command.
- The `sawtooth-dev-{lang}` Dockerfiles are deprecated. Each component now has
  its own Dockerfile for development.

Check out the [build system
RFC](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0025-docker-compose-builds.md)
for more details.

**Note**: Requires Docker Engine 18.02.0 or later.

### Docker

- Each component now has "installed" Dockerfiles that utilize multi-stage
  builds. These Dockerfiles are suitable for publishing to a docker registry.

**Note**: Requires Docker Engine 18.02.0 or later.

### Kubernetes

- This release includes example files for two Kubernetes deployments: A
  five-node network using PoET simulator and a single-node environment using
  dev mode consensus. The Application Developer’s Guide describes how to use
  these example files; see the [Sawtooth
  documentation](https://sawtooth.splinter.dev/docs/1.1/app_developers_guide/).

## Website

The Sawtooth website,
[sawtooth.splinter.dev/](https://sawtooth.splinter.dev/), has been updated
with this release. This update includes:

- A new home page for Sawtooth that provides links to examples,
  documentation, and timely blog posts.
- The structure to easily update static content and blog posts.
- The ability to conduct agile changes, reviews, and approvals through Github.

Expect changes to the Sawtooth website on a regular basis. In the short term,
we plan to:

- Improve website navigation
- Automate the website build and deployment, based on the model for Sawtooth
- Apply consistent formatting throughout the site
  PRs

---

[The Sawtooth Team](https://sawtooth.splinter.dev/)
