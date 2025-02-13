+++
date = 2025-02-13
draft = false
type = "blog"
title = "Bitcoin Core scope, concretely"
description = "A route to re-focus the main Bitcoin Core repo on its core mission."
weight = 10
author = "Antoine Poinsot"
tags = ["Bitcoin", "Bitcoin Core"]
+++

In my last two posts i presented Bitcoin Core's [prioritisation
issues](https://antoinep.com/posts/core_project_direction) and then [provided an
answer](https://antoinep.com/posts/stating_the_obvious) to what the scope and goals of the Bitcoin
Core project should be. Now what?

Well, it's not like we are starting a new project from scratch where we could just begin enforcing
the scope as we develop it. We are fixing things in flight, with users and developers that depend on
or care about existing modules that would be out of scope. Although developers working in these
areas should realize they impose a non-trivial cost on all the other contributors that don't believe
they should be a priority, just throwing shit at each other is not going to move things forward.
Engaging with "just delete the GUI" or "just delete the wallet" is a non-starter.

On the other hand not doing anything is making a choice by default, the choice of continuous burden
on everyone else and reduced overall software quality.  This needs to be addressed if we want to
keep Bitcoin Core the go-to full node client for Bitcoin users, but also to keep critical
contributors motivated to continue working on the project.

The multiprocess project presents an opportunity in this regard. Separate `bitcoin-node`,
`bitcoin-wallet` and `bitcoin-gui` binaries communicating through well-defined interfaces open the
door to splitting the current project into 3. The contributors at
[github.com/bitcoin/bitcoin](https://github.com/bitcoin/bitcoin) would focus on the Bitcoin Core
node and release a `bitcoin-node` program, exposing JSONRPC and IPC interfaces. The contributors
at [github.com/bitcoin-core/wallet](github.com/bitcoin-core/wallet) would work on the
`bitcoin-wallet` process. The Bitcoin Core wallet releases would contain both the wallet and node
processes, presumably with a wrapper to make it transparent to users. This project would release the
equivalent of today's `bitcoind`. Finally, the contributors at
[github.com/bitcoin-core/gui](https://github.com/bitcoin-core/gui) would develop and release the
`bitcoin-gui` program, which would ship with the node and wallet.  Similarly to the wallet it would
presumably ship with a wrapper to abstract for the user the technicality of using multiple
processes. Bitcoin Core the project would still announce and host the node, wallet and GUI releases
at https://bitcoincore.org.

This approach would make it so Bitcoin Core still releases a wallet and a GUI while still reaping
the benefit that developments in these areas don't impact the workflow of contributors to the node
project anymore. Of course, there is no free lunch. The maintenance, fix and update burden shifting
out of the node contributors either means it will shift on to the contributors of the wallet and GUI
project or that it won't happen at all. This is arguably the entire point: we are currently [trading
off](https://antoinep.com/posts/core_project_direction) quality in the node for maintainance of
these areas.  I argue [this is backward](https://antoinep.com/posts/stating_the_obvious).

This could also be an opportunity for the wallet and GUI projects. There's been little interest in
contributing to the Bitcoin Core wallet in the past years, despite there being tremendous interest
in working on Bitcoin wallets in general. For instance
[`rust-bitcoin`](https://github.com/rust-bitcoin) has never made so much progress, and
[BDK](https://github.com/bitcoindevkit/bdk) is a major success [adopted by dozens of
projects](https://bitcoindevkit.org/adoption/all) with probably millions of users altogether. They
have a larger feature set, with the Bitcoin Core wallet still struggling to catch up even years
after a feature has been largely adopted by the industry[^0]. I count at least two dozen active
contributors across these two projects. Regarding the GUI, it's hardly being developed at all
anymore, with only 29 PRs merged in the past year[^1]. There's been talk of revamping it forever
([1](https://github.com/BitcoinDesign/Bitcoin-Core-App),
[2](https://github.com/bitcoin-core/gui-qml)) but it has never really materialized. It would be
reasonable for the wallet to have a lower review bar than the node, and for the GUI to have a lower
one than the wallet, as long as it's advertized to our users. This could enable these projects to
iterate faster and make more progress. Surely, being in their own project shielded from the
[animosity of other
contributors](https://bitcoin-irc.chaincode.com/bitcoin-core-dev/2025-01-30#1086218) and with the
ability to make progress could make working on these project more fun and attract new contributors.
A larger contributor base could in turn lead to improved software quality and increased capacity to
catch up with features.

Doing this raises a number of questions. What branch of the node would the wallet and GUI projects
track? How do you split `depends`? What about the Guix builds? The shared code in `src/common`,
`src/util`, `src/crypto`, in tests, etc? There may be non-trivial and non-ideal choices to make in
the process but we should not overlook the benefits of this approach: we re-focus the main project
toward its critical mission and we keep the wallet and GUI, possibly unleashing more progress in
these areas.


[^0]: See for instance PSBT v2, a BIP that Ava authored and is [still not merged into Bitcoin Core
after 4 years](https://github.com/bitcoin/bitcoin/pull/21283) due to lack of review. Meanwhile
[Ledger started using it 4 years
ago](https://groups.google.com/g/bitcoindev/c/OPx9qvmr5Nc/m/8mp8VHjQBQAJ) and it was [merged in
libwally](https://github.com/ElementsProject/libwally-core/pull/330) 2.5 years ago then [adopted by
Core-Lightning](https://github.com/ElementsProject/lightning/pull/5898) 2 years ago.

[^1]: Running this command on February 12th 2025: `git log --merges --grep "bitcoin-core/gui#" --oneline --since=2024-02-12 |wc -l`.
