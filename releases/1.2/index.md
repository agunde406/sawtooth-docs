# Sawtooth 1.2

<!--
  Copyright 2024 Bitwise IO, Inc.
  Copyright 2018-2020 Cargill Incorporated
  Licensed under Creative Commons Attribution 4.0 International License
  https://creativecommons.org/licenses/by/4.0/
-->

Sawtooth 1.2 (Chime) is now available.  See the [latest
documentation](https://sawtooth.splinter.dev/docs/1.2/) to
get started.

This page describes the new and changed features in Sawtooth 1.2
(since release 1.1).

## New Features

- Support for [Sawtooth PBFT consensus engine version
  1.0](https://sawtooth.splinter.dev/docs/1.2/pbft/introduction-to-sawtooth-pbft.html).
  - Sawtooth PBFT is the recommended consensus mechanism. It is designed to
    handle small to medium network sizes with finality and byzantine fault
    tolerance.
  - Sawtooth PoET is designed for larger networks at the expense of slower /
    probabilistic finality.
  - Sawtooth Raft is designed for small to medium networks but performs more
    slowly than Sawtooth PBFT and lacks byzantine fault tolerance.
- Mobile SDKs for iOS (Swift) and Android (as part of the Java SDK).
  - [Sawtooth SDK Swift](https://github.com/splintercommunity/sawtooth-sdk-swift)
  - [Sawtooth SDK Java](https://github.com/splintercommunity/sawtooth-sdk-java)
- Support for raw transaction headers, as specified in [Sawtooth RFC
  #23](https://github.com/splintercommunity/sawtooth-rfcs/blob/main/text/0023-raw-txn-header.md).
  This feature is backward compatible via the use of a protocol version indicator.
- All core transaction families are compatible with [Sawtooth Sabre release
  0.4](https://sawtooth.splinter.dev/docs/1.2/sabre/sabre_transaction_family.html).

## Non-breaking changes

- PoET has the following patches:
  - Resolve a security defect where a correctly admitted validator could game
    its wait times. Thanks to Huibo Wang, Guoxing Chen, Yinqian Zhang,
    and Zhiqiang Lin for analyzing the poet implementation and identifying the
    defect.
  - Support the updated attestation service API
- A new BlockManager has been implemented. This new feature
  improves block management and helps remove a known race condition that can cause
  network nodes to fork.
- Several core transaction families have been rewritten in Rust: Settings,
  Identity, and BlockInfo.
- All SDKs are in separate repositories for improved build time and release
  scheduling. The following SDKs were moved to their own repositories in this
  release:
  - [splintercommunity/sawtooth-sdk-go](https://github.com/splintercommunity/sawtooth-sdk-go)
  - [splintercommunity/sawtooth-sdk-python](https://github.com/splintercommunity/sawtooth-sdk-python)
  - [splintercommunity/sawtooth-sdk-rust](https://github.com/splintercommunity/sawtooth-sdk-rust)
- The Devmode consensus engine has been moved to a separate repository:
  [splintercommunity/sawtooth-devmode](https://github.com/splintercommunity/sawtooth-devmode).
- Consensus support has been modified to improve compatibility with PBFT
  consensus.
- Cache performance has been improved for settings and identity state.
- Duplicate signature validations have been eliminated.
- Duplicate batches are now removed from the pending queue and candidate blocks
- Transaction processors now receive a registration ACK before receiving
  transaction process requests.
- Long-lived futures are expired when awaiting network message replies.
- Logs now have fewer duplicate log messages.

## Documentation changes

- Improved summary of the supported consensus algorithms: PBFT, PoET, and
  Devmode. See
  ["Introduction"](https://sawtooth.splinter.dev/docs/1.2/).
- Complete procedures for configuring PBFT consensus on a Sawtooth node and
  changing network membership. For procedures to configure either PBFT or PoET
 consensus, see:
- [“Creating a Sawtooth Test Network” (Application Developer’s
  Guide)](https://sawtooth.splinter.dev/docs/1.2/app_developers_guide/creating_sawtooth_network.html)
- [“Setting Up a Sawtooth Network” (System Administrator’s
  Guide)](https://sawtooth.splinter.dev/docs/1.2/sysadmin_guide/setting_up_sawtooth_network.html)
- Technical corrections, bug fixes, and general improvements throughout the
  documentation.
